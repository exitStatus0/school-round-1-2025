# Virtualization with VirtualBox in DevOps

## What is VirtualBox?
[VirtualBox](https://www.virtualbox.org/) is a free virtualization program that allows you to run other operating systems on your computer, as if they were separate machines.  
In simple terms: you can create a **"virtual computer"** inside your laptop or PC.

---

## Why do we need VirtualBox?
1. **Testing without risk**  
   You can run new operating systems, services, and experiments without fear of breaking your main system.

2. **DevOps Environment**  
   DevOps engineers often work with Linux servers. VirtualBox allows you to quickly spin up Linux directly on your computer (even if you have Windows or macOS).

3. **Learning and Practice**  
   You can practice with Docker, Kubernetes, CI/CD tools in an isolated environment.

4. **Project Isolation**  
   Each project can be deployed in its own virtual machine — this is convenient for organizing work environments.

---

## How is it used in DevOps?
- **Local Server**  
  Create a virtual machine with Ubuntu / CentOS and use it as your "mini-server".

- **Training with Tools**  
  On a virtual machine you can:
  - configure Docker and containers,
  - practice with Kubernetes,
  - set up Jenkins or GitLab for CI/CD.

- **Production Environment Simulation**  
  Locally create several virtual machines and connect them into a "cluster". This helps understand how services work in real companies.

---

## Example Scenario
1. Install VirtualBox.  
2. Create a VM with **Ubuntu Server**.  
3. Install Docker and run a container with a web application.  
4. Test deployment without risk to the main OS.  

---

## Pros and Cons of VirtualBox

✅ **Pros:**
- free and easy to use,  
- works on Windows, macOS and Linux,  
- allows training with "live" servers.  

❌ **Cons:**
- virtual machines are "heavier" than containers,  
- require more resources (CPU, RAM, disk).  

---

## Conclusion
VirtualBox is a basic tool that helps future DevOps engineers:
- quickly create test environments,
- practice with infrastructure,
- learn without risk of breaking the main system.

👉 If you're just starting your journey in DevOps — VirtualBox will become your first "playground for experiments".
