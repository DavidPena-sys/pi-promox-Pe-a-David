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
| Proxmox | Compartida | 2,0 GB| 40 GB | 2 GB es el mínimo oficial de RAM y los 40 GB de disco para la raíz y el swap|
| srv-aso | 2 vCPU | 2,0 GB | 80 GB | Porque es lo necesario para que los servicios sean ligeros y todo funciona correctamente |
| cli-linux | 2 vCPU | 2,0 GB | 50 GB | Es lo que se necesita para un cliente ligero, y que funcione medianamente bien |
| Plantilla, ISOs y copias | No aplica | No aplica | 120 GB | Las copias en el mismo disco no protegen un fallo de disco |
| Margen de operacion | 0 | 1,6 GB | 175,8 GB | Es lo que sobra |

Mostrar calculos de RAM y disco, reparto de vCPU, unidades y supuestos.
Cálculo de RAM = 7,6 GB - (2+2+2) = 1,6 GB es el sobrante de margen.
Cálculo de Disco = 465,8 GB - (40+80+50+120) = 175,8 GB que es el sobrante de margen
vCPU = 2+2 = 4 vCPU sobre 4 hilos, es un reparto de 1:1 contra los hilos.
Explicar las cargas que pueden funcionar simultaneamente.
Cargas simultaneas: srv-aso y cli-linux encendidos a la vez funcionan si no se hacen tareas pesadas.

| Limitacion | Evidencia o estimacion | Consecuencia | Medida y limite que permanece |
|---|---|---|---|
| RAM | 7,6 GB | Máximo unas 3 máquinas de 2GB de RAM | Sin ZFS, 2GB por VM |
| CPU | 2 núcleos / 4 hilos | Dos VM con carga a la vez se frenan | Repartir 2 vCPU por VM |
| Disco (rendimiento) | HDD de 7200 rpm | Arrancar o actualizar las dos VM a la vez es lento | Hacer arranques escalonados y copias fuera de horas |
| Disco (integridad) | Un solo disco | Un fallo pierde todo, incluidas las copias del disco | Copiar las copias en un medio externo |
| Red | 1 NIC de 1 Gbps | Un único punto de fallo | Una red interna entre vmbr1 entre srv-aso y cli-linux |

## 3. Que componentes son mas criticos en mi servidor y por que?

Ordenar al menos tres componentes para este equipo y su servicio.
Distinguir cuello de botella, disponibilidad e integridad de los datos.

| Prioridad y componente | Funcion | Efecto si es insuficiente | Efecto si falla | Evidencia y justificacion |
|---|---|---|---|---|
| 1. Disco | Guarda el SO, las VM y las copias | Cuello de botella de E/S con varias VM  | Se pierden disponiblidad e integridad | Un único HDD de 7200 rpm, es el único punto de fallo para los datos |
| 2.RAM | Memoría de Proxmox y de las VM | Cuello de botella: Hacemos un swap en el HDD o el sistema mata procesos | Un módulo malo reduce a 4GB y no cabe todo | 7,6 GB visibles, es el límite de cuantas VM caben |
| 3. CPU | Cálculo de host y VM | Cuello de botella con cargas simultáneas | El host cae y todo con el | 2 núcleos / 4 hilos |

Componente que mejoraria primero y motivo para priorizarlo frente a otro:
Un SSD frente a más RAM, a que con el reparto de RAM alcanza perfectamente para 2-3 VM, pero el HDD es el cuello de botella de esto, sería riesgo de pérdida de datos y el factor que más se nota al arrancar varías VM. La RAM sería el segundo motivo, ya que hay slots libres.

## Fuentes tecnicas

Consultar al menos tres fuentes primarias, incluyendo Proxmox y
fabricante o plataforma virtual. Relacionarlas con las tres respuestas.

| Fuente y URL | Version/modelo y fecha de consulta | Afirmacion respaldada | Aplicacion al nodo |
|---|---|---|---|
| pve.proxmox.com/pve-docs | Proxmox VE 9.2, consultada el 13/10/2026 | CPU de 64 bits con VT, 2 GB mínimo para el SO más los invitados, mínimos | Respuestas 1 y 2  |
| https://www.intel.com/content/www/us/en/products/sku/97143/intel-pentium-processor-g4560-3m-cache-3-50-ghz/specifications.html | Pentium G4560, consultada el 13/10/2026 | VT-x, núcleos/hilos, memoria soportada| Respuestas 1 y 2 |
| https://www.asus.com/motherboards-components/motherboards/prime/prime-b250m-a/ | PRIME B250M-A, consultada el 13/10/2026 | Slots de RAM, máximo de RAM | Respuestas 2 y 3 |


## Dictamen de viabilidad

Apto, apto con condiciones o no apto para la propuesta actual: Cumple los requisitos de Proxmox y admite dos VM ligeras a la vez, pero está muy limitado, se podría hacer pero no sería lo optimo, las limitaciones serían el HDD, la RAM y la ausencia de redundancia.

Sintetizar las tres respuestas y las condiciones pendientes de comprobar.

## Evidencias y revision docente

Enlazar salidas revisadas desde ../evidencias/. Un listado automatico no
sustituye la investigacion. Registrar la pregunta de defensa y respuesta.

[Volver al indice](../README.md)
