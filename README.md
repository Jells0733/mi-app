# MI-APP - Sistema de Gestión de Empleados y Solicitudes

## Descripción Técnica

Sistema full-stack moderno con:
- **Backend**: Node.js + Express + PostgreSQL
- **Frontend**: React + Webpack + Babel
- **Base de Datos**: PostgreSQL
- **Autenticación**: JWT
- **Contenedores**: Docker + Docker Compose
- **Testing**: Jest (Unitarios e Integración)

## Prerrequisitos

- Docker y Docker Compose instalados
- Node.js 18+ (para desarrollo local)
- Git

## Ejecución con Docker (Recomendado)

### Opción 1: Ejecutar toda la aplicación
```bash
# Clonar el repositorio
git clone <tu-repositorio>
cd mi-app

# Ejecutar toda la aplicación
docker-compose up -d

# Ver logs en tiempo real
docker-compose logs -f

# Detener la aplicación
docker-compose down
```

### Opción 2: Ejecutar servicios individuales
```bash
# Solo frontend
docker-compose build frontend
docker-compose up frontend

# Solo backend
docker-compose build backend
docker-compose up backend

# Solo base de datos
docker-compose up db
```

### Opción 3: Usando scripts de construcción
```bash
# En Windows (PowerShell)
.\build-frontend.ps1

# En Linux/Mac
chmod +x build-frontend.sh
./build-frontend.sh
```

##  Desarrollo Local

### Backend
```bash
cd backend
npm install
npm run dev
```

### Frontend
```bash
cd frontend
npm install
npm run dev
```

### Base de Datos
```bash
# PostgreSQL debe estar corriendo en puerto 5432
# Usuario: postgres
# Password: Ntc0394**
# Base de datos: miapp
```

## URLs del Sistema

- **Frontend**: http://localhost:3000
- **Backend API**: http://localhost:4000
- **Base de Datos**: localhost:5432
- **PgAdmin**: http://localhost:5050
  - Email: jorge.llamas@local.dev
  - Password: Ntc0394**

##  Solución de Problemas

### Error: "webpack: not found"
Este error ocurre cuando las dependencias no se instalan correctamente en el contenedor.

**Solución:**
```bash
# Limpiar contenedores e imágenes
docker-compose down
docker rmi mi-app_frontend

# Reconstruir sin cache
docker-compose build --no-cache frontend

# Ejecutar
docker-compose up frontend
```

### Error de permisos en Windows
Si tienes problemas de permisos en Windows, ejecuta PowerShell como administrador.

### Puerto 3000 ocupado
Si el puerto 3000 está ocupado, puedes cambiar el puerto en `docker-compose.yml`:
```yaml
ports:
  - "3001:3000"  # Cambia 3001 por el puerto que prefieras
```

### Problemas de conectividad entre contenedores
```bash
# Verificar que todos los contenedores estén ejecutándose
docker ps

# Ver logs de un contenedor específico
docker logs mi-app-frontend-1
docker logs mi-app-backend-1
docker logs mi-app-db-1
```

## Estructura de Base de Datos

### Tabla: users
```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  username VARCHAR(50) UNIQUE NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  password TEXT NOT NULL,
  role VARCHAR(20) NOT NULL CHECK (role IN ('admin', 'empleado')),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Tabla: empleados
```sql
CREATE TABLE empleados (
  id SERIAL PRIMARY KEY,
  fecha_ingreso DATE,
  nombre VARCHAR(50) NOT NULL,
  salario NUMERIC,
  id_usuario INTEGER UNIQUE REFERENCES users(id)
);
```

### Tabla: solicitudes
```sql
CREATE TABLE solicitudes (
  id SERIAL PRIMARY KEY,
  codigo VARCHAR(50),
  descripcion VARCHAR(50),
  resumen VARCHAR(50),
  id_empleado INTEGER REFERENCES empleados(id) ON DELETE CASCADE
);
```

## Endpoints API

### Autenticación
- `POST /api/auth/register` - Registrar usuario
- `POST /api/auth/login` - Iniciar sesión

### Empleados
- `GET /api/empleados` - Obtener todos los empleados
- `POST /api/empleados` - Crear empleado
- `GET /api/empleados/:id` - Obtener empleado por ID
- `PUT /api/empleados/:id` - Actualizar empleado
- `DELETE /api/empleados/:id` - Eliminar empleado

### Solicitudes
- `GET /api/solicitudes` - Obtener todas las solicitudes
- `POST /api/solicitudes` - Crear solicitud
- `GET /api/solicitudes/:id` - Obtener solicitud por ID
- `PUT /api/solicitudes/:id` - Actualizar solicitud
- `DELETE /api/solicitudes/:id` - Eliminar solicitud

## Testing

### Backend Tests
```bash
cd backend
npm test                    # Ejecutar todos los tests
npm run test:unit          # Solo tests unitarios
npm run test:integration   # Solo tests de integración
npm run test:coverage      # Tests con cobertura
```

### Docker Tests
```bash
# Tests en contenedor
docker-compose run --rm test-runner

# Tests unitarios en contenedor
docker-compose run --rm test-runner npm run test:unit

# Tests de integración en contenedor
docker-compose run --rm test-runner npm run test:integration
```

## Variables de Entorno

### Backend (.env)
```env
NODE_ENV=development
DB_HOST=localhost
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=Ntc0394**
DB_NAME=miapp
JWT_SECRET=tu_jwt_secret_aqui
```

### Frontend (.env)
```env
REACT_APP_API_URL=http://localhost:4000/api
```

## Estructura del Proyecto

```
mi-app/
├── backend/                 # API Node.js + Express
│   ├── src/
│   │   ├── controllers/     # Controladores de la API
│   │   ├── models/         # Modelos de datos
│   │   ├── routes/         # Rutas de la API
│   │   ├── middlewares/    # Middlewares (auth, etc.)
│   │   └── config/         # Configuración de BD
│   ├── tests/              # Tests unitarios e integración
│   └── Dockerfile          # Configuración Docker backend
├── frontend/               # Aplicación React
│   ├── src/
│   │   ├── components/     # Componentes React
│   │   ├── pages/         # Páginas de la aplicación
│   │   ├── services/      # Servicios API
│   │   ├── context/       # Contexto de autenticación
│   │   └── styles/        # Estilos CSS
│   ├── webpack.config.js  # Configuración Webpack
│   ├── .babelrc          # Configuración Babel
│   └── Dockerfile        # Configuración Docker frontend
├── docker-compose.yml     # Orquestación de contenedores
└── README.md             # Este archivo
```

## Funcionalidades del Sistema

### Roles de Usuario
- **admin**: Acceso completo al sistema
- **empleado**: Acceso limitado a sus propias solicitudes

### Módulos Principales
1. **Autenticación**: Login/Registro con JWT
2. **Gestión de Empleados**: CRUD completo
3. **Gestión de Solicitudes**: CRUD con asignación a empleados
4. **Panel de Administración**: Vista completa para admins
5. **Panel de Empleado**: Vista limitada para empleados

### Características Técnicas
- Autenticación JWT
- Middleware de autorización
- Validación de datos
- Manejo de errores
- Tests unitarios e integración
- Contenedores Docker optimizados
- Base de datos PostgreSQL
- API RESTful
- Frontend React con routing
- Hot reload en desarrollo
- Configuración Webpack optimizada

##  Comandos Útiles

### Docker
```bash
# Ver logs de todos los servicios
docker-compose logs -f

# Ver logs de un servicio específico
docker-compose logs -f frontend
docker-compose logs -f backend

# Reconstruir un servicio específico
docker-compose build --no-cache frontend

# Ejecutar comandos dentro de un contenedor
docker-compose exec frontend sh
docker-compose exec backend sh

# Limpiar todo (contenedores, imágenes, volúmenes)
docker-compose down -v --rmi all
```

### Desarrollo
```bash
# Instalar dependencias en ambos proyectos
cd backend && npm install && cd ../frontend && npm install

# Ejecutar tests en ambos proyectos
cd backend && npm test && cd ../frontend && npm test

# Verificar puertos en uso
netstat -ano | findstr :3000
netstat -ano | findstr :4000
```

## Notas de Desarrollo

- El frontend usa Webpack 5 con configuración optimizada para desarrollo
- El backend incluye tests unitarios y de integración completos
- La base de datos se inicializa automáticamente con Docker
- Todos los servicios están configurados para hot reload en desarrollo
- El sistema incluye PgAdmin para gestión visual de la base de datos

##  Contribución

1. Fork el proyecto
2. Crea una rama para tu feature (`git checkout -b feature/AmazingFeature`)
3. Commit tus cambios (`git commit -m 'Add some AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abre un Pull Request

##  Licencia

Este proyecto está bajo la Licencia ISC.

