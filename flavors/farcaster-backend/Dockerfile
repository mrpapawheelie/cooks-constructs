# Base Dockerfile for Farcaster API
FROM node:20-alpine
WORKDIR /app
COPY . .
RUN npm install --production || yarn install --production
EXPOSE 80
CMD ["node", "server.js"] 