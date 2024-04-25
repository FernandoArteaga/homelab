# Docker

![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)

Steps and instructions to deploy the services using Docker and Docker Compose.

## Services

- Authelia: Authentication and security
- LLDAP: Authentication
- Traefik: Reverse proxy
- Cloudflare Tunnel: Secure tunnel to the server
- Portainer: Docker management

## Requirements

- [Docker](https://docs.docker.com/get-docker/)
- [openssl](https://www.openssl.org/). Required to generate passwords.
- [sed](https://www.gnu.org/software/sed/). Required for replacing environment variables in the `.env` file.

## Setup

1. Clone the repository and navigate to the `docker` directory.
   ```shell
   git clone git@github.com:FernandoArteaga/homelab.git
   cd docker
   ```
2. Create a `.env` file in the `docker` directory. Make sure to fill in the required environment variables (following points).
    ```shell
    cp .env.default .env
    ```
3. Run `make init` to create some docker networks, and fill the `.env` file with secrets and passwords. 
4. Run `make create-oidc-secrets` to generate a secret password for any OIDC client. Follow the instructions.
5. Create a [Cloudflare API token](https://dash.cloudflare.com/profile/api-tokens) and fill in the `.env` file.
   Select the template `Edit zone DNS` > Select `doodo.shop` as Zone Resources > Set the IP address.
6. Create a [Cloudflare tunnel](https://one.dash.cloudflare.com/e49234a93783622dfaf9a26acb0cbe5f/networks/tunnels)
   and fill the token in the `.env` file.
