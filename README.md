![Banner](./docs/images/banner.png)

<div align="center">
  <div align="center">

[![Stars](https://img.shields.io/github/stars/aws-containers/retail-store-sample-app)](Stars)
![GitHub License](https://img.shields.io/github/license/aws-containers/retail-store-sample-app?color=green)
![Dynamic JSON Badge](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fraw.githubusercontent.com%2Faws-containers%2Fretail-store-sample-app%2Frefs%2Fheads%2Fmain%2F.release-please-manifest.json&query=%24%5B%22.%22%5D&label=release)
![GitHub Release Date](https://img.shields.io/github/release-date/aws-containers/retail-store-sample-app)

  </div>

  <strong>
  <h2>AWS Containers Retail Sample</h2>
  </strong>
</div>

This is a sample application designed to illustrate various concepts related to containers on AWS. It presents a sample retail store application including a product catalog, shopping cart and checkout.

It provides:

- A demo store-front application with themes, pages to show container and application topology information, generative AI chat bot and utility functions for experimentation and demos.
- An optional distributed component architecture using various languages and frameworks
- A variety of different persistence backends for the various components like MariaDB (or MySQL), DynamoDB and Redis
- The ability to run in different container orchestration technologies like Docker Compose, Kubernetes etc.
- Pre-built container images for both x86-64 and ARM64 CPU architectures
- All components instrumented for Prometheus metrics and OpenTelemetry OTLP tracing
- Support for Istio on Kubernetes
- Load generator which exercises all of the infrastructure


![Screenshot](/docs/images/screenshot.png)

## Application Architecture

The application has been deliberately over-engineered to generate multiple de-coupled components. These components generally have different infrastructure dependencies, and may support multiple "backends" (example: Carts service supports MongoDB or DynamoDB).

![Architecture](/docs/images/architecture.png)

| Component                  | Language | Container Image                                                             | Helm Chart                                                                        | Description                             |
| -------------------------- | -------- | --------------------------------------------------------------------------- | --------------------------------------------------------------------------------- | --------------------------------------- |
| [UI](./src/ui/)            | Java     | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-ui)       | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-ui-chart)       | Store user interface                    |
| [Catalog](./src/catalog/)  | Go       | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-catalog)  | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-catalog-chart)  | Product catalog API                     |
| [Cart](./src/cart/)        | Java     | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-cart)     | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-cart-chart)     | User shopping carts API                 |
| [Orders](./src/orders)     | Java     | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-orders)   | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-orders-chart)   | User orders API                         |
| [Checkout](./src/checkout) | Node     | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-checkout) | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-checkout-chart) | API to orchestrate the checkout process |

## Quickstart

The following sections provide quickstart instructions for various platforms.

### Docker

This deployment method will run the application as a single container on your local machine using `docker`.

Pre-requisites:

- Docker installed locally

Run the container:

```
docker run -it --rm -p 8888:8080 public.ecr.aws/aws-containers/retail-store-sample-ui:1.0.0
```

Open the frontend in a browser window:

```
http://localhost:8888
```

To stop the container in `docker` use Ctrl+C.

### Docker Compose

This deployment method will run the application on your local machine using `docker-compose`.

Pre-requisites:

- Docker installed locally

Download the latest Docker Compose file and use `docker compose` to run the application containers:

```
wget https://github.com/aws-containers/retail-store-sample-app/releases/latest/download/docker-compose.yaml

DB_PASSWORD='<some password>' docker compose --file docker-compose.yaml up
```

Open the frontend in a browser window:

```
http://localhost:8888
```

To stop the containers in `docker compose` use Ctrl+C. To delete all the containers and related resources run:

```
docker compose -f docker-compose.yaml down
```
---

# 🛡️ DevOps Strategy: Hardening & Orchestration

---

## 🧊 Phase 1: Hardening the Container (The "Pro" Strategy)
**Goal:** Take a "heavy and risky" application and turn it into a "light and secure" professional image.

### 1. Multi-Stage Builds
* **What:** We use two "stages" in one Dockerfile. **Stage 1 (The Kitchen)** has all the heavy tools to build the code. **Stage 2 (The Dining Table)** only gets the final "cooked" app.
* **Why:** Standard images are 1GB because they keep the "Kitchen tools" (compilers, source code) inside. By throwing them away in Stage 2, we reduced our image from **1.03GB to 111MB**.
> 💡 **Pro Benefit:** Smaller images save money on cloud storage (**FinOps**) and deploy much faster.

### 2. Distroless & Scratch Images
* **What:** "**Distroless**" is a tiny image that has no Operating System (no Ubuntu, no Shell). "**Scratch**" is an empty 0MB starting point.
* **Why:** Most hackers use the "Shell" (`/bin/sh`) to run bad commands if they break into your app. By using Distroless, we **removed the shell**. If there is no shell, the hacker has no tools to work with.
> 💡 **Pro Benefit:** This is "**Security by Design.**" You aren't just fixing bugs; you are removing the place where bugs live.

### 3. SBOM (Software Bill of Materials)
* **What:** A digital "**Ingredients List**" of every library used inside your container.
* **Why:** If a library (like `log4j`) has a security hole tomorrow, you don't need to guess if you are at risk. You just **check your SBOM file**.
> 💡 **Pro Benefit:** This provides **Transparency**. Companies love this because it makes "**Compliance**" easy.

### 4. Vulnerability Scanning (Grype & Scout)
* **What:** Using AI and Security Databases to check for "**CVEs**" (Common Vulnerabilities and Exposures).
* **Why:** Even if your container is small, the libraries inside (Spring, Jackson) might have bugs. We scan to find and "**Patch**" (update) them.
> 💡 **Pro Benefit:** "**Shift-Left Security.**" We find the problem *before* it reaches the customer.

### 5. Digital Signing (Cosign)
* **What:** Putting a "**Digital Wax Seal**" on your image.
* **Why:** To prove that **Raj** built this image and no one changed it on the way to the server.
> 💡 **Pro Benefit:** This ensures **Integrity**. It prevents "Man-in-the-Middle" attacks.

---

## 📋  Technical Documentation: The Artifact Record

| Artifact Type | Tooling | Purpose | Location |
| :--- | :--- | :--- | :--- |
| **Container Image** | Docker Buildx | Hardened Multi-arch Runtime | Docker Hub |
| **Digital Signature** | Cosign | Authenticity Verification | Attached to Image OCI |
| **Inventory (SBOM)** | Syft | Vulnerability/License Audit | GitHub Artifacts |
| **Provenance** | GitHub SLSA | Proof of Build Origin | Attached to Image OCI |
| **Release Record** | Custom Script | Immutable SHA Mapping | release.log |

---

## ☸️ Phase 2: Kubernetes Orchestration (The Infrastructure)
Before we could monitor the app, we had to "teach" Kubernetes how to run it. This phase moved us from a single machine (Docker) to a distributed system.

* **Kustomize for Clean Code:** We created a "**Base**" (the common parts) and "**Overlays**" (the specific settings for production). This allowed us to change a database password or an image tag in one place instead of ten.
* **Microservice Interconnect:** We defined **Services** for every pod. This provided a stable "**Internal Phone Book**" (DNS) so that the UI could talk to catalog or orders without needing to know their changing IP addresses.
* **Init Containers (The Waiters):** We used **Init Containers** to solve the "**Boot Order**" problem. For example, the catalog app is told to wait until the catalog-db is actually ready. This prevented the app from crashing during startup.
* **StatefulSets for Data:** For databases like **MySQL** and **Postgres**, we used **StatefulSets** instead of Deployments. This ensured that our data had a "sticky identity" and wouldn't be lost if a pod restarted.
* **Ingress (The Front Door):** We set up an **Ingress Controller (Traefik)**. This allowed us to map a real web address (`retail.local`) to our cluster, letting us access the store through a browser just like a real user.
* **Secret & Config Management:** We separated our code from our configuration. We used **ConfigMaps** for general settings and **Kubernetes Secrets** for passwords, ensuring that sensitive data was handled more securely than plain text.

---
## 🌉 Phase 3: CI/CD 
This phase focused on removing manual intervention and ensuring every deployment is secure, signed, and traceable.

### 🚀 Continuous Integration (CI) with GitHub Actions
We built a sophisticated pipeline that doesn't just "build" but "secures."

* **Quality Gates:** We integrated **Anchore (Grype)** to scan images. If a critical vulnerability is found, the pipeline "breaks," preventing a dangerous image from ever being deployed.
* **Automated Tagging (The End of "Latest"):**
    * We moved away from the `:latest` tag anti-pattern.
    * Our pipeline now generates a unique **Git SHA** tag for every build. This provides a 1:1 map between the running container and the specific commit in GitHub.

### 📝 The Manifest Write-back
We automated the update of our **Kustomize** manifests to ensure the cluster always matches the latest verified build.

* **Surgical Updates:** Upon a successful build and security scan, a GitHub Action uses `kustomize edit set image` to surgically update the `newTag` in our `k8s/overlays/production` folder.
* **Triggering the Loop:** This commit to the repository acts as the signal that triggers the **GitOps Loop**.

---

## Phase 4: GitOps with ArgoCD (The Brain)
This was our biggest leap. We moved away from manual `kubectl apply` commands to a "**GitOps**" workflow.

* **ArgoCD Installation:** We deployed **ArgoCD** into our cluster to act as the "Source of Truth" controller.
* **Single Source of Truth:** We connected our GitHub repository to ArgoCD. Now, the Git repo is the "**Boss**"—whatever is written in Git is what exists in the cluster.
* **Self-Healing:** If someone accidentally deletes a pod or changes a setting manually, ArgoCD detects the "**drift**" and automatically fixes the cluster within seconds.
* **The Automated Loop:** We created a **Write-back Pipeline**. When a code change is pushed:
    1.  GitHub Actions builds the image.
    2.  It performs security scans.
    3.  It **automatically updates** the `newTag` in our Kustomize file with the unique Git SHA.
    4.  ArgoCD sees the Git update and rolls out the new version to the store.

---

## 📊 Phase 5: Observability & Automation (The "Run" Phase)

### 4. Cluster Monitoring
After we got our containers running, we needed to see how they performed. We moved from "guessing" to "**knowing**."

* **Prometheus Stack:** We installed a full monitoring suite using **Helm**. This allowed us to track CPU, Memory, and Network traffic for all 10 microservices.
* **Service Discovery:** We used **ServiceMonitors** to tell Prometheus how to "find" our apps. This is the "Senior" way to automate monitoring—as soon as a new pod starts, it is automatically tracked.
* **Database Exporters:** Since databases (MySQL, Postgres, Redis) don't speak "Prometheus," we deployed **Exporters**. These act as translators, turning database stats into beautiful graphs.
* **Grafana Dashboards:** We connected Prometheus to Grafana to visualize the health of the **Retail Store**. We used the "Spring Boot Statistics" dashboard to see real-time Java memory usage and error rates.

---

## App-Logic

diagram-export-4-13-2026-6_24_19-PM.png