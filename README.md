# Descripción

"Drupal ECS Boilerplate" proporciona una plantilla base para desplegar aplicaciones Drupal en Amazon ECS (Elastic Container Service),
optimizando la configuración para entornos de producción.

Incluye configuraciones predefinidas en el fichero `settings.php` para el manejo de proxy inverso, lo que facilita la integración con redes
y servicios externos, así como la aplicación de un parche custom, `patches/fix_path_processor_front.patch`, que permite que las URLs hacia y
desde la página principal sean consistentes, usando `/` como referencia.

En resumen, este boilerplate está diseñado para simplificar el proceso de configuración y despliegue de aplicaciones Drupal en la nube,
permitiendo asegurar una alta disponibilidad y escalabilidad detrás de la infraestructura correcta.

# Instrucciones

## Cómo crear un proyecto

1. Clonar el repositorio en local.
2. Montar el proyecto en su entorno de trabajo LAMP.
2. Lanzar un `composer install`

También puede generar una build a partir del código del repositorio y levantar un contenedor siguiendo las mismas instrucciones que se especifican en: https://hub.docker.com/_/drupal.

## Cómo generar una imagen Docker del proyecto

Una vez haya terminado de trabajar, puede generar una imagen del proyecto con un comando similar al siguiente:

```
docker image build -t fulanito/mi-proyecto-drupal:v1.0 .
```

En este punto puede subir la imagen generada a un registro de Docker y usarla en la nube.

# Observaciones importantes

## Configuración de proxy inverso

Se ha adaptado el fichero `web/sites/default/default.settings.php` suponiendo que se accederá al contenedor a través 
de un proxy inverso que se puede encontrar en los siguientes rangos de IPs:

```
$settings['reverse_proxy_addresses'] = [
  - '10.0.0.0/16',
  - '10.1.0.0/16',
  - '10.2.0.0/16',
  - '10.3.0.0/16',
  - '10.4.0.0/16',
];
```

En el mismo fichero, se han habilitado también, las siguientes configuraciones:

```
$settings['reverse_proxy'] = TRUE;

$settings['reverse_proxy_trusted_headers'] = \Symfony\Component\HttpFoundation\Request::HEADER_X_FORWARDED_FOR | 
\Symfony\Component\HttpFoundation\Request::HEADER_X_FORWARDED_HOST | \Symfony\Component\HttpFoundation\Request::HEADER_X_FORWARDED_PORT | \Symfony\Component\HttpFoundation\Request::HEADER_X_FORWARDED_PROTO | \Symfony\Component\HttpFoundation\Request::HEADER_FORWARDED;
```

## Seguridad de su proyecto

Tenga cuidado de no subir al repositorio público de Dockerhub imágenes con información sensible como contraseñas o
tokens de acceso.

En la mayoría de los casos lo recomendable es utilizar repositorios privados, tanto para el código del proyecto como
para la imagen de Docker que usará para desplegar su aplicación a modo de artefacto.

# Referencias

- https://hub.docker.com/_/drupal
- https://github.com/docker-library/drupal/tree/master/10.3/php8.3/apache-bookworm
- https://www.58bits.com/blog/canonical-urls-https-and-drupal-89-reverse-proxy-settings
