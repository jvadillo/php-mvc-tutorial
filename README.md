# 🛠️ Guía Práctica: Construye tu propio MVC en PHP (paso a paso con comentarios)

Esta guía enseña a crear una arquitectura **MVC (Modelo - Vista - Controlador)** con PHP puro, de forma **incremental**. Está pensada para principiantes y contiene explicaciones y comentarios en el código. El objetivo aquí es no usar ningún framework (como Laravel o Symfony), sino crear un pequeño esqueleto MVC desde cero, para explicar qué hace cada parte y cómo se comunican entre sí.

Comenzaremos con una salida simple desde un modelo hasta llegar a un CRUD completo usando base de datos. El objetivo es aprender a:
- Separar la lógica de negocio (Modelo), la interfaz (Vista) y el control de flujo (Controlador).
- Usar rutas simples con $_GET['action'].
- Conectar todo de manera sencilla, sin automatismos ni frameworks.

---

## 📊 Introducción: ¿Qué es el patrón MVC?

El patrón **MVC** separa una aplicación en tres componentes:

- **Modelo (Model):** Gestiona los datos y la lógica del negocio.
- **Vista (View):** Presenta la información al usuario (HTML).
- **Controlador (Controller):** Intermediario entre vista y modelo. Recibe peticiones, consulta el modelo y llama a la vista adecuada.

### Ejemplo de flujo:

```
Usuario -> Introduce en el Navegador -> index.php?action=form
       -> Controlador (UserController)
       -> Modelo (User)
       -> Vista (HTML) con resultado
```

Este enfoque ayuda a mantener el código ordenado, reutilizable y más fácil de mantener.

---

## 📃 Paso 1: Primer contacto con MVC (sin formularios ni BBDD)

### ✅ Objetivo:

Mostrar un nombre simple desde un modelo a la vista, usando un controlador.

### ⚖️ Estructura inicial:

```plaintext
/mini-mvc
├── index.php
├── controllers/
│   └── UserController.php
├── models/
│   └── User.php
└── views/
    └── home.php
```

### 🔢 index.php

```php
<?php
// Punto de entrada. Decide qué acción ejecutar según la URL.
$action = $_GET['action'] ?? 'home';

require_once 'controllers/UserController.php';

$controller = new UserController();
$controller->$action(); // Llama al método correspondiente
```

### 👨‍💼 controllers/UserController.php

```php
<?php
require_once 'models/User.php';

class UserController {
    public function home() {
        // Creamos un modelo y obtenemos un nombre
        $user = new User();
        $name = $user->getUserName();

        // Enviamos el dato a la vista
        require 'views/home.php';
    }
}
```

### 📄 models/User.php

```php
<?php
class User {
    public function getUserName() {
        return "Juan Pérez"; // Nombre simulado
    }
}
```

### 📃 views/home.php

```php
<!DOCTYPE html>
<html>
<head><title>Inicio</title></head>
<body>
    <h1>Bienvenido <?= htmlspecialchars($name) ?></h1> <!-- Muestra el nombre enviado desde el controlador -->
</body>
</html>
```

---

## 📥 Paso 2: Formulario para capturar datos (sin BBDD)

### ✅ Objetivo:

Permitir al usuario ingresar nombre y edad, y mostrar los resultados. No se guardan en una base de datos todavía.

### 📄 models/User.php (ampliado)

```php
class User {
    public $name;
    public $age;

    public function __construct($name = '', $age = 0) {
        $this->name = $name;
        $this->age = $age;
    }

    public function getUserName() {
        return $this->name ?: 'Juan Pérez';
    }

    public function save() {
        // Simulamos guardado (no hace nada realmente)
        return true;
    }
}
```

### 👨‍💼 controllers/UserController.php (agregado)

```php
public function form() {
    require 'views/user_form.php'; // Muestra el formulario
}

public function saveUser() {
    // Recogemos los datos enviados por el formulario
    $name = $_POST['name'] ?? '';
    $age = $_POST['age'] ?? 0;

    $user = new User($name, $age);
    $user->save(); // Simulado

    require 'views/user_result.php';
}
```

### 📃 views/user\_form.php

```php
<!DOCTYPE html>
<html>
<head><title>Formulario</title></head>
<body>
<h1>Formulario de usuario</h1>
<form method="POST" action="index.php?action=saveUser">
    Nombre: <input type="text" name="name"><br>
    Edad: <input type="number" name="age"><br>
    <input type="submit" value="Enviar">
</form>
</body>
</html>
```

### 📃 views/user\_result.php

```php
<!DOCTYPE html>
<html>
<head><title>Resultado</title></head>
<body>
<h1>Datos recibidos</h1>
<p>Nombre: <?= htmlspecialchars($user->name) ?></p>
<p>Edad: <?= htmlspecialchars($user->age) ?></p>
</body>
</html>
```

---

