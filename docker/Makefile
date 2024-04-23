include ../makefile.env
include .env


init:
	AUTHELIA_SECRET_PATH="./auth/authelia/secrets"
	LLDAP_SECRET_PATH="./auth/authelia/secrets"
	@docker network create ${DOCKER_NETWORK}
	@echo "Created - ${DOCKER_NETWORK} network"
	@sed -e "s/POSTGRES_PWD=.*/POSTGRES_PWD=$$(openssl rand -hex 16)/" \
		 .env > .env_temp && mv .env_temp .env

	@openssl rand -out ${LLDAP_SECRET_PATH}/JWT_SECRET -hex 32
	@openssl rand -out ${LLDAP_SECRET_PATH}/KEY_SEED -hex 32
	@openssl rand -out ${LLDAP_SECRET_PATH}/LDAP_USER_PASS -hex 16

	@openssl genrsa -out ${AUTHELIA_SECRET_PATH}/OIDC_JWKS_RSA_KEY 4096
	@openssl rand -out ${AUTHELIA_SECRET_PATH}/OIDC_HMAC_SECRET -hex 32
	@openssl rand -out ${AUTHELIA_SECRET_PATH}/JWT_SECRET -hex 32
	@openssl rand -out ${AUTHELIA_SECRET_PATH}/SESSION_SECRET -hex 32
	@openssl rand -out ${AUTHELIA_SECRET_PATH}/STORAGE_ENCRYPTION_KEY -hex 32
	@cp ${LLDAP_SECRET_PATH}/LDAP_USER_PASS ${AUTHELIA_SECRET_PATH}/AUTHENTICATION_BACKEND_LDAP_PASSWORD
	@echo "Generated - random passwords"

create-oidc-secret:
	@docker run authelia/authelia:latest authelia crypto hash generate pbkdf2 --random --random.length 32 --random.charset alphanumeric
	@echo "\n---Authelia OIDC Configuration---"
	@echo "Save the $(GREEN)'Digest:'$(COLOR_OFF) value in the 'auth/authelia/config/configuration.yml' file under 'identity_providers.oidc.clients[*].client_secret'."
	@echo "The 'Random Password:' value should be set on the website where the OIDC client is registered."
	@echo "---Authelia OIDC Configuration---\n"

create-db:
	@docker exec -it doodo-shop-postgres-1 createdb --username=${POSTGRES_USER} --owner=${POSTGRES_USER} ${LLDAP_DB_NAME} || true
	@docker exec -it doodo-shop-postgres-1 createdb --username=${POSTGRES_USER} --owner=${POSTGRES_USER} ${AUTHELIA_DB_NAME} || true

compose-up:
	@docker compose --env-file .env -f ./${COMPOSE_NAME}/compose.yaml up -d

compose-down:
	@docker compose --env-file .env -f ./${COMPOSE_NAME}/compose.yaml down


storage-update-images:
	@docker pull postgres:${POSTGRES_VERSION}

storage-up: storage-update-images
	@$(MAKE) compose-up COMPOSE_NAME=storage

storage-down:
	@$(MAKE) compose-down COMPOSE_NAME=storage


networking-update-images:
	@docker pull traefik:${TRAEFIK_VERSION}
	@docker pull cloudflare/cloudflared:${CF_VERSION}

networking-up: networking-update-images
	@$(MAKE) compose-up COMPOSE_NAME=networking

networking-down:
	@$(MAKE) compose-down COMPOSE_NAME=networking


auth-update-images:
	@docker pull lldap/lldap:${LLDAP_VERSION}
	@docker pull authelia/authelia:${AUTHELIA_VERSION}

auth-up: auth-update-images
	@$(MAKE) compose-up COMPOSE_NAME=auth

auth-down:
	@$(MAKE) compose-down COMPOSE_NAME=auth


admin-update-images:
	@docker pull adminer:latest

admin-up: admin-update-images
	@$(MAKE) compose-up COMPOSE_NAME=admin

admin-down:
	@$(MAKE) compose-down COMPOSE_NAME=admin
