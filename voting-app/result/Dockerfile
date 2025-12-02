# result/Dockerfile
FROM node:18-alpine AS build
WORKDIR /app
COPY result/package*.json ./
RUN npm ci --silent
COPY result/ ./
# If your result app has a build step, run it here (for SPAs)
# RUN npm run build

FROM node:18-alpine
WORKDIR /app
COPY --from=build /app ./

# install netcat (for wait-for)
RUN apk add --no-cache bash netcat-openbsd

COPY scripts/wait-for.sh /usr/local/bin/wait-for.sh
RUN chmod +x /usr/local/bin/wait-for.sh

ENV REDIS_HOST=redis \
    REDIS_PORT=6379 \
    POSTGRES_HOST=db \
    POSTGRES_PORT=5432 \
    HOST=0.0.0.0 \
    PORT=3000

EXPOSE 3000

CMD ["sh", "-c", "wait-for.sh ${REDIS_HOST}:${REDIS_PORT} -- wait-for.sh ${POSTGRES_HOST}:${POSTGRES_PORT} -- node server.js"]