# 🔧 Proyecto de Automatización con Vagrant y Ansible

Este proyecto configura automáticamente un entorno de infraestructura local utilizando **Vagrant** y **Ansible**, compuesto por **tres máquinas virtuales**:

- 🧠 `ansible`: nodo de control, donde se instala Ansible y desde donde se ejecutan los playbooks.

- 🖥️ `node1` y `node2`: nodos gestionados por Ansible, donde se instalan y configuran servicios.

---

## 📦 Requisitos previos

Para correr este proyecto necesitás tener instalado en tu máquina:

- [Vagrant](https://www.vagrantup.com/) (versión recomendada: `>= 2.4`)
- [VirtualBox](https://www.virtualbox.org/) u otro provider compatible

> Nota: **No necesitás tener Ansible instalado en tu máquina**. Ansible se instala automáticamente dentro de la VM `ansible`.

---

## 🖥️ Infraestructura creada

Cuando ejecutás `vagrant up`, se crean 3 VMs con IPs privadas:

| Hostname | IP             | Rol                 |
| -------- | -------------- | ------------------- |
| ansible  | 192.168.50.1   | Controlador Ansible |
| node1    | 192.168.50.100 | Nodo gestionado     |
| node2    | 192.168.50.200 | Nodo gestionado     |

---

## ⚙️ Configuración realizada por Ansible

Desde la VM `ansible`, se ejecuta un playbook que configura lo siguiente en `node1` y `node2`:

### 🔹 Servicios instalados

- **Nginx**: servidor web básico.
- **Docker**: motor de contenedores, con grupo `docker` creado.
- **Node.js**: versión 18.x instalada desde NodeSource.

### 🔹 Usuarios y permisos

- Se crea el usuario `devops` con shell `/bin/bash`.
- Se agregan los usuarios `devops` y `vagrant` al grupo `docker` para poder usar Docker sin `sudo`.

### 🔹 Firewall

- Se configura `ufw` para permitir los siguientes puertos:
  - `22` (SSH)
  - `80` (HTTP)
  - `443` (HTTPS)

---

## 🚀 Cómo usar este proyecto

1. **Clonar el repositorio**

   ```bash
   git clone https://github.com/fernando1419/vagrant-ansible.git
   cd vagrant-ansible
   ```

2. **Levantar y provisionar las VMs automáticamente**

   ```bash
   vagrant up
   ```

   Este comando:

   - Crea las 3 VMs (1 VM Nodo de Control + 2 VMs Nodos gestionados).
   - Instala Ansible en la VM ansible (Nodo de Control).
   - Sincroniza tu carpeta actual dentro de la VM.
   - Ejecuta el playbook de Ansible para configurar los nodos.

3. **Verificar que nos podamos conectar a cada una de las VMS**

   ```bash
   # Abrimos consola y nos conectamos a las VMs
   vagrant ssh ansible

   # Nos conectamos y verificamos que esten corriendo los servicios y NodeJs en node1:
   vagrant ssh node1
   systemctl status nginx
   systemctl status docker
   node -v

   # Nos conectamos y verificamos que esten corriendo los servicios y NodeJs en node2:
   vagrant ssh node2
   systemctl status nginx
   systemctl status docker
   node -v
   ```

## ✅ Comandos útiles

Conectarse a una VM:

```bash
vagrant ssh ansible
```

Reprovisionar si hiciste cambios en el playbook:

```bash
vagrant provision ansible
```

Apagar todas las VMs:

```bash
vagrant halt
```

Eliminar todas las VMs:

```bash
vagrant destroy -f
```

## 🕯️ Corriendo el linter

1- Verificar que esté bien instalado ansible-linter:

```bash
which ansible-lint
# ~/.local/bin/ansible-lint
```

2- Se puede correr el linter de Ansible, haciendo:

```bash
cd /home/vagrant/ansible_project/
ansible-lint playbook.yml
```

## 💡 Notas

Las tareas de Ansible están organizadas en roles reutilizables.
Podés extender este proyecto agregando más servicios o nodos simplemente editando el inventario y los roles.

El código del playbook vive dentro de la carpeta sincronizada en la VM /home/vagrant/ansible_project.

## 🧯 Troubleshooting: Resetear todo el entorno

Si algo salió mal (problemas de conexión SSH, claves corruptas, errores inesperados con Ansible o Vagrant), podés **borrar completamente las máquinas virtuales** y levantar todo de nuevo con estos pasos:

🔁 Paso 1: Destruir todas las VMs

```bash
vagrant destroy -f
```

🔼 Paso 3: Levantar todo de nuevo

```bash
vagrant up
```

## 🧪 Verificar que funciona

Una vez levantado todo, probá desde la VM ansible:

```bash
cd /home/vagrant/ansible_project
ansible -i inventories/vagrant/hosts.ini all -m ping
```

Si todo salió bien, deberías ver algo como:

```json
node2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

node1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

## 📄 Licencia

Este proyecto está liberado sin ninguna licencia. Usalo libremente para aprender, modificar y mejorar.
