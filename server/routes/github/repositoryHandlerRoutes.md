---
  title: 'repositoryHandlerRoutes.mjs'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # repositoryHandlerRoutes.mjs
  # Code Explanation

The provided code is a Node.js application using Express framework to create RESTful APIs for managing repositories and files on GitHub. It utilizes the Octokit library to interact with the GitHub API for repository creation, file uploads, and fetching repository contents.

### Dependencies
- **Express**: A Node.js web application framework used for building APIs.
- **Octokit**: A GitHub API client library for Node.js.
- **Multer**: Middleware for handling file uploads.
- **@octokit/rest**: GitHub REST API client for Octokit.
- **helpers**: Custom helper functions for handling GitHub operations.
- **middleware**: Custom middleware functions for authentication.

### Functions and Endpoints
1. **`setupOctokit(token)`**: Function to initialize Octokit with the provided GitHub access token.

2. **`/create-repository`**: POST endpoint to create a new repository on GitHub.
   - Parameters:
     - `name`: Name of the repository.
     - `description`: Description of the repository.
   - Operations:
     - Sanitizes the repository name and description.
     - Creates a new repository using `octokit.rest.repos.createForAuthenticatedUser`.
     - Updates the README.md file with initial content.
   - Example Usage:
     ```bash
     POST /create-repository
     Body: { "name": "MyLibrary", "description": "A sample library" }
     ```

3. **`/create-folder`**: POST endpoint to create folders in a repository on GitHub.
   - Parameters:
     - `repository`: Name of the repository.
     - `structure`: Folder structure to create.
   - Operations:
     - Creates folders with a `.gitkeep` file to maintain empty folders.
   - Example Usage:
     ```bash
     POST /create-folder
     Body: { "repository": "MyLibrary", "structure": { "structure": ["folder1", "folder2"] } }
     ```

4. **`/repository-contents`**: GET endpoint to fetch contents of a repository on GitHub.
   - Parameters:
     - `repo`: Name of the repository.
     - `path`: Path within the repository (optional).
   - Operations:
     - Fetches contents of the specified repository.
   - Example Usage:
     ```bash
     GET /repository-contents?repo=MyLibrary&path=folder1
     ```

5. **`/repositories/library`**: GET endpoint to fetch user's repositories containing "library" in the name.
   - Operations:
     - Searches for repositories with "library" in the name and sorts them by stars.
   - Example Usage:
     ```bash
     GET /repositories/library
     ```

6. **`/upload-file`**: POST endpoint to upload files to a repository on GitHub.
   - Parameters:
     - `repository`: Name of the repository.
     - `path`: Path to upload the files.
     - `model`: Model for file processing.
     - `instructions`: Instructions for file processing.
   - Operations:
     - Uploads files to the specified repository with markdown content.
   - Example Usage:
     ```bash
     POST /upload-file
     Body: { "repository": "MyLibrary", "path": "docs", "model": "3.5-turbo", "instructions": "Process files" }
     ```

### Middleware
- **`ensureAuthenticated`**: Middleware function to ensure user authentication before accessing the endpoints.

This code snippet demonstrates how to interact with the GitHub API to manage repositories, upload files, and fetch repository contents using Node.js and Express.
  
  ## Component Code
  ```jsx
  import express from "express";
import { Octokit } from "@octokit/rest";
import { ensureAuthenticated } from "../middleware/authMiddleware.mjs";
import multer from "multer";
import { createFile } from "../helpers/aiHelper.mjs";
import {
  createMarkdown,
  createOrUpdateFile,
  fileExists,
  initialReadme,
  sendProgressUpdate,
  handleError
} from "../helpers/helpers.mjs"
import { sanitizeCreateRepository } from "../helpers/formValidations.mjs";


const router = express.Router();
const storage = multer.memoryStorage();
const upload = multer({ storage: storage });

const setupOctokit = (token) => new Octokit({ auth: token });



router.post("/create-repository", ensureAuthenticated, async (req, res) => {
  const token = req.user.accessToken;
  const octokit = setupOctokit(token);

  try {
    let { name, description } = req.body;

    const { sanitizedTitle, sanitizedDescription } = sanitizeCreateRepository(
      name,
      description
    );
    const repoName = `library-${sanitizedTitle}`;

    const response = await octokit.rest.repos.createForAuthenticatedUser({
      name: repoName,
      description: sanitizedDescription,
      private: false,
      auto_init: true,
    });

    const readmeResponse = await octokit.rest.repos.getContent({
      owner: response.data.owner.login,
      repo: repoName,
      path: "README.md",
    });

    const readmeContent = initialReadme(
      repoName,
      sanitizedDescription,
      new Date().getFullYear(),
      req.user.profile.displayName || req.user.profile.username
    );

    await octokit.rest.repos.createOrUpdateFileContents({
      owner: response.data.owner.login,
      repo: repoName,
      path: "README.md",
      message: "Update README with initial content",
      content: Buffer.from(readmeContent).toString("base64"),
      sha: readmeResponse.data.sha,
    });

    res.json({
      message: "Repository created successfully!",
      repository: response.data,
      repositoryUrl: response.data.html_url,
    });
  } catch (error) {
    handleError(res, "Failed to create repository", error);
  }
});

router.post("/create-folder", ensureAuthenticated, async (req, res) => {
  const token = req.user.accessToken;
  const { repository, structure } = req.body;
  const octokit = setupOctokit(token);

  try {
    for (const folder of structure.structure) {
      const path = `${folder}/.gitkeep`;
      await createOrUpdateFile(octokit, req.user.profile.username, repository, path, "Create folder with .gitkeep", "");
    }
    res.json({ message: "Selected folder structure created successfully!" });
  } catch (error) {
    handleError(res, "Failed to create selected folder structure", error);
  }
});

router.get("/repository-contents", ensureAuthenticated, async (req, res) => {
  const token = req.user.accessToken;
  const { repo, path = "" } = req.query;

  if (!repo) return res.status(400).send("Repository name is required.");

  const octokit = setupOctokit(token);

  try {
    const response = await octokit.rest.repos.getContent({
      owner: req.user.profile.username,
      repo: repo,
      path: path,
      ref: "main",
    });

    const contents = Array.isArray(response.data)
      ? response.data
      : [response.data];

    res.json({
      message: "Repository contents fetched successfully!",
      contents: contents.map((item) => ({
        name: item.name,
        path: item.path,
        type: item.type,
        download_url: item.download_url || null,
        content: item.content
          ? Buffer.from(item.content, "base64").toString("utf-8")
          : null,
      })),
    });
  } catch (error) {
    handleError(res, "Failed to fetch repository contents", error);
  }
});

router.get("/repositories/library", ensureAuthenticated, async (req, res) => {
  const token = req.user.accessToken;
  const username = req.user.profile.username;
  const octokit = setupOctokit(token);

  try {
    const { data } = await octokit.rest.search.repos({
      q: `user:${username} library in:name`,
      sort: "stars",
      order: "desc",
    });

    const filteredRepos = data.items.filter(
      (repo) => !repo.name.startsWith(`library-${username}-config`)
    );

    res.json({
      message: "Repositories fetched successfully!",
      repositories: filteredRepos,
    });
  } catch (error) {
    handleError(res, "Failed to fetch repositories", error);
  }
});

router.post(
  "/upload-file",
  ensureAuthenticated,
  upload.array("files", 4),
  async (req, res) => {
    if (!req.files || req.files.length === 0) {
      return res.status(400).send("No files uploaded.");
    }

    const { repository, path: uploadPath, model, instructions } = req.body;
    const token = req.user.accessToken;
    const octokit = setupOctokit(token);
    const author = req.user.profile.displayName || req.user.profile.username;

    try {
      const results = [];
      let processedFiles = 0;

      for (const file of req.files) {
        const content = file.buffer.toString("utf8");
        const completion = await createFile(content, model, instructions);
        const explanation = completion.text;
        const markdownContent = createMarkdown(
          file.originalname,
          explanation,
          content,
          author
        );

        const cleanPath = uploadPath.startsWith("/")
          ? uploadPath.substring(1)
          : uploadPath;
        let baseFileName = file.originalname.replace(
          /\.(js|jsx|ts|tsx|py|java|rb|php|html|css|cpp|c|go|rs|swift|kt|m|h|cs|json|xml|sh|yml|yaml|vue|svelte|qwik|sv|astro|mjs)$/,
          ""
        );
        let extension = ".md";
        let markdownFilePath = `${cleanPath}/${baseFileName}${extension}`;

        let version = 1;
        while (
          await fileExists(
            octokit,
            req.user.profile.username,
            repository,
            markdownFilePath
          )
        ) {
          markdownFilePath = `${cleanPath}/${baseFileName}_v${version}${extension}`;
          version++;
        }

        const response = await octokit.rest.repos.createOrUpdateFileContents({
          owner: req.user.profile.username,
          repo: repository,
          path: markdownFilePath,
          message: `Upload file ${file.originalname}`,
          content: Buffer.from(markdownContent).toString("base64"),
        });

        results.push(response.data);

        processedFiles++;
        sendProgressUpdate(processedFiles, req.files.length);

        if (model !== "3.5-turbo") {
          await new Promise((resolve) => setTimeout(resolve, 500));
        }
      }

      res.json({
        message: "Files uploaded successfully!",
        data: results,
      });
    } catch (error) {
      console.error("Error during file upload:", error);
      handleError(res, "Failed to upload files", error);
    }
  }
);



export default router;
  ```
  