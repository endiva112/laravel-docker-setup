# Getting Started con Laravel

> **⚠️ WIP (Work In Progress)** - Este documento está en construcción y se irá actualizando con más información.

## Pruebas de Funcionamiento de Laravel

Una vez tengas tu entorno configurado, puedes realizar estas pruebas para verificar que Laravel funciona correctamente.

---

## 1. Verificar Cambios en Vistas

### Modificar la vista de bienvenida

Accede a `resources/views/welcome.blade.php` y agrega un elemento HTML, por ejemplo:
```html
<h1 style="color: white">Mi modificación</h1>
```

Colócalo justo debajo de la etiqueta `<body>`.

Luego visita http://localhost y comprueba que aparece tu modificación.

---

## 2. Crear una Nueva Vista y Ruta

### Crear la vista

Puedes crear una vista manualmente o usar el siguiente comando:
```bash
docker compose exec php php artisan make:view prueba
```

### Agregar contenido a la vista

Edita `resources/views/prueba.blade.php` con el siguiente HTML:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Prueba</title>
</head>
<body>
    <h1>Esta es mi vista de prueba</h1>
    <p>Si ves esto, las vistas funcionan correctamente</p>
</body>
</html>
```

### Crear una ruta para la vista

En Laravel, para que una vista sea visible necesita una ruta. Abre `routes/web.php` y agrega:
```php
Route::get('/prueba', function () { 
    return view('prueba'); 
});
```

**Sintaxis Laravel: Rutas**

- `Route::get('/prueba', ...)` - Define la URL a la que responderá (en este caso `/prueba`)
- `function () { ... }` - La función que se ejecutará cuando se acceda a esa URL
- `return view('prueba')` - Retorna la vista llamada `prueba.blade.php`

La ruta puede ser diferente al nombre de la vista. Por ejemplo, una vista llamada `ejemplo1.blade.php` podría tener perfectamente una ruta `/pokemon`. La ruta define la URL en el navegador, mientras que el nombre de la vista es el archivo que se renderiza.

### Probar la nueva vista

Abre http://localhost/prueba en tu navegador.

---

## 3. Crear un Controlador

### ¿Qué es un controlador en Laravel?

Un controlador es una clase que organiza la lógica de tu aplicación. En lugar de escribir todo el código dentro de las rutas, los controladores te permiten separar responsabilidades, hacer tu código más mantenible y reutilizable. Manejan las peticiones HTTP y deciden qué respuesta devolver.

### Crear el controlador
```bash
docker compose exec php php artisan make:controller TestController
```

### Editar el controlador

Edita `src/app/Http/Controllers/TestController.php`:
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class TestController extends Controller
{
    public function index()
    {
        return view('prueba', [
            'mensaje' => 'Controlador funcionando correctamente'
        ]);
    }
}
```

**¿Qué hace este código?**

El método `index()` retorna la vista `prueba` y le pasa una variable llamada `$mensaje` con un texto. Esto permite enviar datos desde el controlador a la vista.

### Modificar la vista para usar datos del controlador

Modifica `src/resources/views/prueba.blade.php`:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Prueba</title>
</head>
<body>
    <h1>Esta es mi vista de prueba</h1>
    <p>{{ $mensaje }}</p>
</body>
</html>
```

### Actualizar la ruta

Ahora la vista no funcionará hasta que modifiquemos la ruta para que use el controlador. Edita `routes/web.php`:
```php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\TestController;

Route::get('/', function () {
    return view('welcome');
});

Route::get('/prueba', [TestController::class, 'index']);
```

**¿Qué cambia?**

- Se importa el controlador con `use App\Http\Controllers\TestController;`
- La ruta ahora apunta al método `index` del `TestController` en lugar de usar una función anónima
- Esto permite que el controlador maneje la lógica y pase datos a la vista

---

## 4. Crear Modelos y Migraciones

### ¿Para qué sirven?

- **Modelos**: Representan las tablas de la base de datos como clases PHP, permitiendo interactuar con los datos de forma sencilla
- **Migraciones**: Son archivos que definen la estructura de las tablas. Permiten versionar y compartir cambios en la base de datos de forma controlada, sin necesidad de crear tablas manualmente con SQL

### Crear modelo y migración
```bash
docker compose exec php php artisan make:model Producto -m
```

El flag `-m` crea automáticamente la migración asociada.

### Editar la migración

Edita el archivo de migración en `src/database/migrations/XXXX_XX_XX_XXXXXX_create_productos_table.php`:
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('productos', function (Blueprint $table) {
            $table->id();
            $table->string('nombre');
            $table->text('descripcion')->nullable();
            $table->decimal('precio', 8, 2);
            $table->integer('stock')->default(0);
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('productos');
    }
};
```

**¿Qué se ha modificado?**

Solo se ha modificado el método `up()` agregando los campos de la tabla:

- `id()` - Crea un campo ID autoincremental
- `string('nombre')` - Campo de texto para el nombre
- `text('descripcion')->nullable()` - Campo de texto largo, puede ser nulo
- `decimal('precio', 8, 2)` - Campo decimal para precios (8 dígitos totales, 2 decimales)
- `integer('stock')->default(0)` - Campo numérico con valor por defecto 0
- `timestamps()` - Crea campos `created_at` y `updated_at` automáticamente

### Ejecutar la migración
```bash
docker compose exec php php artisan migrate
```

### Verificar en la base de datos

Accede a phpMyAdmin en http://localhost:8080/ con:
- **Usuario**: laravel
- **Contraseña**: secret

Deberías ver la tabla `productos` creada.

---

## 5. Insertar Datos con Tinker

### ¿Qué es Tinker?

Tinker es una consola interactiva de Laravel que permite ejecutar código PHP y probar funcionalidades de forma rápida sin necesidad de crear scripts o controladores. Es muy útil para pruebas rápidas y experimentación.

### Abrir Tinker
```bash
docker compose exec php php artisan tinker
```

### Crear un producto

Copia y ejecuta **cada línea por separado**:
```php
$producto = new App\Models\Producto();
```
```php
$producto->nombre = 'Laptop';
```
```php
$producto->descripcion = 'Laptop gaming';
```
```php
$producto->precio = 1200.50;
```
```php
$producto->stock = 10;
```
```php
$producto->save();
```

### Ver todos los productos
```php
App\Models\Producto::all();
```

### Salir de Tinker
```bash
exit
```

### Verificar en phpMyAdmin

Vuelve a phpMyAdmin y verifica que el producto se ha agregado correctamente a la tabla `productos`.

---

## 6. Mostrar Datos en una Vista

### Crear vista y controlador
```bash
docker compose exec php php artisan make:view prueba2
```
```bash
docker compose exec php php artisan make:controller ProductsController
```

### Editar el controlador

Edita `app/Http/Controllers/ProductsController.php`:
```php
<?php

namespace App\Http\Controllers;

use App\Models\Producto;
use Illuminate\Http\Request;

class ProductsController extends Controller
{
    public function index()
    {
        $productos = Producto::all();

        return view('prueba2', [
            'productos' => $productos
        ]);
    }
}
```

**¿Qué hace este controlador?**

- Importa el modelo `Producto`
- El método `index()` obtiene todos los productos de la base de datos con `Producto::all()`
- Pasa la colección de productos a la vista `prueba2`

### Editar la vista

Modifica `resources/views/prueba2.blade.php`:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Prueba</title>
</head>
<body>
    <h1>Lista de Productos</h1>
    
    @if($productos->count() > 0)
        <table border="1">
            <tr>
                <th>ID</th>
                <th>Nombre</th>
                <th>Descripción</th>
                <th>Precio</th>
                <th>Stock</th>
            </tr>
            @foreach($productos as $producto)
            <tr>
                <td>{{ $producto->id }}</td>
                <td>{{ $producto->nombre }}</td>
                <td>{{ $producto->descripcion }}</td>
                <td>${{ number_format($producto->precio, 2) }}</td>
                <td>{{ $producto->stock }}</td>
            </tr>
            @endforeach
        </table>
    @else
        <p>No hay productos</p>
    @endif
</body>
</html>
```

**Sintaxis Laravel: Vistas Blade**

- `@if($productos->count() > 0)` - Condicional: verifica si hay productos
- `@foreach($productos as $producto)` - Bucle: itera sobre cada producto
- `{{ $producto->nombre }}` - Imprime el valor de forma segura (escapa HTML)
- `@else` y `@endif` - Cierre de condicionales
- `@endforeach` - Cierre del bucle

### Agregar la ruta

Edita `routes/web.php` y agrega la nueva ruta:
```php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\TestController;
use App\Http\Controllers\ProductsController;

Route::get('/', function () {
    return view('welcome');
});

Route::get('/prueba', [TestController::class, 'index']);

Route::get('/prueba2', [ProductsController::class, 'index']);
```

### Configurar el modelo

Si intentas acceder a la ruta ahora, verás un error. Necesitas configurar el modelo. Edita `app/Models/Producto.php`:
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Producto extends Model
{
    protected $table = 'productos';
}
```

Esto le indica a Laravel qué tabla de la base de datos debe usar este modelo.

### Probar la vista

Abre http://localhost/prueba2 y deberías ver la tabla con los productos.

---

## 🚧 Más contenido próximamente...

Este documento se irá actualizando con más ejemplos y funcionalidades de Laravel.