# LogiCrisis

LogiCrisis is a multi-agent logistics recovery simulation built for exploring how LLM-powered agents collaborate under crisis conditions. The project models a disrupted Indian supply network and lets agents negotiate, reroute cargo, form coalitions, and recover from cascading failures in a realistic environment.

This repository was created as part of the Meta PyTorch OpenEnv Hackathon and focuses on multi-agent coordination, negotiation, and resilience planning in logistics operations.

## Why this project matters

Modern supply chains are fragile. A single disruption can trigger delays, spoilage, and missed deadlines across the network. LogiCrisis provides a controlled environment to study:

- cooperative decision-making under uncertainty
- coalition formation between agents
- negotiation and resource allocation
- cold-chain and emergency logistics behavior
- reinforcement learning and LLM-based policy research

## Key features

- Multi-agent environment with specialized logistics roles
- Partial observability and hidden-state reasoning
- Realistic logistics actions such as rerouting, bidding, and coalition formation
- Reward system covering delivery, negotiation, cold-chain safety, and efficiency
- OpenEnv-compatible API for reset, step, grading, and state inspection
- Demo app and inference pipeline for quick experimentation

## Project overview

The environment simulates a logistics network across India with multiple cities and disrupted routes. Agents must recover cargo movement while balancing costs, deadlines, and coordination constraints.

## Quick start

### 1. Clone the repository

```bash
git clone https://github.com/Saniya6112003/LOGICRIASIS.git
cd LOGICRIASIS
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Run the inference baseline

```bash
python inference.py
```

### 4. Start the API server

```bash
uvicorn api.app:app --reload --port 8000
```

### 5. Launch the demo

```bash
python demo/app.py
```

## Environment variables

The project supports a few optional environment variables for LLM-based inference:

- `API_BASE_URL`: OpenAI-compatible API endpoint
- `MODEL_NAME`: Model to use for agent action generation
- `HF_TOKEN`: Hugging Face token if required by your provider

## Project structure

```text
LOGICRIASIS/
├── agents/            # Agent prompts and policy logic
├── api/               # FastAPI OpenEnv server
├── demo/              # Gradio-based interactive demo
├── environment/       # World model, rewards, schemas, and tasks
├── inference.py       # Main inference script
├── requirements.txt   # Python dependencies
├── run.py             # Runner entry point
└── README.md          # Project overview
```

## Example use cases

- Test how different agent strategies recover from route failures
- Evaluate which coalition patterns improve recovery performance
- Compare heuristic policies with LLM-driven policies
- Build new crisis scenarios for logistics research

## License

This project is licensed under the MIT License.

## Author

Built and maintained by Saniya Randive.
