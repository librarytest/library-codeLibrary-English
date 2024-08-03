---
  title: 'index.mjs'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # index.mjs
  # Explanation of the Provided Code

The code snippet provided is written in JavaScript and utilizes the Express framework for building web applications. It also incorporates Passport.js for authentication using GitHub OAuth, WebSocket for real-time communication, and dotenv for environment variable management.

### Dependencies
- **Express**: A Node.js web application framework that provides a robust set of features for web and mobile applications.
- **Passport**: An authentication middleware for Node.js that supports various authentication strategies, including OAuth.
- **WebSocket**: A protocol that enables bidirectional communication between clients and servers.
- **dotenv**: A zero-dependency module that loads environment variables from a `.env` file into `process.env`.

### Code Explanation
1. **Import Statements**:
   - `express`, `passport`, `session`, `path`, `url`, and `ws` are imported along with specific modules like `GitHubStrategy` for GitHub OAuth.
   - Routes for GitHub, client, and AI are imported from respective files.

2. **Configuration**:
   - The `.env` file is configured using `config()` to load environment variables.
   - Express app is initialized, and the port is set to either the environment variable `PORT` or `8080`.

3. **Static File Serving**:
   - Static files are served from the `dist` directory using `express.static`.

4. **Passport Configuration**:
   - GitHub OAuth strategy is set up with client ID, client secret, and callback URL.
   - Serialization and deserialization functions are defined for user authentication.

5. **Authentication Routes**:
   - Routes for GitHub authentication and callback are set up.
   - A signout route is defined to log out the user and redirect to the home page.
   - An API endpoint is created to fetch the user's profile if authenticated.

6. **Route Handling**:
   - Client, GitHub, and AI routes are used in the application.

7. **WebSocket Server Setup**:
   - The Express server is started, and a WebSocket server is created using the `ws` library.
   - Upon a WebSocket connection, the user ID is extracted from the headers and stored in the WebSocket object.

8. **Export**:
   - The WebSocket server instance is exported for external use.

### Example Usage
```javascript
// Start the server
import { wss } from './app.js'; // Assuming the file is named app.js

// WebSocket connection handling
wss.on('connection', (ws, req) => {
    const userId = req.headers['sec-websocket-protocol'];
    ws.userId = userId;
    console.log('WebSocket connection established for user:', userId);
});
```

In this example, the server is started, and the WebSocket server is used to handle connections, extracting the user ID from the headers and logging the connection establishment.

This code snippet demonstrates a basic setup for a web application with GitHub OAuth authentication, WebSocket communication, and route handling using Express.js.
  
  ## Component Code
  ```jsx
  import express from 'express';
import passport from 'passport';
import { Strategy as GitHubStrategy } from 'passport-github';
import session from 'express-session';
import { config } from 'dotenv';
import path from 'path';
import { fileURLToPath } from 'url';
import { WebSocketServer } from 'ws';
import githubRoutes from './routes/github/githubRoutes.mjs';
import clientRoutes from './routes/clientRoutes.mjs';
import aiRoutes from './routes/aiRoutes.mjs';

config();
const app = express();
const port = process.env.PORT || 8080;

// Get the directory name of the current module file
const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

app.use(express.json());
// Serve static files from the dist directory
app.use(express.static(path.join(__dirname, 'dist')));

passport.use(new GitHubStrategy({
    clientID: process.env.GITHUB_CLIENT_ID,
    clientSecret: process.env.GITHUB_CLIENT_SECRET,
    callbackURL: "https://libraryai-efbafc7d6218.herokuapp.com/auth/github/callback"
}, (accessToken, refreshToken, profile, cb) => {
    profile.accessToken = accessToken; // Attach accessToken
    const user = {
        profile: profile,
        accessToken: accessToken
    };
    return cb(null, user); // Pass the whole structured user object
}));

app.use(session({
    secret: process.env.SESSION_SECRET,
    resave: false,
    saveUninitialized: true
}));

app.use(passport.initialize());
app.use(passport.session());

passport.serializeUser((user, done) => done(null, user));
passport.deserializeUser((obj, done) => done(null, obj));

app.get('/auth/github', passport.authenticate('github', { scope: ['public_repo'] }));

app.get('/auth/github/callback', 
    passport.authenticate('github', { failureRedirect: '/login' }),
    (req, res) => {
        res.redirect('/library');
    });

app.get('/signout', (req, res, next) => {
    req.logout((err) => {
        if (err) {
            return next(err);
        }
        res.redirect('/'); // Redirect to home page after logging out
    });
});

app.get('/api/user/profile', (req, res) => {
    if (req.isAuthenticated()) {
        res.json({ profile: req.user.profile });
    } else {
        res.status(401).send('Not authenticated');
    }
});

// Use the client routes
app.use('/', clientRoutes);
app.use('/api', githubRoutes);
app.use('/ai', aiRoutes);

// Setup WebSocket server
const server = app.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});

const wss = new WebSocketServer({ server });

wss.on('connection', (ws, req) => {
    const userId = req.headers['sec-websocket-protocol']; // Assuming user ID is passed as a subprotocol
    ws.userId = userId;
    console.log('WebSocket connection established for user:', userId);
});

export { wss };
//////
  ```
  