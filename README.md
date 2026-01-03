# 🚀 Entorno de Desarrollo Laravel con Docker

Entorno completo y listo para usar con Laravel + Docker. Simplemente clona, construye y empieza a programar.

---

## 📋 Requisitos previos

- [Docker Desktop](https://www.docker.com/products/docker-desktop) instalado
- Git instalado

---

## 🏗️ Estructura del proyecto

```
laravel-docker-setup/
├── docker/
│   ├── nginx/
│   │   └── default.conf        # Configuración de Nginx
│   └── php/
│       └── Dockerfile          # Imagen PHP personalizada
├── docker-compose.yml          # Orquestación de contenedores
├── .gitignore                  # Archivos que Git ignora
├── .env.example                # Plantilla de configuración
└── README.md                   # Este archivo
```

---

## ⚡ Inicio rápido (3 pasos)

### 1️⃣ Clonar el repositorio

Abre una terminal en la carpeta donde quieras crear el proyecto y reemplaza mi-proyecto por el nombre que prefieras (por ejemplo: `proyecto-hola-mundo`).

```bash
git clone https://github.com/endiva112/laravel-docker-setup.git mi-proyecto
cd mi-proyecto
```

### 2️⃣ Construir los contenedores

```bash
docker compose build
```

Esto puede tardar 2-3 minutos la primera vez (descarga e instala todo).

### 3️⃣ Crear un proyecto Laravel nuevo

```bash
docker compose run --rm composer create-project laravel/laravel .
```

**¡Ya está!** Ahora sigue con la [configuración inicial](#️-configuración-inicial).

---

## 🛠️ Configuración inicial

### 1. Configurar la base de datos

Laravel crea automáticamente un archivo `.env`. Verifica que tenga estos valores:

```env
DB_CONNECTION=mysql
DB_HOST=db                    # ← Nombre del servicio en docker-compose
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=laravel
DB_PASSWORD=secret
```

Si no existe el archivo `.env`, copia el `.env.example`:

```bash
# En Windows
copy .env.example .env

# En Linux/Mac
cp .env.example .env
```

### 2. Levantar todos los contenedores

```bash
docker compose up -d
```

La bandera `-d` ejecuta en segundo plano (detached mode).

### 3. Generar la clave de aplicación

```bash
docker compose exec php php artisan key:generate
```

### 4. Ejecutar las migraciones

```bash
docker compose exec php php artisan migrate
```

### 5. ¡A programar!

Abre tu navegador en http://localhost y deberías ver la pantalla de bienvenida de Laravel.

---

## 🌐 URLs de acceso

| Servicio | URL | Credenciales |
|----------|-----|--------------|
| **Laravel** | http://localhost | - |
| **phpMyAdmin** | http://localhost:8080 | Usuario: `laravel`<br>Contraseña: `secret`<br>Servidor: `db` |
| **Vite (dev)** | http://localhost:5173 | (cuando ejecutes `npm run dev`) |

---

## 📝 Comandos del día a día

### Laravel Artisan

```bash
# Crear controlador
docker compose exec php php artisan make:controller HomeController

# Crear modelo con migración
docker compose exec php php artisan make:model Post -m

# Limpiar caché
docker compose exec php php artisan cache:clear

# Ver todas las rutas
docker compose exec php php artisan route:list

# Ejecutar migraciones
docker compose exec php php artisan migrate

# Revertir última migración
docker compose exec php php artisan migrate:rollback
```

### Composer (dependencias PHP)

```bash
# Instalar paquete
docker compose run --rm composer require vendor/package

# Actualizar dependencias
docker compose run --rm composer update

# Ver paquetes instalados
docker compose run --rm composer show
```

### NPM / Node (frontend)

```bash
# Instalar dependencias
docker compose run --rm node npm install

# Modo desarrollo (Vite hot reload)
docker compose run --rm --service-ports node npm run dev

# Compilar para producción
docker compose run --rm node npm run build
```

### Gestión de contenedores

```bash
# Ver estado de contenedores
docker compose ps

# Ver logs en tiempo real
docker compose logs -f

# Logs de un servicio específico
docker compose logs -f php
docker compose logs -f web

# Reiniciar un servicio
docker compose restart php

# Parar todos los contenedores
docker compose down

# Parar y eliminar volúmenes (⚠️ BORRA LA BASE DE DATOS)
docker compose down -v
```

---

## 💡 Tips y trucos

### Entrar al contenedor PHP

Si necesitas ejecutar varios comandos seguidos:

```bash
docker compose exec php bash

# Ahora estás dentro del contenedor
php artisan migrate
composer require guzzlehttp/guzzle
php artisan make:controller ApiController

# Salir
exit
```

### Crear alias para comandos más cortos

**En Linux/Mac** (archivo `~/.bashrc` o `~/.zshrc`):
```bash
alias artisan='docker compose exec php php artisan'
alias composer='docker compose run --rm composer'
alias npm='docker compose run --rm node npm'
```

**En Windows PowerShell** (archivo `$PROFILE`):
```powershell
function artisan { docker compose exec php php artisan $args }
function composer { docker compose run --rm composer $args }
function npm { docker compose run --rm node npm $args }
```

Después de configurar los alias:
```bash
# En vez de
docker compose exec php php artisan migrate

# Simplemente
artisan migrate
```

---

## 🐛 Solución de problemas

### Error: "Permission denied" al crear archivos

**Causa**: El UID del contenedor no coincide con tu usuario.

**Solución**:

1. Averigua tu UID:
```bash
# Linux/Mac
id -u

# Windows WSL
id -u
```

2. Edita `docker-compose.yml` y cambia:
```yaml
php:
  user: "TU_UID:TU_UID"  # Ejemplo: "1000:1000"
```

3. Reconstruye:
```bash
docker compose down
docker compose build
docker compose up -d
```

### Error 502 Bad Gateway

**Causa**: PHP-FPM no está corriendo o Nginx no lo encuentra.

**Solución**:
```bash
docker compose restart php
docker compose restart web
docker compose logs -f php
```

### Error de conexión a la base de datos

**Verifica**:
1. Que el servicio `db` esté corriendo: `docker compose ps`
2. Que el archivo `.env` tenga `DB_HOST=db` (no `localhost`)
3. Espera 10-15 segundos después de `docker compose up` (MySQL tarda en iniciar)

### La aplicación no refleja cambios en el código

**Causa**: Caché de Laravel activa.

**Solución**:
```bash
docker compose exec php php artisan cache:clear
docker compose exec php php artisan config:clear
docker compose exec php php artisan view:clear
```

### Quiero empezar desde cero

```bash
# Eliminar todo (contenedores + volúmenes + código)
docker compose down -v
rm -rf vendor node_modules public storage bootstrap/cache

# Volver a crear proyecto
docker compose run --rm composer create-project laravel/laravel .
```

---

## 🔄 Workflow de desarrollo típico

```bash
# Lunes por la mañana
docker compose up -d

# Trabajar normalmente
docker compose exec php php artisan make:controller UserController
docker compose exec php php artisan migrate

# Instalar paquete si necesitas
docker compose run --rm composer require laravel/sanctum

# Trabajar con Vite/Tailwind
docker compose run --rm --service-ports node npm run dev

# Al terminar el día (opcional)
docker compose down
```

---

## 🎓 Comparación: Docker vs. Laravel Sail

| Aspecto | Este proyecto (Docker puro) | Laravel Sail |
|---------|------------------------------|--------------|
| **Complejidad inicial** | Media (entiendes lo que pasa) | Baja (todo automático) |
| **Control** | Total | Limitado |
| **Aprendizaje** | Aprendes Docker de verdad | Aprendes menos |
| **Comandos** | `docker compose exec php php artisan` | `sail artisan` |
| **Personalización** | Fácil (editas Dockerfile) | Más complicado |
| **Portabilidad** | Funciona en cualquier proyecto | Solo Laravel |

**Recomendación**: Usa este proyecto si quieres aprender Docker y tener control total. Usa Sail si solo quieres programar Laravel sin complicaciones.

---

## 📚 Recursos útiles

- [Documentación oficial de Laravel](https://laravel.com/docs)
- [Docker Compose reference](https://docs.docker.com/compose/)
- [PHP-FPM configuration](https://www.php.net/manual/es/install.fpm.php)
- [Nginx con Laravel](https://laravel.com/docs/deployment#nginx)

---

## 🤝 Contribuir

¿Encontraste un bug o tienes una mejora? ¡Los Pull Requests son bienvenidos!

1. Haz un fork del proyecto
2. Crea una rama con tu feature (`git checkout -b feature/mejora-increible`)
3. Commit tus cambios (`git commit -m 'Agrega función increíble'`)
4. Push a la rama (`git push origin feature/mejora-increible`)
5. Abre un Pull Request

---

## 📄 Licencia

Este proyecto es de código abierto bajo licencia MIT. Úsalo libremente para aprender y construir proyectos increíbles.

---

## 🙏 Agradecimientos

Creado como recurso educativo para estudiantes que quieren aprender Laravel con Docker sin complicaciones.

Si te ha sido útil, ¡dale una ⭐ en GitHub!