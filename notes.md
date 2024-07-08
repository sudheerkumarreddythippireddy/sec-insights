
#.devcontainer
--------------
#Dockerfile

Sure, let's break down this Dockerfile line by line:

1. **FROM python:3.11.3-slim-bullseye**
   - This line specifies the base image for the Docker image. The base image here is a slim version of Debian (Bullseye) with Python 3.11.3 installed. Using a slim image helps in keeping the Docker image size smaller by including only essential packages.

2. **ENV PYTHONUNBUFFERED True**
   - This environment variable ensures that the Python output is unbuffered. This is useful for logging because it makes sure that the output appears immediately and is not held in a buffer.

3. **RUN apt-get update**
   - This command updates the package lists from the repositories to ensure you get the latest versions of the packages and their dependencies.

4. **RUN apt-get install libpq-dev gcc build-essential wkhtmltopdf s3fs -y**
   - This command installs additional packages and dependencies that are required for the application. 
     - `libpq-dev`: Libraries and header files for PostgreSQL client development.
     - `gcc`: The GNU Compiler Collection.
     - `build-essential`: A package that includes essential tools for building software.
     - `wkhtmltopdf`: A command line tool to convert HTML to PDF using Webkit.
     - `s3fs`: A FUSE filesystem that allows you to mount an S3 bucket as a local file system.

5. **RUN pip install poetry==1.6.1**
   - This command installs Poetry, a tool for dependency management and packaging in Python, at version 1.6.1.

6. **ENV APP_HOME /app**
   - This environment variable sets the application home directory to `/app`.

7. **COPY . $APP_HOME**
   - This command copies the contents of the current directory on the host machine to the `/app` directory in the Docker image.

8. **CMD ["/bin/bash"]**
   - This sets the default command to be run when a container is started from the image. Here, it is set to start a bash shell.

Here's a concise summary:

- **Base Image:** Uses a slim Debian-based Python 3.11.3 image.
- **Environment Configuration:** Ensures Python outputs are unbuffered.
- **Dependency Installation:** Updates the package list and installs required system packages and Python dependencies.
- **Application Setup:** Copies the application code into the container.
- **Default Command:** Opens a bash shell when the container starts.

This Dockerfile sets up an environment with the necessary dependencies and configurations to run a Python application that requires PostgreSQL development libraries, building tools, a PDF conversion tool, and a connection to S3 storage.

#devcontainer.json

1. **`"name": "sec_insights"`**
   - This specifies the name of the development container. In this case, the container is named "sec_insights".

2. **`"build": { "dockerfile": "./Dockerfile", "context": ".." }`**
   - **`"dockerfile": "./Dockerfile"`**: This specifies the path to the Dockerfile that should be used to build the container image. Here, it is located in the current directory.
   - **`"context": ".."`**: This specifies the build context, which is the set of files and directories that are accessible to the Docker engine during the build process. The context is set to the parent directory of the current directory.

3. **`"features": { ... }`**
   - This section specifies additional features to be included in the container. Each feature is specified by its name and version, and some may have additional configuration options. These features enhance the functionality of the container by including additional tools and utilities.

   - **`"ghcr.io/devcontainers-contrib/features/pipx-package:1": {}`**: Includes the `pipx-package` feature from `devcontainers-contrib` at version 1. This feature typically allows installing Python CLI tools using `pipx`.

   - **`"ghcr.io/devcontainers-contrib/features/poetry:2": {}`**: Includes the `poetry` feature from `devcontainers-contrib` at version 2. This feature installs Poetry, a dependency management tool for Python.

   - **`"ghcr.io/warrenbuckley/codespace-features/sqlite:1": {}`**: Includes the `sqlite` feature from `warrenbuckley/codespace-features` at version 1. This feature installs SQLite, a lightweight database engine.

   - **`"ghcr.io/devcontainers/features/docker-in-docker:2": {}`**: Includes the `docker-in-docker` feature from `devcontainers` at version 2. This feature allows Docker to run inside the container, enabling Docker commands to be executed within the development container.

   - **`"ghcr.io/devcontainers/features/aws-cli:1": {}`**: Includes the `aws-cli` feature from `devcontainers` at version 1. This feature installs the AWS Command Line Interface (CLI), enabling interaction with AWS services.

   - **`"ghcr.io/devcontainers/features/node:1": {}`**: Includes the `node` feature from `devcontainers` at version 1. This feature installs Node.js, a JavaScript runtime.

4. **`"postCreateCommand": "bash .devcontainer/post_create_command.sh"`**
   - This specifies a command to be run after the container is created and started. In this case, it runs a bash script located at `.devcontainer/post_create_command.sh`. This script can be used to perform additional setup or configuration tasks that need to be done after the container is up and running.

### Summary

- **Container Name**: `sec_insights`
- **Build Configuration**: Uses a Dockerfile located in the current directory and sets the build context to the parent directory.
- **Features**: 
  - Installs `pipx` for Python CLI tools.
  - Installs Poetry for Python dependency management.
  - Installs SQLite for lightweight database use.
  - Enables Docker-in-Docker functionality.
  - Installs AWS CLI for interacting with AWS services.
  - Installs Node.js for JavaScript runtime.
- **Post-Create Command**: Runs a bash script for additional setup after the container is created.

This configuration sets up a comprehensive development environment with necessary tools and dependencies for working on the "sec_insights" project.

#post_create_command.sh

1. **`cd backend/`**
   - This command changes the current directory to the `backend` directory. This is typically where the backend code and related files are located.

2. **`poetry install`**
   - This command installs all dependencies listed in the `pyproject.toml` file using Poetry, which is a dependency manager and packaging tool for Python. It ensures that all required packages and their specified versions are installed in the virtual environment managed by Poetry.

3. **`cp .env.development .env`**
   - This command copies the file `.env.development` to a new file named `.env`. The `.env.development` file typically contains environment-specific configuration variables for the development environment. By copying it to `.env`, these variables are made available for use in the application.

4. **`set -a`**
   - This command marks all variables that are currently in the shell environment for export to subsequently executed commands. This is necessary to ensure that the variables sourced from the `.env` file are available to any command run afterward in the same shell session.

5. **`source .env`**
   - This command reads and executes the content of the `.env` file in the current shell. This sets the environment variables defined in the `.env` file, making them available for use in the current shell session.

6. **`make migrate`**
   - This command uses the `make` utility to run a target named `migrate`. The `make` utility reads from a `Makefile`, which contains a set of directives used to automate tasks. The `migrate` target typically runs database migrations, which are used to apply schema changes to the database. This might involve running a command like `python manage.py migrate` for Django or `alembic upgrade head` for SQLAlchemy, depending on the framework being used.

### Summary

- **Change Directory**: Move to the `backend` directory where the backend code is located.
- **Install Dependencies**: Use Poetry to install all required dependencies listed in `pyproject.toml`.
- **Set Environment Variables**: Copy development environment variables from `.env.development` to `.env`, export them, and source them in the current shell session.
- **Run Migrations**: Use `make migrate` to apply database schema changes, ensuring the database is up-to-date with the current state of the application.

This sequence of commands sets up the backend environment, installs necessary dependencies, configures environment variables, and applies database migrations, making the backend application ready for development or testing.

#backend
--------

#.vscode

#settings.json

### Commands:

1. **`cd backend/`**
   - This command changes the current working directory to the `backend` directory. This is typically where the backend code and related configuration files are located.

2. **`poetry install`**
   - This command uses Poetry, a Python dependency management tool, to install all the dependencies listed in the `pyproject.toml` file. Poetry handles virtual environments and ensures that all the specified packages and their versions are installed correctly.

3. **`cp .env.development .env`**
   - This command copies the `.env.development` file to a new file named `.env`. The `.env.development` file contains environment-specific variables for the development environment, such as database credentials, API keys, and other configuration settings. By copying it to `.env`, you make these settings available for the application to use.

4. **`set -a`**
   - This command marks all currently defined variables to be automatically exported to the environment of subsequently executed commands. This is useful when you need to source an environment file and ensure its variables are available to all future commands in the session.

5. **`source .env`**
   - This command reads and executes the content of the `.env` file in the current shell session. It sets the environment variables defined in the `.env` file, making them available for use in the current shell session.

6. **`make migrate`**
   - This command uses the `make` utility to run a target named `migrate` defined in a `Makefile`. The `Makefile` contains a set of directives used to automate tasks. The `migrate` target usually runs database migration scripts, which apply changes to the database schema. Depending on the framework, this might involve commands like `python manage.py migrate` for Django, `flask db upgrade` for Flask with Flask-Migrate, or `alembic upgrade head` for SQLAlchemy with Alembic.

### Summary:

- **Change Directory**: Move to the `backend` directory.
- **Install Dependencies**: Use Poetry to install all required dependencies listed in the `pyproject.toml` file.
- **Set Environment Variables**: Copy the development environment configuration to `.env`, export all shell variables, and source the `.env` file to load the environment variables into the current shell session.
- **Run Database Migrations**: Use `make migrate` to apply database schema changes, ensuring the database is up-to-date with the current application state.

This sequence of commands sets up the backend environment by installing dependencies, configuring environment variables, and applying necessary database migrations. This prepares the backend application for development or testing by ensuring all dependencies are installed, the environment is configured, and the database schema is current.

#docker-compose.yml

This Docker Compose file defines and configures a multi-service application with three services: a FastAPI application (`llama-app-fastapi`), a PostgreSQL database (`db`), and a LocalStack instance (`localstack`). Let's break down each part of the file:

### Version
```yaml
version: '3'
```
- Specifies the version of the Docker Compose file format.

### Services

#### llama-app-fastapi
```yaml
services:
  llama-app-fastapi:
    build:
      context: .
    volumes:
      - ./:/app
    ports:
      - "127.0.0.1:8000:8000"
    depends_on:
      - db
    env_file:
      - .env
      - .env.docker
    environment:
      BACKEND_CORS_ORIGINS: '["http://localhost", "http://localhost:8000"]'
```
- **build:** Specifies the build context for the Docker image. The context is set to the current directory (`.`), which means it will look for a Dockerfile in this directory.
- **volumes:** Mounts the current directory on the host (`./`) to the `/app` directory in the container. This allows for live reloading of the app when code changes.
- **ports:** Maps port 8000 on the host (only accessible from localhost) to port 8000 in the container.
- **depends_on:** Specifies that this service depends on the `db` service, meaning the `db` service will be started before this one.
- **env_file:** Specifies environment files to load variables from. In this case, `.env` and `.env.docker` files.
- **environment:** Sets additional environment variables. `BACKEND_CORS_ORIGINS` is set to allow CORS for `http://localhost` and `http://localhost:8000`.

#### db
```yaml
  db:
    image: ankane/pgvector:v0.5.0
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: llama_app_db
    ports:
      - "127.0.0.1:5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data/
```
- **image:** Specifies the Docker image to use. Here, it uses the `ankane/pgvector:v0.5.0` image.
- **environment:** Sets environment variables for the PostgreSQL container.
  - `POSTGRES_USER`: Username for PostgreSQL.
  - `POSTGRES_PASSWORD`: Password for PostgreSQL.
  - `POSTGRES_DB`: Database name for PostgreSQL.
- **ports:** Maps port 5432 on the host (only accessible from localhost) to port 5432 in the container.
- **volumes:** Mounts a named volume `postgres_data` to the PostgreSQL data directory (`/var/lib/postgresql/data/`). This ensures persistent storage for the database.

#### localstack
```yaml
  localstack:
    container_name: "${LOCALSTACK_DOCKER_NAME-localstack_main}"
    image: localstack/localstack
    ports:
      - "127.0.0.1:4566:4566" # LocalStack Gateway
      - "127.0.0.1:4510-4559:4510-4559" # external services port range
    environment:
      - DEBUG=${DEBUG-}
      - DOCKER_HOST=unix:///var/run/docker.sock
    volumes:
      - "${LOCALSTACK_VOLUME_DIR:-./volume}:/var/lib/localstack"
      - "/var/run/docker.sock:/var/run/docker.sock"
```
- **container_name:** Sets the name of the container using an environment variable with a default value `localstack_main`.
- **image:** Specifies the Docker image to use for LocalStack.
- **ports:** Maps ports on the host (only accessible from localhost) to the corresponding ports in the container.
  - Port 4566 is used for the LocalStack gateway.
  - Ports 4510-4559 are used for external services.
- **environment:** Sets environment variables for the LocalStack container.
  - `DEBUG`: Sets the debug mode, using the value of the `DEBUG` environment variable if set.
  - `DOCKER_HOST`: Specifies the Docker host.
- **volumes:** Mounts volumes to the container.
  - Mounts a directory (either specified by `LOCALSTACK_VOLUME_DIR` or `./volume`) to `/var/lib/localstack` in the container.
  - Mounts the Docker socket from the host to the container, allowing LocalStack to interact with Docker.

### Volumes
```yaml
volumes:
  postgres_data:
```
- Defines a named volume `postgres_data` that will be used by the `db` service to persist data.

### Summary
- **`llama-app-fastapi`**: A FastAPI application that depends on the PostgreSQL database. It is built from the current directory and uses environment variables from `.env` and `.env.docker`. It maps the host's port 8000 to the container's port 8000.
- **`db`**: A PostgreSQL database using the `ankane/pgvector:v0.5.0` image. It sets up a user, password, and database and maps the host's port 5432 to the container's port 5432. It uses a named volume for data persistence.
- **`localstack`**: A LocalStack instance for mocking AWS services. It maps several ports and uses environment variables for configuration. It also mounts the Docker socket to interact with Docker.

This configuration sets up a development environment with a backend service, a database, and a local AWS service emulator.

#localstack-cors-config.json

This JSON object represents a Cross-Origin Resource Sharing (CORS) configuration for an AWS S3 bucket. CORS is a security feature implemented by web browsers to control how resources hosted on one origin (domain) can be accessed by web pages from another origin.

Let's break down each part of this configuration:

### Structure and Fields

```json
{
    "CORSRules": [
        {
            "AllowedHeaders": [
                "*"
            ],
            "AllowedMethods": [
                "GET",
                "HEAD"
            ],
            "AllowedOrigins": [
                "*"
            ],
            "ExposeHeaders": [
                "ETag"
            ]
        }
    ]
}
```

1. **`CORSRules`**: This is a list of rules that define the CORS behavior for the S3 bucket. Each rule specifies the conditions under which a browser's request from another origin is allowed.

#### Rule Details

The rule inside the `CORSRules` list has several key components:

- **`AllowedHeaders`**: Specifies which headers are allowed in the actual request. The `*` means that any headers are allowed.
    ```json
    "AllowedHeaders": ["*"]
    ```

- **`AllowedMethods`**: Lists the HTTP methods that are allowed when accessing the resource. In this case, only `GET` and `HEAD` requests are allowed.
    ```json
    "AllowedMethods": ["GET", "HEAD"]
    ```

- **`AllowedOrigins`**: Specifies which origins are allowed to access the resource. The `*` means that any origin is allowed.
    ```json
    "AllowedOrigins": ["*"]
    ```

- **`ExposeHeaders`**: Identifies which headers are safe to expose to the API of a CORS API specification. In this case, only the `ETag` header is exposed.
    ```json
    "ExposeHeaders": ["ETag"]
    ```

### Summary

- **`AllowedHeaders: ["*"]`**: Any headers are allowed in the request.
- **`AllowedMethods: ["GET", "HEAD"]`**: Only GET and HEAD methods are allowed.
- **`AllowedOrigins: ["*"]`**: Any origin is allowed to make the request.
- **`ExposeHeaders: ["ETag"]`**: The ETag header can be exposed to the client.

This configuration allows any origin to send GET and HEAD requests to the S3 bucket and allows all headers in the request. The response will expose the ETag header to the client. This is a permissive CORS policy useful for publicly accessible resources.

#pyproject.toml

This is a `pyproject.toml` file used by Poetry, a tool for dependency management and packaging in Python. It defines the metadata, dependencies, and settings for the Python project. Let's break down each section of the file:

### [tool.poetry]

#### Project Metadata
```toml
[tool.poetry]
name = "llama-app-backend"
version = "0.1.0"
description = "Backend for Llama App"
authors = ["Sourabh Desai <sourabhdesai@gmail.com>"]
readme = "README.md"
packages = [{include = "app"}]
```
- **name**: The name of the project is "llama-app-backend".
- **version**: The current version of the project is "0.1.0".
- **description**: A short description of the project: "Backend for Llama App".
- **authors**: The list of authors for the project, with their names and email addresses.
- **readme**: The path to the README file, which is "README.md".
- **packages**: Specifies which packages to include in the distribution. Here, it includes the "app" package.

#### Project Dependencies
```toml
[tool.poetry.dependencies]
python = "^3.10,<3.12"
fastapi = "0.100.1"
pydantic = "^1.10.8"
uvicorn = "^0.22.0"
sqlalchemy = {extras = ["async"], version = "^2.0.15"}
aiosqlite = "^0.19.0"
asyncpg = "^0.27.0"
alembic = "^1.11.1"
psycopg2 = {extras = ["binary"], version = "^2.9.6"}
psycopg2-binary = "^2.9.6"
sse-starlette = "^1.6.1"
pypdf = "^3.9.1"
anyio = "^3.7.0"
s3fs = "^2023.6.0"
fsspec = "^2023.6.0"
pdfkit = "^1.0.0"
pgvector = "^0.1.8"
sentry-sdk = {extras = ["fastapi"], version = "^1.28.1"}
llama-index = "0.9.7"
polygon = "^1.1.0"
polygon-api-client = "^1.12.3"
nltk = "^3.8.1"
cachetools = "^5.3.1"
greenlet = "^2.0.2"
email-validator = "^2.0.0.post2"
```
- **python**: Specifies the compatible Python versions, which are 3.10 and 3.11.
- **fastapi**: FastAPI framework version 0.100.1.
- **pydantic**: Pydantic for data validation, version 1.10.8.
- **uvicorn**: Uvicorn ASGI server, version 0.22.0.
- **sqlalchemy**: SQLAlchemy ORM with async support, version 2.0.15.
- **aiosqlite**: SQLite support for async Python, version 0.19.0.
- **asyncpg**: PostgreSQL database adapter for async Python, version 0.27.0.
- **alembic**: Database migration tool for SQLAlchemy, version 1.11.1.
- **psycopg2**: PostgreSQL adapter with binary support, version 2.9.6.
- **psycopg2-binary**: Another form of PostgreSQL adapter, version 2.9.6.
- **sse-starlette**: Server-sent events support for Starlette, version 1.6.1.
- **pypdf**: PDF manipulation library, version 3.9.1.
- **anyio**: Async networking and concurrency library, version 3.7.0.
- **s3fs**: Filesystem for S3, version 2023.6.0.
- **fsspec**: File system specification library, version 2023.6.0.
- **pdfkit**: Library to convert HTML to PDF, version 1.0.0.
- **pgvector**: PostgreSQL extension for vector similarity search, version 0.1.8.
- **sentry-sdk**: Sentry integration for FastAPI, version 1.28.1.
- **llama-index**: A library (presumably for indexing or similar functionality), version 0.9.7.
- **polygon**: Polygon.io API client, version 1.1.0.
- **polygon-api-client**: Another client for Polygon.io, version 1.12.3.
- **nltk**: Natural Language Toolkit, version 3.8.1.
- **cachetools**: Tools for caching, version 5.3.1.
- **greenlet**: Lightweight in-process concurrent programming, version 2.0.2.
- **email-validator**: Library to validate email addresses, version 2.0.0.post2.

#### Development Dependencies
```toml
[tool.poetry.group.dev.dependencies]
pylint = "^2.17.4"
pytest = "^7.3.2"
sseclient-py = "^1.7.2"
pdfkit = "^1.0.0"
fire = "^0.5.0"
sec-edgar-downloader = "~5.0"
pytickersymbols = "^1.13.0"
awscli-local = "^0.20"
```
- **pylint**: Python code linter, version 2.17.4.
- **pytest**: Testing framework, version 7.3.2.
- **sseclient-py**: Client for server-sent events, version 1.7.2.
- **pdfkit**: HTML to PDF converter, version 1.0.0.
- **fire**: CLI creation tool, version 0.5.0.
- **sec-edgar-downloader**: Library to download SEC filings, version ~5.0.
- **pytickersymbols**: Library for ticker symbols, version 1.13.0.
- **awscli-local**: Local AWS CLI wrapper, version 0.20.

#### Scripts
```toml
[tool.poetry.scripts]
start = "app.main:start"
migrate_db = "app.main:migrate_db"
```
- **start**: A script to start the application by running `app.main:start`.
- **migrate_db**: A script to run database migrations by executing `app.main:migrate_db`.

### [build-system]

```toml
[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```
- **requires**: Specifies the build dependencies. Here, it requires `poetry-core`.
- **build-backend**: Specifies the build backend to use, which is `poetry.core.masonry.api`.

### Summary

- **Project metadata**: Provides the basic information about the project.
- **Dependencies**: Lists the libraries required for the project to run, including both runtime and development dependencies.
- **Scripts**: Defines command-line scripts that can be executed with `poetry run`.
- **Build system**: Specifies the build system requirements and backend for the project.

This configuration ensures that all necessary dependencies are installed and that specific commands can be easily run using Poetry, facilitating development and deployment of the backend application for the Llama App.

#frontend

####.eslintrc.cjs
-----------------------------------


This ESLint configuration file is set up to enforce coding standards and catch errors in a TypeScript project, specifically for a Next.js application. Here's a detailed explanation of each part:

1. **Require and Path Modules**:
   ```javascript
   // eslint-disable-next-line @typescript-eslint/no-var-requires
   const path = require("path");
   ```
   This line imports the `path` module to handle and transform file paths. The `eslint-disable-next-line` comment is used to ignore a specific ESLint rule for the next line.

2. **Config Object Declaration**:
   ```javascript
   /** @type {import("eslint").Linter.Config} */
   const config = {
     overrides: [
       {
         extends: [
           "plugin:@typescript-eslint/recommended-requiring-type-checking",
         ],
         files: ["*.ts", "*.tsx"],
         parserOptions: {
           project: path.join(__dirname, "tsconfig.json"),
         },
       },
     ],
   ```
   - **Type Annotation**:
     ```javascript
     /** @type {import("eslint").Linter.Config} */
     ```
     This is a JSDoc comment that provides type information to editors and tools, indicating that `config` is of type `import("eslint").Linter.Config`.
   
   - **Overrides**:
     ```javascript
     overrides: [
       {
         extends: [
           "plugin:@typescript-eslint/recommended-requiring-type-checking",
         ],
         files: ["*.ts", "*.tsx"],
         parserOptions: {
           project: path.join(__dirname, "tsconfig.json"),
         },
       },
     ],
     ```
     The `overrides` array is used to specify different ESLint configurations for certain files. Here, it applies the `@typescript-eslint/recommended-requiring-type-checking` rules for TypeScript files (`*.ts`, `*.tsx`). The `parserOptions` section specifies the path to the `tsconfig.json` file, enabling type-aware linting.

3. **Parser Configuration**:
   ```javascript
   parser: "@typescript-eslint/parser",
   parserOptions: {
     project: path.join(__dirname, "tsconfig.json"),
   },
   ```
   - **Parser**:
     ```javascript
     parser: "@typescript-eslint/parser",
     ```
     This sets the parser to `@typescript-eslint/parser`, which allows ESLint to understand TypeScript syntax.
   
   - **Parser Options**:
     ```javascript
     parserOptions: {
       project: path.join(__dirname, "tsconfig.json"),
     },
     ```
     This specifies the path to the `tsconfig.json` file, enabling ESLint to parse the TypeScript files correctly.

4. **Plugins and Extends**:
   ```javascript
   plugins: ["@typescript-eslint"],
   extends: ["next/core-web-vitals", "plugin:@typescript-eslint/recommended"],
   ```
   - **Plugins**:
     ```javascript
     plugins: ["@typescript-eslint"],
     ```
     This includes the `@typescript-eslint` plugin, which provides linting rules specific to TypeScript.
   
   - **Extends**:
     ```javascript
     extends: ["next/core-web-vitals", "plugin:@typescript-eslint/recommended"],
     ```
     This extends the base ESLint configuration with recommended settings for Next.js (`next/core-web-vitals`) and TypeScript (`plugin:@typescript-eslint/recommended`).

5. **Rules**:
   ```javascript
   rules: {
     "@typescript-eslint/consistent-type-imports": [
       "warn",
       {
         prefer: "type-imports",
         fixStyle: "inline-type-imports",
       },
     ],
     "@typescript-eslint/no-unused-vars": ["warn", { argsIgnorePattern: "^_" }],
   },
   ```
   - **Consistent Type Imports**:
     ```javascript
     "@typescript-eslint/consistent-type-imports": [
       "warn",
       {
         prefer: "type-imports",
         fixStyle: "inline-type-imports",
       },
     ],
     ```
     This rule enforces the consistent use of type imports. It issues a warning (`"warn"`) and prefers the use of type imports (`"prefer": "type-imports"`). The `fixStyle` option specifies the style for fixing the import, here set to `inline-type-imports`.
   
   - **No Unused Variables**:
     ```javascript
     "@typescript-eslint/no-unused-vars": ["warn", { argsIgnorePattern: "^_" }],
     ```
     This rule issues a warning (`"warn"`) for unused variables. The `argsIgnorePattern` option allows unused variables that start with an underscore (`^_`).

6. **Export Configuration**:
   ```javascript
   module.exports = config;
   ```
   This line exports the configuration object, making it available for ESLint to use.


####next.config.mjs

This code snippet configures a Next.js application to integrate with Sentry for error tracking and performance monitoring. Here's a detailed explanation of each part:

1. **Importing withSentryConfig**:
   ```javascript
   import { withSentryConfig } from "@sentry/nextjs";
   ```
   This line imports the `withSentryConfig` function from the `@sentry/nextjs` package, which is used to configure Sentry within a Next.js application.

2. **Environment Validation**:
   ```javascript
   await import("./src/env.mjs");
   ```
   This line dynamically imports an `env.mjs` file from the `src` directory, likely used for environment validation and configuration. The comment above it suggests that setting the `SKIP_ENV_VALIDATION` environment variable can bypass this validation, which can be useful for Docker builds.

3. **Next.js Configuration Object**:
   ```javascript
   /** @type {import("next").NextConfig} */
   const config = {
     reactStrictMode: true,

     /**
      * If you have `experimental: { appDir: true }` set, then you must comment the below `i18n` config
      * out.
      *
      * @see https://github.com/vercel/next.js/issues/41980
      */
     i18n: {
       locales: ["en"],
       defaultLocale: "en",
     },
   };
   ```
   - **Type Annotation**:
     ```javascript
     /** @type {import("next").NextConfig} */
     ```
     This JSDoc comment provides type information, indicating that `config` is of type `import("next").NextConfig`.
   
   - **Configuration Settings**:
     ```javascript
     reactStrictMode: true,
     ```
     Enables React's strict mode to help identify potential problems in the application.
     
     ```javascript
     i18n: {
       locales: ["en"],
       defaultLocale: "en",
     },
     ```
     Configures internationalization (i18n) settings with English as the default and only locale. The comment suggests that if `experimental: { appDir: true }` is enabled, this i18n configuration should be commented out due to an issue tracked on GitHub.

4. **Exporting Configuration with Sentry Integration**:
   ```javascript
   export default withSentryConfig(config, {
     // For all available options, see:
     // https://github.com/getsentry/sentry-webpack-plugin#options

     // Suppresses source map uploading logs during build
     silent: true,

     org: "llama-test",
     project: "javascript-nextjs",
   }, {
     // For all available options, see:
     // https://docs.sentry.io/platforms/javascript/guides/nextjs/manual-setup/

     // Upload a larger set of source maps for prettier stack traces (increases build time)
     widenClientFileUpload: true,

     // Transpiles SDK to be compatible with IE11 (increases bundle size)
     transpileClientSDK: true,

     // Routes browser requests to Sentry through a Next.js rewrite to circumvent ad-blockers (increases server load)
     tunnelRoute: "/monitoring",

     // Hides source maps from generated client bundles
     hideSourceMaps: true,

     // Automatically tree-shake Sentry logger statements to reduce bundle size
     disableLogger: true,
   });
   ```
   - **withSentryConfig Function**:
     ```javascript
     export default withSentryConfig(config, { ... }, { ... });
     ```
     This function wraps the Next.js configuration (`config`) with Sentry-specific settings. It takes three arguments:
     - The Next.js configuration object (`config`).
     - Sentry Webpack plugin options (first object).
     - Sentry Next.js SDK options (second object).

   - **Sentry Webpack Plugin Options**:
     ```javascript
     {
       silent: true,
       org: "llama-test",
       project: "javascript-nextjs",
     }
     ```
     These options configure the Sentry Webpack plugin:
     - `silent`: Suppresses source map uploading logs during the build process.
     - `org`: Specifies the Sentry organization.
     - `project`: Specifies the Sentry project.

   - **Sentry Next.js SDK Options**:
     ```javascript
     {
       widenClientFileUpload: true,
       transpileClientSDK: true,
       tunnelRoute: "/monitoring",
       hideSourceMaps: true,
       disableLogger: true,
     }
     ```
     These options configure the Sentry Next.js SDK:
     - `widenClientFileUpload`: Uploads a larger set of source maps for better stack traces but increases build time.
     - `transpileClientSDK`: Ensures the SDK is compatible with IE11 but increases bundle size.
     - `tunnelRoute`: Routes browser requests to Sentry through a Next.js rewrite to avoid ad-blockers, which increases server load.
     - `hideSourceMaps`: Hides source maps from generated client bundles.
     - `disableLogger`: Tree-shakes Sentry logger statements to reduce bundle size.

###package.json
-----------------------------------------
This `package.json` file specifies the configuration for a Next.js frontend project named `llama-app-frontend`. It includes information about the project's dependencies, devDependencies, scripts, and metadata. Here's a detailed breakdown:

### Project Information

- **name**: The name of the project is `llama-app-frontend`.
- **version**: The current version of the project is `0.1.0`.
- **private**: This is set to `true`, indicating that the project is not intended to be published as a package to a registry like npm.

### Scripts

The `scripts` section defines custom commands that can be run using `npm run <script-name>`:

- **build**: Runs the Next.js build command to create an optimized production build.
  ```json
  "build": "next build"
  ```
- **dev**: Starts the Next.js development server.
  ```json
  "dev": "next dev"
  ```
- **lint**: Runs the Next.js lint command to lint the codebase.
  ```json
  "lint": "next lint"
  ```
- **start**: Starts the Next.js production server.
  ```json
  "start": "next start"
  ```

### Dependencies

These are the packages required for the project to run in production:

- **@headlessui/react**: A set of completely unstyled, fully accessible UI components.
- **@heroicons/react**: React components for Heroicons, a set of free, MIT-licensed high-quality SVG icons.
- **@sentry/nextjs**: Sentry SDK for error tracking and performance monitoring in Next.js.
- **@t3-oss/env-nextjs**: Environmental validation for Next.js projects.
- **@tailwindcss/forms**: A plugin that provides a basic reset for form styles with Tailwind CSS.
- **@wojtekmaj/react-hooks**: A collection of useful React hooks.
- **classnames**: A utility for conditionally joining classNames together.
- **downshift**: Primitives to build simple, flexible, WAI-ARIA compliant React autocomplete/dropdown/select/combobox components.
- **fuse.js**: A lightweight fuzzy-search library.
- **lodash**: A modern JavaScript utility library delivering modularity, performance, and extras.
- **lodash.debounce**: A debounce method from lodash.
- **md5**: A JavaScript function for hashing messages with MD5.
- **next**: The Next.js framework.
- **react**: The React library.
- **react-dom**: React package for working with the DOM.
- **react-ga4**: A library for Google Analytics 4 integration with React.
- **react-github-btn**: A React component for GitHub buttons.
- **react-icons**: Include popular icons in your React projects easily.
- **react-intersection-observer**: React implementation of the Intersection Observer API to monitor the visibility of elements.
- **react-pdf**: Display PDFs in your React app.
- **react-select**: A flexible and beautiful Select Input control for React.
- **react-use-intercom**: A React hook for Intercom integration.
- **react-window**: React components for efficiently rendering large lists and tabular data.
- **uuid**: A library for generating unique identifiers.
- **zod**: A TypeScript-first schema declaration and validation library.

### DevDependencies

These are packages required for the development and build process:

- **@tailwindcss/forms**: The same plugin listed in dependencies; having it in both is redundant.
- **@types/eslint**: TypeScript definitions for ESLint.
- **@types/lodash**: TypeScript definitions for lodash.
- **@types/lodash.debounce**: TypeScript definitions for lodash's debounce method.
- **@types/md5**: TypeScript definitions for md5.
- **@types/node**: TypeScript definitions for Node.js.
- **@types/prettier**: TypeScript definitions for Prettier.
- **@types/react**: TypeScript definitions for React.
- **@types/react-dom**: TypeScript definitions for React DOM.
- **@types/react-window**: TypeScript definitions for react-window.
- **@types/uuid**: TypeScript definitions for uuid.
- **@typescript-eslint/eslint-plugin**: ESLint plugin for TypeScript-specific linting rules.
- **@typescript-eslint/parser**: ESLint parser for TypeScript.
- **autoprefixer**: A PostCSS plugin to parse CSS and add vendor prefixes.
- **eslint**: A tool for identifying and fixing problems in JavaScript code.
- **eslint-config-next**: ESLint configuration for Next.js projects.
- **eslint-config-prettier**: Configures ESLint to work with Prettier.
- **postcss**: A tool for transforming CSS with JavaScript plugins.
- **prettier**: An opinionated code formatter.
- **prettier-plugin-tailwindcss**: A Prettier plugin for automatically sorting Tailwind CSS classes.
- **tailwindcss**: A utility-first CSS framework for rapidly building custom designs.
- **typescript**: The TypeScript language.

### Metadata

- **ct3aMetadata**: Metadata for internal use, likely to track initial versions or configurations.
  ```json
  "initVersion": "7.13.1"
  ```

This `package.json` file sets up a comprehensive development environment for a Next.js application with TypeScript, Sentry integration, and a range of utilities and tools to enhance development and production workflows.

###postcss.config.cjs
----------------------------------
This code defines and exports a configuration object for PostCSS, a tool for transforming CSS with JavaScript plugins. Here's a detailed explanation of each part:

1. **Configuration Object**:
   ```javascript
   const config = {
     plugins: {
       tailwindcss: {},
       autoprefixer: {},
     },
   };
   ```
   This object defines the PostCSS configuration, specifying which plugins to use.

2. **Plugins**:
   - **tailwindcss**:
     ```javascript
     tailwindcss: {},
     ```
     This line adds the `tailwindcss` plugin to the PostCSS configuration. The empty object (`{}`) indicates that default settings will be used. The `tailwindcss` plugin is used for incorporating Tailwind CSS, a utility-first CSS framework, into the project. Tailwind CSS provides a wide range of utility classes that help in rapidly building custom designs without leaving the HTML.

   - **autoprefixer**:
     ```javascript
     autoprefixer: {},
     ```
     This line adds the `autoprefixer` plugin to the PostCSS configuration. Again, the empty object (`{}`) indicates that default settings will be used. `autoprefixer` is a PostCSS plugin that automatically adds vendor prefixes to CSS rules to ensure compatibility with different browsers. This helps in writing standard CSS without worrying about cross-browser issues.

3. **Exporting the Configuration**:
   ```javascript
   module.exports = config;
   ```
   This line exports the `config` object, making it available for PostCSS to use. When PostCSS runs, it will look for this configuration file and apply the specified plugins (`tailwindcss` and `autoprefixer`) to the CSS files it processes.

### Summary

This configuration file sets up PostCSS with two plugins:
- **Tailwind CSS** for using utility-first CSS classes in the project.
- **Autoprefixer** for adding vendor prefixes to CSS rules to ensure compatibility across different browsers.

By exporting this configuration, you enable PostCSS to apply these transformations to your CSS during the build process.

####prettier.config.cjs
------------------------------------
This code defines and exports a configuration object for Prettier, an opinionated code formatter, and includes a plugin for Tailwind CSS. Here's a detailed explanation of each part:

1. **Type Annotation**:
   ```javascript
   /** @type {import("prettier").Config} */
   ```
   This JSDoc comment provides type information for the `config` object, indicating that it adheres to the Prettier configuration type. This helps with editor support and ensures that the configuration object is correctly structured according to Prettier's expected format.

2. **Configuration Object**:
   ```javascript
   const config = {
     plugins: [require.resolve("prettier-plugin-tailwindcss")],
   };
   ```
   This object defines the Prettier configuration:

   - **plugins**:
     ```javascript
     plugins: [require.resolve("prettier-plugin-tailwindcss")],
     ```
     This line specifies the plugins to be used by Prettier. The `require.resolve("prettier-plugin-tailwindcss")` call dynamically resolves the path to the `prettier-plugin-tailwindcss` module. Including this plugin allows Prettier to understand and format Tailwind CSS classes in a specific order, which can help maintain a consistent style across your project.

3. **Exporting the Configuration**:
   ```javascript
   module.exports = config;
   ```
   This line exports the `config` object, making it available for Prettier to use. When Prettier runs, it will apply the configuration specified in this file, including any plugins listed.

### Summary

This Prettier configuration file does the following:
- Declares a configuration object with a type annotation to ensure proper structure and type support.
- Specifies the `prettier-plugin-tailwindcss` plugin, which helps in formatting Tailwind CSS classes in a consistent order.
- Exports the configuration object so that Prettier can use it when formatting code.

By including the `prettier-plugin-tailwindcss` plugin, you enhance Prettier's capabilities to better handle Tailwind CSS, ensuring that your utility classes are ordered consistently according to Tailwind's recommended conventions.

####sentry.client.config.ts
-------------------------------------

This code snippet configures the initialization of Sentry for a Next.js application on the client side. Sentry is used for error tracking and performance monitoring. Here's a detailed breakdown of each part:

1. **Imports**:
   ```javascript
   import * as Sentry from "@sentry/nextjs";
   import { SENTRY_DSN } from "~/constants";
   ```
   - **Sentry**: The `@sentry/nextjs` package is imported, which provides tools and methods for integrating Sentry with a Next.js application.
   - **SENTRY_DSN**: This constant is imported from a local module (`~/constants`). It typically contains the Data Source Name (DSN) for the Sentry project, which is a unique identifier for your Sentry project.

2. **Sentry Initialization**:
   ```javascript
   Sentry.init({
     dsn: SENTRY_DSN,

     // Adjust this value in production, or use tracesSampler for greater control
     tracesSampleRate: 1,

     // Setting this option to true will print useful information to the console while you're setting up Sentry.
     debug: false,

     replaysOnErrorSampleRate: 1.0,

     // This sets the sample rate to be 10%. You may want this to be 100% while
     // in development and sample at a lower rate in production
     replaysSessionSampleRate: 0.1,

     // You can remove this option if you're not planning to use the Sentry Session Replay feature:
     integrations: [
       new Sentry.Replay({
         // Additional Replay configuration goes in here, for example:
         maskAllText: true,
         blockAllMedia: true,
       }),
     ],
   });
   ```
   - **dsn**:
     ```javascript
     dsn: SENTRY_DSN,
     ```
     The DSN (Data Source Name) is used to identify the project within Sentry and connect your application to the Sentry service.

   - **tracesSampleRate**:
     ```javascript
     tracesSampleRate: 1,
     ```
     This sets the rate at which transactions (performance monitoring) are sampled. A value of `1` means 100% of transactions are captured. This is typically adjusted in production to a lower value to reduce the volume of data.

   - **debug**:
     ```javascript
     debug: false,
     ```
     This option, when set to `true`, enables debug mode and prints useful information to the console. It is set to `false` here, meaning debug information will not be printed.

   - **replaysOnErrorSampleRate**:
     ```javascript
     replaysOnErrorSampleRate: 1.0,
     ```
     This sets the sample rate for session replays when an error occurs. A value of `1.0` means 100% of error sessions will have replays.

   - **replaysSessionSampleRate**:
     ```javascript
     replaysSessionSampleRate: 0.1,
     ```
     This sets the sample rate for session replays in general. A value of `0.1` means 10% of sessions will have replays. This can be adjusted to a higher rate in development and a lower rate in production.

   - **integrations**:
     ```javascript
     integrations: [
       new Sentry.Replay({
         // Additional Replay configuration goes in here, for example:
         maskAllText: true,
         blockAllMedia: true,
       }),
     ],
     ```
     This specifies additional integrations for Sentry. In this case, the `Replay` integration is added, which provides session replay functionality. The replay integration is configured with options:
     - **maskAllText**: When set to `true`, all text in the replay is masked to protect sensitive information.
     - **blockAllMedia**: When set to `true`, all media (like images and videos) is blocked in the replay to reduce data volume and protect privacy.

### Summary

This configuration file initializes Sentry for a Next.js application on the client side, setting up error tracking, performance monitoring, and session replay features. Key settings include:
- **dsn**: Connects the application to the specific Sentry project.
- **tracesSampleRate**: Controls the sampling rate for performance monitoring.
- **debug**: Enables or disables debug mode.
- **replaysOnErrorSampleRate** and **replaysSessionSampleRate**: Control the sampling rates for session replays.
- **integrations**: Adds the session replay feature with specific configurations to mask text and block media.

By adjusting these settings, you can control the amount of data sent to Sentry, enabling efficient monitoring and debugging of your application in both development and production environments.

####sentry.edge.config.ts
-------------------------------

This code snippet configures the initialization of Sentry for edge features in a Next.js application. Edge features include middleware and edge routes, which can run at the network edge for improved performance and lower latency. Here’s a detailed explanation of each part:

1. **Imports**:
   ```javascript
   import * as Sentry from "@sentry/nextjs";
   import { SENTRY_DSN } from "~/constants";
   ```
   - **Sentry**: The `@sentry/nextjs` package is imported, which provides tools and methods for integrating Sentry with a Next.js application.
   - **SENTRY_DSN**: This constant is imported from a local module (`~/constants`). It typically contains the Data Source Name (DSN) for the Sentry project, which is a unique identifier for your Sentry project.

2. **Sentry Initialization**:
   ```javascript
   Sentry.init({
     dsn: SENTRY_DSN,

     // Adjust this value in production, or use tracesSampler for greater control
     tracesSampleRate: 1,

     // Setting this option to true will print useful information to the console while you're setting up Sentry.
     debug: false,
   });
   ```
   - **dsn**:
     ```javascript
     dsn: SENTRY_DSN,
     ```
     The DSN (Data Source Name) is used to identify the project within Sentry and connect your application to the Sentry service.

   - **tracesSampleRate**:
     ```javascript
     tracesSampleRate: 1,
     ```
     This sets the rate at which transactions (performance monitoring) are sampled. A value of `1` means 100% of transactions are captured. This is typically adjusted in production to a lower value to reduce the volume of data.

   - **debug**:
     ```javascript
     debug: false,
     ```
     This option, when set to `true`, enables debug mode and prints useful information to the console. It is set to `false` here, meaning debug information will not be printed.

### Summary

This configuration file initializes Sentry for edge features in a Next.js application, setting up error tracking and performance monitoring. Key settings include:
- **dsn**: Connects the application to the specific Sentry project.
- **tracesSampleRate**: Controls the sampling rate for performance monitoring.
- **debug**: Enables or disables debug mode.

By including this configuration, Sentry will be initialized whenever an edge feature (such as middleware or edge routes) is loaded, both in production and when running locally. This ensures consistent monitoring and error tracking across all parts of the application.




#tsconfig.json

This JSON object is a TypeScript configuration file (`tsconfig.json`). It specifies the compiler options and other settings for a TypeScript project. Let's break down each section and its options:

### `compilerOptions`

The `compilerOptions` section specifies the settings for the TypeScript compiler.

```json
{
  "compilerOptions": {
    "target": "es2017",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "checkJs": true,
    "skipLibCheck": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "noUncheckedIndexedAccess": true,
    "baseUrl": ".",
    "paths": {
      "~/*": ["./src/*"]
    }
  }
}
```

#### Options:

- **`target`**: Specifies the output language level. `"es2017"` means the output will be compatible with ECMAScript 2017.
- **`lib`**: Specifies the libraries to be included in the compilation. This includes the DOM, DOM iterable, and ESNext.
- **`allowJs`**: Allows JavaScript files to be compiled.
- **`checkJs`**: Enables type checking in JavaScript files.
- **`skipLibCheck`**: Skips type checking of declaration files (`.d.ts`).
- **`strict`**: Enables all strict type-checking options (`strict: true`).
- **`forceConsistentCasingInFileNames`**: Ensures consistent casing in file names.
- **`noEmit`**: Prevents the compiler from writing output files.
- **`esModuleInterop`**: Enables compatibility with ES modules by creating namespace objects for imports.
- **`module`**: Specifies the module code generation method. `"esnext"` means it will use the latest ECMAScript module standard.
- **`moduleResolution`**: Specifies how modules are resolved. `"node"` means it will follow Node.js module resolution.
- **`resolveJsonModule`**: Allows importing JSON modules.
- **`isolatedModules`**: Ensures that each file can be safely transpiled without relying on other imports.
- **`jsx`**: Specifies how JSX should be handled. `"preserve"` means JSX will be left as-is for further processing.
- **`incremental`**: Enables incremental compilation for faster rebuilds.
- **`noUncheckedIndexedAccess`**: Ensures that accessing properties on objects through dynamic keys requires the compiler to check if the key exists.
- **`baseUrl`**: Sets the base directory for non-relative module names. Here, it is set to the current directory (`"."`).
- **`paths`**: Sets up path mapping. `"~/*"` maps to `"./src/*"`, allowing imports using `~/` as a shortcut to the `src` directory.

### `include`

The `include` section specifies the files and directories to include in the compilation.

```json
"include": [
  ".eslintrc.cjs",
  "next-env.d.ts",
  "**/*.ts",
  "**/*.tsx",
  "**/*.cjs",
  "**/*.mjs"
]
```

- Includes specific files and patterns such as:
  - `.eslintrc.cjs`: ESLint configuration file.
  - `next-env.d.ts`: TypeScript declaration file generated by Next.js.
  - `**/*.ts`: All TypeScript files.
  - `**/*.tsx`: All TypeScript files with JSX.
  - `**/*.cjs`: All CommonJS modules.
  - `**/*.mjs`: All ECMAScript modules.

### `exclude`

The `exclude` section specifies the files and directories to exclude from the compilation.

```json
"exclude": ["node_modules"]
```

- Excludes the `node_modules` directory to avoid compiling third-party libraries.

### Summary

This TypeScript configuration file sets up a project to:
- Target ES2017.
- Include standard and latest ECMAScript libraries along with DOM support.
- Allow and check JavaScript files.
- Enable strict type-checking and ensure consistent casing in file names.
- Prevent output file generation while resolving modules in a Node.js fashion.
- Handle JSX and JSON modules.
- Enable incremental compilation and safe isolated module transpilation.
- Use path mapping for cleaner imports.
- Include specific TypeScript and JavaScript files while excluding the `node_modules` directory.

This configuration is well-suited for a modern TypeScript project, particularly one that uses Next.js or a similar framework.

#tailwind.config.ts

This JavaScript file is a configuration for Tailwind CSS, a utility-first CSS framework. The file defines custom themes, colors, fonts, and other styling options to be used in a project. Let's break down each part of the configuration:

### Imports and Type Definition

```js
import { type Config } from "tailwindcss";
```

- **Importing Config**: This line imports the type definition for Tailwind CSS's configuration from the `tailwindcss` package. This helps with TypeScript support, ensuring that the configuration object conforms to the expected structure.

### Exporting Configuration Object

```js
export default {
  content: ["./src/**/*.{js,ts,jsx,tsx}"],
```

- **content**: This option specifies the paths to all the template files in the project. Tailwind CSS will scan these files for class names to generate the necessary styles. It includes all files with `.js`, `.ts`, `.jsx`, and `.tsx` extensions within the `src` directory.

### Theme Customization

```js
  theme: {
    extend: {
      fontFamily: {
        lora: ["Lora", "serif"],
        nunito: ["Nunito Sans", "sans-serif"],
      },
```

- **fontFamily**: Customizes the font families available in the project. Two custom font families are defined:
  - `lora`: Uses "Lora" with "serif" as a fallback.
  - `nunito`: Uses "Nunito Sans" with "sans-serif" as a fallback.

```js
      colors: {
        "gradient-start": "rgba(255, 255, 204, 0.2)",
        "gradient-end": "rgba(204, 153, 255, 0.2)",
        "gradient-start-light": "rgba(255, 255, 204, 0.1)",
        "gradient-end-light": "rgba(204, 153, 255, 0.1)",
        "gray-00": "#F9F9FA",
        "gray-15": "#E9E9ED",
        "gray-30": "#D2D2DC",
        "gray-60": "#9EA2B0",
        "gray-90": "#3F3F46",
        "gray-pdf": "#F7F7F7",
        "llama-purple-light": "#EDDDFC",
        "llama-purple": "#D09FF6",
        "llama-magenta-light": "#FBD7F9",
        "llama-magenta": "#F48FEF",
        "llama-red-light": "#FBDBD9",
        "llama-red": "#F49B95",
        "llama-orange-light": "#FAE9D3",
        "llama-orange": "#F1BA72",
        "llama-yellow-light": "#FDF6DD",
        "llama-yellow": "#F8EC78",
        "llama-lime-light": "#E5FAD2",
        "llama-lime": "#A1E66D",
        "llama-teal-light": "#D9FBEC",
        "llama-teal": "#66D8A7",
        "llama-cyan-light": "#DAFAFB",
        "llama-cyan": "#70E4EC",
        "llama-blue-light": "#EDF5FD",
        "llama-blue": "#87B6F3",
        "llama-indigo-light": "#EDECFD",
        "llama-indigo": "#817AF2",
      },
```

- **colors**: Adds custom color definitions to the theme. These colors can be used in the project's styles. Each color is given a unique name, such as "gradient-start" or "llama-purple", and a corresponding color value.

```js
      backgroundImage: (theme) => ({
        gradient: "url('https://llama-app-frontend.vercel.app/Gradient.png')",
      }),
```

- **backgroundImage**: Defines custom background images. In this case, it adds a background image named `gradient`, which uses an image URL.

```js
      backgroundSize: {
        "100%": "100%",
      },
      backgroundPosition: {
        center: "center",
      },
      backgroundRepeat: {
        "no-repeat": "no-repeat",
      },
    },
  },
```

- **backgroundSize**: Specifies custom background sizes. Here, "100%" is defined as "100%".
- **backgroundPosition**: Sets custom background positions. "center" is defined as "center".
- **backgroundRepeat**: Defines custom background repeat properties. "no-repeat" is defined as "no-repeat".

### Plugins

```js
  plugins: [],
} satisfies Config;
```

- **plugins**: Specifies an array of plugins to use with Tailwind CSS. Currently, no plugins are added (`[]`).

### Summary

This Tailwind CSS configuration file customizes various aspects of the project's styling:
- **Content paths**: Includes all relevant files in the `src` directory for class scanning.
- **Fonts**: Adds "Lora" and "Nunito Sans" font families with appropriate fallbacks.
- **Colors**: Defines a comprehensive set of custom colors, primarily prefixed with "llama-" for brand consistency.
- **Backgrounds**: Sets a custom background image and related properties like size, position, and repeat behavior.

By extending the theme and adding these customizations, the project can use Tailwind CSS utility classes to apply consistent and branded styles throughout the application.

#sentry.server.config.ts

This JavaScript file configures the initialization of Sentry for a Next.js application on the server side. Sentry is a popular error-tracking and performance-monitoring platform. This configuration ensures that any errors or performance issues occurring on the server are captured and sent to Sentry for monitoring and debugging.

### Breakdown of the Configuration

#### Importing Sentry and Constants

```js
import * as Sentry from "@sentry/nextjs";
import { SENTRY_DSN } from "~/constants";
```

- **Sentry Import**: Imports everything from the `@sentry/nextjs` package, which is Sentry's SDK for Next.js applications.
- **SENTRY_DSN Import**: Imports the `SENTRY_DSN` constant from the project's constants file. The DSN (Data Source Name) is a unique identifier for the Sentry project, which tells Sentry where to send the captured data.

#### Initializing Sentry

```js
Sentry.init({
  dsn: SENTRY_DSN,

  // Adjust this value in production, or use tracesSampler for greater control
  tracesSampleRate: 1,

  // Setting this option to true will print useful information to the console while you're setting up Sentry.
  debug: false,
});
```

- **`Sentry.init()`**: Initializes Sentry with the specified configuration options.
  - **`dsn`**: Sets the DSN to the value imported from `~/constants`, which tells Sentry where to send the captured data.
  - **`tracesSampleRate`**: Sets the sample rate for tracing. Here, it is set to `1`, meaning that 100% of the transactions will be captured. In production, this value should be adjusted based on the needs and volume of your application, or a more sophisticated sampling method like `tracesSampler` can be used.
  - **`debug`**: When set to `true`, Sentry will print useful debugging information to the console, which can be helpful during setup. In this case, it is set to `false` to disable these debug logs.

### Summary

This file sets up Sentry for a Next.js server by:
1. Importing the necessary Sentry package and the DSN from the project's constants.
2. Initializing Sentry with the DSN and configuration options to control tracing and debugging.

By setting up Sentry in this way, the server will be able to capture and send error and performance data to the Sentry platform, helping developers monitor and debug issues in their application.
This configuration snippet appears to be for setting up services and environment variables on Render, a cloud platform for deploying and running web applications. Let's break down the key components:

### Services Configuration

#### Web Service (`llama-app-backend`)

- **Type**: Docker-based web service.
- **Name**: `llama-app-backend`.
- **Runtime**: Docker.
- **Repository**: Points to the GitHub repository (`https://github.com/run-llama/sec-insights.git`) containing the Dockerized application.
- **Region**: Deployment region is `oregon`.
- **Plan**: Standard plan.
- **Root Directory**: Specifies `./backend` as the root directory where the Dockerfile and application code reside.
- **Scaling**: Defines scaling options:
  - `minInstances`: Minimum instances of the service (2).
  - `maxInstances`: Maximum instances of the service (10).
  - `targetMemoryPercent` and `targetCPUPercent`: Optional parameters for memory and CPU usage targets.

- **Health Check Path**: Specifies `/api/health/` as the endpoint for health checks.
- **Initial Deploy Hook**: Executes `make seed_db_based_on_env` during the initial deployment.
- **Environment Variables**: Sets environment variables necessary for the application:
  - `DATABASE_URL`: Obtained from the `llama-app-db` database service's `connectionString`.
  - Several other variables (`IS_PREVIEW_ENV`, `LOG_LEVEL`, `BACKEND_CORS_ORIGINS`, `S3_BUCKET_NAME`, `S3_ASSET_BUCKET_NAME`, `CDN_BASE_URL`, `SENTRY_DSN`) are configured with values specific to different environments (`previewValue` for preview environments).

#### Cron Service (`llama-app-cron`)

- **Type**: Docker-based cron service.
- **Name**: `llama-app-cron`.
- **Runtime**: Docker.
- **Repository**: Same as the web service.
- **Region**: Deployment region is `oregon`.
- **Plan**: Standard plan.
- **Root Directory**: Specifies `./backend`.
- **Schedule**: Runs the `seed_db_based_on_env` command at `0 5 31 2 ?`, a non-existent date (`Feb 31st`), effectively preventing automatic runs.
- **Docker Command**: Executes `make seed_db_based_on_env`.
- **Environment Variables**: Similar to the web service, it sets up necessary environment variables.

### Environment Variable Groups

Environment variables are organized into groups (`envVarGroups`), each specifying variables for different configurations:

- **`general-settings`**: Contains general settings such as `IS_PREVIEW_ENV`, `LOG_LEVEL`, CORS origins (`BACKEND_CORS_ORIGINS`), S3 bucket names, CDN URLs, and Sentry DSNs for different environments.
  
- **`prod-web-secrets`**: Holds production secrets like API keys (`OPENAI_API_KEY`, `AWS_KEY`, `AWS_SECRET`, `POLYGON_IO_API_KEY`).

- **`preview-web-secrets`**: Stores preview environment secrets prefixed with `PREVIEW_`.

### Summary

This configuration snippet sets up Docker-based web and cron services on Render, configuring them with scaling options, health checks, deployment hooks, and environment variables tailored for different environments (production and preview). It also establishes connections to a PostgreSQL database (`llama-app-db`) and manages secrets securely using environment variable groups. This setup ensures robust deployment and management of the application on Render, with tailored configurations for different deployment scenarios.