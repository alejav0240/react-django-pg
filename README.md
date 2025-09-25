Perfecto 🔥, lo que quieres es **crear los proyectos (Django, React y React Native) directamente desde Docker**, sin tener que instalar nada local en tu máquina.

Te paso el **workflow completo** con `docker-compose` para que levantes todo desde cero:

---

## 1️⃣ Estructura inicial del proyecto

Primero, crea una carpeta raíz para todo:

```bash
mkdir proyecto-app
cd proyecto-app
```

Dentro tendrás:

```
proyecto-app/
│── docker-compose.yml
│── backend/       # Django
│── frontend/      # React
└── react-native/  # React Native (Expo)
```

---

## 2️⃣ Docker Compose (creación de proyectos)

Aquí un `docker-compose.yml` que permite **inicializar** los proyectos y luego usarlos:

```yaml
version: "3.9"

volumes:
  pg_data:

services:
  db:
    image: postgres:15
    container_name: pgDB
    environment:
      POSTGRES_DB: prueba_db
      POSTGRES_USER: prueba_user
      POSTGRES_PASSWORD: prueba_pass
    volumes:
      - pg_data:/var/lib/postgres/datanpm
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    ports:
      - "5432:5432"
  api:
    build: ./api
    container_name: djangoApi
    volumes:
      - ./api:/app
    ports:
      - "8000:8000"
    environment:
      DB_NAME: appdb
      DB_USER: postgres
      DB_PASSWORD: postgres
      DB_HOST: postgres_db
      DB_PORT: 5432
    command: tail -f /dev/null
    depends_on:
      - db
  web:
    build: ./web
    container_name: reactWeb
    ports: 
      - "5173:5173"
    volumes:
      - ./web:/app
      - /app/node_modules
    command: tail -f /dev/null
    depends_on:
      - api
  mobile:
    build: ./mobile
    container_name: reactApp
    volumes:
      - ./mobile:/app
      - /app/node_modules
    command: tail -f /dev/null
    ports:
      - "19006:19006"   # Expo web
      - "8081:8081"     # Metro Bundler
```

👉 Este `compose` no ejecuta aún los servidores, solo prepara los contenedores para que puedas crear los proyectos desde dentro.

---

## 3️⃣ Crear proyectos dentro de contenedores

### 🔹 Django

Levanta el contenedor:

```bash
docker compose up -d django_backend
```

Crea el proyecto Django:

```bash
docker compose exec django_backend django-admin startproject backend .
```

Instala dependencias (crear `requirements.txt` mínimo con `Django psycopg2-binary djangorestframework`):

```bash
echo "Django==5.0
psycopg2-binary
djangorestframework" > backend/requirements.txt
```

Instalar en el contenedor:

```bash
docker compose exec django_backend pip install -r requirements.txt
```

---

### 🔹 React

Levanta el contenedor:

```bash
docker compose up -d react_frontend
```

Crea el proyecto React:

```bash
docker compose exec react_frontend npx create-react-app . 
```

---

### 🔹 React Native (con Expo)

Levanta el contenedor:

```bash
docker compose up -d react_native
```

Crea el proyecto con Expo:

```bash
docker compose exec react_native npx create-expo-app .
```

Esto instalará Expo dentro del contenedor.

---

## 4️⃣ Correr los proyectos

Una vez creados, ajustamos los `Dockerfile` y `docker-compose.yml` para correrlos normalmente:

* Django en `http://localhost:8000`
* React en `http://localhost:3000`
* React Native (Expo) en `http://localhost:19006`

---

👉 Pregunta clave:
¿Quieres que te prepare **un `docker-compose` final con todo ya listo para desarrollo (servidores corriendo)**, o prefieres mantener este flujo en **dos fases (crear primero, luego ajustar para correr)**?
