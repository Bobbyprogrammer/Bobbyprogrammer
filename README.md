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

ci.yml File Code 

```
name: build-and-lint

# triggers: define when this workflow should run
# trigger on push to these branches
on:
  push:
    branches: [main, dev]
  pull_request:
    branches: [main, dev]

    # define a job (multiple jobs also)
jobs:
  build-and-lint:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repo
        uses: actions/checkout@v3

      - name: Setup Node
        uses: actions/setup-node@v3
        with:
          node-version: 20

      - name: Install backend dependencies
        working-directory: backend
        run: npm ci

      - name: Lint backend
        working-directory: backend
        run: npm run lint

      - name: Install frontend dependencies
        working-directory: frontend
        run: npm ci

      - name: Lint frontend
        working-directory: frontend
        run: npm run lint

      - name: Build backend image
        run: docker build -t backend ./backend

      - name: Build frontend image
        run: docker build -t frontend ./frontend

  # deploy:
  #   needs: build-and-lint
  #   if: github.event_name == 'push'
  #   runs-on: ubuntu-latest
  #   steps:
  #     - name: Checkout (optional)
  #       uses: actions/checkout@v4

  #     - name: Add EC2 host to known_hosts
  #       run: |
  #         mkdir -p ~/.ssh
  #         ssh-keyscan -H "$EC2_HOST" >> ~/.ssh/known_hosts
  #       env:
  #         EC2_HOST: ${{ secrets.EC2_HOST }}

  #     - name: Write SSH key
  #       run: |
  #         echo "${EC2_KEY}" > ec2_key.pem
  #         chmod 600 ec2_key.pem
  #       env:
  #         EC2_KEY: ${{ secrets.EC2_KEY }}

  #     - name: Deploy on EC2 (git pull + docker compose up -d --build)
  #       env:
  #         EC2_HOST: ${{ secrets.EC2_HOST }}
  #         EC2_USER: ${{ secrets.EC2_USER }}
  #         EC2_APP_DIR: ${{ secrets.EC2_APP_DIR }}
  #       run: |
  #         ssh -i ec2_key.pem ${EC2_USER}@${EC2_HOST} << EOF
  #           set -e
  #           cd "${EC2_APP_DIR}"

  #           git fetch --all
  #           git reset --hard origin/main

  #           docker compose up -d --build

  #           docker image prune -f

  #           docker compose ps
  #         EOF
```


deploy.yml File code 

```
name: Deploy to VPS

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Deploy via SSH
        uses: appleboy/ssh-action@v1.2.0

        with:
          host: ${{ secrets.HOST }}
          username: ${{ secrets.USERNAME }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          port: ${{ secrets.PORT }}

          script: |
            cd /root/apps/mern_crud

            git fetch origin
            git reset --hard origin/main

            docker compose down
            docker compose up -d --build

            docker image prune -f

            docker ps
```
