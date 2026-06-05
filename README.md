# 🎬 Streaming Local — Jellyfin

Proyecto final de **Administración de Redes** — Universidad Católica de Colombia  
Despliegue automatizado de un servidor de streaming multimedia usando **Jellyfin**,  
**Ansible**, **Docker** y **AWS EC2**.

---

## 📁 Estructura del repositorio
streaming-local/
├── ansible/
│   ├── ansible.cfg                        # Configuración de rutas de roles
│   ├── group_vars/
│   │   └── all.yml                        # Variables globales (sin credenciales)
│   ├── inventories/
│   │   ├── aws/hosts.yml                  # Inventario AWS (IP auto-generada)
│   │   └── local/hosts.yml                # Inventario entorno local
│   ├── playbooks/
│   │   ├── provision_aws.yml              # Crea EC2 + Security Group en AWS
│   │   ├── deploy_aws.yml                 # Despliega Jellyfin en EC2
│   │   └── deploy_local.yml               # Despliega Jellyfin local
│   └── roles/
│       ├── docker/tasks/main.yml          # Rol: instala Docker CE
│       └── jellyfin/
│           ├── tasks/main.yml             # Rol: despliega Jellyfin
│           ├── handlers/main.yml          # Handler: reinicia Jellyfin
│           └── templates/
│               └── docker-compose.yml.j2  # Template del compose
├── docker/
│   └── docker-compose.yml                 # Compose de referencia
└── .gitignore                             # Excluye .pem, keys y credenciales

---

## ⚙️ Requisitos previos

```bash
sudo apt install python3 python3-pip -y
pip install ansible boto3 botocore
ansible-galaxy collection install amazon.aws community.docker
```

### Credenciales AWS:

```bash
export AWS_ACCESS_KEY_ID="tu-access-key-id"
export AWS_SECRET_ACCESS_KEY="tu-secret-access-key"
export AWS_DEFAULT_REGION="us-east-1"
```

---

## 🚀 Fase 1 — AWS

```bash
cd ansible/
ansible-playbook playbooks/provision_aws.yml
ansible-playbook playbooks/deploy_aws.yml -i inventories/aws/hosts.yml
```

Acceder: `http://<IP-EC2>:8096`

---

## 🏠 Fase 2 — Local

```bash
ansible-playbook playbooks/deploy_local.yml \
  -i inventories/local/hosts.yml --ask-become-pass
```

Acceder: `http://localhost:8096` o `http://<IP-local>:8096`

---

## 📡 Puertos

| Puerto | Protocolo | Función |
|---|---|---|
| 22 | TCP | SSH — administración remota |
| 8096 | TCP | Jellyfin — Web UI y streaming |
| 7359 | UDP | Descubrimiento automático en LAN |
| 1900 | UDP | DLNA — compatibilidad Smart TV |

---

## 🛠️ Tecnologías

| Tecnología | Uso |
|---|---|
| Ansible | Automatización del despliegue |
| Docker CE 29.5.3 | Contenedorización de Jellyfin |
| AWS EC2 t3.micro | Servidor cloud |
| AWS Security Groups | Firewall de la instancia |
| Jellyfin | Servidor de streaming multimedia |
| Ubuntu 22.04 / 24.04 | Sistema operativo base |

---

## 🔒 Seguridad

- Credenciales AWS solo como variables de entorno, nunca en archivos.
- Llaves `.pem` excluidas via `.gitignore`.
- Security Group con mínimos puertos necesarios.

---

## 🌐 Contexto comunitario

Jellyfin opera 100% offline en LAN — ideal para comunidades con conectividad limitada. Sin suscripción, sin datos externos, compatible con redes mesh OpenWrt.
