# Proyecto Final · DataExplorer

Vagrant + Ansible · Nginx · Prometheus · Grafana · Node Exporter · Blackbox Exporter · JMeter

## Descripción General

El proyecto **DataExplorer** consiste en la creación de un entorno de virtualización automatizado utilizando **Vagrant** y **Ansible**, con el despliegue de servicios modernos de administración y monitoreo:

- **Nginx** como servidor web.
- **Prometheus** para la recolección de métricas.
- **Grafana** para la visualización de datos y dashboards provisionados automáticamente.
- **Node Exporter** para obtener métricas del sistema.
- **Blackbox Exporter** para medir latencia HTTP del servidor Nginx.
- **JMeter** para pruebas de carga automatizadas desde el nodo `control`.

Este entorno se compone de **tres máquinas virtuales** interconectadas en la misma red privada.

---

## Infraestructura de Red

| IP              | Nombre / Rol | Servicios Instalados                                  |
| --------------- | ------------ | ----------------------------------------------------- |
| `192.168.10.10` | control      | Ansible, JMeter (pruebas de carga)                    |
| `192.168.10.11` | server       | Nginx, Node Exporter                                  |
| `192.168.10.12` | monitor      | Prometheus, Node Exporter, Grafana, Blackbox Exporter |

---

## Requisitos

- Vagrant 2.3+
- VirtualBox o libvirt + vagrant-libvirt
- Ansible 2.14+
- Git, curl
- Host con ~4 vCPU y ~8 GB RAM libres

- **Sistema Operativo:** Windows, Linux o macOS
- **Software necesario:**
  - [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
  - [Vagrant](https://developer.hashicorp.com/vagrant/downloads)
  - [Git](https://git-scm.com/downloads)
  - [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/)
- **Hardware sugerido:** 4 CPU, 8 GB RAM, 25 GB libres.

---

## Estructura del Proyecto

```
DataExplorer/
├─ docs/
│  ├─ proyectoFinal.pdf
│  └─ RequerimientosProyectoFinalDataExplorer.pdf
├─ Vagrantfile
├─ provisioning/
│  ├─ inventory/
│  │  └─ hosts.ini
│  ├─ group_vars/
│  │  ├─ all.yml                 # puertos y targets (node_exporter, blackbox)
│  │  ├─ server.yml
│  │  └─ monitor.yml
│  ├─ roles/
│  │  ├─ nginx/
│  │  │  └─ tasks/main.yml
│  │  ├─ node_exporter/
│  │  │  └─ tasks/main.yml
│  │  ├─ prometheus/
│  │  │  ├─ tasks/main.yml
│  │  │  ├─ templates/prometheus.yml.j2
│  │  │  └─ handlers/main.yml
│  │  ├─ grafana/
│  │  │  ├─ tasks/main.yml
│  │  │  └─ files/
│  │  │     ├─ dashboards.yml
│  │  │     └─ dashboards/dataexplorer-dashboard.json
│  │  ├─ blackbox_exporter/
│  │  │  └─ tasks/main.yml
│  │  └─ jmeter/
│  │     ├─ tasks/main.yml
│  │     └─ files/dataexplorer-nginx.jmx
│  ├─ site.yml                   # play principal (control / server / monitor)
│  └─ verify.yml                 # PRUEBAS automáticas (URI checks + JMeter)
└─ README.md
```

---

## Pasos para Implementar

### Clonar Repositorio y Proyecto

```
git clone https://github.com/driosoft-pro/data-explore-virtualizacion-automatizada.git
```

```
cd data-explore-virtualizacion-automatizada
```

### 1) Levantar VMs

- Windows/macOS:

```
vagrant up
```

- Linux (libvirt):

```
VAGRANT_DEFAULT_PROVIDER=libvirt vagrant up --provider=libvirt
```

## Mas opciones

```
vagrant up --no-parallel
```

### O

```
vagrant up control
```

```
vagrant up monitor
```

```
vagrant up server
```

### 2) Generar SSH config

- Bash/Zsh:

```
vagrant ssh-config control server monitor > .vagrant/ssh-config
```

- Nushell:

```
vagrant ssh-config control server monitor | save -f .vagrant/ssh-config
```

- PowerShell:

(Se debe ejecutar en 3 pasos, usando -Append para añadir la información)

```
vagrant ssh-config control | Out-File -Encoding ascii .vagrant/ssh-config
```

```
vagrant ssh-config server | Out-File -Encoding ascii -Append .vagrant/ssh-config
```

```
vagrant ssh-config monitor | Out-File -Encoding ascii -Append .vagrant/ssh-config
```

### 3) Validar inventario y conectividad

```
ansible-inventory -i provisioning/inventory/hosts.ini --graph
```

```
ansible -i provisioning/inventory/hosts.ini all -m ping
```

### 4) Provisionar

```
ansible-playbook -i provisioning/inventory/hosts.ini provisioning/site.yml -b
```

#### Esto instala y configura:

- control: JMeter CLI y plan de prueba.
- server: Nginx + Node Exporter.
- monitor: Prometheus + Node Exporter + Grafana + Blackbox Exporter.
- Dashboard de Grafana provisionado automáticamente (DataExplorer - Nginx / Node / Prometheus).

### 5) Verificar servicios

```
ansible-playbook -i provisioning/inventory/hosts.ini provisioning/verify.yml -b
```

#### Este playbook verifica:

- Nginx responde 200 en server.
- Node Exporter expone métricas en server y monitor.
- Prometheus está listo en monitor.
- Grafana está disponible en monitor.
- Blackbox Exporter expone /metrics en monitor.
- JMeter ejecuta una prueba de carga desde control contra http://192.168.10.11/ y genera results.jtl.

### 6) Probar en el navegador

- Nginx: http://192.168.10.11/
- Prometheus: http://192.168.10.12:9090/
- Grafana: http://192.168.10.12:3000/ (admin/admin)

### El dashboard DataExplorer se carga automáticamente en Grafana y muestra:

- Uso de CPU (%)
- Uso de RAM (%)
- Load Average (1/5/15)
- Tráfico de red RX/TX (Bytes/s)
- Latencia HTTP (probe_duration_seconds) medida con Blackbox Exporter.

## Comandos útiles

```
vagrant status
```

```
vagrant reload
```

```
vagrant halt
```

```
vagrant destroy -f
```

---

## Verificación de Servicios

| Servicio                | Dirección                         | Descripción                              |
| ----------------------- | --------------------------------- | ---------------------------------------- |
| Nginx                   | http://192.168.10.11/             | Página web principal                     |
| Node Exporter (Server)  | http://192.168.10.11:9100/metrics | Métricas del servidor                    |
| Node Exporter (Monitor) | http://192.168.10.12:9100/metrics | Métricas del monitor                     |
| Prometheus              | http://192.168.10.12:9090/        | Panel de métricas / targets y consultas  |
| Grafana                 | http://192.168.10.12:3000/        | Panel y Dashboards (admin/admin)         |
| Blackbox Exporter       | http://192.168.10.12:9115/metrics | Probing HTTP externo (latencia de Nginx) |

---

## Objetivo

#### Desplegar 3 VMs en una red privada 192.168.10.0/24:

- control (192.168.10.10) – nodo de apoyo con JMeter (pruebas de carga).
- server (192.168.10.11) – Nginx + Node Exporter.
- monitor (192.168.10.12) – Prometheus + Grafana + Node Exporter + Blackbox Exporter.

## Descripción General

El proyecto **DataExplorer** consiste en la creación de un entorno de virtualización automatizado utilizando **Vagrant** y **Ansible**, con el despliegue de servicios modernos de administración y monitoreo:

- **Nginx** como servidor web.
- **Prometheus** para la recolección de métricas.
- **Grafana** para la visualización de datos.
- **Node Exporter** para obtener métricas del sistema.

Este entorno se compone de **tres máquinas virtuales** interconectadas en la misma red privada.

##### Todo el entorno se levanta y verifica con dos comandos:

```
vagrant up
```

```
ansible-playbook -i provisioning/inventory/hosts.ini provisioning/site.yml provisioning/verify.yml -b
```

---

## Licencia

Proyecto educativo bajo licencia **MIT**. Uso libre con fines de aprendizaje.

---

## ✍️ Autores

- **Deyton Riasco Ortiz** — driosoftpro@gmail.com
- **Samuel Izquierdo Bonilla** — samuelizquierdo98@gmail.com
- **Daniel David Garcia Restrepo** — daniel_dav.garcia@uao.edu.co
- **Luisa FernandaMuñoz Cardona** — luisa_fer.munoz@uao.edu.co
- **Dana Isabella Mosquera Mosquera** — danna_isa.mosquera@uao.edu.co

  **Año:** 2025
