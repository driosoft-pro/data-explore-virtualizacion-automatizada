# Proyecto Final: DataExplorer (Vagrant + Ansible + Nginx + Prometheus + Grafana)

## Descripción General

El proyecto **DataExplorer** consiste en la creación de un entorno de virtualización automatizado utilizando **Vagrant** y **Ansible**, con el despliegue de servicios modernos de administración y monitoreo:

- **Nginx** como servidor web.
- **Prometheus** para la recolección de métricas.
- **Grafana** para la visualización de datos.
- **Node Exporter** para obtener métricas del sistema.

Este entorno se compone de **tres máquinas virtuales** interconectadas en la misma red privada.

---

## 🖥️ Infraestructura de Red

| IP             | Nombre / Rol | Servicios Instalados               |
| -------------- | ------------ | ---------------------------------- |
| `192.168.10.1` | control      | Ansible, Playbooks                 |
| `192.168.10.2` | server       | Nginx, Node Exporter               |
| `192.168.10.3` | monitor      | Prometheus, Node Exporter, Grafana |

---

## ⚙️ Requisitos Previos

- **Sistema Operativo:** Windows, Linux o macOS
- **Software necesario:**
  - [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
  - [Vagrant](https://developer.hashicorp.com/vagrant/downloads)
  - [Git](https://git-scm.com/downloads)
  - [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/)
- **Hardware sugerido:** 4 CPU, 8 GB RAM, 25 GB libres.

---

## 📂 Estructura del Proyecto

```
DataExplorer/
├─ Vagrantfile
├─ provisioning/
│  ├─ inventory/
│  │  └─ hosts.ini
│  ├─ group_vars/
│  │  ├─ all.yml
│  │  ├─ server.yml
│  │  └─ monitor.yml
│  ├─ roles/
│  │  ├─ nginx/
│  │  │  └─ tasks/main.yml
│  │  ├─ node_exporter/
│  │  │  └─ tasks/main.yml
│  │  ├─ prometheus/
│  │  │  └─ tasks/main.yml
│  │  └─ grafana/
│  │     └─ tasks/main.yml
│  └─ site.yml
└─ README.md
```

---

## Pasos para Implementar

### Crear Repositorio y Proyecto

```bash
mkdir DataExplorer && cd DataExplorer
git init
git remote add origin https://github.com/<usuario>/DataExplorer.git
```

### Configurar Vagrantfile

Crea un `Vagrantfile` que defina las tres VMs con las IPs y recursos descritos, compartiendo el directorio de Ansible con la VM **control**.

### Crear Inventario (inventory/hosts.ini)

```ini
[control]
192.168.10.1 ansible_user=vagrant ansible_ssh_private_key_file=.vagrant/machines/control/virtualbox/private_key

[server]
192.168.10.2 ansible_user=vagrant ansible_ssh_private_key_file=.vagrant/machines/server/virtualbox/private_key

[monitor]
192.168.10.3 ansible_user=vagrant ansible_ssh_private_key_file=.vagrant/machines/monitor/virtualbox/private_key

[all:vars]
ansible_python_interpreter=/usr/bin/python3
```

### Playbook Principal (`site.yml`)

```yaml
- hosts: server
  become: true
  roles:
    - nginx
    - node_exporter

- hosts: monitor
  become: true
  roles:
    - prometheus
    - node_exporter
    - grafana
```

### Ejecutar el Aprovisionamiento

```bash
vagrant up
vagrant ssh control
cd /vagrant/provisioning
ansible -i inventory/hosts.ini all -m ping
ansible-playbook -i inventory/hosts.ini site.yml
```

---

## 🔍 Verificación de Servicios

| Servicio                | Dirección                        | Descripción                          |
| ----------------------- | -------------------------------- | ------------------------------------ |
| Nginx                   | http://192.168.10.2/             | Página web principal                 |
| Node Exporter (Server)  | http://192.168.10.2:9100/metrics | Métricas del servidor                |
| Node Exporter (Monitor) | http://192.168.10.3:9100/metrics | Métricas del monitor                 |
| Prometheus              | http://192.168.10.3:9090/        | Panel de métricas                    |
| Grafana                 | http://192.168.10.3:3000/        | Panel de visualización (admin/admin) |

---

## Buenas Prácticas

- Ejecutar `ansible-lint` para validar YAMLs.
- Asegurar conectividad entre máquinas antes de correr el playbook.
- Crear snapshots de las VMs con `vagrant snapshot save` antes de probar cambios.

---

## Entregables Finales

1. **Repositorio GitHub** con todo el código y archivos YAML.
2. **Documento PDF** explicativo con capturas del proceso y resultados.
3. **Video (10 min máx)** mostrando:
   - Funcionamiento del servidor web.
   - Monitoreo en Prometheus.
   - Dashboards en Grafana.

---

## Licencia

Proyecto educativo bajo licencia **MIT**. Uso libre con fines de aprendizaje.

---

## ✍️ Autores

- **Deyton Riasco Ortiz** — driosoftpro@gmail.com
- **Samuel Izquierdo Bonilla** — samuelizquierdo98@gmail.com
- **Daniel David Garcia Restrepo** — 
- **Luisa FernandaMuñoz Cardona** — 
- **Dana Isabella Mosquera Mosquera** — 
  
  **Año:** 2025
