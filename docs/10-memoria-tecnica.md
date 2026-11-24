# Memoria tecnica: nodo Proxmox individual

La memoria presenta el resultado tecnico por temas. Debe explicar como esta
construido el laboratorio, por que se tomaron las decisiones y con que pruebas
se demostro su funcionamiento. Usa la bitacora como fuente, pero no copies el
diario completo: resume la configuracion final, los resultados y las
limitaciones que sigan abiertas.

## Resumen

Se va a construir un laboratorio de virtualización con Proxmox VE 9.2 dentro de VirtualBox, en el equipo de clase. En el nodo habrá un servidor (`srv-aso`) con un servicio ASO y un cliente (`cli-linux`) que lo usa, en una red interna aislada.

## Necesidad y objetivos

Necesidad que atiende el laboratorio, en un aula no se pueden instalar servicios en los equipos reales ni romperlos para practicar. Hace falta un entorno aislado, como este, que se pueda reconstruir y del que se puedan hacer copias.

- Objetivo comprobable 1: el panel de Proxmox es accesible desde el anfitrión en `https://192.168.56.20:8006` y el nodo tiene salida a Internet. Conseguido en las fases 3 y 4.
- Objetivo comprobable 2: `cli-linux` usa el servicio de `srv-aso` con un usuario autorizado, y un usuario sin permisos no puede acceder.
- Objetivo comprobable 3: una copia de seguridad del servicio se restaura y el servicio vuelve a funcionar.

## Alcance y criterios de aceptacion

Incluye por el momento, un nodo Proxmox virtualizado, una plantilla Linux, dos máquinas (`srv-aso` y `cli-linux`) en la red `vmbr1`, un servicio ASO y sus copias de seguridad.

Queda fuera, montar un cluster de varios nodos, la alta disponibilidad y el acceso desde fuera del aula.

| Resultado | Prueba para darlo por aceptado |
|---|---|
| Nodo instalado | Panel accesible, `pveversion` correcto y KVM disponible |
| Red | `vmbr0` y `vmbr1` UP con las IPs del diseño, el nodo resuelve nombres y descarga paquetes |
| Máquinas | `srv-aso` y `cli-linux` arrancan y se comunican en 192.168.56.0/24 |
| Servicio | El cliente usa el servicio con un usuario autorizado, y otro sin permiso no puede |
| Copias | Se restaura una copia y los datos aparecen igual que antes |

## Modalidad e inventario

El inventario se hizo sobre el PC dado por el profesor en clase (Pentium G4560, 7,6 GiB de RAM y HDD de 500 GB), que resultó apto con condiciones. El laboratorio se ha montado virtualizado en VirtualBox en el equipo de clase (8 hilos y 15 GiB de RAM), porque no hay suficientes equipos para toda la clase, así que se decidió que todos lo hiciéramos de esta manera. Se comprobó que la virtualización anidada funciona.

[Inventario](01-inventario-hardware.md)

## Diseno de red

El nodo tiene dos bridges: `vmbr0` (10.0.2.20/24) sobre el adaptador NAT de VirtualBox, para salir a Internet, y `vmbr1` (192.168.56.20/24) sobre el adaptador solo-anfitrión, para la gestión y la red del laboratorio. `srv-aso` (.30) y `cli-linux` (.40) irán en `vmbr1`.

[Diagrama y direccionamiento](02-diagrama-red.md)

## Instalacion y configuracion de Proxmox

Se instaló Proxmox VE 9.2 en una VM de 3 vCPU, 8 GB de RAM y 80 GB de disco, y se añadió `vmbr1` a mano. Se comprobaron el panel, la red, el DNS y KVM. En la configuración base se creó el almacenamiento `backups`, se revisaron las cuentas y se repartieron los recursos, 2 GB para el nodo, `srv-aso` con 2 vCPU, 2 GB y 10 GB, y `cli-linux` con 1 vCPU, 2 GB y 8 GB.

[Procedimiento](03-instalacion-proxmox.md)

## Plantilla Linux y despliegue

[Plantilla](04-plantilla-linux.md) y [VMs/LXC](05-vms-lxc.md)

## Servicio ASO

[Instalacion y pruebas](06-servicio-aso.md)

## Automatizacion con Bash y Ansible

[Automatizacion](07-automatizacion.md)

## Copias y restauracion

[Procedimiento y validacion](08-copias-restauracion.md)

## Rendimiento y seguridad

[Resultados](09-rendimiento-seguridad.md)

## Incidencias y decisiones

- Instalación en VirtualBox en vez de física, porque no hay equipos para toda la clase. En el portátil con Windows la virtualización anidada no funcionaba por Hyper-V, así que se montó en el equipo de clase.
- El nodo se llama `pve-pena` porque el hostname no admite la ñ.
- El `ping` no sale del nodo por la limitación del NAT de VirtualBox con ICMP, la conexión se comprueba con `getent hosts` y `apt update`.
- Los repositorios enterprise de Proxmox dan error 401 porque necesitan suscripción. Se desactivaron y se añadio `pve-no-subscription`, y ahora `apt update` termina sin ningún error.
- Las copias están en el mismo disco que el laboratorio, queda anotado como riesgo.

[Bitacora](00-bitacora.md)

## Presupuesto y sostenibilidad

## Conclusiones

## Fuentes y anexos

[Evidencias](../evidencias/README.md) y [volver al indice](../README.md)
