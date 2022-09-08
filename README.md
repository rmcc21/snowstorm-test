# Servicio de Terminología - Servidor de Test

Tener el cuenta que se utiliza como volumen de datos de los contenedores el directorio `/datos` siguiendo el esquema planteado por DIC.

## Configuracion de Linux
Es necesario cambiar el siguiente parametro del kernel para elasticsearch:

`
sudo sysctl -w vm.max_map_count=262144
`

Si se quiere hacer permanente, modificar el archivo `/etc/sysctl.conf` y agregarle la siguiente linea:

`
vm.max_map_count=262144
`

Luego ejecutar `sudo sysctl -p`. Alternativamente reiniciar el servidor.


## Configuracion de Docker
Si se hace uso de la vpn de gobierno para ingresar a los servidores es necesario cambiar la red por defecto de docker. Para ello editar el archivo `/etc/docker/daemon.json`. Si el archivo no existe, crearlo.

```
{
  "default-address-pools":
  [
    {"base":"172.33.0.0/16","size":24}
  ]
}
```

Luego ejecutar lo siguiete:
``` 
sudo systemctl daemon-reload
sudo systemctl restart docker
```
Comprobar que el bridge por defecto de docker ha cambiado el segmento. Si no se realiza comprobar que no existan contenedores usando el segmento anterior.

Nota: Si no se acceder al servidor luego de instalar docker, probar desde otro servidor dentro de la vpn.

## Instalacion Snowstorm 

Tener en cuenta que la indexación inicial requiere como mínimo 16GB de ram, se recomienda realizarla con 32GB.

Se recomienda subir los parámetros de memoria para los contenedores de `snowstorm` y `elasticsearch` al menos a 8GB cada uno.

### Puesta en marcha

`docker-compose up -f docker-compose-test.yml -d`

Tener en cuenta que es necesario realizar la *Indexación Inicial* previamente y por unica vez.

En caso de querer seguir el proceso de arranque

`docker-compose -f docker-compose-test.yml logs -f`


### Indexación inicial
Copiar el archivo de terminologias a `/datos/snomed.zip`.

Por ejemplo

`cp zipSnomedCT_Argentina-EditionRelease_PRODUCTION_20220531T120000Z.zip /datos/snomed.zip`

Luego ejecutar 

`docker-compose up -f docker-compose-initial.yml -d`

El proceso pueder durar mucho tiempo, es recomendable seguir el proceso visualizando los logs. 



*Fuente:
https://github.com/IHTSDO/snowstorm/blob/master/docs/loading-snomed.md*



