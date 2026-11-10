# Sheet Lens Setup Notes

## Tools installed
- Node.js 18+
- Java 21 (OpenJDK)
- Git

## Front end (React + Vite)
Location: `frontend/`

Start the dev server:
    cd frontend
    npm run dev

Runs at http://localhost:5173

## Back end (Spring Boot)
Location: `backend/`

Start the server:
    cd backend
    mvnw.cmd spring-boot:run

Runs at http://localhost:8080
Health check: http://localhost:8080/actuator/health

## Saving work to GitHub
    git add .
    git commit -m "Describe what you changed"
    git push

## Problems I ran into
- `git` not recognized: Git wasn't installed. Installed it and reopened the terminal.
- Tailwind error: needed the `@tailwindcss/vite` plugin in vite.config.js and a restart.
- Extra `sheetlens` folder inside `backend`: moved the inner folder up one level.

## Stacks i needed
- Apache POI for excel
- jgrapht for mapping formula dependencies 


## Errors
## Reading Errors: Which Stage Failed?

Ask when the error happened. Each stage has its own kind of failure.

### Stage 1: Maven downloads
- What's happening: Maven reads pom.xml and fetches libraries
- Failure type: Dependency problem
- Look for: "Could not resolve dependencies", "Could not find artifact"
- Usual cause: typo in groupId, artifactId, or version, or a missing closing tag

### Stage 2: Compile
- What's happening: Java turns your code into runnable form
- Failure type: Code or package problem
- Look for: "cannot find symbol", "package ... does not exist"
- Usual cause: typo, missing import, or library not in pom.xml

### Stage 3: Startup
- What's happening: Spring wires everything together
- Failure type: Configuration problem
- Look for: "APPLICATION FAILED TO START", "Port 8080 was already in use"
- Usual cause: bad settings, or another copy of the server still running

### Stage 4: Running
- What's happening: Your code handles a request
- Failure type: Logic or runtime problem
- Look for: "NullPointerException", a 500 response in the browser
- Usual cause: a bug in my code for that particular input

### Quick rule
- Error before the Spring logo: stage 1 or 2
- Logo appears, then it dies: stage 3
- Server starts fine, error appears when I use it: stage 4

### Reading a long stack trace
1. Find the first line naming an exception (the what).

## Errors That Don't Fit the Four Stages

### Environment and tools (before anything runs)
- Look for: "'git' is not recognized", "'java' is not recognized", "'npm' is not recognized"
- Cause: the tool isn't installed, or the terminal was opened before it was installed
- Fix: install it, then close and reopen the terminal

- Look for: "JAVA_HOME is not defined correctly", "release version 21 not supported"
- Cause: Java is missing, or Maven is using a different Java version than pom.xml asks for
- Fix: run java -version, and make sure it matches java.version in pom.xml

### Wrong folder
- Look for: "no such file or directory", "ENOENT", "Cannot find pom.xml", "no package.json"
- Cause: running a command from the wrong folder
- Fix: check the prompt, cd into frontend or backend, and run again

### Front end (React and Vite)
- Look for: "Failed to resolve import", "Module not found"
- Cause: wrong file path, a deleted file still imported, or a package that isn't installed
- Fix: check the import path, or run npm install

- Look for: a red error overlay in the browser mentioning postcss or a plugin
- Cause: config problem, like the Tailwind plugin missing from vite.config.js
- Fix: check the config, then restart npm run dev (config changes need a restart)

- Look for: a blank white page and errors in the browser console (F12)
- Cause: a JavaScript error while rendering
- Fix: open the console and read the first red line

### Front end talking to back end
- Look for: "CORS policy" or "blocked by CORS" in the browser console
- Cause: the browser refuses requests between different ports (5173 and 8080)
- Fix: use the Vite proxy for /api, or configure CORS in Spring

- Look for: "ERR_CONNECTION_REFUSED", "Network Error"
- Cause: the back end isn't running
- Fix: start the Spring Boot server and check /actuator/health

- Look for: 404 on /api/upload
- Cause: the URL doesn't match the controller mapping, or the proxy isn't set up
- Fix: compare the front end URL against the @PostMapping path

### File upload
- Look for: "MaxUploadSizeExceededException", "413 Payload Too Large"
- Cause: the file is bigger than the multipart limits
- Fix: raise spring.servlet.multipart.max-file-size and max-request-size

- Look for: errors like "invalid header", "zip file is empty", "not an OOXML file" from POI
- Cause: the file is corrupt, password protected, or not really .xlsx
- Fix: validate the file type and handle the error with a friendly message

### Memory and performance
- Look for: "OutOfMemoryError: Java heap space"
- Cause: a huge workbook loaded fully into memory
- Fix: use POI's streaming reader, or raise the memory limit

### Git and GitHub
- Look for: "Repository not found"
- Cause: wrong remote URL (placeholder username, typo, or repo not created)
- Fix: git remote -v, then git remote set-url origin <real URL>

- Look for: "Please tell me who you are"
- Cause: name and email not set
- Fix: git config --global user.name and user.email

- Look for: "rejected", "failed to push some refs"
- Cause: GitHub has commits you don't have locally
- Fix: git pull first, then git push

- Look for: "nothing to commit" or files not showing up
- Cause: forgot git add, or the file is listed in .gitignore
- Fix: git status to see what git sees

### Windows and permissions
- Look for: "Access is denied", "file is being used by another process"
- Cause: a program (often VS Code or a running server) has the folder locked
- Fix: stop the server, close VS Code, and try again

- Problem: terminal says Administrator in the title bar
- Cause: opened cmd with "Run as administrator"
- Fix: open Command Prompt normally, or use VS Code's terminal

### Not an error at all
- Whitelabel Error Page at localhost:8080: the server is working, there's just no endpoint at that URL
- Terminal sitting with a blinking cursor after startup: normal, the server is running and waiting
- "Terminate batch job (Y/N)?": normal after pressing Ctrl + C, answer Y