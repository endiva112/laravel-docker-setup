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

**Elige tu sistema operativo:**

- [⚡ WSL2 en Windows (RECOMENDADO)](#-instalación-en-wsl2-recomendado) - 10-50x más rápido
- [🪟 Windows / 🐧 Linux](#-instalación-en-windows-linux) - Instalación estándar

---

## ⚡ Instalación en WSL2 (RECOMENDADO)

**¿Por qué WSL2?**
- ⚡ **10-50x más rápido** que Docker en Windows nativo
- 🐧 Comportamiento idéntico a producción (Linux)
- 💻 Experiencia de desarrollo fluida y profesional

### Requisitos previos

1. **Windows 10/11** con WSL2 habilitado
2. **Docker Desktop** con integración WSL2 activada
3. **Ubuntu** desde Microsoft Store

#### 🔧 Instalar Ubuntu en WSL2

Si aún no tienes Ubuntu instalado:

```powershell
# Desde PowerShell como administrador
wsl --install -d Ubuntu

# Sigue las instrucciones para crear usuario y contraseña
# Luego cierra y vuelve a abrir "Ubuntu" desde el menú de inicio
```

---

### 🚀 Pasos de instalación (WSL2)

**⚠️ IMPORTANTE:** Ejecuta todos los comandos desde el **terminal de Ubuntu**, NO desde PowerShell/CMD.

#### Paso 1️⃣: Navega a tu carpeta de proyectos EN WSL2

```bash
# Abre "Ubuntu" desde el menú de inicio de Windows

# Ve a tu home (filesystem NATIVO de WSL2, NO /mnt/c/)
cd ~

# Crea una carpeta para tus proyectos
mkdir -p proyectos
cd proyectos
```

**⚠️ CRÍTICO:** Asegúrate de estar en `/home/tu-usuario/...` y NO en `/mnt/c/...`

```bash
# Verifica tu ubicación
pwd
# Debe mostrar: /home/tu-usuario/proyectos (✅ CORRECTO)
# Si muestra: /mnt/c/Users/... (❌ INCORRECTO, rendimiento lento)
```

---

#### Paso 2️⃣: Clonar y preparar el proyecto

```bash
# Clona el repositorio
git clone https://github.com/endiva112/laravel-docker-setup.git mi-proyecto
cd mi-proyecto

# IMPORTANTE: Crea la carpeta src/ ANTES del build
mkdir src
```

**¿Por qué crear `src/` antes?** Para que Docker no la cree como root y cause problemas de permisos.

---

#### Paso 3️⃣: Construir los contenedores

```bash
docker compose build
```

**¿Qué está pasando?**
- Docker construye una imagen personalizada de PHP
- Instala extensiones que Laravel necesita (MySQL, GD, ZIP, etc.)
- Descarga las imágenes de Nginx, MySQL, phpMyAdmin, etc.
- **Esto tarda 2-3 minutos la primera vez**

---

#### Paso 4️⃣: Crear el proyecto Laravel

```bash
docker compose run --rm composer create-project laravel/laravel .
```

**¿Qué hace esto?**
- Composer descarga Laravel y todas sus dependencias
- Todo se instala en la carpeta `src/`
- El contenedor de Composer se elimina automáticamente al terminar
- **Esto tarda 1-2 minutos** (descarga ~50MB de código)

---

#### Paso 5️⃣: Levantar todos los servicios

```bash
docker compose up -d
```

**¿Qué hace esto?**
- Nginx (servidor web) empieza a escuchar en el puerto 80
- PHP-FPM (intérprete de PHP) se levanta
- MySQL (base de datos) se inicia
- phpMyAdmin (interfaz web para MySQL) se levanta
- `-d` = "detached mode" (segundo plano)

**Verifica que todo esté corriendo:**
```bash
docker compose ps
```

Deberías ver 4 contenedores activos.

---

#### Paso 6️⃣: Configurar Laravel

```bash
# Generar la clave de aplicación
docker compose exec php php artisan key:generate

# Ejecutar las migraciones de base de datos
docker compose exec php php artisan migrate
```

---

### ✅ ¡Listo!

Abre tu navegador en:
- **Laravel**: http://localhost
- **phpMyAdmin**: http://localhost:8080
  - Servidor: `db`
  - Usuario: `laravel`
  - Contraseña: `secret`

---

### 💡 Editar código en WSL2

**Opción 1: VSCode con WSL (recomendado)**

```bash
# Instala la extensión "WSL" en VSCode
# Luego, desde el terminal de Ubuntu:
code .
```

VSCode se abrirá conectado directamente a WSL2.

**Opción 2: Acceder desde Windows Explorer**

En Windows Explorer, escribe en la barra de direcciones:
```
\\wsl$\Ubuntu\home\tu-usuario\proyectos\mi-proyecto
```

Puedes crear un acceso directo para facilitar el acceso.

---

### 📊 Comparativa de rendimiento (WSL2 vs Windows)

| Operación | Windows nativo | WSL2 |
|-----------|----------------|------|
| `composer install` | ~180 segundos | ~8 segundos |
| Carga de página | 500-2000ms | 50-150ms |
| `php artisan migrate` | 5-15 segundos | 1-3 segundos |

**La diferencia es abismal** 🚀

---

## 🪟 Instalación en Windows y Linux

**Mejor para:** Instalación rápida sin configuración adicional (Windows) o uso nativo (Linux).

### Windows: Requisitos

- [Docker Desktop para Windows](https://www.docker.com/products/docker-desktop)
- Git for Windows

**Nota sobre rendimiento en Windows:** El rendimiento será más lento que WSL2 debido a NTFS. Si buscas velocidad, usa WSL2.

### Linux: Requisitos

```bash
# Instala Docker y Docker Compose
sudo apt update
sudo apt install docker.io docker-compose-v2

# Añade tu usuario al grupo docker (para no usar sudo)
sudo usermod -aG docker $USER
newgrp docker
```

---

### 🚀 Pasos de instalación (Windows / Linux)

#### Paso 1️⃣: Clonar y preparar el proyecto

```bash
# Clona el repositorio (donde quieras)
git clone https://github.com/endiva112/laravel-docker-setup.git mi-proyecto
cd mi-proyecto

# IMPORTANTE (solo Linux): Crea src/ ANTES del build
mkdir src
```

**Usuarios de Windows:** No necesitan crear `src/` manualmente.

---

#### Paso 2️⃣: Construir los contenedores

```bash
docker compose build
```

---

#### Paso 3️⃣: Crear el proyecto Laravel

```bash
docker compose run --rm composer create-project laravel/laravel .
```

---

#### Paso 4️⃣: Levantar todos los servicios

```bash
docker compose up -d
```

---

#### Paso 5️⃣: Configurar Laravel

```bash
docker compose exec php php artisan key:generate
docker compose exec php php artisan migrate
```

---

### ✅ ¡Listo!

Abre tu navegador en:
- **Laravel**: http://localhost
- **phpMyAdmin**: http://localhost:8080

---

## 🎓 Entendiendo el flujo

```
1. git clone          → Descargas la configuración Docker
                         (todavía NO tienes Laravel)

2. mkdir src          → Creas src/ con TUS permisos (WSL2/Linux)
                         (Windows: Docker lo hace automáticamente)

3. docker compose     → Docker construye las imágenes
   build                 (instala PHP, Nginx, MySQL en contenedores)
                         (todavía NO tienes Laravel)

4. docker compose     → Crea un contenedor TEMPORAL de Composer
   run composer          Descarga e instala Laravel en src/
                         El contenedor se elimina automáticamente
                         (AHORA SÍ tienes Laravel en src/)

5. docker compose     → Levantas todos los servicios permanentes
   up -d                 Los contenedores ya encuentran Laravel en src/
                         (servidor web, PHP, base de datos)

6. artisan key:       → Configuración inicial de Laravel
   generate + migrate    (Laravel ya está, los servicios ya están)
```

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

**En Linux/Mac/WSL2** (archivo `~/.bashrc` o `~/.zshrc`):
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

### Error: "Permission denied" o "does not exist and could not be created" (WSL2/Linux)

**Síntomas:**
```
Failed to download laravel/laravel from dist: /var/www/html/./vendor/composer does not exist and could not be created
Now trying to download from source
```

**Causa:** La carpeta `src/` fue creada por Docker como root y tu usuario no puede escribir en ella.

**Solución:**

```bash
# 1. Borra src/ y empieza de nuevo
rm -rf src/

# 2. Crea src/ con tus permisos ANTES de cualquier comando Docker
mkdir src

# 3. Ahora sí, instala Laravel
docker compose run --rm composer create-project laravel/laravel .
```

**Si el problema persiste** (tu UID no es 1000):

```bash
# 1. Averigua tu UID
id -u

# 2. Si NO es 1000, crea un archivo .env en la raíz del proyecto:
echo "DOCKER_UID=$(id -u)" > .env
echo "DOCKER_GID=$(id -g)" >> .env

# 3. Reconstruye la imagen PHP
docker compose build --no-cache php

# 4. Vuelve a intentar
docker compose run --rm composer create-project laravel/laravel .
```

---

### Rendimiento lento en WSL2

**Verifica tu ubicación:**

```bash
pwd
```

- ❌ Si ves `/mnt/c/...` → Estás en el filesystem de Windows montado (LENTO)
- ✅ Si ves `/home/...` → Estás en el filesystem nativo de WSL2 (RÁPIDO)

**Solución:** Mueve el proyecto al filesystem de WSL2:

```bash
# Desde Ubuntu
cd ~
mkdir proyectos
cd proyectos

# Clona de nuevo aquí (NO copies desde /mnt/c/)
git clone https://github.com/endiva112/laravel-docker-setup.git mi-proyecto
cd mi-proyecto
mkdir src
# ... continúa con la instalación normal
```

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
rm -rf src/  # Linux/Mac/WSL2
rmdir /S /Q src  # Windows (CMD)

# Volver a crear proyecto
mkdir src  # Solo WSL2/Linux
docker compose run --rm composer create-project laravel/laravel .
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