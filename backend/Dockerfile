# ---------- Etapa de build ----------
FROM node:20-slim AS builder
WORKDIR /app

# Prisma necesita OpenSSL para generar/ejecutar el cliente
RUN apt-get update && apt-get install -y openssl && rm -rf /var/lib/apt/lists/*

COPY package*.json ./
RUN npm ci

COPY prisma ./prisma
RUN npx prisma generate

COPY . .
RUN npm run build          # OJO: asume que compila a ./dist

# ---------- Etapa de ejecución ----------
FROM node:20-slim AS runner
WORKDIR /app

RUN apt-get update && apt-get install -y openssl && rm -rf /var/lib/apt/lists/*

# Copiamos node_modules completos para tener disponible el CLI de Prisma
# (necesario para "prisma migrate deploy" al arrancar)
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/prisma ./prisma
COPY --from=builder /app/package*.json ./

EXPOSE 3010

# Aplica migraciones y arranca el servidor
CMD ["sh", "-c", "npx prisma migrate deploy && node dist/index.js"]
