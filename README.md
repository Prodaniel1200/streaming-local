# 🎬 Streaming Local — Jellyfin

Proyecto final de **Administración de Redes** — Universidad Católica de Colombia  
Despliegue automatizado de un servidor de streaming multimedia usando Jellyfin,  
Ansible, Docker y AWS EC2.

---

## 📁 Estructura del repositorio

```
streaming-local/
├── ansible/
│   ├── inventories/
│   │   ├── aws/hosts.yml          # Inventario AWS (IP generada automáticamente)
│   │   └── local/hosts.yml        # Inventario entorno local
│   ├── group_vars/
│   │   └── all.yml                # Variables globales (sin credenciales)
│   ├── playbooks/
│   │   ├── provision_aws.yml      # Crea EC2 + Security Group en AWS
│   │   ├── deploy_aws.yml         # Despliega Jellyfin en EC2
│   │   └── deploy_local.yml       # Despliega Jellyfin en servidor local
│   └── roles/
│       ├── docker/                # Rol: instala Docker CE
│       └── jellyfin/              # Rol: despliega Jellyfin con Docker Compose
├── docker/
│   └── docker-compose.yml         # Compose de referencia
├── docs/
│   └── informe-tecnico.md
└── .gitignore
```

---

## ⚙️ Requisitos previos

### En tu máquina de control (desde donde corres Ansible):

```bash
# Python y pip
sudo apt install python3 python3-pip -y

# Ansible
pip install ansible

# Módulos AWS para Ansible
pip install boto3 botocore
ansible-galaxy collection install amazon.aws
ansible-galaxy collection install community.docker
```

### Credenciales AWS (variables de entorno — nunca en archivos):

```bash
export AWS_ACCESS_KEY_ID="tu-access-key-id"
export AWS_SECRET_ACCESS_KEY="tu-secret-access-key"
export AWS_DEFAULT_REGION="us-east-1"
```

---

## 🚀 Fase 1 — Despliegue en AWS

### Paso 1: Crear el Key Pair en AWS
En la consola de AWS → EC2 → Key Pairs → Crear key pair  
- Nombre: `streaming-key`  
- Tipo: RSA, formato `.pem`  
- Guardar en `~/.ssh/streaming-key.pem`

```bash
chmod 400 ~/.ssh/streaming-key.pem
```

### Paso 2: Aprovisionar la infraestructura

```bash
ansible-playbook ansible/playbooks/provision_aws.yml
```

Esto crea automáticamente:
- Security Group con puertos 22 (SSH) y 8096 (Jellyfin)
- Instancia EC2 t2.micro Ubuntu 22.04
- Actualiza el inventario con la IP pública

### Paso 3: Desplegar Jellyfin en EC2

```bash
ansible-playbook ansible/playbooks/deploy_aws.yml \
  -i ansible/inventories/aws/hosts.yml
```

### Paso 4: Acceder a Jellyfin

Abrir en el navegador: `http://<IP-EC2>:8096`  
Completar el wizard de configuración inicial de Jellyfin.

---

## 🏠 Fase 2 — Despliegue en entorno local

```bash
# Editar la IP del servidor local
nano ansible/inventories/local/hosts.yml

# Desplegar
ansible-playbook ansible/playbooks/deploy_local.yml \
  -i ansible/inventories/local/hosts.yml
```

---

## 🔒 Seguridad

- Las credenciales AWS se pasan como variables de entorno, nunca en archivos.
- Las llaves `.pem` están en `.gitignore`.
- Los Security Groups solo abren los puertos estrictamente necesarios.
- Se recomienda usar `ansible-vault` para variables sensibles en producción.

---

## 🛠️ Tecnologías utilizadas

| Tecnología | Uso |
|---|---|
| Ansible | Automatización del despliegue |
| Docker / Docker Compose | Contenedorización de Jellyfin |
| AWS EC2 | Servidor cloud |
| AWS Security Groups | Firewall de la instancia |
| Jellyfin | Servidor de streaming multimedia |
| Ubuntu 22.04 | Sistema operativo base |
| OpenWrt / Cisco | Red local comunitaria |
