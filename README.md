
# 📚 Student Tracker App – Dockerized FastAPI + Vault + MongoDB

This project is a **Cloud Native-ready FastAPI application** that allows students to:
- Register
- Track their weekly progress
- View other students’ progress

It connects to a **MongoDB Atlas database** using secrets managed securely via **HashiCorp Vault**, and is fully Dockerized for easy deployment.

---

## 🚀 Features

- 🔐 Vault integration for secure secret management
- ⚡ FastAPI backend with auto-generated Swagger docs
- 🐳 Dockerized for consistency and portability
- ☁️ MongoDB Atlas used as the database

---

## 📦 Prerequisites

Before you begin:

- [Docker](https://www.docker.com/products/docker-desktop) installed and running
- [Vault](https://developer.hashicorp.com/vault/docs/install) server running (locally or remotely)
- A **MongoDB Atlas cluster** or any MongoDB instance
- Basic familiarity with terminal/command line

---

## 🔐 Setting Up Vault (Do This Yourself)

This app does **not** come with shared Vault credentials for security reasons.  
To use the app:

### 1. Start Your Vault Server

```bash
vault server -dev
````

Or use a remote Vault instance (e.g., hosted, or in dev mode on another VM).

### 2. Enable and Create a KV Secrets Engine

```bash
vault secrets enable -path=secret kv-v2
```

### 3. Store Your MongoDB URI

```bash
vault kv put secret/mongodb uri="your-mongodb-connection-uri"
```

### 4. Create AppRole Credentials

```bash
vault auth enable approle

vault policy write student-tracker - <<EOF
path "secret/data/mongodb" {
  capabilities = ["read"]
}
EOF

vault write auth/approle/role/student-tracker \
  token_policies="student-tracker" \
  secret_id_ttl=60m \
  token_num_uses=10 \
  token_ttl=60m \
  token_max_ttl=120m

vault read auth/approle/role/student-tracker/role-id
vault write -f auth/approle/role/student-tracker/secret-id
```

Keep the `role_id` and `secret_id` — you'll need them when running the container.

---

## 🐳 Build and Run the Docker Container

### 1. Build the Image

```bash
docker build -t yourdockerhubusername/student-tracker:latest .
```

### 2. Run the Container with Vault Credentials

```bash
docker run -d -p 8000:8000 \
  -e VAULT_ADDR=http://<your-vault-address>:8200 \
  -e VAULT_ROLE_ID=<your-role-id> \
  -e VAULT_SECRET_ID=<your-secret-id> \
  yourdockerhubusername/student-tracker:latest
```

---

## 🔗 Test the API

Once the app is running, visit:

```
http://localhost:8000/docs
```

You’ll see the interactive Swagger UI where you can test all endpoints.

---

## 📁 Project Structure

```
.
├── app/                    # FastAPI application code
│   ├── main.py
│   └── ...
├── templates/              # HTML templates (if any)
├── Dockerfile              # Docker build instructions
├── requirements.txt        # Python dependencies
├── .gitignore
└── README.md
```

---

## ✨ Credits

This project is part of a 12-week **Cloud Native Bootcamp** covering:

* Docker
* Kubernetes
* CI/CD with GitHub Actions
* GitOps with ArgoCD
* Monitoring & Observability
* ...and more!

Mentors: **Chisom, Jimoh, Ileriayo**

---

## 💬 License & Contribution

Feel free to fork, adapt, and use for your own learning.
PRs welcome if you want to make it better!

---

## 🙌🏽 Built and maintained by:

**Zoe – Cloud Native Learner & DevOps Enthusiast**
Let’s connect on [LinkedIn]https://www.linkedin.com/in/oluwatimileyin-atanda/
\#ZoeInMyDna #CloudNative #DevOps #Docker #FastAPI #Vault

```

---

Let me know if you’d like to:
- Add environment variable defaults or `.env` support
- Include Kubernetes deployment instructions
- Add badge/status support (build passing, image size, etc.)
```

