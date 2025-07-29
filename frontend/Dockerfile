# Base
FROM node:18-alpine

# Instalar dependencias del sistema necesarias
RUN apk add --no-cache python3 make g++

# Crear directorio de trabajo
WORKDIR /app

# Copiar package.json y package-lock.json primero
COPY package*.json ./

# Instalar todas las dependencias (incluyendo devDependencies)
RUN npm install --legacy-peer-deps

# Copiar archivos de configuración
COPY webpack.config.js ./
COPY .babelrc ./

# Copiar el resto de archivos
COPY src ./src
COPY public ./public

# Exponer puerto (WebPack dev server)
EXPOSE 3000

# Configurar el host para que webpack-dev-server sea accesible desde fuera del contenedor
ENV HOST=0.0.0.0

# Arrancar app
CMD ["npm", "run", "dev"]
