FROM node:lts-alpine

RUN apk update && apk upgrade --no-cache

WORKDIR /app

ENV NODE_OPTIONS="--dns-result-order=ipv4first"

COPY package*.json ./

RUN --mount=type=cache,target=/root/.npm \
    npm install --prefer-offline --no-audit --no-fund --maxsockets=3 --fetch-retries=10

COPY . .

EXPOSE 9000

CMD ["npm", "start"]
