FROM node:lts-alpine

WORKDIR /app

ENV NODE_OPTIONS="--dns-result-order=ipv4first"

COPY package*.json ./

RUN --mount=type=cache,target=/root/.npm \
    npm install --prefer-offline --no-audit --no-fund --maxsockets=3 --fetch-retries=10 --fetch-retry-mintimeout=20000 --fetch-retry-maxtimeout=120000

COPY . .

ENV REACT_APP_API_URL=http://localhost:9000

RUN npm run build

RUN --mount=type=cache,target=/root/.npm \
    npm install -g serve --prefer-offline --no-audit --no-fund

EXPOSE 3000

CMD ["serve", "-s", "build", "-l", "3000"]
