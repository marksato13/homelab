# Arquitectura

> Estado: 🔜 **planificado**. Diseño sobre papel, nada montado.

Todos los direccionamientos de este documento usan los rangos reservados para documentación de la **RFC 5737** (`192.0.2.0/24`, `198.51.100.0/24`, `203.0.113.0/24`). No son direcciones reales de ninguna red.

---

## Principio de diseño

**Un lab plano no enseña nada sobre seguridad de redes.** Si todo vive en la misma VLAN, nunca escribes una política entre zonas, nunca depuras por qué un flujo no pasa y nunca ves la diferencia entre permitir y registrar.

La segmentación es el ejercicio, no un adorno.

---

## Zonas

| VLAN | Nombre | Rango | Qué contiene |
|---:|---|---|---|
| 10 | GESTIÓN | `192.0.2.0/24` | Interfaces de gestión de switches, hipervisor, IPMI |
| 20 | SERVICIOS | `198.51.100.0/24` | Wazuh, Zabbix, NetBox, LibreNMS |
| 30 | LAB | `203.0.113.0/24` | VMs desechables, topologías GNS3 |
| 40 | DMZ | `192.0.2.128/25` | Lo que se expone, si algo se expone |
| 99 | CAPTURA | sin IP | Puerto espejo. Interfaz en promiscuo, sin direccionamiento |

**La VLAN 99 no lleva IP a propósito.** Una interfaz de captura con dirección es una interfaz atacable y, peor, una que puede generar tráfico y contaminar lo que estás midiendo.

---

## Reglas entre zonas

La matriz que se implementará en el firewall. `LOG` significa permitido pero registrado.

| Origen → Destino | GESTIÓN | SERVICIOS | LAB | DMZ | Internet |
|---|:---:|:---:|:---:|:---:|:---:|
| **GESTIÓN** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **SERVICIOS** | ❌ | ✅ | LOG | ❌ | ✅ |
| **LAB** | ❌ | LOG | ✅ | ❌ | LOG |
| **DMZ** | ❌ | ❌ | ❌ | ✅ | ✅ |

Las decisiones que importan:

**LAB no alcanza GESTIÓN.** Es el punto entero del diseño: una VM comprometida en el laboratorio no debe poder tocar el hipervisor ni los switches.

**DMZ no inicia nada hacia dentro.** Solo responde.

**SERVICIOS sí sale a Internet** porque Wazuh y Zabbix necesitan actualizar feeds y firmas.

---

## Firewall

**pfSense** u **OPNsense** como VM sobre el hipervisor, con las VLANs en troncal desde el switch.

Ventaja de virtualizarlo: rehaces la topología sin tocar un cable, y puedes tener snapshots antes de cada cambio de política.

Ventaja de tenerlo físico: no dependes de que el hipervisor esté encendido para tener red. Para un lab, la VM compensa.

---

## Captura de tráfico

```
   [ Switch gestionable ]
      |            |
   puertos      puerto espejo (SPAN)
   normales          |
                     v
            NIC en promiscuo, sin IP
                     |
              [ VM de sensor ]
               Zeek + Suricata
                     |
                     v
              [ Malcolm / SIEM ]
```

El sensor tiene **dos interfaces**: una en la VLAN 99 para capturar (promiscua, sin IP) y otra en GESTIÓN para administrarlo y enviar los logs.

Sin puerto espejo en el switch no hay captura posible de tráfico ajeno. Es el requisito de hardware que no se puede sustituir por software.

---

## Orden de despliegue

Cada capa depende de la anterior:

1. **Hipervisor** — sin esto no hay nada
2. **Firewall + VLANs** — la segmentación antes que los servicios, no después
3. **NetBox** — documentar el direccionamiento desde el principio, no cuando ya no te acuerdas
4. **Zabbix** — visibilidad de disponibilidad
5. **Wazuh** — SIEM y agentes
6. **Sensor Zeek + Suricata** — requiere que el puerto espejo ya esté configurado
7. **Malcolm** — el último: consume lo que los anteriores generan

Montar Malcolm antes de tener segmentación es tener un analizador de tráfico sin nada interesante que analizar.
