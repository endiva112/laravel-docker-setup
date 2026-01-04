# 🚀 Entorno de Desarrollo Laravel con Docker

Entorno Docker completo y listo para usar con Laravel. **Configuración de infraestructura separada del código** para mayor claridad y facilidad de aprendizaje.

---

## 📋 Requisitos previos

- [Docker Desktop](https://www.docker.com/products/docker-desktop) instalado
- Git instalado

---

## 🏗️ Estructura del proyecto

```
laravel-docker-setup/          ← Repositorio (configuración Docker)
├── docker/
│   ├── nginx/
│   │   └── default.conf       ← Configuración del servidor web
│   └── php/
│       └── Dockerfile         ← Imagen PHP con extensiones de Laravel
├── docker-compose.yml         ← Orquestación de contenedores
├── .gitignore
├── .env.example
├── README.md
└── src/                       ← Tu proyecto Laravel irá aquí
    ├── app/                   (se crea en el paso 3)
    ├── public/
    ├── routes/
    └── ...
```

**Filosofía de diseño:**
- 📁 `docker/` y `docker-compose.yml` → **Infraestructura** (lo que clonas del repo)
- 📁 `src/` → **Código de Laravel** (lo que tú creas después)
- Esta separación hace más fácil entender qué es qué mientras aprendes

---

## ⚡ Inicio rápido

### Paso 1️⃣: Clonar la configuración Docker

```bash
git clone https://github.com/TU-USUARIO/laravel-docker-setup.git mi-proyecto
cd mi-proyecto
```

**¿Qué acabas de hacer?**
- Descargaste la **configuración del entorno de desarrollo**
- Tienes los Dockerfiles, configuración de Nginx, MySQL, etc.
- **No tienes Laravel todavía**, eso viene en el paso 3

---

### Paso 2️⃣: Construir los contenedores

```bash
docker compose build
```

**¿Qué está pasando aquí?**
- Docker está construyendo una imagen personalizada de PHP
- Instala extensiones que Laravel necesita (MySQL, GD, ZIP, etc.)
- Descarga las imágenes de Nginx, MySQL, phpMyAdmin, etc.
- **Esto tarda 2-3 minutos la primera vez**

**Importante:** Este paso **NO instala Laravel**, solo prepara el entorno donde Laravel vivirá.

---

### Paso 3️⃣: Crear el proyecto Laravel

```bash
docker compose run --rm composer create-project laravel/laravel src
```

**¿Qué está pasando aquí?**
- Usas Composer (gestor de paquetes PHP) **dentro de un contenedor temporal**
- Composer descarga Laravel y todas sus dependencias
- Todo se instala en la carpeta `src/`
- `--rm` significa que el contenedor de Composer se elimina automáticamente al terminar
- **IMPORTANTE:** Este comando funciona SIN que los contenedores estén levantados

**Esto tarda 1-2 minutos** (descarga ~50MB de código).

---

### Paso 4️⃣: Levantar todos los servicios

**Ahora que Laravel ya está instalado en `src/`, podemos levantar los servicios:**

```bash
docker compose up -d
```

**¿Qué está pasando?**
- Nginx (servidor web) empieza a escuchar en el puerto 80
- PHP-FPM (intérprete de PHP) se levanta y encuentra Laravel en `/var/www/html`
- MySQL (base de datos) se inicia
- phpMyAdmin (interfaz web para MySQL) se levanta
- `-d` = "detached mode" (segundo plano)

**Verifica que todo esté corriendo:**
```bash
docker compose ps
```

Deberías ver 4 contenedores activos.

---

### Paso 5️⃣: Configurar Laravel

#### a) Generar la clave de aplicación

```bash
docker compose exec php php artisan key:generate
```

**¿Qué hace esto?**
- Laravel necesita una clave única para encriptar datos
- `artisan` es la herramienta de línea de comandos de Laravel
- Se guarda automáticamente en `src/.env`

#### b) Ejecutar las migraciones de base de datos

```bash
docker compose exec php php artisan migrate
```

**¿Qué hace esto?**
- Crea las tablas iniciales en la base de datos MySQL
- Laravel incluye algunas tablas por defecto (usuarios, sesiones, etc.)

---

### ✅ ¡Listo!

Abre tu navegador en:
- **Laravel**: http://localhost
- **phpMyAdmin**: http://localhost:8080
  - Servidor: `db`
  - Usuario: `laravel`
  - Contraseña: `secret`

---

## 🎓 Entendiendo el flujo

```
1. git clone          → Descargas la configuración Docker
                         (todavía NO tienes Laravel)

2. docker compose     → Docker construye las imágenes
   build                 (instala PHP, Nginx, MySQL en contenedores)
                         (todavía NO tienes Laravel)

3. docker compose     → Crea un contenedor TEMPORAL de Composer
   run composer          Descarga e instala Laravel en src/
                         El contenedor se elimina automáticamente
                         (AHORA SÍ tienes Laravel)

4. docker compose     → Levantas todos los servicios permanentes
   up -d                 Los contenedores ya encuentran Laravel en src/
                         (servidor web, PHP, base de datos)

5. artisan key:       → Configuración inicial de Laravel
   generate + migrate    (Laravel ya está, los servicios ya están)
```

**Clave:** Primero preparas el entorno (Docker), luego creas el proyecto (Laravel con `run`), **y después** levantas los servicios (`up`).

**Nota importante:** `docker compose run` crea contenedores temporales que se autodestruyen. No necesitas que los servicios estén levantados para usarlo.

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

# Ver todas las rutas
docker compose exec php php artisan route:list

# Limpiar caché
docker compose exec php php artisan cache:clear
docker compose exec php php artisan config:clear
docker compose exec php php artisan view:clear

# Ejecutar migraciones
docker compose exec php php artisan migrate

# Revertir última migración
docker compose exec php php artisan migrate:rollback

# Crear seeder (datos de prueba)
docker compose exec php php artisan make:seeder UserSeeder
docker compose exec php php artisan db:seed
```

### Composer (dependencias PHP)

```bash
# Instalar paquete
docker compose run --rm composer require guzzlehttp/guzzle

# Actualizar dependencias
docker compose run --rm composer update

# Ver paquetes instalados
docker compose run --rm composer show
```

### NPM / Node (frontend: Vite, Tailwind, etc.)

```bash
# Instalar dependencias (primera vez)
docker compose run --rm node npm install

# Modo desarrollo con hot reload
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

### Entrar al contenedor PHP (modo interactivo)

Si necesitas ejecutar varios comandos seguidos:

```bash
docker compose exec php bash

# Ahora estás "dentro" del contenedor
php artisan migrate
composer require vendor/package
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

**Causa**: El UID del contenedor no coincide con tu usuario (solo Linux/WSL2).

**Solución**:

1. Averigua tu UID:
```bash
# Linux/Mac/WSL2
id -u
```

2. Crea un archivo `.env` en la **raíz del proyecto** (no en `src/`):
```bash
DOCKER_UID=1000
DOCKER_GID=1000
```

Ajusta los valores con tu UID real si es diferente.

3. Reinicia los contenedores:
```bash
docker compose down
docker compose up -d
```

**Usuarios de Windows sin WSL2**: No necesitan hacer esto, funciona automáticamente.

---

### Error 502 Bad Gateway

**Causa**: PHP-FPM no está corriendo o Nginx no lo encuentra.

**Solución**:
```bash
docker compose restart php
docker compose restart web
docker compose logs -f php
```

---

### Error de conexión a la base de datos

**Verifica**:
1. Que el servicio `db` esté corriendo:
```bash
docker compose ps
```

2. Que el archivo `src/.env` tenga estos valores:
```env
DB_HOST=db               # ← IMPORTANTE: debe ser "db", no "localhost"
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=laravel
DB_PASSWORD=secret
```

3. **Espera 10-15 segundos** después de `docker compose up`. MySQL tarda en iniciar completamente.

Si persiste, reinicia:
```bash
docker compose restart db
docker compose exec php php artisan migrate
```

---

### La aplicación no refleja cambios en el código

**Causa**: Caché de Laravel activa.

**Solución**:
```bash
docker compose exec php php artisan cache:clear
docker compose exec php php artisan config:clear
docker compose exec php php artisan view:clear
docker compose exec php php artisan route:clear
```

---

### Quiero empezar desde cero

```bash
# Parar y eliminar TODO (contenedores + volúmenes + código)
docker compose down -v
rm -rf src/  # Linux/Mac
rmdir /S /Q src  # Windows

# Volver a crear proyecto
docker compose run --rm composer create-project laravel/laravel src
docker compose up -d
docker compose exec php php artisan key:generate
docker compose exec php php artisan migrate
```

---

## 🔄 Workflow de desarrollo típico

```bash
# Lunes por la mañana - Levantar el entorno
docker compose up -d

# Trabajar normalmente
docker compose exec php php artisan make:controller UserController
docker compose exec php php artisan migrate

# Instalar paquete si necesitas
docker compose run --rm composer require laravel/sanctum

# Trabajar con Vite/Tailwind (en otra terminal)
docker compose run --rm --service-ports node npm run dev

# Al terminar el día (opcional, puedes dejarlo corriendo)
docker compose down
```

---

## 🔒 Configuración de permisos (solo Linux/WSL2)

Si tienes problemas de permisos al crear archivos en Linux/WSL2, configura tu UID:

```bash
# Averigua tu UID
id -u

# Crea archivo .env en la raíz del proyecto
echo "DOCKER_UID=$(id -u)" > .env
echo "DOCKER_GID=$(id -g)" >> .env

# Reinicia
docker compose down
docker compose up -d
```

**Usuarios de Windows (sin WSL2)**: No necesitan hacer esto.

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
| **Separación código/infra** | Clara (`docker/` vs `src/`) | Todo mezclado |

**Recomendación**: Usa este proyecto si quieres aprender Docker y tener control total. Usa Sail si solo quieres programar Laravel sin complicaciones.

---

## 📚 Recursos útiles

- [Documentación oficial de Laravel](https://laravel.com/docs)
- [Docker Compose reference](https://docs.docker.com/compose/)
- [PHP-FPM configuration](https://www.php.net/manual/es/install.fpm.php)
- [Nginx con Laravel](https://laravel.com/docs/deployment#nginx)
- [Artisan Console](https://laravel.com/docs/artisan)

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

Creado como recurso educativo para estudiantes que quieren aprender Laravel con Docker de forma clara y sin magia.

**Filosofía del proyecto:** Separar la infraestructura (Docker) del código (Laravel) para facilitar el aprendizaje y comprensión de ambas tecnologías.

Si te ha sido útil, ¡dale una ⭐ en GitHub!