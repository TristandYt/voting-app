FROM node:18-alpine AS dependency_builder
WORKDIR /usr/src/app
COPY voting-app/result/package.json voting-app/result/package-lock.json ./
RUN npm install --production

FROM node:18-alpine
WORKDIR /usr/src/app
COPY --from=dependency_builder /usr/src/app/node_modules ./node_modules
COPY voting-app/result/ .

EXPOSE 8888
CMD ["npm", "start"]