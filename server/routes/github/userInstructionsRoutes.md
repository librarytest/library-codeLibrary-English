---
  title: 'userInstructionsRoutes.mjs'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # userInstructionsRoutes.mjs
  # Explanation of Code

The provided code is a Node.js Express router that interacts with the GitHub API using the Octokit library to perform operations related to fetching, saving, updating, and deleting user instructions stored in a GitHub repository.

### Dependencies
- **Express**: A Node.js web application framework used for creating server-side applications.
- **Octokit**: A GitHub API client library for JavaScript that simplifies interactions with the GitHub API.
- **uuid**: A library used to generate unique identifiers.

### Functions
1. **getOctokitInstance**: Creates a new Octokit instance with the provided access token for authentication.
2. **getRepoName**: Generates a repository name based on the GitHub username.
3. **fetchRepoContent**: Fetches the content of a file from a GitHub repository, decodes it from base64, and returns the content along with the SHA hash.
4. **checkOrCreateRepo**: Checks if a repository exists for the user and creates one if it doesn't.
5. **updateRepoContent**: Updates the content of a file in a GitHub repository with new content.

### Routes
1. **GET /get-instructions**: Retrieves user instructions from the GitHub repository. If the file does not exist, an empty array is returned.
2. **POST /save-instructions**: Saves new instructions to the GitHub repository.
3. **POST /update-instruction**: Updates existing instructions in the GitHub repository based on the provided instruction ID.
4. **DELETE /delete-instruction**: Deletes an instruction from the GitHub repository based on the provided instruction ID.

### Example Usage
To use the code:
1. Ensure you have Node.js installed.
2. Install the required dependencies: Express, Octokit, and uuid.
3. Create appropriate middleware for authentication (not shown in the provided code).
4. Use the defined routes to interact with the GitHub repository to manage user instructions.

```javascript
// Example usage of the routes
const express = require('express');
const router = require('./router'); // Assuming the provided code is in a file named router.js

const app = express();
app.use('/instructions', router);

app.listen(3000, () => {
    console.log('Server is running on port 3000');
});
```

This code snippet creates an Express server with the defined routes for managing user instructions stored in a GitHub repository.
  
  ## Component Code
  ```jsx
  import express from 'express';
import { Octokit } from '@octokit/rest';
import { ensureAuthenticated } from '../middleware/authMiddleware.mjs';
import { v4 as uuidv4 } from 'uuid';

const router = express.Router();

// Helper functions
const getOctokitInstance = (token) => new Octokit({ auth: token });

const getRepoName = (githubUsername) => `library-${githubUsername}-config`;

const fetchRepoContent = async (octokit, githubUsername, repoName, path) => {
    const { data } = await octokit.rest.repos.getContent({
        owner: githubUsername,
        repo: repoName,
        path: path,
    });
    const content = Buffer.from(data.content, 'base64').toString('utf8');
    return { content: JSON.parse(content), sha: data.sha };
};

const checkOrCreateRepo = async (octokit, githubUsername, repoName) => {
    try {
        await octokit.rest.repos.get({ owner: githubUsername, repo: repoName });
    } catch (repoError) {
        if (repoError.status === 404) {
            await octokit.rest.repos.createForAuthenticatedUser({
                name: repoName,
                private: false,
            });
        } else {
            throw repoError;
        }
    }
};

const updateRepoContent = async (octokit, githubUsername, repoName, path, message, content, sha) => {
    await octokit.rest.repos.createOrUpdateFileContents({
        owner: githubUsername,
        repo: repoName,
        path: path,
        message: message,
        content: Buffer.from(JSON.stringify(content, null, 2)).toString('base64'),
        sha: sha || undefined,
    });
};

// Routes
router.get('/get-instructions', ensureAuthenticated, async (req, res) => {
    const token = req.user.accessToken;
    const githubUsername = req.user.profile.username;
    const repoName = getRepoName(githubUsername);
    const octokit = getOctokitInstance(token);

    try {
        const { content: instructions } = await fetchRepoContent(octokit, githubUsername, repoName, 'user-instructions.json');
        res.json({ instructions });
    } catch (error) {
        if (error.status === 404) {
            res.json({ instructions: [] });
        } else {
            console.error('Error fetching instructions:', error);
            res.status(500).json({ error: 'Failed to fetch instructions' });
        }
    }
});

router.post('/save-instructions', ensureAuthenticated, async (req, res) => {
    const { instructionName, model, instructions } = req.body;
    const token = req.user.accessToken;
    const githubUsername = req.user.profile.username;
    const repoName = getRepoName(githubUsername);
    const octokit = getOctokitInstance(token);

    try {
        await checkOrCreateRepo(octokit, githubUsername, repoName);

        let instructionsArray = [];
        let sha;

        try {
            const { content, sha: fileSha } = await fetchRepoContent(octokit, githubUsername, repoName, 'user-instructions.json');
            instructionsArray = content;
            sha = fileSha;
        } catch (fileError) {
            if (fileError.status !== 404) {
                throw fileError;
            }
        }

        const id = uuidv4();
        instructionsArray.push({ id, name: instructionName, model, instructions });
        await updateRepoContent(octokit, githubUsername, repoName, 'user-instructions.json', `Add new instruction: ${instructionName}`, instructionsArray, sha);

        res.json({ message: 'Instructions saved successfully!', instruction: { id, name: instructionName, model, instructions } });
    } catch (error) {
        console.error('Error saving instructions:', error);
        res.status(500).json({ error: 'Failed to save instructions' });
    }
});

router.post('/update-instruction', ensureAuthenticated, async (req, res) => {
    const { id, instructionName, model, instructions } = req.body;
    const token = req.user.accessToken;
    const githubUsername = req.user.profile.username;
    const repoName = getRepoName(githubUsername);
    const octokit = getOctokitInstance(token);

    try {
        await checkOrCreateRepo(octokit, githubUsername, repoName);

        let instructionsArray = [];
        let sha;

        try {
            const { content, sha: fileSha } = await fetchRepoContent(octokit, githubUsername, repoName, 'user-instructions.json');
            instructionsArray = content;
            sha = fileSha;
        } catch (fileError) {
            if (fileError.status !== 404) {
                throw fileError;
            }
        }

        const updatedInstructionsArray = instructionsArray.map(instruction =>
            instruction.id === id ? { id, name: instructionName, model, instructions } : instruction
        );
        await updateRepoContent(octokit, githubUsername, repoName, 'user-instructions.json', `Update instruction: ${instructionName}`, updatedInstructionsArray, sha);

        res.json({ message: 'Instruction updated successfully!', instruction: { id, name: instructionName, model, instructions } });
    } catch (error) {
        console.error('Error updating instruction:', error);
        res.status(500).json({ error: 'Failed to update instruction' });
    }
});

router.delete('/delete-instruction', ensureAuthenticated, async (req, res) => {
    const { id } = req.body;
    const token = req.user.accessToken;
    const githubUsername = req.user.profile.username;
    const repoName = getRepoName(githubUsername);
    const octokit = getOctokitInstance(token);

    try {
        let instructionsArray = [];
        let sha;

        try {
            const { content, sha: fileSha } = await fetchRepoContent(octokit, githubUsername, repoName, 'user-instructions.json');
            instructionsArray = content;
            sha = fileSha;
        } catch (fileError) {
            if (fileError.status === 404) {
                return res.status(404).json({ error: 'Instructions file not found' });
            } else {
                throw fileError;
            }
        }

        const updatedInstructions = instructionsArray.filter(instruction => instruction.id !== id);
        await updateRepoContent(octokit, githubUsername, repoName, 'user-instructions.json', `Delete instruction with id: ${id}`, updatedInstructions, sha);

        res.json({ message: 'Instruction deleted successfully!' });
    } catch (error) {
        console.error('Error deleting instruction:', error);
        res.status(500).json({ error: 'Failed to delete instruction' });
    }
});

export default router;
  ```
  