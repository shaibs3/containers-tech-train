# Multi-Stage Dockerfile

# Build stage
FROM node:18 as build

# Set working directory
WORKDIR /app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm install

# Copy the rest of the application code
COPY . .

# Production stage
FROM node:18

# Set working directory
WORKDIR /app

# Copy only necessary files from the build stage
COPY --from=build /app /app

# Expose the application port
EXPOSE 3000

# Start the application
CMD ["node", "index.js"]