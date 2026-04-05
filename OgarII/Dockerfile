FROM node:18
WORKDIR /app
# We don't need the heavy C++ tools anymore!
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 8080
CMD ["node", "cli/index.js", "--port", "8080"]