# 42 Inception: Mandatory Tasks Checklist

## General Setup

- [ ] Work inside a **Virtual Machine**.
- [ ] Place **all configuration files** inside a `srcs/` directory at the root of your repository.
- [ ] Provide a **Makefile** at the repository root to build and run the project using `docker-compose`.
- [ ] Read Docker documentation and other resources if a concept is unclear.

## Docker & Images

- [ ] Use **Docker Compose** to orchestrate your services.
- [ ] **One Dockerfile per service** – build the images yourself instead of pulling pre-made ones (only base Alpine/Debian images allowed).
- [ ] Name each Docker image **exactly the same** as its service.
- [ ] Base every image on the **penultimate stable version** of Alpine or Debian.
- [ ] The Makefile must call docker compose (with a space), which builds the images from your Dockerfiles.
- [ ] Do not use the `latest` tag when specifying images.

## Service Containers
- [ ] Each service must run in its **own dedicated container** (no combining services in one).
- [ ] **NGINX** container: must run only NGINX with TLS **v1.2 or v1.3** enabled.
- [ ] **WordPress** container: install & configure WordPress with **php-fpm** only (no NGINX inside).
- [ ] **MariaDB** container: run MariaDB only, without NGINX.
- [ ] Ensure **two WordPress database users**, one being the administrator. The admin username **cannot contain** `admin`, `Admin`, `administrator`, etc.
- [ ] Configure containers to **restart automatically** after a crash.
- [ ] Avoid hacky patches like `tail -f` or infinite loops. Instead, follow **PID 1 best practices** for long-running services in Docker.


## Volumes

- [ ] Create a volume for the **WordPress database**.
- [ ] Create another volume for the **WordPress site files**.
- [ ] These volumes should map to `/home/<login>/data` on the host (replace `<login>` with your 42 login).

## Networking

- [ ] Define a custom `docker-network` in `docker-compose.yml` to connect your containers (the `network` line must be present).
- [ ] **Do NOT** use `network: host`, `--link`, or `links:`.
- [ ] NGINX must be the **only entry point** to the infrastructure, listening on **port 443**.

## Security Rules

- [ ] **Passwords must not appear** in Dockerfiles.
- [ ] Store sensitive data using **environment variables**—ideally in a `.env` file—and consider Docker secrets for confidential info.
- [ ] Configure NGINX so HTTPS works with TLS v1.2/1.3 only.

## Domain Setup

- [ ] Point a domain name to your **local IP**. It must be `<login>.42.fr` (e.g., `wil.42.fr` if your login is `wil`).

## Forbidden Practices

- [ ] **No pulling pre-made images** – build everything yourself (only Alpine/Debian bases allowed).
- [ ] **No `network: host`, `--link`, or `links:`** directives in `docker-compose.yml`.
- [ ] **No infinite loops** like `tail -f`, `bash`, `sleep infinity`, or `while true` in entrypoints.
- [ ] **Admin usernames** in WordPress must not contain `admin`, `Admin`, or `administrator`.
- [ ] **The `latest` tag is forbidden** when specifying images.
- [ ] **Passwords must not appear** in Dockerfiles.
- [ ] **Never commit credentials or API keys** – keep them locally and ignore them in Git.

## Final Folder Structure (example)

Your project should look similar to this (adapt paths to your login):

```text
.
├── Makefile
├── secrets/
│   ├── credentials.txt
│   ├── db_password.txt
│   └── db_root_password.txt
└── srcs/
    ├── .env
    ├── docker-compose.yml
    └── requirements/
        ├── bonus/
        ├── mariadb/
        │   ├── Dockerfile
        │   ├── .dockerignore
        │   └── conf/
        ├── nginx/
        │   ├── Dockerfile
        │   ├── .dockerignore
        │   └── conf/
        ├── tools/
        └── wordpress/
            ├── Dockerfile
            ├── .dockerignore
            └── conf/
```
