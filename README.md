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

2. **Si usas Windows:**
   - Virtualización habilitada en la BIOS (si ya usaste VirtualBox o VMware, ya la tienes)
   - **Ubuntu desde Microsoft Store** (recomendado para mejor rendimiento)
   - WSL2 instalado (Docker Desktop lo instala automáticamente)

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

### Instalación en 4 pasos

**1. Clona este repositorio**

```bash
git clone https://github.com/endiva112/laravel-docker-setup.git mi-proyecto
```

```bash
cd mi-proyecto
```

**2. Crea la carpeta para Laravel**

```bash
mkdir src
```

**3. Construye los contenedores**

```bash
docker compose build
```

**4. Crea el proyecto Laravel**

```bash
docker compose run --rm composer create-project laravel/laravel .
```

¡Eso es todo! Ahora ve a la [Guía de primeros pasos](docs/01-primeros_pasos.md) para configurar la base de datos y probar que todo funciona.

---

## 🌐 Acceder a tu aplicación

Una vez configurado, tendrás acceso a:

| Servicio | URL | Credenciales |
|----------|-----|--------------|
| **Laravel** | [http://localhost](http://localhost) | - |
| **phpMyAdmin** | [http://localhost:8080](http://localhost:8080) | Usuario: `laravel`<br>Contraseña: `secret`<br>Servidor: `db` |
| **Vite (dev)** | [http://localhost:5173](http://localhost:5173) | (cuando ejecutes `npm run dev`) |

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