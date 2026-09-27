# Roadmap

> Estado: 🔜 **planificado**. Sin fechas: se marcan como completadas cuando lo estén.

Cuatro fases. Cada una deja algo **funcionando y útil** por sí sola, en vez de exigir que todo esté listo para que sirva de algo.

---

## Fase 0 — Decidir el hardware ⬜

Nada empieza hasta que haya máquina.

- [ ] Elegir plataforma (ver [hardware.md](hardware.md))
- [ ] Confirmar **32 GB de RAM mínimo**
- [ ] Confirmar switch gestionable **con puerto espejo (SPAN)**
- [ ] Confirmar **2 NICs**: gestión y captura por separado

**Criterio de salida:** hardware en la mesa y encendido.

---

## Fase 1 — Base y segmentación ⬜

La red antes que los servicios. Si se hace al revés, luego hay que rehacerlo todo.

- [ ] Instalar hipervisor
- [ ] Desplegar pfSense/OPNsense como VM
- [ ] Crear las VLANs de [arquitectura.md](arquitectura.md)
- [ ] Implementar la matriz de reglas entre zonas
- [ ] Verificar la segmentación: **comprobar que LAB no alcanza GESTIÓN**

**Criterio de salida:** una VM en LAB no puede llegar al hipervisor, y está demostrado con una prueba, no supuesto.

---

## Fase 2 — Documentar y vigilar ⬜

- [ ] NetBox con el direccionamiento y el inventario cargados
- [ ] Zabbix **o** LibreNMS (uno, no los dos)
- [ ] Agentes SNMP en switches y firewall
- [ ] Alertas básicas: caída de host, saturación de enlace, disco

**Criterio de salida:** desconectar un switch dispara una alerta que llega.

---

## Fase 3 — Seguridad y captura ⬜

- [ ] Configurar el puerto espejo en el switch
- [ ] VM sensor con dos interfaces (captura promiscua + gestión)
- [ ] Wazuh: manager, indexer, dashboard
- [ ] Agentes Wazuh en las VMs
- [ ] Zeek y Suricata sobre la interfaz de captura
- [ ] Validar que el sensor ve tráfico que no va dirigido a él

**Criterio de salida:** generas tráfico entre dos VMs y aparece en los logs de Zeek.

---

## Fase 4 — Análisis y emulación ⬜

- [ ] Malcolm (sustituye a Zeek/Suricata/Arkime sueltos)
- [ ] Ingesta de PCAP históricos
- [ ] GNS3 con imágenes de vendor
- [ ] Reproducir una topología real en GNS3
- [ ] Oxidized con backup de configs en Git

**Criterio de salida:** un PCAP entra por Malcolm y sale un dashboard que dice algo útil.

---

## Después

Ideas sin compromiso, por orden de interés:

- **Generar tráfico anómalo controlado** para validar detección — la parte difícil y la más interesante
- **Automatizar el despliegue con Ansible** para poder rehacer el lab entero desde cero
- **Comparar Zeek + ML contra Suricata por firmas** sobre el mismo tráfico
- **Documentar cada laboratorio** como entrada reproducible: objetivo, montaje, resultado

---

## Reglas de este roadmap

**Sin fechas inventadas.** Una casilla se marca cuando está hecha y verificada.

**Cada fase deja algo utilizable.** Si la fase 2 se queda sola, ya tienes monitorización funcionando.

**El criterio de salida es una prueba, no una sensación.** «Segmentación configurada» no vale; «una VM en LAB no llega al hipervisor y lo he comprobado» sí.
