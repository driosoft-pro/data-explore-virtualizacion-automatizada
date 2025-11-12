# Proyecto Final · DataExplorer

Vagrant + Ansible · Nginx · Prometheus · Grafana · Node Exporter

## Descripción General

El proyecto **DataExplorer** consiste en la creación de un entorno de virtualización automatizado utilizando **Vagrant** y **Ansible**, con el despliegue de servicios modernos de administración y monitoreo:

- **Nginx** como servidor web.
- **Prometheus** para la recolección de métricas.
- **Grafana** para la visualización de datos.
- **Node Exporter** para obtener métricas del sistema.

Este entorno se compone de **tres máquinas virtuales** interconectadas en la misma red privada.

---

## Infraestructura de Red

| IP              | Nombre / Rol | Servicios Instalados               |
| --------------- | ------------ | ---------------------------------- |
| `192.168.10.10` | control      | Ansible, Playbooks                 |
| `192.168.10.11` | server       | Nginx, Node Exporter               |
| `192.168.10.12` | monitor      | Prometheus, Node Exporter, Grafana |

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
│  │  ├─ all.yml                 # puertos y targets (node_exporter)
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
│  │  └─ grafana/
│  │     └─ tasks/main.yml
│  ├─ site.yml                   # play principal (server / monitor)
│  └─ verify.yml                 # PRUEBAS automáticas (URI checks)
└─ README.md

```

---

## Pasos para Implementar

### Clonar Repositorio y Proyecto

```bash
git clone git@github.com:driosoft-pro/data-explore-virtualizacion-automatizada.git
```

```bash
cd data-explore-virtualizacion-automatizada
```

### 1) Levantar VMs

- Windows/macOS:

```bash
vagrant up
```

- Linux (libvirt):

```bash
VAGRANT_DEFAULT_PROVIDER=libvirt vagrant up --provider=libvirt
```

### 2) Generar SSH config

- Bash/Zsh:

```bash
vagrant ssh-config control server monitor > .vagrant/ssh-config
```

- Nushell:

```nu
vagrant ssh-config control server monitor | save -f .vagrant/ssh-config
```

- PowerShell:

(Se debe ejecutar en 3 pasos, usando -Append para añadir la información)

```vagrant ssh-config control | Out-File -Encoding ascii .vagrant/ssh-config
```

```vagrant ssh-config server | Out-File -Encoding ascii -Append .vagrant/ssh-config
```

```vagrant ssh-config monitor | Out-File -Encoding ascii -Append .vagrant/ssh-config
```

### 3) Validar inventario y conectividad

```bash
ansible-inventory -i provisioning/inventory/hosts.ini --graph
```

```bash
ansible -i provisioning/inventory/hosts.ini all -m ping
```

### 4) Provisionar

```bash
ansible-playbook -i provisioning/inventory/hosts.ini provisioning/site.yml -b --check --diff
```

```bash
ansible-playbook -i provisioning/inventory/hosts.ini provisioning/site.yml -b
```

### 5) Verificar servicios

```bash
ansible-playbook -i provisioning/inventory/hosts.ini provisioning/verify.yml -b
```

### 6) Probar en el navegador

- Nginx: http://192.168.10.11/
- Prometheus: http://192.168.10.12:9090/
- Grafana: http://192.168.10.12:3000/ (admin/admin)

## Comandos útiles

```bash
vagrant status
```

```bash
vagrant reload
```

```bash
vagrant halt
```

```bash
vagrant destroy -f
```

---

## Verificación de Servicios

| Servicio                | Dirección                         | Descripción                          |
| ----------------------- | --------------------------------- | ------------------------------------ |
| Nginx                   | http://192.168.10.11/             | Página web principal                 |
| Node Exporter (Server)  | http://192.168.10.11:9100/metrics | Métricas del servidor                |
| Node Exporter (Monitor) | http://192.168.10.12:9100/metrics | Métricas del monitor                 |
| Prometheus              | http://192.168.10.12:9090/        | Panel de métricas                    |
| Grafana                 | http://192.168.10.12:3000/        | Panel de visualización (admin/admin) |

---

## Objetivo

Desplegar 3 VMs en una red privada 192.168.10.0/24:

- control (192.168.10.10) – nodo de apoyo (opcional)
- server (192.168.10.11) – Nginx + Node Exporter
- monitor (192.168.10.12) – Prometheus + Grafana + Node Exporter

---

## Multi-provider (Windows/macOS/Linux)

- Windows/macOS (VirtualBox): usa la box ubuntu/jammy64.
- Linux (KVM/libvirt): usa la box generic/ubuntu2204.

El mismo repo funciona para ambos; para SSH usamos el ssh-config de Vagrant (portable), así no hardcodeamos rutas de llaves.

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
