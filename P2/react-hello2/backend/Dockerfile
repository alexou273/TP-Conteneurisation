# Multi-stage build pour le backend NestJS/TypeScript
# Context: racine de P2/react-hello2
FROM node:18-alpine AS builder

WORKDIR /app

# Copier toute la racine du context
COPY . .

# Installer les dépendances du backend (pour le build TypeScript)
WORKDIR /app/backend
RUN npm ci && npm cache clean --force

# Builder l'application TypeScript
RUN npm run build

# Stage 2: Runtime
FROM node:18-alpine

WORKDIR /app/backend

# Créer un utilisateur non-root
RUN addgroup -g 1001 -S nodejs && adduser -S nodejs -u 1001

# Copier node_modules depuis le builder
COPY --from=builder --chown=nodejs:nodejs /app/backend/node_modules ./node_modules

# Copier le build du backend
COPY --from=builder --chown=nodejs:nodejs /app/backend/dist ./dist

# Copier le .env depuis la racine
COPY --chown=nodejs:nodejs .env ../

# Changer l'utilisateur pour non-root  
USER nodejs

EXPOSE 3000

CMD ["node", "dist/server.js"]
