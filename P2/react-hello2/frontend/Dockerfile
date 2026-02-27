# Multi-stage build pour le frontend React/Vite
# Context: racine de P2/react-hello2
FROM node:18-alpine AS builder

WORKDIR /app

# Argument de build pour l'URL du backend
ARG BACKEND_API_URL=http://localhost:3000

# Copier toute la racine (frontend/, backend/, db/, .env)
COPY . .

# Se placer dans le répertoire frontend
WORKDIR /app/frontend

# Installer toutes les dépendances (dev + prod) pour le build
RUN npm ci && npm cache clean --force

# Créer le .env.local avec le BACKEND_API_URL pour Vite
RUN echo "BACKEND_API_URL=${BACKEND_API_URL}" > .env.local

# Builder l'application avec Vite
RUN npm run build

# Stage 2: Runtime avec Nginx
FROM nginx:alpine

# Copier la configuration Nginx
RUN echo 'server { listen 80; location / { root /usr/share/nginx/html; try_files $uri $uri/ /index.html; } }' > /etc/nginx/conf.d/default.conf

# Copier le build du frontend depuis le builder
COPY --from=builder /app/frontend/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
