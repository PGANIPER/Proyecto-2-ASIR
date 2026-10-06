# Proyecto-2-ASIR

![Estado](https://img.shields.io/badge/estado-en%20dise%C3%B1o-yellow)
![Ciclo](https://img.shields.io/badge/ciclo-ASIR%202026--2027-blue)
![Licencia](https://img.shields.io/badge/licencia-MIT-green)

**Mini-SOC para una PYME: aplicación web Flask y MySQL protegida con pfSense, vigilada con Wazuh y con alertas explicadas por una IA local.**

Proyecto Intermodular del Ciclo Formativo de Grado Superior en Administración de Sistemas Informáticos en Red (ASIR), curso 2026-2027.

## De qué va

El proyecto simula la red de una asesoría pequeña (GestiNova Asesores S.L., una empresa ficticia de doce empleados) que trabaja con datos de clientes y no tiene ni segmentación de red ni forma de saber si alguien está intentando entrar. Sobre ese escenario monto un Mini-SOC completo, todo virtualizado y con software gratuito:

- Un cortafuegos **pfSense** que separa la red en tres zonas (WAN, DMZ y LAN).
- Una aplicación web en **Flask** con base de datos **MySQL** y login con roles (administrador y operador).
- **Wazuh** como SIEM/EDR, con agentes en el servidor web y en un cliente Windows.
- Un script que lleva las alertas de Wazuh a MySQL y un modelo de lenguaje local (**Ollama**) que las explica en castellano dentro del portal.
- Una máquina **Kali Linux** que hace de atacante externo para generar incidentes reales dentro del laboratorio.

## Arquitectura

```
Internet ─── [Red WAN] ─┬─ pfSense ─┬─ DMZ ─── SRV-WEB
                        │           │
                        └─ Kali     └─ LAN ─┬─ SRV-WAZUH
                                            └─ CLI-WIN
```

| Máquina | Zona | Sistema | Función |
|---|---|---|---|
| pfSense | WAN, DMZ y LAN | pfSense CE | Cortafuegos, router, NAT y DHCP |
| SRV-WEB | DMZ | Ubuntu Server | Flask, MySQL, Ollama y agente de Wazuh |
| SRV-WAZUH | LAN | Ubuntu Server | Servidor, indexador y panel de Wazuh |
| CLI-WIN | LAN | Windows | Equipo de empleado con agente de Wazuh |
| Kali | WAN | Kali Linux | Atacante externo (solo contra el laboratorio) |

Las reglas entre zonas dejan pasar solo lo imprescindible: los puertos de los agentes de Wazuh (1514 y 1515) desde la DMZ hacia SRV-WAZUH, y el volcado de alertas a MySQL desde la LAN hacia la DMZ.

## Tecnologías

| Categoría | Herramientas |
|---|---|
| Virtualización | VirtualBox |
| Cortafuegos | pfSense Community Edition |
| Sistemas operativos | Ubuntu Server 24.04 LTS, Windows (imagen de evaluación) y Kali Linux |
| Monitorización | Wazuh 4.x |
| Base de datos | MySQL 8.0 |
| Aplicación web | Python 3, Flask, HTML, CSS y JavaScript |
| IA local | Ollama con un modelo ligero de la familia llama3.2 |
| Herramientas de trabajo | VS Code (Remote-SSH), Git y GitHub |

## Estructura del repositorio

```
mini-soc-asir/
├── docs/          Anteproyecto, diseño de red y memoria final
├── infra/         Notas y configuración de pfSense y Wazuh
├── src/
│   ├── web/       Aplicación Flask (login, roles y panel de alertas)
│   └── scripts/   Volcado de alertas Wazuh → MySQL y cliente de Ollama
├── database/      Esquema y datos iniciales en SQL
└── pruebas/       Registro de los ataques simulados y sus resultados
```

## Estado y hoja de ruta

El proyecto está en la fase de diseño. La planificación completa está en el [anteproyecto](docs/anteproyecto.md).

**Fase 1. Análisis y diseño (semanas 1 a 5)**
- [x] Crear el repositorio
- [x] Redactar el anteproyecto
- [ ] Diseñar la topología y el plan de direccionamiento (`docs/diseno-red.md`)
- [ ] Definir el modelo de datos de MySQL (`database/`)

**Fase 2. Implementación y pruebas (semanas 6 a 23)**
- [ ] VirtualBox, pfSense y zonas de red
- [ ] SRV-WEB con Ubuntu Server y MySQL
- [ ] Prototipo Flask con login y roles
- [ ] Wazuh, agentes, cliente Windows y Kali
- [ ] Volcado de alertas a MySQL, portal y pruebas de ataque
- [ ] Ollama, explicaciones de alertas y medición de consumo

**Fase 3. Documentación (semanas 24 a 27)**
- [ ] Memoria final y manual de despliegue

**Fase 4. Defensa (semanas 28 a 30)**
- [ ] Presentación y demostración

## Requisitos para reproducirlo

El laboratorio está pensado para un equipo con 32 GB de RAM: las cinco máquinas virtuales reservan unos 22 GB y unos 210 GB de disco (discos dinámicos). Hace falta la virtualización por hardware activada en la BIOS. El manual de despliegue paso a paso se irá añadiendo a `docs/` según avance el proyecto.

## Aviso

Todos los ataques de este proyecto se lanzan dentro de una red virtual aislada, contra máquinas propias y con fines formativos. En el repositorio no se suben contraseñas ni claves: las credenciales van en un archivo `.env` que está excluido por `.gitignore`, y solo se incluye `.env.example` con valores de ejemplo.

## Autor

Daniel Villaclara Moreno ([@PGANIPER](https://github.com/PGANIPER)), estudiante de 2.º de ASIR.

## Licencia

Distribuido bajo licencia MIT. Consulta el archivo [LICENSE](LICENSE).
