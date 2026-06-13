# 🔥 MERN Stack • Docker  • GitHub Actions • CI/CD



## Dockerfile for Node.js Backend

### Purpose

This Dockerfile creates a Docker image for a Node.js backend application. It installs dependencies, copies the application source code, exposes the backend port, and starts the server.

### Dockerfile

````markdown
```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 5000

CMD ["node", "index.js"]
```
````



## Dockerfile for Vite Frontend Application

### Purpose

This Dockerfile creates a Docker image for a Vite frontend application. It installs dependencies, builds the production assets, exposes the application port, and serves the built application using Vite Preview.

### Dockerfile

````markdown
```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build

EXPOSE 5173

CMD ["npm", "run", "preview"]
```
````







# Docker Compose for MERN Application

## Purpose

This `docker-compose.yml` file orchestrates multiple containers and allows the frontend and backend services to run together using a single command.

Project Structure:

```text
project-root/
│
├── backend/
│   ├── Dockerfile
│   └── .env
│
├── frontend/
│   ├── Dockerfile
│   └── .env
│
└── docker-compose.yml
```

## Docker Compose File

```yaml
services:
  backend:
    build: ./backend
    container_name: backend
    restart: always
    env_file:
      - ./backend/.env
    ports:
      - "5000:5000"

  frontend:
    build: ./frontend
    container_name: frontend
    restart: always
    env_file:
      - ./frontend/.env
    ports:
      - "5173:5173"
    depends_on:
      - backend
```

## Explanation

### backend Service

#### build: ./backend

Builds the Docker image using the Dockerfile located inside the backend directory.

```yaml
build: ./backend
```

---

#### container_name

Assigns a custom name to the container.

```yaml
container_name: backend
```

---

#### restart: always

Automatically restarts the container if it crashes or the server reboots.

```yaml
restart: always
```

---

#### env_file

Loads environment variables from the backend `.env` file.

```yaml
env_file:
  - ./backend/.env
```

---

#### ports

Maps host port 5000 to container port 5000.

```yaml
ports:
  - "5000:5000"
```

Application URL:

```text
http://localhost:5000
```

---

### frontend Service

#### build: ./frontend

Builds the Docker image from the frontend Dockerfile.

```yaml
build: ./frontend
```

---

#### container_name

Assigns a custom container name.

```yaml
container_name: frontend
```

---

#### restart: always

Automatically restarts the frontend container when needed.

```yaml
restart: always
```

---

#### env_file

Loads frontend environment variables.

```yaml
env_file:
  - ./frontend/.env
```

---

#### ports

Maps port 5173 from the container to the host.

```yaml
ports:
  - "5173:5173"
```

Application URL:

```text
http://localhost:5173
```

---

#### depends_on

Ensures the backend container starts before the frontend container.

```yaml
depends_on:
  - backend
```

Note:
`depends_on` controls startup order only. It does not guarantee that the backend is fully ready to accept requests.

## Common Commands

### Build Images

```bash
docker compose build
```

### Start Containers

```bash
docker compose up
```

### Start in Detached Mode

```bash
docker compose up -d
```

### Stop Containers

```bash
docker compose down
```

### View Running Containers

```bash
docker ps
```

### View Logs

```bash
docker compose logs
```

### Rebuild and Start

```bash
docker compose up --build
```

## Benefits of Docker Compose

* Manage multiple containers with one file
* Simplified local development
* Consistent environments
* Easy deployment
* Better service orchestration

## CI/CD Checklist

*  Backend Dockerfile created
*  Frontend Dockerfile created
*  .dockerignore configured
*  Environment variables added
*  docker-compose.yml configured
*  Containers communicate successfully
*  Images build successfully
*  Application accessible through mapped ports
*  Ready for deployment pipeline


ESLint & Prettier Setup for React/Vite Frontend
Purpose

This command installs ESLint and Prettier along with essential React plugins for code quality, formatting, and CI/CD validation.
Installation

```npm i -D eslint prettier @eslint/js globals eslint-plugin-react-hooks eslint-plugin-react-refresh ```

ESLint & Prettier Setup for Node.js Backend
Purpose

This command installs ESLint and Prettier for a Node.js backend application to enforce code quality and consistent formatting.

Installation

``` npm i -D eslint prettie ```

Backend eslint.config.js
 ```
export default [
  {
    files: ["**/*.js"],
    rules: {
      semi: ["error", "always"],
      "no-unused-vars": "warn",
    },
  },
];
```

Frontend eslint.config.js

```
import js from '@eslint/js';
import globals from 'globals';
import reactHooks from 'eslint-plugin-react-hooks';
import reactRefresh from 'eslint-plugin-react-refresh';
import { defineConfig, globalIgnores } from 'eslint/config';

export default defineConfig([
  globalIgnores(['dist']),
  {
    files: ['**/*.{js,jsx}'],
    extends: [
      js.configs.recommended,
      reactHooks.configs.flat.recommended,
      reactRefresh.configs.vite,
    ],
    languageOptions: {
      globals: globals.browser,
      parserOptions: { ecmaFeatures: { jsx: true } },
    },
    rules: {
      'react-hooks/set-state-in-effect': 'off',
    },
  },
]);

```

.prettierrc file code 
```
{
  "semi": true,
  "singleQuote": true,
  "printWidth": 100
}
```
 ADD same prettier in frontend

Add these into scripts of  backend
```
 "lint": "eslint .",
    "lint:fix": "eslint . --fix",
    "format": "prettier --write .",
    "check-format": "prettier --check ."
```
