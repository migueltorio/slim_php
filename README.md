# Tutorial Slim + Doctrine
## 1. Introducción
En el tutorial vamos a desarrollar una servicio Web utilizando el framewokr Slim para PHP.
## 2. Preparación del ambiente de trabajo (30 min)
### 2.1. Instalación de Composer
Composer es una manejador de dependencias, que nos va a permitir instalar todas las librerias (y sus dependencias) que vamos a necesitar en el proyecto. Para instalar composer se debe acceder al [sitio oficial](https://getcomposer.org) y seguir las instrucciones de instalación. Composer esta disponible tanto para Windows, Linux y MACOS.

Una vez que hayamos instalado (siguiendo las instrucciones del sitio oficial), debemos comprobar que el mismo funciona correctamente abriendo la consola del sistema y escribiendo el siguiente comando:
```
C:\Users\username>composer -V
Composer version 1.0.0 2016-01-10 20:34:53
```
### 2.2. Instalación de Postgresql 
Como repositorio de datos, vamos a utilizar Postgresql. Una base de datos relacional y orientada a objetos libre.
Se puede descargar e instalar, siguiendo las instrucción del [sitio oficial](https://www.postgresql.org/).
### 2.3. Instalación de PHP
Necesitaremos PHP, para realizar pruebas. Podemos descargarlo en el [sitio oficial](http://windows.php.net/download/). Para este tutorial utilizaremos la versión 5.6.
También necesitamos que se encuentre habilitada la extensión PDO. Las instrucciones para instalar se pueden ver [aquí](http://php.net/manual/es/book.pdo.php).

Para verificar que PHP se encuentra correctamente instalado, probamos lo siguiente desde la consola de comandos:
```
C:\Users\migue> php -v
PHP 5.6.28 (cli) (built: Nov  9 2016 06:40:27)
Copyright (c) 1997-2016 The PHP Group
Zend Engine v2.6.0, Copyright (c) 1998-2016 Zend Technologies
```
### 2.4. Instalación de Postman
Postman es un cliente REST, que nos va a permitir probar nuestro servicio web. Se puede descargar desde el [sitio oficial](https://www.getpostman.com/).
### 2.5. Instalación de ATOM (opcional)
A fin de codificar mas cómodamente, recomiento instalar in IDE. Propongo [ATOM](https://atom.io/), que es un IDE open source y altamente customizable.
Se puede descargar del sitio oficial y está disponible para Linux, Windows y MACOS.


## 3. Instalación de SLIM, DOCTRINE y RESPECT
Para empezar nos ubicamos en la carpeta del proyecto y generamos la siguiente estructura:
```
├── proyecto
│   └── src
│       └── public
│       └── endpoints
│       └── entity
```
La carpeta public, sera la raiz de nuestro sitio. La carpeta endpoints, contendrá las defininiciones de los endpoints. La carpeta entity contendrá las clases.
Luego creamos en la raiz de la carpeta un archivo *composer.json* con el siguiente contenido:
```
{
    "require": {
        "slim/slim": "*",
        "doctrine/orm": "*",
        "respect/validation": "*"
    },
    "autoload": {
        "psr-4": {"Custom\\": "src/"}
    }
}
```
Le indicamos al composer, que instale Slim que es el framework para generar el web service. Doctrine es un mapeador de objetos-relacional (ORM) escrito en PHP que proporciona una capa de persistencia para objetos PHP. Respect es una librería para validar datos.
Instalamos los paquetes con el siguiente comando, desde la consola:
```
composer update
```
En la carpeta vendor, se encuentran todas las librerías necesarias para ejecutar el framework. El archivo composer contiene las definiciones de las dependencias que estan instaladas.
Si abrimos el archivo composer, tiene la siguiente estructura:
```
{
    "require": {
        "slim/slim": "3.0"
    }
}
```
Creamos un archivo *index.php* en *src/public*:
```
<?php

//establecemos los espacios de nombres
use \Psr\Http\Message\ServerRequestInterface as Request;
use \Psr\Http\Message\ResponseInterface as Response;
use \Doctrine\ORM\Tools\Setup;
use \Doctrine\ORM\EntityManager;
use \Respect\Validation\Validator as v;

//hacemos el autoload de las librerias
require '../../vendor/autoload.php';

//Doctrine
$paths = array(__DIR__ . "/src");
$isDevMode = false;
//aca especificamos los datos de la conexion a la base de datos
$dbParams = array(
    'driver' => 'pdo_pgsql',
    'host' => '127.0.0.1',
    'user' => 'postgres',
    'password' => 'postgres',
    'dbname' => 'slimphp',
);
$configDoctrine = Setup::createAnnotationMetadataConfiguration($paths, $isDevMode, null, null, false);
$entityManager = EntityManager::create($dbParams, $configDoctrine);

//Slim
$app = new \Slim\App;

//endpoints
include_once '../endpoints/endpoints.php'

//creamos el web service
$app->run();
```
Creamos un archivo *endpoints.php* en *src/endpoints*:
```
<?php
$app->get('/index', function ($request,$response) {
    $response->getBody()->write("Hola");
    return $response;
});
```
Luego desde la consolta, ubicados en la raiz del proyecto, ejecutamos el siguiente comando:
```
php -S localhost:80
```
Usando Postman hacemos una solicitud GET al endpoint que hemos creado:
```
http://localhost/src/public/index.php/index
```
Si todo se hizo correctamente, recibiremos lo siguiente:
```
Hola
```

## 4. Endpoints
Ahora ya estamos listos para empezar a crear los endpoints. Seguiremos el estilo de arquitectura REST como protocolo para intercambio de información entre el usuario final y el almacén de datos (base de datos).
REST define un conjunto de operaciones bien definidas que se aplican a todos los recursos de información que son, entre otras, POST, GET, PUT y DELETE.

Según la definición, las operaciones las vamos a definir como:
- GET: Pide una representación del recurso especificado
- POST: Envía los datos para que sean procesados por el recurso identificado. Creación de un recurso.
- PUT: Igual que POST solo que lo utilizaremos para modificar un recurso existente.
- DELETE: Elimina un recurso.




