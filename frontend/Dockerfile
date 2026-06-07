# ---------- Etapa de build ----------
FROM node:20-slim AS builder
WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .

# CRA "hornea" las variables REACT_APP_* en tiempo de build.
# Usamos /api para que el frontend llame a su propio origen y Nginx
# reenvíe al backend (ver nginx.conf).
ARG REACT_APP_API_URL=/api
ENV REACT_APP_API_URL=$REACT_APP_API_URL

RUN npm run build          # genera ./build

# ---------- Etapa de ejecución (Nginx) ----------
FROM nginx:alpine AS runner
COPY --from=builder /app/build /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
