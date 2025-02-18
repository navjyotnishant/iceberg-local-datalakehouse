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
    ```

5. **Access the UIs:**

    - **Nessie UI:** Open your browser and navigate to `http://localhost:30020`.
    - **MinIO UI:** Open your browser and navigate to `http://localhost:30001`.
    - **Dremio UI:** Open your browser and navigate to `http://localhost:30047`.


## Setup Minio

1. **Access the MinIO UI:**
    - Open your browser and navigate to `http://localhost:30001`.
    - Log in using the following credentials:
        - **Username:** `admin`
        - **Password:** `password`

2. **Create a bucket:**
    - Go to the Buckets section.
    - Create a new bucket named `lakehouse`.

## Setup Dremio

You can now start experimenting with Dremio and Iceberg in your deployment. Start with creating your account in Dremio welcome page.

### Add a Nessie Source

1. **Click the “Add Source” button in the bottom left corner of the Dremio interface.**
2. **Select Nessie from the list of available sources.**

### Configure the Nessie Source

There are two sections to fill out: General and Storage Settings.

#### General Settings (Connecting to the Nessie Server):

- **Name:** Enter `nessie` as the source name.
- **Endpoint URL:** Use the following URL for the Nessie API endpoint:
    ```
    http://nessie:19120/api/v2
    ```
- **Authentication:** Choose `None`.

#### Storage Settings:

- **Access Key:** Enter `admin` (Minio username).
- **Secret Key:** Enter `password` (Minio password).
- **Root Path:** Enter `lakehouse` (this is the bucket where the Iceberg tables are stored).

#### Connection Properties:

- Set `fs.s3a.path.style.access` to `true`.
- Set `fs.s3a.endpoint` to `minio:9000`.
- Set `dremio.s3.compat` to `true`.
- **Encrypt Connection:** Uncheck this option (since Nessie is running locally on HTTP).

3. **Save the Source:** After completing all the settings, click `Save`. The Nessie source will now be connected to Dremio, allowing you to browse the tables stored in the Nessie catalog.

4. **Run Test DDL and DML:**

    Refer to the `dremio-poc.sql` file for sample DDL and DML statements to test your setup.


## Cleanup

1. **Delete the k3d cluster when done:**

    ```sh
    k3d cluster delete iceberg-cluster
    ```

## Conclusion


