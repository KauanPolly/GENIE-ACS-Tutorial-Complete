# GENIE-ACS-Tutorial-Complete
Step-by-step setup of GenieACS v1.2.13 (no GUI) on Ubuntu with MongoDB, Node.js, and systemd. Includes built-in web UI, firewall rules, NTP sync, and security tips. Uses only safe, documented IPs. Lightweight and production-ready TR-069 ACS setup.


# 📡 GenieACS v1.2.13 - Instalação Completa (sem GUI) em Ubuntu Server

Este repositório contém o passo a passo completo para instalar e configurar o **GenieACS v1.2.13** com interface web simples (Node.js embutido), banco de dados MongoDB e serviços `systemd`, tudo rodando em um servidor Ubuntu.

> ✅ Todos os IPs públicos e dados sensíveis foram substituídos por endereços de documentação:
> - IPv4: `192.0.2.0/24`  
> - IPv6: `::0`

---

## ✅ Pré-requisitos

- Ubuntu Server 20.04 ou 22.04
- Acesso root ou `sudo`
- Internet
- Node.js ≥ 12.13
- MongoDB ≥ 3.6

---

## 🔧 Passo 1 – Instalar Dependências

```bash
sudo apt update
sudo apt install -y build-essential curl git mongodb npm
```

---

## ⬇️ Passo 2 – Instalar GenieACS Backend

```bash
sudo npm install -g genieacs@1.2.13
```

Crie diretórios e o usuário:

```bash
sudo adduser --system --group --home /opt/genieacs genieacs
sudo mkdir -p /opt/genieacs/ext /var/log/genieacs
sudo chown -R genieacs:genieacs /opt/genieacs /var/log/genieacs
```

---

## ⚙️ Passo 3 – Arquivo de Ambiente `/opt/genieacs/genieacs.env`

```ini
GENIEACS_UI_JWT_SECRET=supersecreto123
GENIEACS_LOG_FORMAT=simple
GENIEACS_ACCESS_LOG_FORMAT=simple
```

---

## 🧩 Passo 4 – Criar Serviços systemd

Crie os arquivos em `/etc/systemd/system/` como `genieacs-cwmp.service`, por exemplo:

```ini
[Unit]
Description=GenieACS CWMP
After=network.target

[Service]
Type=simple
User=genieacs
WorkingDirectory=/opt/genieacs
ExecStart=/usr/bin/genieacs-cwmp
EnvironmentFile=/opt/genieacs/genieacs.env
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Repita para os outros serviços:  
- `genieacs-nbi.service`  
- `genieacs-fs.service`  
- `genieacs-ui.service`  

Ative todos:

```bash
sudo systemctl daemon-reexec
sudo systemctl enable --now genieacs-cwmp genieacs-nbi genieacs-fs genieacs-ui
```

---

## 🔐 Firewall (UFW)

```bash
sudo ufw allow 22/tcp      # SSH
sudo ufw allow 7547/tcp    # CWMP
sudo ufw allow 7557/tcp    # NBI
sudo ufw allow 7567/tcp    # FS
sudo ufw allow 3000/tcp    # Interface Web simples
sudo ufw enable
```

---

## 🕒 Sincronização de horário com NTP

```bash
sudo timedatectl set-ntp true
timedatectl status
```

---

## 🌐 Endpoints Importantes

- Interface Web (painel simples): `http://192.0.2.10:3000`
- Protocolo CWMP: `http://192.0.2.10:7547`
- API NBI: `http://192.0.2.10:7557`
- Servidor de arquivos: `http://192.0.2.10:7567`

---

## 🔒 Segurança

- Nunca publique senhas reais, chaves ou IPs públicos de produção.
- Este tutorial usa endereços reservados para documentação.

---

**Autor:** Tutorial criado com base em ambiente de produção real, adaptado para uso seguro e compartilhamento público.
