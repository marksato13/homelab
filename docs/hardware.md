# Hardware

> Estado: 🔜 **planificado**. Nada comprado todavía.

## El requisito que manda: RAM

Sumando lo que pide cada stack de los que no caben en el portátil:

| Servicio | RAM mínima | RAM cómoda |
|---|---:|---:|
| Malcolm (Zeek + Suricata + Arkime + OpenSearch) | 16 GB | 24 GB |
| Wazuh (indexer + manager + dashboard) | 8 GB | 12 GB |
| Zabbix server + BD | 2 GB | 4 GB |
| NetBox + postgres + redis | 2 GB | 4 GB |
| GNS3 con una topología media | 4 GB | 8 GB |
| Hipervisor y sistema base | 2 GB | 4 GB |

**Todo a la vez no hace falta.** Malcolm y GNS3 rara vez se usan simultáneamente. Pero por debajo de **32 GB** el lab se convierte en un ejercicio de apagar cosas para encender otras.

| Escenario | RAM | Qué permite |
|---|---:|---|
| Mínimo viable | 32 GB | Wazuh + Zabbix + NetBox permanentes, y Malcolm **o** GNS3 puntualmente |
| Cómodo | 64 GB | Todo a la vez, sin pensar |

## Almacenamiento

Arkime y Malcolm guardan paquetes completos. El consumo depende del tráfico capturado y del tiempo de retención, no del número de servicios.

| Uso | Espacio |
|---|---|
| SO + VMs de servicio | 250 GB SSD |
| Capturas y logs | 1 TB en adelante, ampliable |

**SSD para el sistema y los índices de OpenSearch** — ahí la latencia de disco se nota mucho. Para el almacén de PCAP, HDD sirve.

## CPU

Menos crítico que la RAM. Cualquier procesador de 6-8 núcleos con virtualización (VT-x/AMD-V y VT-d) es suficiente. Zeek y Suricata escalan con hilos, así que más núcleos ayudan en captura a alta velocidad, pero no es el cuello de botella de un lab.

## Red — esto sí importa

Para captura en vivo hacen falta **dos cosas** que no se pueden improvisar:

**Switch gestionable con puerto espejo (SPAN).** Sin espejo no hay forma de que Zeek o Suricata vean tráfico que no va dirigido a ellos. Cualquier switch gestionable de gama básica lo hace.

**NIC dedicada para captura**, separada de la de gestión. La interfaz de captura va en modo promiscuo y sin IP; la de gestión, con IP fija.

Mínimo recomendable: **2 puertos Ethernet**. Con tres, uno para WAN del firewall, otro para LAN y otro para el espejo.

## Opciones de plataforma

| Opción | A favor | En contra |
|---|---|---|
| **Mini-PC** (NUC o similar) | Silencioso, bajo consumo, barato | Suele tener una sola NIC; ampliar RAM limitado |
| **PC de sobremesa reutilizado** | RAM y discos ampliables, PCIe para NICs | Consume y hace ruido |
| **Servidor de segunda mano** (Dell R-series, HP DL) | Mucha RAM barata, ILO/iDRAC, varias NICs | Ruidoso y consume: difícil en casa |
| **VPS / cloud** | Sin hardware que mantener | No sirve para captura en vivo de tu red |

Para un lab de redes en casa, un **sobremesa con 32-64 GB y una NIC dual añadida por PCIe** suele ser el mejor equilibrio entre precio, ampliabilidad y ruido.

## Hipervisor

| Opción | Nota |
|---|---|
| **Proxmox VE** | Gratuito, basado en Debian, buena gestión web, soporta LXC y KVM. La opción por defecto para un lab |
| **VMware ESXi** | Estándar en empresa. La licencia gratuita ha cambiado de condiciones; verificar disponibilidad actual |
| **VirtualBox** | Solo si el lab va sobre un equipo de escritorio que además se usa para otra cosa |
| **Solo Docker** | Suficiente para los servicios, insuficiente para emular topologías con GNS3 |

Un lab de redes gana mucho con un hipervisor de tipo 1: te permite tratar el firewall (pfSense/OPNsense) como una VM más y rehacer la topología sin recablear.
