FROM node:22.16.0-alpine3.22 AS builder

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

ENV NODE_ENV=production
ENV VUE_APP_API_URL=/api

RUN npm run build


FROM nginx:1.28.0-alpine

COPY --chown=frontend-user:frontend-group nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=builder --chown=frontend-user:frontend-group /app/dist /usr/share/nginx/html

RUN apk update && apk upgrade &&\
    apk add --no-cache curl &&\
    addgroup -g 1001 -S frontend-group && adduser -u 1001 -S frontend-user -G frontend-group &&\
    chown -R frontend-user:frontend-group /var/cache/nginx /var/log/nginx /etc/nginx /tmp &&\
    touch /var/run/nginx.pid &&\
    chown frontend-user:frontend-group /var/run/nginx.pid

USER frontend-user

EXPOSE 80

HEALTHCHECK \
  --interval=30s \
  --timeout=5s \
  --start-period=10s \
  --retries=5 \
  CMD curl -f http://localhost:80 || exit 1

CMD ["nginx", "-g", "daemon off;"]
