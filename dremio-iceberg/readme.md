# Iceberg Local Datalakehouse POC Environment

This document provides a step-by-step guide to set up a Proof of Concept (POC) environment for the Iceberg Local Datalakehouse using k3d, including installation, deployment, and port forwarding.

## Prerequisites

- Docker installed on your machine
- kubectl installed on your machine

## Installation of k3d

1. **Download and install k3d:**

    ```sh
    wget -q -O - https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash
    ```

2. **Verify the installation:**

    ```sh
    k3d version
    ```

## Create a k3d Cluster

1. **Create a new k3d cluster:**

    ```sh
    k3d cluster create --servers 2 --agents 3
    ```

2. **Verify the cluster is running:**

    ```sh
    kubectl get nodes -n iceberg-poc -o wide
    ```

## Deployment

1. **Clone the repository:**

    ```sh
    git clone https://github.com/navjyotnishant/iceberg-local-datalakehouse.git
    cd iceberg-local-datalakehouse/dremio-iceberg
    ```

2. **Apply the Kubernetes manifests:**

    ```sh
    kubectl apply -f k3d/
    ```

3. **Verify the deployment:**

    ```sh
    kubectl get pods -n iceberg-poc -o wide
    ```


4. **Port forward the necessary services to access them locally:**

        ```sh
        kubectl port-forward service/nessie -n iceberg-poc 30020:19120 --address 0.0.0.0 &
        kubectl port-forward service/minio -n iceberg-poc 30001:9001 --address 0.0.0.0 &
        kubectl port-forward service/dremio -n iceberg-poc 30047:9047 --address 0.0.0.0 &

        kubectl port-forward service/spark -n iceberg-poc 30080:8080 --address 0.0.0.0 &
        kubectl port-forward service/spark -n iceberg-poc 30082:4040 --address 0.0.0.0 &
        kubectl port-forward service/spark -n iceberg-poc 30084:8888 --address 0.0.0.0 &
        ```

5. **Access the UIs:**

        - **Nessie UI:** Open your browser and navigate to `http://localhost:30020`.
        - **MinIO UI:** Open your browser and navigate to `http://localhost:30001`.
        - **Dremio UI:** Open your browser and navigate to `http://localhost:30047`.
        - **Jupyter-Notebook** Open your browser and navigate to `http://localhost:30084`.
        - **Master-web-ui** Open your browser and navigate to `http://localhost:30080`.
        - **Spark-job-ui** Open your browser and navigate to `http://localhost:30082`.


## Setup Dremio

You can now start experimenting with Dremio and Iceberg in your deployment.


## Cleanup

1. **Delete the k3d cluster when done:**

    ```sh
        k3d cluster delete iceberg-cluster
        ```

## Conclusion


