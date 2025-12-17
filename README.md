# ☁️ SkyOps - Cloud & Operations Microservice

![Python](https://img.shields.io/badge/Python-3.11-blue.svg?logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-0.111-009688?logo=fastapi)
![Prometheus](https://img.shields.io/badge/Prometheus-Metrics-E6522C?logo=prometheus)
![Docker](https://img.shields.io/badge/Docker-Enabled-2496ED?logo=docker)
![Kubernetes](https://img.shields.io/badge/Kubernetes-Ready-326CE5?logo=kubernetes)
![CI/CD](https://img.shields.io/badge/CI/CD-GitHub_Actions-2088FF?logo=github-actions)

**SkyOps** is a Python **FastAPI** service focused on operational excellence:
- Health/Readiness endpoints for containers
- **/status** returns CPU/RAM + environment info
- **/metrics** exposes **Prometheus** counters/gauges/histograms
- **/simulate-cpu** to generate controlled CPU load (great for HPA demos)
- JSON logs with request IDs for traceability

------------------------

## 🛠 Tech & Languages

| Layer | Tech | Notes |
|------|------|------|
| Language | **Python 3.11** | Popular for DevOps & APIs |
| Framework | **FastAPI** | Async, typed, auto docs |
| Metrics | **prometheus_client** | Counters, histograms, gauges |
| System | **psutil** | Process CPU/memory stats |
| Container | **Docker** | Build & ship |
| Orchestrator | **Kubernetes** | Probes, scaling, services |

---

## 🌐 Architecture

<p align="center">
  <img src="https://raw.githubusercontent.com/your-org/skyops-assets/main/architecture.png" alt="SkyOps Architecture" width="650" />
</p>

Flow:
1. Client hits API (`/health`, `/ready`, `/status`, `/simulate-cpu`, `/metrics`)  
2. Middleware emits **request metrics** + **JSON logs** with `x-request-id`  
3. **Prometheus** scrapes `/metrics`  
4. Deployed as **Docker** container to **Kubernetes** (Deployment + Service)

---

## 📦 Repository Structure

skyops/
├─ app/
│ └─ main.py
├─ tests/
│ └─ test_health.py
├─ k8s/
│ ├─ deployment.yaml
│ └─ service.yaml
├─ Dockerfile
├─ requirements.txt
├─ Makefile
└─ README.md

yaml
Copy code

---------

## ▶️ Run in Google Colab

Use the one-cell setup from the notebook. After it prints the URL, test:

```python
import httpx
print(httpx.get("http://127.0.0.1:8001/health").json())
print(httpx.get("http://127.0.0.1:8001/status").json()["process"])
🔗 API Endpoints
Method	Path	Description
GET	/	Service info
GET	/health	Liveness check
GET	/ready	Readiness check
GET	/status	CPU, memory, env snapshot
POST	/simulate-cpu	Busy-loop to simulate CPU load
GET	/metrics	Prometheus metrics

Examples
Status

bash
Copy code
curl http://localhost:8001/status | jq
Simulate CPU for 10s with 2 workers

bash
Copy code
curl -X POST http://localhost:8001/simulate-cpu \
  -H "Content-Type: application/json" \
  -d '{"seconds": 10, "parallelism": 2}'
Metrics

bash
Copy code
curl http://localhost:8001/metrics
🧪 Tests
bash
Copy code
pytest -q
Included tests:

/health returns {"status": "ok"}

/ready returns {"ready": true}

🐳 Docker
Build:

bash
Copy code
docker build -t skyops:latest .
Run:

bash
Copy code
docker run -p 8001:8001 skyops:latest
Open: http://localhost:8001/health

☸️ Kubernetes
Update image in k8s/deployment.yaml:

yaml
Copy code
image: ghcr.io/YOUR_GH_USERNAME/skyops:latest
Apply:

bash
Copy code
kubectl apply -f k8s/
kubectl get pods
kubectl port-forward svc/skyops 8001:80
📊 Prometheus Metrics
Key series:

skyops_http_requests_total{method,path,status}

skyops_http_request_duration_seconds_bucket{method,path,le}

skyops_cpu_percent

skyops_memory_rss_bytes

Scrape config snippet:

yaml
Copy code
- job_name: "skyops"
  static_configs:
    - targets: ["skyops.default.svc.cluster.local:80"]
📈 Interactive Docs
Swagger UI: /docs

ReDoc: /redoc

<p align="center"> <img src="https://raw.githubusercontent.com/your-org/skyops-assets/main/fastapi-docs.png" alt="FastAPI Docs" width="700" /> </p>
🔐 Production Notes
Add auth (API key/JWT) for non-public deployments

Centralize logs (Loki/ELK) — logs already JSON formatted

Add tracing (OpenTelemetry) and propagate x-request-id

Create HPA based on CPU to demo /simulate-cpu scaling

👤 Author
Siddharth Raut — Cloud & DevOps Engineer

Email: siduk2500@gmail.com

LinkedIn: siddharth-raut-

📝 License
MIT © 2025 Siddharth Raut





