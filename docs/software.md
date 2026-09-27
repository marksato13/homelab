# Stack de software

> Estado: 🔜 **planificado**.

Organizado por capas. Cada una depende de la anterior; el orden de la tabla es el orden de despliegue.

---

## Capa 1 — Base

| Componente | Opción | Nota |
|---|---|---|
| Hipervisor | **Proxmox VE** | Gratuito, KVM + LXC, gestión web |
| Firewall | **pfSense** u **OPNsense** | Como VM, con VLANs en troncal |
| Switch | Gestionable con **SPAN** | Requisito no negociable para captura |

---

## Capa 2 — Fuente de verdad

| Componente | ⭐ | Por qué primero |
|---|---:|---|
| [NetBox](https://github.com/netbox-community/netbox) | 21 606 | Documentar el direccionamiento **antes** de que crezca. Después nadie lo rellena |

Levanta con `netbox-docker`: NetBox + PostgreSQL + Redis, unos 2 GB.

---

## Capa 3 — Monitorización

| Componente | ⭐ | Rol |
|---|---:|---|
| [Zabbix](https://github.com/zabbix/zabbix) | 6 416 | Disponibilidad, umbrales, alertas |
| [LibreNMS](https://github.com/librenms/librenms) | 4 908 | Autodescubrimiento por SNMP, gráficas |
| [prometheus/snmp_exporter](https://github.com/prometheus/snmp_exporter) | 2 177 | Métricas SNMP hacia Prometheus/Grafana |
| [akpw/mktxp](https://github.com/akpw/mktxp) | 1 085 | Exporter específico de MikroTik |

Zabbix y LibreNMS se solapan. **Elegir uno** para empezar: Zabbix si quieres control fino de las plantillas, LibreNMS si prefieres que descubra solo.

---

## Capa 4 — Seguridad

| Componente | ⭐ | RAM | Rol |
|---|---:|---|---|
| [Wazuh](https://github.com/wazuh/wazuh) | 17 007 | ~8 GB | SIEM, HIDS, gestión de vulnerabilidades, FIM |
| [Suricata](https://github.com/OISF/suricata) | 6 673 | ~2 GB | IDS/IPS por firmas |
| [Zeek](https://github.com/zeek/zeek) | 8 008 | ~2 GB | Logs estructurados de comportamiento de red |

**Zeek y Suricata no compiten, se complementan.** Suricata dispara con una firma conocida: «esto es tal malware». Zeek no juzga, describe: cada conexión, cada certificado TLS, cada consulta DNS, en logs consultables. Para detectar lo que *no* tiene firma todavía, necesitas los de Zeek.

---

## Capa 5 — Análisis de tráfico

| Componente | ⭐ | RAM | Rol |
|---|---:|---|---|
| [Malcolm](https://github.com/cisagov/Malcolm) | 2 532 | 16 GB | Suite completa con dashboards hechos |
| [Arkime](https://github.com/arkime/arkime) | 7 498 | 8 GB+ | Full packet capture indexado |
| [ntopng](https://github.com/ntop/ntopng) | 8 209 | ~500 MB | Visión en tiempo real, ligero |

**Malcolm ya incluye Zeek, Suricata, Arkime y OpenSearch.** Si vas a montarlo, no despliegues esos por separado: instala Malcolm y ahórrate integrar cuatro piezas a mano.

`ntopng` es la excepción ligera: cabe incluso en el portátil y da visión inmediata sin montar nada más.

---

## Capa 6 — Emulación

| Componente | Rol |
|---|---|
| **GNS3** | Emular topologías reales con imágenes de vendor |
| [Cisco-Images-for-GNS3-and-EVE-NG](https://github.com/hegdepavankumar/Cisco-Images-for-GNS3-and-EVE-NG) | Imágenes de routers y switches |
| **EVE-NG** | Alternativa a GNS3, mejor con topologías grandes |

Aquí se prueban los cambios antes de tocar equipamiento real.

---

## Automatización transversal

| Componente | ⭐ | Rol |
|---|---:|---|
| [Ansible](https://github.com/ansible/ansible) | 70 794 | Configuración de VMs y dispositivos. **Desde un bastión, nunca desde un portátil** |
| [Oxidized](https://github.com/ytti/oxidized) | 3 592 | Backup de configs con histórico en Git |
| [Batfish](https://github.com/batfish/batfish) | 1 487 | Validar configuraciones **antes** de aplicarlas |

Oxidized y Batfish también caben en el portátil (ver [neteng-workstation](https://github.com/marksato13/neteng-workstation)). En el lab tiene más sentido que corran permanentemente.

---

## Lo que NO va al lab

| | Por qué |
|---|---|
| Herramientas ofensivas de diario | Van en una VM aislada y desechable, con snapshot para revertir |
| Servicios expuestos a Internet | Un lab no es producción. Si algo debe exponerse, va a DMZ y con criterio |
| Datos reales de cliente | Nunca. Ni en el lab ni en este repositorio |

*Estrellas verificadas en septiembre de 2026.*
