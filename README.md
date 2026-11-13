# Future Build Days — FinPulse / Financial Health Check-Up
finPulse: Financial Health Scanner for Next Gen Clients

Built at Futury Build Days for the Deutsche Bank Wealth Management challenge: “Design the Financial Health Scanner of the Future for Next Gen clients.”

What it does. finPulse shows a customer’s finances as a Wealth Tree that grows or shrinks with their real behaviour. The roots are savings by type, the trunk is investments, the leaves are spending habits (they rot when spending is far above peers), the soil is financial confidence, and the branches are life projects.

Who it’s for. The challenge’s Next Gen personas: Lena (24, first salary), Alex (29, starting a business) and Maya (31, inheritance), plus Max (26, heir of a family company). All data is demo data.

Core principle. Every score and figure is calculated by code: math, statistics and simulation. AI only explains the numbers in words, and guardrails reject any number the AI makes up.

How it’s built

Assessment model: a CFPB Financial Well-Being regression (2016 US survey, n = 5,208, R² 0.57) with age-band percentiles.
Prediction engine and AI agents (Python): a Monte Carlo simulation of 2,000 futures, a prediction agent that explains goals, risks and the best change, and an action agent that builds a ranked plan, both protected by guardrails.
Projects section: a life-project planner where the AI estimates costs and the app checks affordability.
Backend (Java 21, Spring Boot): the Wealth Tree engine, bank-data scoring against German household benchmarks (Destatis), the CFPB model ported to Java, monthly progress memory, product and learning recommendations, and a bridge that runs the Python engine and agents in one call.

Example: over 12 months, Maya’s score goes from 49 to 73 with the plan, versus 49 to 46 doing nothing.

Status: a hackathon prototype on demo data. The AI gives educational guidance, not investment advice.


- `backend/`: Java 21 / Spring Boot API (tree, scanner, progress, learning) and the Python bridge.
- `prediction-agent/`: Python simulation engine, prediction and action agents, guardrails.
- `projects-section/`: the customer dashboard (static site).

## Run it

You need Java 21+, Maven, Python 3.10+ and Node.js.

```bash
# 1. API key (one key for everything): copy the example and paste your key
cp backend/.env.example backend/.env
#    then edit backend/.env:  OPENAI_API_KEY=sk-...

# 2. Build the backend once
cd backend && mvn package -DskipTests && cd ..

# 3. Start everything (API :8080, AI bridge :8091, dashboard :5500)
python3 backend/tools/run_demo.py --components .
```

Open http://localhost:5500/index.html?user=max (also `maya`, `lena`, `alex`).
Stop with Ctrl+C. Logs are in `backend/data/run/`.

## Where the API keys go

| File | What | Needed? |
|---|---|---|
| `backend/.env` | `OPENAI_API_KEY` (+ optional `OPENAI_MODEL`, `OPENAI_BASE_URL`) | Yes, for live AI wording |
| `prediction-agent/.env` | `HIP_*` gateway key, only when running the agents on their own | Optional |
| `projects-section/js/secrets.js` | browser key for the life-projects planner (copy `secrets.example.js`) | Optional |

All three files are in `.gitignore`. Never commit a real key.
Without a key the demo still runs: the numbers come from the simulation and the text falls back to rule-based wording.

## Tests

```bash
cd backend && mvn test                                     # Java
cd prediction-agent && SKIP_AI=1 python3 -m pytest -q      # Python
```

More detail: [docs/DEMO_SETUP.md](docs/DEMO_SETUP.md), [docs/ACTION_MAPPING.md](docs/ACTION_MAPPING.md) and each component's README.
