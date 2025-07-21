# Build Your First AI Microservice  
*From API to Docker to Deployment*

---

## 🧭 Why Are We Here?

You now have a solid, reliable FastAPI app wrapping your model — with retries, fallbacks, and caching. That’s great for development.

But if you want other teams, clients, or services to use your model in production, you need to:
- Package it
- Ship it
- Monitor it
- Make it easy to restart, scale, and secure

> This chapter shows you how to take your working API and turn it into a **deployable, monitorable microservice**.

---

## 🎯 What You’ll Build

You’ll learn how to:
- ✅ Dockerize your FastAPI app
- ✅ Add a health check endpoint
- ✅ Set up structured logging
- ✅ Deploy it to the cloud (EC2, on-prem, etc.)
- ✅ Understand what makes a model “production ready”

---

## 🛠️ Step 1: Dockerize Your App

### Create a `Dockerfile`
```Dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### `requirements.txt`
```
fastapi
uvicorn
openai
backoff
redis
```

### Build & Run Locally
```bash
docker build -t ai-service .
docker run -p 8000:8000 ai-service
```

Now your AI is portable. Anyone can run it.

---

## ❤️ Step 2: Add a Health Endpoint

Kubernetes, Fly.io, and most orchestrators expect your service to respond at `/health`.

```python
@app.get("/health")
def health():
    return {"status": "ok"}
```

Make this fast and reliable. Don’t do any model calls here.

---

## 📊 Step 3: Add Structured Logging

```python
import json, time

@app.middleware("http")
async def log_requests(request, call_next):
    start = time.time()
    response = await call_next(request)
    duration = round((time.time() - start) * 1000)

    log = {
        "method": request.method,
        "path": request.url.path,
        "status": response.status_code,
        "duration_ms": duration
    }
    print(json.dumps(log))
    return response
```

Later, you can stream this to a log system like:
- Logtail
- Loki + Grafana
- ELK Stack

---

## ☁️ Step 4: Deploy It Manually to the Cloud or On-Prem

### Option 1: Run It On-Prem or on a VM

1. SSH into your VM (EC2, DigitalOcean, etc.):
   ```bash
   ssh user@your-server-ip
   ```
2. Clone your repo:
   ```bash
   git clone your-repo-url && cd your-repo
   ```
3. Build and run the Docker container:
   ```bash
   docker build -t ai-service .
   docker run -d -p 80:8000 --env-file .env ai-service
   ```

> Use `.env` to store keys like `OPENAI_API_KEY`, and access them in code with `os.getenv()`.

### Option 2: Add Reverse Proxy (Optional)
Use Nginx to serve FastAPI through port 80/443:
```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Then reload nginx:
```bash
sudo systemctl reload nginx
```

### Option 3: Auto-Restart with systemd (Optional)
Create a service unit:
```ini
[Unit]
Description=AI Service
After=docker.service

[Service]
Restart=always
ExecStart=/usr/bin/docker run -p 8000:8000 ai-service

[Install]
WantedBy=default.target
```
Then run:
```bash
sudo systemctl enable ai-service
sudo systemctl start ai-service
```

Now your model API auto-restarts on boot or crash.

All of this gives your dev team real control over deployment, monitoring, and scaling without relying on no-code platforms.

---

## 📦 Microproject: Ship Your AI Microservice

Take your `/predict` FastAPI app and:

✅ Dockerize it with a proper `Dockerfile`  
✅ Add a `/health` route  
✅ Add structured logging middleware  
✅ Deploy it to a VM or on-prem instance

### Bonus:
- Add a `/version` route with commit hash or model version  
- Use a `.env` file to manage secrets locally  
- Add an HTTP header for tracing requests across systems

---

## 🧠 Reflection Questions

1. What’s the difference between running locally and deploying a container?
2. What would you monitor in your deployed AI system?
3. What happens if your API crashes in the middle of the night?

---

## ✅ Best Practices for Deployable AI Services

✅ Always expose `/health` for infra tools  
✅ Log requests in structured format (JSON)  
✅ Use a real HTTP server (`uvicorn`, `gunicorn`) not `app.run()`  
✅ Don’t bundle secrets in your code — use env vars  
✅ Think about versioning early — models, APIs, logic  
✅ Deploy from clean repos — not random zip files  
✅ Make it easy for teammates to run your service

> 🚀 If someone else can deploy it without you, you’ve done it right.

---

## ✅ You Now Know:

✅ How to package and deploy your model as a real microservice  
✅ How to log and monitor it like a backend engineer  
✅ How to expose health, status, and versioning endpoints  
✅ How to move from local hacks to production-grade AI infrastructure