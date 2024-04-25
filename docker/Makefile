include ../makefile.env
include .env

AUTHELIA_SECRET_PATH="./services/auth/authelia/secrets"
LLDAP_SECRET_PATH="./services/auth/lldap/secrets"

init:
	@docker network create ${DOCKER_NETWORK}
	@echo "Created - ${DOCKER_NETWORK} network"

	@mkdir -p $(AUTHELIA_SECRET_PATH) ${LLDAP_SECRET_PATH}
	@echo "Generated - directories for secrets"

	@openssl rand -out $(LLDAP_SECRET_PATH)/JWT_SECRET -hex 32
	@openssl rand -out $(LLDAP_SECRET_PATH)/KEY_SEED -hex 32
	@openssl rand -out $(LLDAP_SECRET_PATH)/LDAP_USER_PASS -hex 16

	@openssl genrsa -out $(AUTHELIA_SECRET_PATH)/OIDC_JWKS_RSA_KEY 4096
	@openssl rand -out $(AUTHELIA_SECRET_PATH)/OIDC_HMAC_SECRET -hex 32
	@openssl rand -out $(AUTHELIA_SECRET_PATH)/JWT_SECRET -hex 32
	@openssl rand -out $(AUTHELIA_SECRET_PATH)/SESSION_SECRET -hex 32
	@openssl rand -out $(AUTHELIA_SECRET_PATH)/STORAGE_ENCRYPTION_KEY -hex 32
	@cp $(LLDAP_SECRET_PATH)/LDAP_USER_PASS $(AUTHELIA_SECRET_PATH)/AUTHENTICATION_BACKEND_LDAP_PASSWORD
	@echo "Generated - random passwords"

create-oidc-secret:
	@docker run authelia/authelia:latest authelia crypto hash generate pbkdf2 --random --random.length 32 --random.charset alphanumeric
	@echo "\n$(U_BLUE)---Authelia OIDC Configuration---$(COLOR_OFF)"
	@echo "Save the $(GREEN)'Digest:'$(COLOR_OFF) value in the $(I_YELLOW)'auth/authelia/config/configuration.yml'$(COLOR_OFF) " \
			"file under $(I_YELLOW)'identity_providers.oidc.clients[*].client_secret'$(COLOR_OFF)."
	@echo "The $(GREEN)'Random Password:'$(COLOR_OFF) value should be set on the website where the OIDC client is registered."
	@echo "$(U_BLUE)---Authelia OIDC Configuration---$(COLOR_OFF)\n"

compose-up:
	@docker compose --env-file .env -f ./services/${COMPOSE_NAME}/compose.yml up -d

compose-down:
	@docker compose --env-file .env -f ./services/${COMPOSE_NAME}/compose.yml down


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


observability-update-images:
	@docker pull louislam/uptime-kuma:${UPTIME_KUMA_VERSION}

observability-up: observability-update-images
	@$(MAKE) compose-up COMPOSE_NAME=observability

observability-down:
	@$(MAKE) compose-down COMPOSE_NAME=observability
