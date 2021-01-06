# Backend Elijo Estudiar v1.1.0
El prinipal diferencial del presente relese son los filtros por edad.
Bienvenidos a la página del backend de Elijo Estudiar. Este backend está programado en Drupal 7 y deberá utilizar el proyecto d7main de DATA. Si estás interesado no dudes en contactarnos <a href="mailto:devops@datauy.org">devops@datauy.org</a>, iremos actualizando la instalación en la medida de lo posible y en función de la demanda. Dejamos unas notas de otra instalación para considerar:
## Instalación
`lsb_release -a
No LSB modules are available.
Distributor ID:	Debian
Description:	Debian GNU/Linux 9.11 (stretch)
Release:	9.11
Codename:	stretch`

1. git clone https://github.com/datauy/d7main.git
2. cd d7main/sites/
3. git clone https://github.com/datauy/ReportaCiudad.git
4. cp default/default.settings.php ReportaCiudad/
5. cd ReportaCiudad/
6. mv default.settings.php settings.php
7. Modificar archivo de configuración con datos de la base:  
```php 
$databases['default']['default'] = array (
      'database' => 'reportaciudad',
      'username' => 'reportaciudad',
      'password' => '<passwd>',
      'host' => 'localhost',
      'port' => '',
      'driver' => 'mysql',
      'prefix' => '',
    );
```
8. Crear base de datos
	* `create user 'reportaciudad' @'localhost' identified by '<password>';`
	* `create database reportaciudad;`
	* `grant all privileges on database reportaciudad.* to 'reportaciudad'@'localhost';`
	* Testear conexión:
		- `mysql -u reportaciudad -p reportaciudad`
9. Cargar dump de base:
	* `cd backups/`
	* `gunzip ReportaCiudad-2019-07-08\:-0311.mysql.gz`
	* `mysql -u reportaciudad -p reportaciudad < ReportaCiudad-2019-07-08\:-0311.mysql`
10. Crear usuarios del sistema:
  * Usuario anónimo: `insert into users ()`
  * admin:
    - Generar pass para admin: `./scripts/password-hash.sh <pass>`
    - `INSERT INTO `users` (`uid`, `name`, `pass`, `mail`, `theme`, `signature`, `signature_format`, `created`, `access`, `login`, `status`, `timezone`, `language`, `picture`, `init`, `data`) VALUES ('1', 'admin', '<pass>', '<mail>', '', '', NULL, '0', '1', '1', '1', NULL, '', '0', 'admin', NULL);` 
11. Crear carpeta de archivos en directorio ReportaCiudad:
  * `mkdir files`
  * `sudo chown datauy:www-data files`
  * `sudo chmod g+w files`  
Como se actualiza el core respecto a la versión instalada deberán realizarse procedimientos adicionales:

* Instalar drush (tener composer instalado y requisitos de drush 8)
  - `wget https://github.com/drush-ops/drush/releases/download/8.0.1/drush.phar`
  - `chmod +x drush.phar`
  - `sudo mv drush.phar /usr/local/bin/drush`
* Realizar rebuild del registro:
  - Installing and running Registry Rebuild without Drush or Drupal  
Fortunately, there is another handy way to run registry rebuild. This way doesn’t require a full Drupal bootstrap, so will run without the pesky fatal error.  
  Steps:

    Download registry rebuild from Registry Rebuild into your modules folder and untar it
    On the command line, cd to the registry_rebuild folder. In that folder, you will see a file called registry_rebuild.php
    We want to run that directly using PHP, so run the following command:  `php registry_rebuild.php`  
Once it has finished, the registry table will be rebuilt and will contain the new path for your class. You can now go about your Drupal business without the fatal error

* hacer actualización del core:
  - `drush pm-update drupal`
  - `drush cc all`
* realizar una limpieza de cachés:
`drush cc drush`

Y Listo!!!
PD: Si se actualiza la versión de PHP a 7.x hay que hacer un hack para que funcione porque algunas funciones cambian en las versiones y esta se hizo con PHP 5.5 at line 762 of module.inc, just before it uses cached_get():  `require_once DRUPAL_ROOT . '/includes/cache.inc';`
