# 03-instalacion-proxmox

## Objetivo
Instalar Proxmox dentro de una máquina virtual de VirtualBox en mi portátil y dejarlo con la red que diseñé en la fase 2, para poder entrar al panel web desde el navegador.


## Procedimiento
1. Me descargué la ISO de Proxmox VE 9.2.
2. En VirtualBox creé una máquina nueva, `pve-pena`, de tipo Linux Debian 64 bits, con 10 GB de RAM, 6 CPUs y un disco de 80 GB. No le di toda la RAM del portátil para que Windows siguiera funcionando bien.
3. Le puse dos adaptadores de red:
   - Adaptador 1 en NAT, para que tenga Internet.
   - Adaptador 2 en solo-anfitrión (red 192.168.56.0/24), para poder entrar al panel desde mi PC. Le puse el modo promiscuo en "Permitir todo".
4. Con la máquina apagada, activé la virtualización anidada con VBoxManage.
5. Monté la ISO, arranqué la máquina y seguí el instalador. En la parte de red puse:
   - Hostname: `pve-pena.lab`
   - IP: `10.0.2.20/24`
   - Gateway: `10.0.2.2`
   - DNS: `8.8.8.8`
6. Al acabar quité la ISO y reinicié.
7. Como el instalador solo configura una tarjeta de red, añadí a mano el bridge `vmbr1` con la IP `192.168.56.20` en `/etc/network/interfaces`.
8. Entré desde el navegador a `https://192.168.56.20:8006` con el usuario `root` y funcionó.

## Comandos usados
# En Windows, con la máquina apagada, para activar el nested
cd "C:\Program Files\Oracle\VirtualBox"
.\VBoxManage.exe modifyvm "Proxmox" --nested-hw-virt on

# Editar la red para añadir vmbr1
nano /etc/network/interfaces

# Lo que añadí al final del archivo
auto vmbr1
iface vmbr1 inet static
    address 192.168.56.20/24
    bridge-ports nic1
    bridge-stp off
    bridge-fd 0
# Aplicar los cambios
systemctl restart networking

# Comprobaciones
hostnamectl
ip -br addr
pveversion
df -h
```
```bash

```

## Evidencias
![Aviso de KVM](<C:\Users\david\Desktop\clase\GRADO SUPERIOR\2º\PI\fase3\evidencias\03\03-01-aviso-kvm.png>)

Aviso que salió al empezar la instalación diciendo que no hay KVM.

![Adaptador 1](<C:\Users\david\Desktop\clase\GRADO SUPERIOR\2º\PI\Red1.png>)

Adaptador 1 de la máquina en NAT.

![Adaptador 2](<C:\Users\david\Desktop\clase\GRADO SUPERIOR\2º\PI\Red2.png>)

Adaptador 2 en solo-anfitrión, con el modo promiscuo en "Permitir todo".

![Almacenamiento](<C:\Users\david\Desktop\clase\GRADO SUPERIOR\2º\PI\Almacenamiento.png>)

Disco virtual de la máquina, donde está instalado Proxmox.

![Comprobaciones](<C:\Users\david\Desktop\clase\GRADO SUPERIOR\2º\PI\Captura1.png>)

Comprobaciones dentro de Proxmox: el hostname, las IPs de `vmbr0` y `vmbr1`, la versión (9.2.2) y el disco.

![Panel web](<C:\Users\david\Desktop\clase\GRADO SUPERIOR\2º\PI\Captura2.png>)

Panel web de Proxmox abierto desde el navegador de mi portátil.

## Incidencias
- **Aviso de que no hay KVM.** Es porque Windows 11 tno deja usar la virtualización anidada.
- **El hostname no acepta la ñ**, así que el nodo se llama `pve-pena`.


## Conclusion
Proxmox está instalado dentro de VirtualBox y puedo entrar al panel desde mi portátil. Me han salido varios problemas con la red y con KVM, pero los he podido solucionar o buscar otra forma de hacerlo. Me queda pendiente la actualización del sistema.


[Volver al indice](../README.md)
