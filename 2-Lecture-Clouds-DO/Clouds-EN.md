# Lecture: Cloud Hosting using DigitalOcean and its role in DevOps

## 1. What is cloud hosting?

**Cloud hosting** is when websites, applications, or databases run not on one specific server, but in the "cloud" — a large network of servers in data centers.  
Users rent only the resources they need and can quickly scale them up or down.

### Main advantages:
- **Scalability** — can add more CPU, RAM, or disk with a few clicks.  
- **Pay for what you use** — no need to buy your own equipment, pay only for resources.  
- **Reliability** — if one server fails, another takes over the work.  
- **Flexibility** — suitable for both small projects and large companies.  

---

## 2. DigitalOcean as an example

**DigitalOcean** is a company that provides cloud hosting services. It's known for simplicity and convenience for developers.

### Main DigitalOcean services:
- **Droplets** — virtual servers that can be created in minutes.  
- **Kubernetes (DOKS)** — managed service for running containers.  
- **App Platform** — platform for deploying applications without complex configurations.  
- **Databases** — managed PostgreSQL, MySQL, Redis, and others.  
- **Spaces and Volumes** — file storage and additional disks.  

### What makes DigitalOcean different?
- Simple interface and transparent pricing.  
- Start from a few dollars per month.  
- Focus on developers and startups.  

---

## 3. What role does cloud hosting play in DevOps?

**DevOps** is an approach where developers and system administrators work together to quickly and safely deliver changes to applications.

Cloud services (like DigitalOcean) are needed here for:
- **Infrastructure as Code (IaC):** everything can be described in configurations (e.g., Terraform), and anyone on the team can recreate the environment.  
- **CI/CD:** cloud allows quickly building and deploying code, connecting Jenkins, GitLab CI, or GitHub Actions.  
- **Test environments:** easily create temporary servers for testing new versions.  
- **Monitoring and scaling:** can automatically increase resources under load.  

---

## 4. Summary

| Term                 | What it means                                                   |
|----------------------|------------------------------------------------------------------|
| **Cloud hosting**    | Hosting websites/apps on a network of servers in data centers. |
| **DigitalOcean**     | Provider that offers simple-to-use cloud services.             |
| **DevOps**           | Work approach that combines development and administration.     |
| **DigitalOcean's role** | Allows DevOps teams to quickly launch, test, and scale infrastructure. |

---

## 5. Diagram: DevOps + DigitalOcean

### Text Diagram

```
┌─────────────────┐    Writes code    ┌─────────────────┐
│   DevOps team   │ ─────────────────►│ GitHub / GitLab │
└─────────────────┘                   └─────────────────┘
         │                                      │
         │ IaC (Terraform, Ansible)             │ CI/CD pipeline
         ▼                                      ▼
┌─────────────────┐                   ┌─────────────────┐
│ Monitoring,     │◄──────────────────┤ DigitalOcean    │
│ logging         │                   │ Droplets /      │
└─────────────────┘                   │ Kubernetes      │
                                      └─────────────────┘
                                               │
                                               │ App hosting
                                               ▼
                                      ┌─────────────────┐
                                      │     Users       │
                                      └─────────────────┘
```

**Diagram Explanation:**
1. **DevOps team** writes code and pushes it to Git repository
2. **GitHub/GitLab** runs CI/CD pipeline for automatic testing and deployment
3. **DigitalOcean** hosts the application on Droplets or Kubernetes
4. **Users** get access to the application
5. **IaC** allows automating infrastructure creation
6. **Monitoring** provides feedback about system status

---

✅ Conclusion: **Cloud hosting** makes infrastructure accessible, flexible, and simple.  
**DigitalOcean** — one of the most convenient options for developers.  
In the world of **DevOps**, this is the foundation for fast code work and its stable deployment.
