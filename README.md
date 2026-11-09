# ProjectAI

# ProjectAI — FitCoach

**FitCoach** is an AI-powered fitness coaching application that helps users manage their fitness journey through personalized workout planning, progress tracking, recovery insights, nutrition support, and activity synchronization.

The project uses an **Angular** frontend, a **Spring Boot** REST API, **MySQL** for persistent storage, and an AI provider that can run locally with **Ollama**.

## Key Features

- **User accounts and authentication** — registration, login, and JWT-based authentication.
- **User profile and goals** — manage fitness profile information and fitness goals.
- **AI workout planning** — generate personalized workout plans using the configured AI provider.
- **Workout management** — organize plans into weeks, days, and exercises.
- **Dashboard** — access a summary of fitness information and activity.
- **Progress tracking** — record measurements and analyze progress over time.
- **AI fitness insights** — generate progress analysis and coaching responses.
- **Recovery support** — recovery check-ins, assessments, and AI-assisted recovery recommendations.
- **Nutrition and recipes** — recipe support and macro/nutrition calculations using the project's food catalog.
- **Strava integration** — connect a Strava account and import activity data.

> The AI provider is configurable. The default setup uses a local Ollama server, so an external AI API key is not required for the default configuration.

## Technology Stack

| Layer | Technologies |
|---|---|
| Frontend | Angular 18, TypeScript, RxJS |
| Backend | Java 17, Spring Boot 3.3.5 |
| API | Spring Web, REST, Spring Security |
| Authentication | JWT |
| Persistence | Spring Data JPA, Hibernate |
| Database | MySQL 8 |
| Database migrations | Flyway |
| AI | Ollama by default; optional OpenAI-compatible provider configuration |
| API documentation | Springdoc OpenAPI / Swagger UI |
| Build tools | npm, Angular CLI, Maven |
| Optional local environment | Docker Compose |

## Project Structure

```text
ProjectAI-main/
├── backend/
│   ├── src/main/java/       # Spring Boot application source
│   │   └── .../controller/  # REST controllers
│   │   └── .../service/     # Business logic and AI services
│   │   └── .../entity/      # JPA entities
│   │   └── .../repository/  # Data repositories
│   └── src/main/resources/
│       ├── db/migration/    # Flyway SQL migrations
│       ├── nutrition/      # Nutrition data
│       └── prompts/        # AI prompt templates
├── frontend/                # Angular application
├── modules/                 # Additional project modules
├── docs/                    # Project documentation
├── docker-compose.yml       # MySQL, backend, and frontend services
├── .env.example             # Example environment configuration
├── ARCHITECTURE.md          # Architecture overview
└── LOCAL_AI.md              # Local AI setup guide
```

## Prerequisites

For running the application locally, install:

- **JDK 17**
- **Maven 3.6+** (or use the Maven wrapper if one is added to the backend)
- **Node.js** and **npm**
- **Angular CLI 18** (can also be run through the npm scripts)
- **MySQL 8**
- **Ollama** for the default local AI configuration

Alternatively, use Docker Desktop and Docker Compose for the database and application services.

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/fatma863/IA-project.git
cd IA-project
```

If your repository URL is different, replace the URL above with your own.

### 2. Configure environment variables

Copy `.env.example` to `.env` in the project root and update values when necessary.

```bash
cp .env.example .env
```

On Windows, you can also create `.env` by copying `.env.example` in File Explorer.

Important variables:

| Variable | Purpose | Default |
|---|---|---|
| `DATABASE_URL` | JDBC URL for MySQL | `jdbc:mysql://localhost:3306/fitcoach...` |
| `DATABASE_USERNAME` | MySQL username | `root` |
| `DATABASE_PASSWORD` | MySQL password | empty |
| `JWT_SECRET` | Secret used to sign JWTs | Replace before deployment |
| `AI_PROVIDER` | AI provider selection | `ollama` |
| `AI_ENDPOINT` | AI chat-completion endpoint | `http://localhost:11434/v1/chat/completions` |
| `AI_MODEL` | Model name | `llama3.2:1b` |
| `AI_API_KEY` | API key when using a remote provider | empty |
| `STRAVA_CLIENT_ID` | Strava OAuth client ID | empty |
| `STRAVA_CLIENT_SECRET` | Strava OAuth client secret | empty |

Do not commit real API keys, passwords, OAuth secrets, or production JWT secrets to GitHub.

### 3. Start the local AI provider (Ollama)

Install Ollama, then download the configured model:

```bash
ollama pull llama3.2
```

Start Ollama if it is not already running. The backend's default configuration expects the local OpenAI-compatible endpoint:

```text
http://localhost:11434/v1/chat/completions
```

The default model configured in the backend is `llama3.2:1b`. If you use a different model, update `AI_MODEL` in `.env`.

See [LOCAL_AI.md](LOCAL_AI.md) for additional setup details and optional provider configuration.

### 4. Start MySQL

Create a MySQL database named `fitcoach`, or use the Docker Compose setup below. The backend uses Flyway migrations to manage the database schema.

### 5. Run the backend

Open a terminal:

```bash
cd backend
mvn spring-boot:run
```

The backend runs on:

```text
http://localhost:8080
```

The port can be changed with the `SERVER_PORT` environment variable.

### 6. Run the frontend

Open another terminal:

```bash
cd frontend
npm install
npm start
```

The Angular development server normally runs at:

```text
http://localhost:4200
```

Use the URL printed by Angular CLI if it selects a different port.

## Run with Docker Compose

The repository includes a `docker-compose.yml` file for MySQL, the Spring Boot backend, and the Angular frontend.

1. Make sure Docker Desktop is running.
2. Make sure Ollama is installed and running on the host if you want to use the default local AI provider.
3. From the project root, run:

```bash
docker compose up --build
```

Open the application at:

- Frontend: http://localhost:4200
- Backend: http://localhost:8080

The Compose configuration maps MySQL to host port `3307` and stores database data in a Docker volume named `mysql_data`.

To stop the services:

```bash
docker compose down
```

To stop the services and remove the database volume as well:

```bash
docker compose down -v
```

**Warning:** `docker compose down -v` deletes the persisted MySQL data in the Compose volume.

## API Documentation and Health

The backend includes Springdoc OpenAPI. When the backend is running, try:

- Swagger UI: http://localhost:8080/swagger-ui.html
- OpenAPI JSON: http://localhost:8080/v3/api-docs
- Actuator health: http://localhost:8080/actuator/health

Availability of protected endpoints depends on authentication and application configuration.

## Main Backend Modules

The backend is organized into REST controllers, services, repositories, entities, DTOs, security components, and configuration.

Some of the main API areas include:

- Authentication and user profiles
- Goals and workout plans
- Dashboard and progress measurements
- Progress analysis
- AI chat and coaching
- Recipes and nutrition
- Recovery check-ins and assessments
- Strava account connection and activity synchronization

For implementation details, see [ARCHITECTURE.md](ARCHITECTURE.md).

## Tests and Build

### Backend

Run backend tests:

```bash
cd backend
mvn test
```

Package the backend:

```bash
mvn clean package
```

### Frontend

Run frontend tests:

```bash
cd frontend
npm install
npm test
```

Create a production build:

```bash
npm run build
```

## Security Notes

- Replace the default `JWT_SECRET` with a strong, private value before deployment.
- Keep database passwords, AI API keys, and Strava OAuth credentials out of source control.
- Configure production CORS, HTTPS, database access, and secret management before exposing the application publicly.
- The Docker Compose credentials are for local development only; do not reuse them in production.

## Additional Documentation

- [Architecture overview](ARCHITECTURE.md)
- [Local AI / Ollama setup](LOCAL_AI.md)
- [AI progress prompts and notes](PROMPTS_PROGRESS.md)
- [Contribution progress notes](CARTE_CONTRIBUTION_PROGRESS.md)

---

**Project:** ProjectAI — FitCoach  
**Description:** AI-powered fitness coaching and progress tracking application.
