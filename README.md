## Student Information

- **Student Name:** `DAMIS GABRIEL MANFOUO`  
- **Student ID:** `041204270`

## 1. Explanation of the Application

This project is a cloud-native e-commerce demo for Best Buy, based on the Algonquin Pet Store (On Steroids)architecture.  
The system is built as a set of microservices deployed on Kubernetes. Customers use a public **Store-Front** web app, while employees use an internal **Store-Admin** app to manage products and monitor orders. Business logic is split into independent services, backed by a MongoDB database and integrated with AI models.

## 2. Main Components

- **Store-Front (Vue)**  
  Customer-facing SPA used to browse products, view details, and place orders.  
  Talks to **Product-Service** and **Order-Service**.

- **Store-Admin (Vue)**  
  Employee-facing SPA for managing inventory and viewing order status.  
  Uses **Order-Service**, **Makeline-Service**, and **AI-Service**.

- **Product-Service (Rust)**  
  REST API for product catalog operations (list, search, create, update, delete).  
  Stores product data in MongoDB.

- **Order-Service (Node.js)**  
  REST API for creating and querying orders.  
  Persists initial order data and publishes order messages to the **RabbitMQ order queue**.

- **Order Queue (RabbitMQ)**  
  Message broker used to decouple order intake from order processing.  
  Buffers incoming orders so they can be processed reliably and asynchronously.

- **Makeline-Service (Go)**  
  Background worker that consumes messages from the order queue.  
  Simulates fulfillment steps and updates order status in MongoDB (e.g., Pending → Processing → Completed).

- **Order Database (MongoDB, StatefulSet)**  
  Central persistent store for orders (and optionally products).  
  Deployed as a StatefulSet to provide stable storage for the application.

- **AI-Service (Python)**  
  Backend service that integrates with external AI models.  
  Calls **GPT-4** for text generation of the description of products and **DALL·E** for generated images, and exposes simple HTTP endpoints to the frontends.

## 3. Data Flow and Cloud-Native Characteristics

1. Customers place orders through **Store-Front**, which calls **Order-Service**.  
2. **Order-Service** writes the order and pushes a message to the **RabbitMQ** queue.  
3. **Makeline-Service** consumes messages, processes orders, and updates status in **MongoDB**.  
4. **Store-Admin** reads order and product data from **Order-Service** and **Product-Service** for monitoring and management.  
5. Both frontends can call **AI-Service** to leverage GPT-4 and DALL·E without exposing API keys.

Each microservice is stateless (except MongoDB), containerized, and runs as its own Deployment in Kubernetes. This design supports independent scaling, fault isolation, and aligns with cloud-native best practices.

## 4. Deployment instructions

This guide explains how to deploy the **entire Best Buy cloud-native application** to a Kubernetes cluster using three YAML files:

- `secrets.yaml` – Kubernetes Secrets for OpenAI (GPT-4 & DALL·E).
- `all-in-one.yaml` – Main application (MongoDB, RabbitMQ, APIs, frontends, AI services).
- `admin-tasks.yaml` – Virtual Customer & Virtual Worker tasks.

+ Creata an Azure Kubernates CLuster
+ Use Kubectl to launch these file on a cluster

---

## 1. Prerequisites

Before starting, you should have:

- A running **Kubernetes cluster** (for example, AKS from CST8915).
- `kubectl` installed and configured to talk to your cluster.
- All your Docker images already built and pushed to a registry,  
  and the image names in the YAML files are correct.
- The YAML manifests:
  - `bestbuy-openai-secrets.yaml`
  - `bestbuy-all-in-one.yaml`
  - `bestbuy-admin-tasks.yaml`


## 5. Links Tables


| Repositories | Links | Docker Hub Images |
|----------|----------|------------------|
| Ai Service  | https://github.com/Gabriel10-10/ai-service-L8  | https://hub.docker.com/repository/docker/gabriel1010/ai-service-l9/general |
| Dall-E Service | https://github.com/Gabriel10-10/dall-e-service-L8  | https://hub.docker.com/repository/docker/gabriel1010/dall-e-service-l9 |
| Gpt Service    | https://github.com/Gabriel10-10/gpt-service-L8     | https://hub.docker.com/repository/docker/gabriel1010/gpt-service-l9/general |
| Makeline Service| https://github.com/Gabriel10-10/makeline-service-L8 | https://hub.docker.com/repository/docker/gabriel1010/makeline-service-l9/general |
| Order service  |  https://github.com/Gabriel10-10/order-service-L8   | https://hub.docker.com/repository/docker/gabriel1010/order-service-l9/general |
| Product service | https://github.com/Gabriel10-10/product-service-L8 | https://hub.docker.com/repository/docker/gabriel1010/product-service-l9/general |
| Store Front    | https://github.com/Gabriel10-10/Store-front-Finalexam | https://hub.docker.com/repository/docker/gabriel1010/store-front-final/general |
| Store admin    | https://github.com/Gabriel10-10/store-admin-L8  | https://hub.docker.com/repository/docker/gabriel1010/store-admin-l9/general |
| Virtual Customer | https://github.com/Gabriel10-10/virtual-customer-L8 | https://hub.docker.com/repository/docker/gabriel1010/virtual-customer-l9/general |
| Virtual Worker | https://github.com/Gabriel10-10/virtual-worker-L8 | https://hub.docker.com/repository/docker/gabriel1010/virtual-worker-l9 |

## Youtube Links

https://youtu.be/XaHSnkSUCAM