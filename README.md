📘 Document 1
damashz-deploy/README.md
Damashz – End-to-End GitOps Deployment Guide
1. Project Overview

Damashz is a cloud-native, multi-service application deployed using a full GitOps workflow.

This setup demonstrates:

Local Kubernetes cluster using kind

ArgoCD for GitOps

GitHub Actions for CI

GHCR as container registry

PostgreSQL StatefulSet with persistent volume

FastAPI backend deployed declaratively

This environment runs entirely on a single Windows machine using WSL2.

2. Infrastructure Stack
Host Environment

OS: Windows 10/11

WSL2 enabled

Ubuntu WSL distribution

Docker Desktop (WSL integration enabled)

1 CPU

16GB RAM

~250GB SSD

3. Architecture Overview
4. Repositories
damashz-api

Purpose:

FastAPI backend

Docker build

GitHub Actions CI

Push image to GHCR

Registry:

ghcr.io/aashishsingh420/damashz-api
damashz-deploy

Purpose:

Kubernetes manifests

Kustomize overlays

ArgoCD source of truth

Structure:

damashz-deploy/
├── apps/
│   ├── damashz-api/
│   └── postgres/
├── environments/
│   └── dev/
└── kustomization.yaml
5. CI/CD Flow

Pipeline Behavior:

Push to main

GitHub Action builds Docker image

Image pushed to GHCR

ArgoCD detects commit in deploy repo

ArgoCD syncs manifests

Kubernetes updates Deployment

Pod pulls new image

6. Kubernetes Components

Namespace:

damashz

Resources:

Resource	Type
damashz-api	Deployment
damashz-api	Service (ClusterIP)
damashz-postgres	StatefulSet
damashz-postgres	Service
damashz-postgres-secret	Secret
damashz-api-secret	Secret
damashz-api-config	ConfigMap
PVC	PersistentVolumeClaim
7. Postgres Deployment

Image: postgres:16

StatefulSet

PVC attached

Secret-based credentials

ClusterIP service

Port: 5432

Data persists across pod restarts.

8. API Deployment

Image:

ghcr.io/aashishsingh420/damashz-api

Port: 8000

Service Type: ClusterIP

Environment variables injected via:

ConfigMap

Secret

9. Local Access

Temporary local access via:

kubectl port-forward svc/damashz-api -n damashz 8000:8000

Access:

http://localhost:8000/health
10. Operational Commands

Check pods:

kubectl get pods -n damashz

Check services:

kubectl get svc -n damashz

Check Argo app:

kubectl get application damashz-dev -n argocd

Check logs:

kubectl logs <pod-name> -n damashz
11. Current Status

Cluster Health: Healthy
Argo Sync: Synced
Pods: Running
Database: Running with PVC

System is stable.
