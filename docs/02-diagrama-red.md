# 02-diagrama-red

## Objetivo
El objetivo es diseñar la red del laboratorio Proxmox antes de empezar a realizar la instalación de servicios.

## Diseño de red
Nombre del nodo Proxmox: pve-peña

Interfaz de gestión: 192.168.56.20/24 (vmbr1)

Bridge de Proxmox: vmbr0, es el bridge exterior que saldría hacía fuera y vmbr1, es el bridge del laboratorio 

Red o VLAN de laboratorio: 192.168.56.0/24

IPs de las VMs/LXC: 192.68.56.30/24 - server y 192.168.56.40 - cliente

## Tabla
| Elemento | Nombre | IP | Función | Recursos |
|---|---|---|---|---|
| Nodo Proxmox | pve-peña | 10.0.2.20/24 (vmbr0) y 192.168.56.20 (vmbr1) | Hipervisor | Máquina Virtual |
| Servidor ASO | srv-aso | 192.168.56.30/24 | Servidor principal | Máquina Virtual/Contendor |
| Cliente | cli-linux | 192.168.56.40/24 | Pruebas | Máquina Virtual/Contenedor |

## Evidencias

## Conclusion

[Volver al indice](../README.md)
