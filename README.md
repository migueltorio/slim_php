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

## 3. Instalación de SLIM
Para empezar nos ubicamos en la carpeta del proyecto y generamos la siguiente estructura:
```
├── proyecto
│   └── src
│       └── public
```
La carpeta public es la que será nuestro webroot. Luego debemos instalar Slim con el siguiente comando:
```
composer require slim/slim "^3.0"
```
Luego de la instalación, la estructura del proyecto debería ser la siguiente:
```
├── proyecto
│   └── src
│       └── public
│   └── vendor
│   composer
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
Creamos un archivo en:
```
src/public/index.php
```
Con el siguiente contenido:
```
<?php
use \Psr\Http\Message\ServerRequestInterface as Request;
use \Psr\Http\Message\ResponseInterface as Response;

require '../../vendor/autoload.php';

$app = new \Slim\App;
$app->get('/hello/{name}', function (Request $request, Response $response) {
    $name = $request->getAttribute('name');
    $response->getBody()->write("Hello, $name");

    return $response;
});
$app->run();
```
Luego, probamos nuesto nuevo "servicio web" ejecutando el siguiente comando desde la consola:
```
php -S localhost:8080
```
Si abrimos postman y hacemos una solicitud GET a la http://localhost:8080/src/public/index.php/hello/{Usuario} obtenendremos como respuesta "Hello, Usuario".

## 3. Instalación de DOCTRINE
Doctrine es un ORM para PHP que nos proporciona una capa de abstracción sobre el sistema de gestión de base de datos.
El primer paso es modificar el archivo *composer.json* :
```
{
    "require": {
        "slim/slim": "3.*",
        "doctrine/orm": "2.4.*"
    },
    "autoload": {
        "psr-4": {
            "App\\": "src/App"
        }
    }
}
```
Lo que hacemos es indicarle a composer, que necesitamos doctrine y ademas indicamos donde se va a ubicar el espacio de nombres App.
Escribimos en la consola, el comando para instalar y actualizar las dependencias especificadas en *composer.json*:
```
composer update
```
Creamos una carpeta *App* dentro de *src* y dentro de *App* otra carpeta con el nombre *Entity*. El arbol de directorios queda así:
```
├── proyecto
│   └── src
│       └── public
│       └── App
│           └── Entity
```
Creamos el siguiente archivo *src/App/AbstractResource.php*, donde vamos a especificar el controlador de base de datos que vamos a utilizar así como las credenciales de conexión:
```
<?php

namespace App;

use Doctrine\ORM\EntityManager;
use Doctrine\ORM\Tools\Setup;

abstract class AbstractResource
{
    /**
     * @var \Doctrine\ORM\EntityManager
     */
    private $entityManager = null;

    /**
     * @return \Doctrine\ORM\EntityManager
     */
    public function getEntityManager()
    {
        if ($this->entityManager === null) {
            $this->entityManager = $this->createEntityManager();
        }

        return $this->entityManager;
    }

    /**
     * @return EntityManager
     */
    public function createEntityManager()
    {
        $path = array('Path/To/Entity');
        $devMode = true;

        $config = Setup::createAnnotationMetadataConfiguration($path, $devMode);

        // define credentials...
        $connectionOptions = array(
            'driver'   => 'pdo_pgsql',
            'host'     => 'localhost',
            'dbname'   => 'slimphp',
            'user'     => 'postgres',
            'password' => 'postgres',
        );

        return EntityManager::create($connectionOptions, $config);
    }
}
```


