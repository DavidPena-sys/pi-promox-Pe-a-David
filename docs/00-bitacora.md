# Bitacora de sesiones

La bitacora es el registro cronologico del trabajo. Anade una entrada al final
de cada sesion y conserva las anteriores. Escribe datos comprobables y enlaza
las evidencias utiles; evita frases generales como "he trabajado en Proxmox".

## Sesion 01 - 13/10/2026

- Objetivo: iniciar GitHub y la documentacion Markdown.
- Acciones realizadas: inventario de un equipo fisico con Lliurex (`lscpu`, `free`, `lsblk`, `ip a`, `df`) y con Windows desde Hiren's Boot (CPU-Z, HWiNFO64 y CrystalDiskInfo). Rellenado el informe `01-inventario-hardware.md` 
- Decisiones y motivos: version prevista Proxmox VE 9.2. Instalacion directa en el equipo fisico, sin VirtualBox. Uso de ext4/LVM en lugar de ZFS para no gastar RAM en cache. Reparto previsto: 2 GB y 2 vCPU para `srv-aso` y para `cli-linux`, con 1,6 GB de RAM de margen. Se prioriza mejorar el disco (SSD) antes que la RAM, porque el HDD es el cuello de botella y el unico punto de fallo.
- Pruebas y resultados: se esperaba confirmar que el equipo cumple los requisitos de Proxmox. Resultado: CPU x86_64 de 64 bits con VT-x (Pentium G4560, 2 nucleos / 4 hilos), 7,6 GiB de RAM utiles, HDD Toshiba DT01ACA050 de 465,8 GB.
- Documentos o evidencias: [Inventario de hardware](01-inventario-hardware.md).
- Incidencias y solucion o estado pendiente: Ninguna.
- Siguiente paso: Seguir con la Fase 2.

## Sesion 02 - 20/10/2026

- Objetivo: Fase 2, diseñar la red del laboratorio Proxmox antes de instalar servicios.
- Acciones realizadas: He rellenado el documento `02-diagrama-red.md` con los apartados Objetivo, Diseño de red, Tabla y Conclusión. Definidos nombre del nodo, interfaz de gestión, bridges, red de laboratorio, IPs, gateway, DNS y puertos.
- Decisiones y motivos: red 192.168.56.0/24. `vmbr0` como bridge exterior y `vmbr1` como bridge del laboratorio. Nodo en 192.168.56.20, `srv-aso` en 192.168.56.30 y `cli-linux` en 192.168.56.40. El nodo es el gateway de los invitados. DNS 8.8.8.8. De momento solo se documenta el puerto 8008, que sería el puerto de proxmox.
- Pruebas y resultados: ninguna todavía, esta sesión es solo diseño y no se ha instalado ni configurado nada.
- Documentos o evidencias: [Diseño de red](02-diagrama-red.md).
- Incidencias y solucion o estado pendiente: ninguna.
- Siguiente paso: Realizar la instalación de Proxmox

Anadir una entrada con esta estructura en cada sesion. El historial de
GitHub registra los commits; no hace falta incluir el hash del propio
commit dentro de su entrada de bitacora.

[Volver al indice](../README.md)







