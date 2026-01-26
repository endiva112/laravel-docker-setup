# Laravel Docker Setup 🚀

> **Entorno de desarrollo Laravel con Docker** - Aprende Laravel sin complicaciones de instalación

¿Quieres aprender Laravel pero te abruma instalar PHP, MySQL, Composer y demás herramientas? Este proyecto te permite empezar en minutos usando Docker, sin ensuciar tu sistema con instalaciones complejas.

## ✨ ¿Qué obtienes con esto?

- **Laravel listo para usar** - La última versión, configurada y funcionando
- **MySQL + phpMyAdmin** - Base de datos visual sin complicaciones  
- **Nginx + PHP-FPM** - Servidor web profesional
- **Node.js + Vite** - Para el frontend (Tailwind, React, Vue, etc.)
- **Composer** - Gestor de paquetes PHP ya configurado

Todo empaquetado en contenedores Docker que funcionan igual en Windows, Mac o Linux.

---

## 📋 Requisitos previos

Antes de empezar necesitas:

1. **Docker Desktop** (Windows/Mac) o **Docker** (Linux)  
   👉 [Descargar Docker Desktop](https://www.docker.com/products/docker-desktop)

2. **⚠️⚠️⚠️ Si usas Windows:**
   - Virtualización habilitada en la BIOS (si ya usaste VirtualBox o VMware, ya la tienes)
   - ⚠️ **Ubuntu desde Microsoft Store** (recomendado para mejor rendimiento)
   - WSL2 instalado (Docker Desktop lo instala automáticamente)

3. **Visual Studio Code** Cualquier IDE es válido, pero es el que usará en este proyecto

Puedes comprobar si wsl está instalado ejecutando en un CMD
```bash
wsl --version
```

> **💡 Tip para Windows:** Docker funciona en Windows, pero es **50 veces más lento**. Ubuntu en WSL2 da velocidad nativa de Linux. Vale la pena los 5 minutos extra de configuración.

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
├── docs/
│   └── GettingStarted.md      ← Tutorial paso a paso
├── .gitignore
├── .env.example
├── README.md
└── src/                       ← Tu proyecto Laravel irá aquí
    ├── app/                   (se crea durante la instalación)
    ├── public/
    ├── routes/
    └── ...
```

**Filosofía de diseño:**
- 📁 `docker/` y `docker-compose.yml` → **Infraestructura** (lo que clonas del repo)
- 📁 `src/` → **Código de Laravel** (lo que tú creas después)
- Esta separación hace más fácil entender qué es qué mientras aprendes

---

## 🚀 Inicio rápido

### Instalación en 9 pasos

# **1. Clona este repositorio**

Es aquí donde determinas el nombre que le quieras dar a tu proyecto, el nombre por defecto es `mi-proyecto`
```bash
git clone https://github.com/endiva112/laravel-docker-setup.git mi-proyecto
```

Accedemos al proyecto que acabamos de crear
```bash
cd mi-proyecto
```

# **2. Crea la carpeta para Laravel**

```bash
mkdir src
```

# **3. Agrega tu usuario al grupo Docker**

```bash
sudo usermod -aG docker $USER
```

⚠️ Ahora debes cerrar la terminal para que se apliquen los cambios y volverla a abrir

# **4. Construye los contenedores**

```bash
docker compose build
```

# **5. Crea el proyecto Laravel**

```bash
docker compose run --rm composer create-project laravel/laravel .
```
Esto instala todo lo necesario y se crea nuestro proyecto Laravel
⚠️
# **6. Modificar el .env**

El proyecto Laravel que acabamos de instalar utiliza `sqlite` por defecto, esto debemos cambiarlo para que se ataque a la base de datos que usa nuestro contenedor `MySQL`

Accedemos a la carpeta de nuestro proyecto
```bash
cd src/
```

Y lanzamos Visual Studio Code para modificarlo
```bash
code .
```

Una vez dentro, solo hay que encontrar el `.env` y modificar esta sección:
```bash
DB_CONNECTION=sqlite
# DB_HOST=127.0.0.1
# DB_PORT=3306
# DB_DATABASE=laravel
# DB_USERNAME=root
# DB_PASSWORD=
```

por esta otra:

```bash
DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=laravel
DB_PASSWORD=secret
```

Guardamos y volvemos a la ruta donde nos encontrabamos para seguir con la instalación.

```bash
cd ..
```

# **7. Levantar los contenedores**

```bash
docker compose up -d
```

# **8. Configurar Laravel**

Generar la clave de aplicación.

```bash
docker compose exec php php artisan key:generate
```

¿Qué hace esto?

- Laravel necesita una clave única para encriptar datos
- artisan es la herramienta de línea de comandos de Laravel
- Se guarda automáticamente en src/.env

Ejecutar las migraciones de base de datos

```bash
docker compose exec php php artisan migrate
```

¿Qué hace esto?

- Crea las tablas iniciales en la base de datos MySQL
- Laravel incluye algunas tablas por defecto (usuarios, sesiones, etc.)
- Nota: La primera vez puede no verse ningún cambio. El objetivo es verificar que la conexión a MySQL funciona correctamente.

# **9. Configurar Vite**

Si instalas **Laravel Breeze** o trabajas con assets frontend (JS/CSS), necesitas Vite.

### 1. Instala las dependencias de Node

```bash
docker compose run --rm node npm install
```

### 2. Configura vite.config.js para Docker

Ahora edita (en VSCode) en `vite.config.js` para poder usar Vite correctamente y que los cambios se reflejen automáticamente.

```javascript
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
    ],
    server: {
        host: '0.0.0.0',        // ← Escucha en todas las interfaces (Docker)
        port: 5173,
        strictPort: true,       // ← Falla si el puerto está ocupado
        hmr: {
            host: 'localhost',  // ← El navegador conecta a localhost
            port: 5173,
        },
        watch: {
            usePolling: true,   // ← Necesario para que funcione en Docker
        },
    },
});
```

### 3. Compila los assets

No lances este comando si aún no tienes Laravel Breeze corriendo, abajo hay una guía para instalarlo.
```bash
docker compose run --rm --service-ports node npm run dev
```

Recuerda no cerrar el terminal o la app dejará de funcionar, es muy últil tenerlo abierto, pues aquí se podrán ver errores por parte de Vite.

---

## 🌐 Acceder a tu aplicación

Una vez configurado, tendrás acceso a:

| Servicio | URL | Credenciales |
|----------|-----|--------------|
| **Laravel** | [http://localhost](http://localhost) | - |
| **phpMyAdmin** | [http://localhost:8080](http://localhost:8080) | Usuario: `laravel`<br>Contraseña: `secret`<br>Servidor: `db` |
| **Vite (dev)** | [http://localhost:5173](http://localhost:5173) | (cuando ejecutes `npm run dev`) |

---

### Workflow diario

**Terminal 1:** Servicios de Laravel
```bash
docker compose up -d
```

```bash
cd src/
```

```bash
code .
```

**Terminal 2:** Vite en modo desarrollo (hot reload)
```bash
docker compose run --rm --service-ports node npm run dev
```

---

## 🎨 Frontend con Laravel Breeze

Si necesitas autenticación (login, registro, etc.) con un frontend completo, consulta la guía de instalación de Laravel Breeze:

➡️ **[Guía de instalación de Laravel Breeze](docs/02-Laravel-Breeze.md)**

Esta guía cubre:
- Instalación de Breeze
- Configuración de Vite para Docker
- Compilación de assets
- Solución de problemas comunes

---

## 📚 Documentación

- **[Getting Started](docs/01-primeros_pasos.md)** - Tutorial completo desde cero
  - Configuración inicial
  - Primer "Hola Mundo"
  - Rutas, vistas y controladores
  - Trabajar con la base de datos
  - Mostrar datos en pantalla

---

## 🎯 Comandos esenciales

### Gestión de contenedores

```bash
docker compose up -d
```

```bash
docker compose ps
```

```bash
docker compose logs -f
```

```bash
docker compose down
```

### Laravel Artisan

```bash
docker compose exec php php artisan make:controller MiControlador
```

```bash
docker compose exec php php artisan make:model Post -m
```

```bash
docker compose exec php php artisan migrate
```

Más comandos en [Getting Started](docs/GettingStarted.md#comandos-del-día-a-día).

---

## 💻 ¿Windows o WSL2?

Si usas Windows, **instala Ubuntu desde Microsoft Store**:

1. Abre Microsoft Store
2. Busca "Ubuntu" e instala
3. Ábrelo y crea usuario/contraseña
4. Clona el proyecto dentro de Ubuntu (no en C:\)

### ¿Por qué?

| Operación | Windows (NTFS) | Ubuntu (WSL2) |
|-----------|----------------|---------------|
| `composer install` | ~180 segundos | ~8 segundos |
| Carga de página | 500-2000ms | 50-150ms |
| `php artisan migrate` | 5-15 segundos | 1-3 segundos |

**La diferencia es brutal** 🚀 Docker lee archivos desde Linux 50x más rápido.

---

## ⚠️ Importante

- Este setup es **solo para desarrollo**, no para producción
- La carpeta `src/` debe crearse **antes** de `docker compose build` para evitar problemas de permisos
- Si tienes errores de permisos en Linux, ejecuta: `sudo usermod -aG docker $USER` y reinicia sesión

---

## 🤝 Contribuir

¿Encontraste un error o tienes una mejora? ¡Los Pull Requests son bienvenidos!

1. Haz un fork del proyecto
2. Crea una rama: `git checkout -b feature/mejora-increible`
3. Commit: `git commit -m 'Agrega función increíble'`
4. Push: `git push origin feature/mejora-increible`
5. Abre un Pull Request

---

## 📄 Licencia

Este proyecto es de código abierto bajo licencia MIT. Úsalo libremente para aprender y construir proyectos increíbles.

---

## 🙏 Agradecimientos

Creado como recurso educativo para estudiantes que quieren aprender Laravel con Docker de forma clara y sin magia.

**Si te ha sido útil, ¡dale una ⭐ en GitHub!**

---

## 🆘 ¿Necesitas ayuda?

- 📖 [Documentación oficial de Laravel](https://laravel.com/docs)
- 🐋 [Docker Compose reference](https://docs.docker.com/compose/)
- 📘 [Tutorial completo](docs/GettingStarted.md)

---

<sub>💡 **Filosofía del proyecto:** Separar la infraestructura (Docker) del código (Laravel) para facilitar el aprendizaje y comprensión de ambas tecnologías.</sub>
