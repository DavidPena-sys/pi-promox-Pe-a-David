# Inventario investigado y viabilidad

## Contexto de la observacion

- Fecha y sistema utilizado: 13/10/2026 | Lliurex y Windows Hiren's Boot
- Modalidad, equipo anfitrion y VM VirtualBox si procede: PENDIENTE.
- Version de Proxmox prevista: Proxmox VE 9.2

## Inventario inicial

| Area | Dato observado y evidencia | Especificacion y fuente | Limite o diferencia | Justificacion |
|---|---|---|---|---|
| CPU | Intel Pentium G4560 @ 3,5 GHz, 2 núcleos, 4 hilos, 14nm, L 3 3 MiB, VT-x | Ficha Intel y comando LSCPU | Solo 2 Núcleos físicos y el reloj mínimo de 800 MHz | VT-x es el requisito de CPU para KVM, y lo tiene |
| RAM | 8 GiB DDR4, 2 × 4 GiB Crucial CT4G4DFS824A, módulos 33/17 y 34/17, dual channel, funcionando a DDR4-2400 CL17 | Módulos DDR4-2400 de 1,20 V, la placa admite hasta 64 GB, y la fuente es CPU-Z, y la ficha de ASUS | El límite es de 7,6 GiB porque el firmware tiene parte reservada, módulos en el slot 1 y 3 | Ocupa solo 2 de 4 slots, y podría ampliarse si hiciera falta |
| Almacenamiento | HDD Toshiba DT01ACA050, 500,1 GB (465,8 GiB utilizables) | Tanto con el comando diskpart, como con el Disk Manager | Un solo disco y sin redundancia. | Es un HDD, no un SSD y todo el servicio dependería de el |
| Red | NIC Realtek RTL8168/8111 Gigabit| Realtek RTL8111/8168, 1000 Mbps máx, y se ve tanto con el ip a, como con el Hiren's Boot | No se midió la velocidad real, porque no teníamos cable | Una sola NIC |
| Plataforma | ASUS PRIME B250M-A, chipset B250, BIOS AMI(0810) | Página de soporte de ASUS y se vio con el CPU-Z | BIOS de 2017 | La placa es compatbible con el G4560 |

## 1. Puedo instalar Proxmox en este equipo?

Contrastar el inventario con requisitos oficiales de la version prevista.
Responder por separado y justificar con datos y fuentes:

- Puedo instalar y arrancar Proxmox: Sí, ya que piden CPU de 64 bits, con Intel VT o AMD-V, al menos 1GB de RAM para evaluación y 2GB para el sistema y servicio, un disco y una NIC.
- Puedo ejecutar el servidor ASO y el cliente del proyecto: Sí, con un reparto ajustado, ya que el equipo tiene 7,6 GiB de RAM usables, 4 hilos y 465,8GiB de disco, Proxmox necesita unos 2 GB y el server puede llevar 2 GB y 2 vCPU cada uno así  que dejaríamos un margen de 1,6 GB.
- Condiciones o comprobaciones pendientes: Hacer una copia de los 235GB del disco actual porque el instalador borra el disco, comprobar que VT-x está activado en la BIOS y que existe el /dev/kvm, luego conectar un cable de red y medir la velocidad, copiar la ISO de Proxmox al pendrive de Ventoy y probar el arranque.

## 2. Que limitaciones tendre con este hardware?

Analizar RAM, CPU, disco y red para las cargas previstas. Identificar lo
medido y lo estimado. En VirtualBox, investigar host real y VM: version,
RAM reservada al anfitrion, vCPU, VDI, espacio fisico y red local.
No se utiliza el Proxmox del centro como recurso.

### Reparto de recursos

| Elemento | CPU/vCPU | RAM | Disco | Justificacion |
|---|---|---|---|---|
| Proxmox | | | | |
| srv-aso | | | | |
| cli-linux | | | | |
| Plantilla, ISOs y copias | No aplica | No aplica | | |
| Margen de operacion | | | | |

Mostrar calculos de RAM y disco, reparto de vCPU, unidades y supuestos.
Explicar las cargas que pueden funcionar simultaneamente.

| Limitacion | Evidencia o estimacion | Consecuencia | Medida y limite que permanece |
|---|---|---|---|
| | | | |

## 3. Que componentes son mas criticos en mi servidor y por que?

Ordenar al menos tres componentes para este equipo y su servicio.
Distinguir cuello de botella, disponibilidad e integridad de los datos.

| Prioridad y componente | Funcion | Efecto si es insuficiente | Efecto si falla | Evidencia y justificacion |
|---|---|---|---|---|
| 1. | | | | |
| 2. | | | | |
| 3. | | | | |

Componente que mejoraria primero y motivo para priorizarlo frente a otro:
PENDIENTE.

## Fuentes tecnicas

Consultar al menos tres fuentes primarias, incluyendo Proxmox y
fabricante o plataforma virtual. Relacionarlas con las tres respuestas.

| Fuente y URL | Version/modelo y fecha de consulta | Afirmacion respaldada | Aplicacion al nodo |
|---|---|---|---|
| | | | |

## Dictamen de viabilidad

Apto, apto con condiciones o no apto para la propuesta actual: PENDIENTE.

Sintetizar las tres respuestas y las condiciones pendientes de comprobar.

## Evidencias y revision docente

Enlazar salidas revisadas desde ../evidencias/. Un listado automatico no
sustituye la investigacion. Registrar la pregunta de defensa y respuesta.

[Volver al indice](../README.md)
