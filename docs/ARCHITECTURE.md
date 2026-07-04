# TaskApp Kubernetes Architecture

## Overview

TaskApp is deployed on a 3-node k3s Kubernetes cluster on AWS EC2.

- 1 control-plane node
- 2 worker nodes
- Dedicated namespace: `taskapp`
- PostgreSQL runs as a StatefulSet with persistent storage
- Backend runs as a Deployment with 2 replicas
- Frontend runs as a Deployment with 2 replicas
- Services expose frontend, backend, and Postgres internally
- Traefik Ingress routes external HTTP traffic to the frontend and backend

## Request Flow

User browser -> Ingress/Traefik -> Frontend Service -> Frontend Pods

API traffic follows:

Frontend -> Backend Service -> Backend Pods -> PostgreSQL Service -> Postgres StatefulSet/PVC

## Kubernetes Objects

- Namespace: `taskapp`
- Secret: `taskapp-secret`
- StatefulSet: `postgres`
- PVC: PostgreSQL data storage
- Deployment: `backend`
- Deployment: `frontend`
- Services: `frontend`, `backend`, `postgres`
- Ingress: `taskapp-ingress`

## Multi-node Design

The cluster uses three real EC2 nodes. The app is designed to run with multiple frontend and backend replicas to reduce single-node risk.

## Networking

Kubernetes DNS resolves internal services such as:

- `frontend.taskapp.svc.cluster.local`
- `backend.taskapp.svc.cluster.local`
- `postgres.taskapp.svc.cluster.local`

During deployment, a pod DNS issue was resolved by allowing node-to-node traffic in the AWS security group.

## Evidence

Screenshots and logs are stored in `docs/EVIDENCE/`.
