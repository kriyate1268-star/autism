# Early Autism Screening & AI-Powered Child Monitoring

A full-stack web application for early autism screening, therapy tracking, and AI-powered developmental support for children aged 4–11.

> **Disclaimer:** This project is intended for educational, research, and demonstration purposes. It is not a medical diagnostic tool and does not replace professional clinical assessment.

## Features

- **Autism Screening** — 10-question M-CHAT-R/F-style screening questionnaire with rule-based risk scoring and optional ML-based prediction.
- **AI Doctor Chatbot** — An Anthropic Claude-powered assistant that can use the child's screening history, therapy plans, and progress information to provide contextual responses.
- **Therapy Management** — Create therapy plans, log sessions, and track improvement scores.
- **Interactive Games** — Pattern recognition and emotion identification games, with results contributing to progress tracking.
- **Progress Monitoring** — Charts showing screening scores and therapy trends over time.
- **Feedback Reports** — Child-specific progress summaries generated from therapy sessions and games.
- **User Authentication** — Account registration and login with password hashing.
- **Database Management** — Persistent application data stored using SQLite and Prisma ORM.

## Tech Stack

| Component | Technology |
|---|---|
| Frontend | Next.js 14, React, TypeScript |
| Styling | Tailwind CSS, shadcn/ui-style components |
| Backend | Next.js App Router, API routes, server actions |
| Database | SQLite |
| ORM | Prisma ORM with `better-sqlite3` |
| Authentication | NextAuth.js v5, credentials provider, bcrypt |
| AI Integration | Anthropic Claude API |
| Machine Learning | Python, FastAPI, scikit-learn, joblib |
| Visualization | Charts for screening and therapy progress |

## Dataset and Responsible Use

The project includes `Autism-Child-Data.csv`, a synthetic dataset used to demonstrate and develop the machine-learning prediction pipeline.

- **Purpose:** Software development, experimentation, and demonstration.
- **Limitations:** Results obtained using synthetic data do not establish real-world predictive accuracy or clinical validity.
- **Responsible use:** Screening results are estimates for demonstration purposes only. This application is not a substitute for professional clinical assessment and must not be used to diagnose autism.
- **Questionnaire limitations:** The application's 10-question M-CHAT-R/F-style questionnaire is a software demonstration. The project does not establish that the questionnaire or its scoring is clinically validated for the full target age range of 4–11 years.

## Setup

### Prerequisites

- Node.js 18 or later
- npm
- Git
- Python 3, if you want to run the optional ML prediction service

### 1. Clone the repository

```bash
git clone https://github.com/kriyate1268-star/autism.git
cd autism
```

### 2. Install dependencies

```bash
npm install
```

### 3. Configure environment variables

Copy the example environment file:

```bash
cp .env.example .env
```

Open `.env` and configure the variables required by the application.

- **`AUTH_SECRET`** — Secret used by the authentication configuration. Generate a value with:

  ```bash
  openssl rand -base64 32
  ```

- **`ANTHROPIC_API_KEY`** — Required for the AI Doctor chatbot. Obtain an API key through the [Anthropic Console](https://console.anthropic.com/).

Do not commit `.env` or publish API keys, passwords, or other secrets.

The application uses a local SQLite database. Follow the repository's Prisma configuration for the database URL and location.

### 4. Set up the database

Run the Prisma migration command:

```bash
npx prisma migrate dev
```

If your checkout does not contain the required migration files, check the Prisma schema and configuration before creating a new migration.

### 5. Start the development server

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

If port 3000 is already in use, Next.js may select another available port. Check the terminal output for the actual URL.

## Available Scripts

| Command | Description |
|---|---|
| `npm run dev` | Start the development server |
| `npm run build` | Create a production build |
| `npm start` | Run the production build |
| `npm run lint` | Run ESLint |
| `npm run ml:predict` | Start the optional Python ML prediction service |

## Optional: Machine-Learning Prediction Service

The application supports an optional Python/FastAPI service for machine-learning-based risk prediction. When the service is unavailable, the application can fall back to its rule-based scoring logic.

### 1. Create a Python virtual environment

From the project root:

```bash
cd ml
python3 -m venv venv
source venv/bin/activate
pip install fastapi uvicorn joblib pandas scikit-learn
cd ..
```

On Windows, activate the environment with:

```powershell
ml\venv\Scripts\activate
```

The `ml/venv/` directory is excluded from Git. Each developer must create the virtual environment and install the required dependencies locally.

### 2. Start the prediction service

From the project root:

```bash
npm run ml:predict
```

The service is configured to use port `5000`.

If the service exposes the documented health endpoint, check it with:

```bash
curl http://localhost:5000/health
```

### 3. Configure the service URL

If necessary, set `ML_API_URL` in `.env` to the URL expected by the application. The documented default is:

```text
http://localhost:5000
```

Check the implementation if you need to change the URL or health-check path.

### Prediction limitations

The application uses rule-based scoring when the ML service is not running. The README documents the following demonstration thresholds:

- **High risk:** Total questionnaire score of 7 or higher.
- **Medium risk:** Total questionnaire score of 4–6.
- **Low risk:** Total questionnaire score below 4.

These thresholds are application-specific demonstration logic and should not be interpreted as clinically validated diagnostic criteria.

The repository includes `ml/train_model.py` and `ml/autism_model.pkl`. The training script and dataset can be inspected to understand how the model was developed. Performance on synthetic data alone does not demonstrate that the model generalizes to real children or clinical settings.

## Viewing the Database

To inspect the database visually, run the following command from the project root:

```bash
npx prisma studio
```

Prisma Studio typically opens at [http://localhost:5555](http://localhost:5555).

Use it to inspect the database tables defined by your Prisma schema, such as users, children, screenings, therapy plans, therapy sessions, progress reports, and chat messages, where applicable.

## Project Structure

```text
autism/
├── src/
│   ├── app/
│   │   ├── (auth)/              # Login and registration
│   │   ├── dashboard/           # Dashboard and child management
│   │   ├── screening/[childId]/ # Screening questionnaire
│   │   ├── results/[screeningId]/
│   │   ├── therapy/[childId]/   # Therapy activities and games
│   │   ├── monitoring/          # Progress charts and reports
│   │   ├── feedback/[childId]/  # Feedback and progress history
│   │   ├── chatbot/             # AI Doctor interface
│   │   ├── api/                 # API route handlers
│   │   └── actions/              # Server actions
│   ├── components/              # Shared React components
│   └── lib/                     # Shared utilities and Prisma client
├── prisma/                      # Prisma schema and migrations
├── ml/
│   ├── predict_api.py            # FastAPI prediction service
│   ├── train_model.py            # Model training script
│   └── autism_model.pkl          # Trained model artifact
├── Autism-Child-Data.csv         # Demonstration dataset
├── .env.example                 # Example environment variables
├── .gitignore
├── package.json
└── README.md
```

This structure is representative. File locations may differ slightly as the project evolves.

## Demo Flow

1. Register an account and log in.
2. Create a child profile.
3. Complete the 10-question screening questionnaire.
4. View the resulting rule-based estimate or ML prediction, depending on service availability.
5. Create a therapy plan and log a therapy session.
6. Play a pattern recognition or emotion identification game.
7. Interact with the AI Doctor chatbot, if the Anthropic API key is configured.
8. Review monitoring charts and progress reports.

## Security and Privacy

- Keep `.env` and other files containing secrets out of version control.
- Do not commit local databases, virtual environments, or generated build files.
- Use synthetic or appropriately de-identified data for demonstrations.
- Do not upload identifiable child information to public repositories.
- Review API access, authentication, and data handling before deploying the application publicly.

## Notes

- The actual database URL and location depend on the Prisma configuration in the repository.
- The AI Doctor chatbot requires a valid Anthropic API key and appropriate API access.
- The optional ML service requires its Python dependencies and a compatible trained model.
- Model predictions and questionnaire scores are experimental outputs, not medical diagnoses.
- Verify the setup instructions against the current code whenever dependencies or configuration change.
