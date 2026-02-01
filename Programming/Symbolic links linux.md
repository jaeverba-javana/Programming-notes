---
title: Symbolic link
---

# link simbólico linux

Un **enlace simbólico** (symlink o soft link) en Linux es un tipo de archivo que actúa como un acceso directo, apuntando a otro archivo o directorio ubicado en cualquier parte del sistema de archivos. Es similar a los accesos directos de Windows o los alias de macOS.[1][2][3]

## Crear un enlace simbólico

Para crear un enlace simbólico utilizas el comando **ln** con la opción **-s**:[2][1]

```bash
ln -s [archivo_destino] [nombre_del_enlace_simbolico]
```

**Ejemplo práctico:**
```bash
ln -s /usr/bin/python /home/usuario/bin/python
```

Este comando crea un enlace simbólico llamado `python` en `/home/usuario/bin/` que apunta al archivo original en `/usr/bin/python`.[3]

## Verificar enlaces simbólicos

Puedes verificar que el enlace se creó correctamente usando `ls -l`:[1]

```bash
ls -l /home/usuario/bin/
lrwxrwxrwx 1 usuario usuario 15 Feb 23 10:16 python -> /usr/bin/python
```

El archivo aparece con una **l** al inicio y muestra la flecha `->` indicando hacia dónde apunta.[3]

## Eliminar un enlace simbólico

Para eliminar un enlace simbólico utilizas el comando **unlink**:[3]

```bash
unlink /ruta/del/enlace/simbolico
```

También puedes usar `rm`:
```bash
rm /ruta/del/enlace/simbolico
```

## Diferencias clave: Enlaces simbólicos vs Enlaces duros

**Enlaces simbólicos (soft links):**
- Pueden apuntar a archivos o directorios en diferentes sistemas de archivos[5][2]
- Si se elimina el archivo original, el enlace se "rompe"[7][5]
- Pueden cruzar particiones y dispositivos[7]
- Cualquier usuario puede crearlos[7]

**Enlaces duros (hard links):**
- Solo pueden crearse dentro del mismo sistema de archivos[4][2]
- Comparten el mismo inodo que el archivo original[4]
- Si se elimina un enlace duro, el archivo permanece mientras existan otros enlaces duros[4]

## Consideraciones importantes

- Si renombras o mueves el archivo original, el enlace simbólico se romperá automáticamente porque referencia la ruta, no el contenido[3][7]
- El tamaño de un enlace simbólico es igual al número de caracteres de la ruta que referencia[7]
- Los permisos del enlace simbólico no son relevantes; se utilizan los permisos del archivo original[7]

Los enlaces simbólicos son especialmente útiles para crear accesos directos, gestionar versiones de software, y organizar estructura de directorios de manera eficiente en sistemas Linux.

Citations:
[1] Cómo crear un enlace simbólico en Linux - Guía completa - Hostinger https://www.hostinger.com/co/tutoriales/crear-enlace-simbolico-linux
[2] Tutorial de enlace simbólico en Linux: cómo crear y remover un ... https://www.freecodecamp.org/espanol/news/tutorial-de-enlace-simbolico-en-linux-como-crear-y-remover-un-enlace-simbolico/
[3] Vínculos simbólicos en Linux: Creación y uso en archivos y directorios https://apuntes.de/linux-certificacion-lpi/vinculos-simbolicos/
[4] Linux ln: comando para crear enlaces en Linux con ejemplos - IONOS https://www.ionos.com/es-us/digitalguide/servidores/configuracion/comando-ln-de-linux/
[5] Cómo crear y eliminar enlaces simbólicos en Linux - AlexHost https://alexhost.com/es/faq/como-crear-y-eliminar-enlaces-simbolicos-en-linux/
[6] Tutorial de Symlink en Linux: cómo crear y eliminar un enlace ... https://www.freecodecamp.org/espanol/news/tutorial-de-symlink-en-linux-como-crear-y-eliminar-un-enlace-simbolico/
[7] Enlace simbólico - Wikipedia, la enciclopedia libre https://es.wikipedia.org/wiki/Enlace_simb%C3%B3lico
[8] Como crear un enlace en Linux - HostGator https://www.hostgator.co/blog/crear-enlace-linux/
[9] Crear un enlace simbólico en Linux - Desarrollo Web https://desarrolloweb.com/faq/crear-un-enlace-simbolico-en-linux
[10] Crear Enlaces Simbólicos y Duros en Linux - Maxi Zamorano https://www.maxizamorano.com/entrada/22/crear-enlaces-simbolicos-y-duros-en-linux
