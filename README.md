# OpenSSH no Ubuntu/Debian

Este repositório contém um tutorial completo para instalação, configuração, ativação na inicialização, execução e uso do OpenSSH em distribuições baseadas no Ubuntu/Debian.

## 1. Instalando o OpenSSH

No Ubuntu/Debian, o OpenSSH pode ser instalado pelo gerenciador de pacotes APT:

```bash
sudo apt update && sudo apt install -y openssh-server
```

Após a instalação, verifique se o serviço está ativo:

```bash
systemctl status ssh
```

Se o serviço não estiver em execução, inicie-o manualmente:

```bash
sudo systemctl start ssh
```

## 2. Ativando o OpenSSH na Inicialização

Para garantir que o OpenSSH inicie automaticamente ao ligar o sistema, execute:

```bash
sudo systemctl enable ssh
```

## 3. Configuração do OpenSSH

O arquivo principal de configuração do OpenSSH está localizado em:

```bash
/etc/ssh/sshd_config
```

Para editar as configurações, utilize um editor de texto como o `nano`:

```bash
sudo nano /etc/ssh/sshd_config
```

Algumas configurações recomendadas:

- **Alterar a porta padrão (opcional):**
  ```bash
  Port 2222  # Altere para outra porta para maior segurança
  ```
- **Desativar login com usuário root (recomendado):**
  ```bash
  PermitRootLogin no
  ```
- **Habilitar autenticação por chave pública (opcional):**
  ```bash
  PubkeyAuthentication yes
  ```

Após qualquer modificação no arquivo, reinicie o serviço:

```bash
sudo systemctl restart ssh
```

## 4. Permitindo Acesso no Firewall

Se o firewall UFW estiver ativado, permita conexões SSH:

```bash
sudo ufw allow OpenSSH
```

Caso tenha alterado a porta padrão, libere a nova porta:

```bash
sudo ufw allow 2222/tcp  # Substitua 2222 pela porta configurada
```

Ative o firewall (se necessário):

```bash
sudo ufw enable
```

## 5. Conectando-se ao Servidor SSH

Para conectar-se a partir de outra máquina remota:

```bash
ssh usuario@ip_do_servidor
```

Se a porta foi alterada, use:

```bash
ssh -p 2222 usuario@ip_do_servidor
```

Substitua `usuario` pelo nome do usuário e `ip_do_servidor` pelo IP da máquina onde o OpenSSH está rodando.

## 6. Gerando e Usando Chaves SSH (Opcional)

Para maior segurança, utilize autenticação por chave pública:

1. No cliente, gere um par de chaves:
   ```bash
   ssh-keygen -t rsa -b 4096
   ```
   O arquivo será salvo em `~/.ssh/id_rsa` (chave privada) e `~/.ssh/id_rsa.pub` (chave pública).

2. Copie a chave pública para o servidor:
   ```bash
   ssh-copy-id usuario@ip_do_servidor
   ```

3. Agora, conecte-se sem precisar digitar senha:
   ```bash
   ssh usuario@ip_do_servidor
   ```

## 7. Transferindo Arquivos e Pastas entre Máquinas com SSH

O SSH também pode ser usado para copiar arquivos e diretórios entre máquinas usando o `scp` (Secure Copy Protocol) e o `rsync`.

### 7.1 Copiando Arquivos com `scp`

Para copiar um arquivo da máquina local para uma máquina remota:

```bash
scp arquivo.txt usuario@ip_do_servidor:/caminho/de/destino/
```

Para copiar um arquivo de uma máquina remota para a máquina local:

```bash
scp usuario@ip_do_servidor:/caminho/do/arquivo.txt ./
```

Se a porta SSH foi alterada, use a opção `-P`:

```bash
scp -P 2222 arquivo.txt usuario@ip_do_servidor:/caminho/de/destino/
```

### 7.2 Copiando Diretórios com `scp`

Para copiar um diretório inteiro para uma máquina remota:

```bash
scp -r pasta/ usuario@ip_do_servidor:/caminho/de/destino/
```

Para copiar um diretório inteiro de uma máquina remota para a máquina local:

```bash
scp -r usuario@ip_do_servidor:/caminho/da/pasta/ ./
```

### 7.3 Sincronizando Arquivos com `rsync`

O `rsync` pode ser usado para transferências mais eficientes:

```bash
rsync -avz arquivo.txt usuario@ip_do_servidor:/caminho/de/destino/
```

Para sincronizar um diretório local com um remoto:

```bash
rsync -avz pasta/ usuario@ip_do_servidor:/caminho/de/destino/
```

Para sincronizar um diretório remoto com um local:

```bash
rsync -avz usuario@ip_do_servidor:/caminho/da/pasta/ ./
```

## 8. Solução de Problemas

- **Verifique se o serviço está ativo:**
  ```bash
  sudo systemctl status ssh
  ```
- **Verifique logs para identificar erros:**
  ```bash
  sudo journalctl -xe | grep ssh
  ```
- **Se não conseguir acessar, teste a conexão manualmente:**
  ```bash
  nc -zv ip_do_servidor 22  # Substitua 22 pela porta configurada
  ```
