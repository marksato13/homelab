# homelab

> Diseño y documentación de un laboratorio propio de redes y seguridad. Lo que no cabe en un portátil.

Repositorio hermano de **[neteng-workstation](https://github.com/marksato13/neteng-workstation)**, que documenta el entorno de trabajo del día a día. La frontera entre ambos es concreta y medible: **la RAM**.

> ⚠️ **Estado: en diseño.** Aquí está la arquitectura planificada y el porqué de cada decisión. No hay infraestructura montada todavía. Los documentos se marcan como `🔜 planificado` o `✅ implementado`, sin fechas inventadas.

---

## Por qué hace falta un homelab

El portátil de trabajo es un HP Victus con **15.6 GB de RAM**, de los cuales Docker ya reserva 8.1. Eso da para librerías de automatización y contenedores ligeros, pero se queda corto ante tres realidades:

**Los stacks de análisis piden más RAM de la que hay.** Malcolm —la suite de la CISA que junta Zeek, Suricata, Arkime y OpenSearch— pide 16 GB dedicados. Wazuh necesita 8 GB entre indexer, manager y dashboard. No es cuestión de optimizar: no entran.

**Los servidores tienen que estar encendidos.** Zabbix, LibreNMS o NetBox solo valen si están disponibles siempre. Un portátil que se suspende y cambia de red no monitoriza nada.

**La captura en vivo necesita una NIC de verdad.** Analizar un PCAP en Docker funciona; poner una interfaz en modo promiscuo sobre un puerto espejo, no.

---

## Qué irá aquí

### Análisis de tráfico y detección de anomalías

| Proyecto | ⭐ | Requisito | Rol |
|---|---:|---|---|
| [cisagov/Malcolm](https://github.com/cisagov/Malcolm) | 2 532 | 16 GB dedicados | **Pieza central.** Zeek + Suricata + Arkime + OpenSearch con dashboards hechos |
| [zeek/zeek](https://github.com/zeek/zeek) | 8 008 | NIC en promiscuo | Convierte tráfico en logs estructurados. La base para análisis de comportamiento |
| [OISF/suricata](https://github.com/OISF/suricata) | 6 673 | NIC en promiscuo | IDS/IPS por firmas. Complementa a Zeek: uno detecta lo conocido, el otro describe lo raro |
| [arkime/arkime](https://github.com/arkime/arkime) | 7 498 | Disco masivo | Full packet capture indexado |

### SIEM y monitorización

| Proyecto | ⭐ | RAM | Rol |
|---|---:|---|---|
| [wazuh/wazuh](https://github.com/wazuh/wazuh) | 17 007 | ~8 GB | SIEM + HIDS + gestión de vulnerabilidades |
| [zabbix/zabbix](https://github.com/zabbix/zabbix) | 6 416 | ~2 GB | Monitorización de infraestructura |
| [librenms/librenms](https://github.com/librenms/librenms) | 4 908 | ~2 GB | Autodescubrimiento por SNMP |
| [netbox-community/netbox](https://github.com/netbox-community/netbox) | 21 606 | ~2 GB | IPAM + DCIM. Fuente de verdad |

### Emulación de topologías

| Proyecto | ⭐ | Rol |
|---|---:|---|
| **GNS3** | — | Emular topologías reales antes de tocar hardware |
| [hegdepavankumar/Cisco-Images-for-GNS3-and-EVE-NG](https://github.com/hegdepavankumar/Cisco-Images-for-GNS3-and-EVE-NG) | 2 848 | Imágenes de routers y switches |
| **VirtualBox** o **Proxmox VE** | — | Hipervisor |

### Referencia

[khuedoan/homelab](https://github.com/khuedoan/homelab) (9 637 ⭐) — de disco vacío a cluster funcionando, todo automatizado. La referencia del género.

*Datos de estrellas verificados en septiembre de 2026.*

---

## Documentación

| | Estado |
|---|---|
| [docs/arquitectura.md](docs/arquitectura.md) | 🔜 Diseño de red, VLANs y segmentación |
| [docs/hardware.md](docs/hardware.md) | 🔜 Requisitos y opciones de equipamiento |
| [docs/software.md](docs/software.md) | 🔜 Stack por capas y orden de despliegue |
| [docs/roadmap.md](docs/roadmap.md) | 🔜 Fases, de lo mínimo a lo completo |

---

## Principios

**Nada de credenciales ni datos reales en este repo.** Ni IPs de producción, ni comunidades SNMP, ni configuraciones de cliente. Los ejemplos usan rangos de documentación (RFC 5737: `192.0.2.0/24`, `198.51.100.0/24`, `203.0.113.0/24`).

**Infraestructura como código donde tenga sentido.** Si un servicio se puede levantar con un `docker-compose.yml` o un playbook versionado, no se documenta «cómo lo hice clicando».

**Segmentación desde el primer día.** Un lab plano no enseña nada sobre seguridad de redes. VLANs y políticas entre zonas desde el diseño inicial.

**Documentar el porqué, no solo el cómo.** El comando se busca en dos minutos; la razón por la que elegiste esa arquitectura, no.

---

## Licencia

MIT — ver [LICENSE](LICENSE).
