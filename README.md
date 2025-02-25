# Atividade de Linux PB - JAN 2025 | DevSecOps

## Projeto prático AWS

O objetivo do projeto consiste em desenvolver e testar habilidades em Linux, AWS e automação de processos através da configuração de um ambiente de servidor web monitorado.

### Etapas

1 - Configuração do Ambiente

2 - Configuração do Servidor Web

3 - Monitoramento e Notificações

4 - Testes

5 - Desafio Extra

### Tecnologias Utilizadas

- **Infraestrutura:**	AWS EC2, VPC, Security Groups, Subnets
- **Servidor Web:** 	   Nginx, Systemd
- **Automação:**    	   Bash Scripting, Cron
- **Monitoramento:** 	cURL, Discord Webhooks, Logs
- **Ferramentas:**   	SSH, Visual Studio Code, Amazon Linux 2 AMI
- **Segurança:**     	Key Pairs, Security Groups
- **Armazenamento:**	   Amazon EBS, gp3

## Etapa 1 - Configuração do Ambiente

Este guia descreve o processo de criação de uma VPC (Virtual Private Cloud) e uma instância EC2 (Elastic Compute Cloud) na AWS, incluindo a configuração de segurança e rede necessária para o projeto. Serão utilizadas as configurações padrão da AWS, com exceção dos campos específicos que serão alterados conforme as necessidades do projeto. Apenas os passos destacados precisam de atenção e ajustes.

A **VPC** é um ambiente de rede isolado e seguro na AWS, onde você pode executar recursos como instâncias EC2, bancos de dados e outros serviços. Esta etapa cria uma VPC com sub-redes públicas e privadas para organizar e proteger os recursos do projeto.

O **EC2** é um serviço que permite criar e gerenciar máquinas virtuais (instâncias) na AWS. Nesta etapa, criamos uma instância EC2 para hospedar aplicações ou serviços do projeto.

### 1.1 Criando e configurando uma VPC

1. No console principal da AWS, utilize a barra de busca localizada no topo para buscar pelo serviço de **VPC**.
2. Clique em ***"Create VPC"*** para iniciar o processo de criação.

- **VPC and more**: Esta opção cria a VPC juntamente com duas Subnets públicas e duas privadas.
- **IPv4 CIDR block**: Defina o intervalo de endereços de IP como `10.0.0.0/24`. Este bloco permite 256 endereços de IP, o que é mais do que suficiente para os fins deste projeto.
- Clique em ***CREATE VPC*** para finalizar a criação.

### 1.2 Configurando Acesso via Gateway - Regras de Segurança

As **regras de segurança** definem como o tráfego de rede pode acessar os recursos dentro da VPC. Nesta etapa, configuramos as regras de entrada (inbound) para permitir acesso HTTP e SSH apenas a partir do seu IP.

1. No painel esquerdo, selecione ***"Security Groups"***.
2. Selecione o Security Group "default" que foi criado automaticamente com a VPC.
3. Na aba ***"Inbound Rules"***, clique em ***"Edit Inbound Rules"***.
4. Adicione as seguintes regras para liberar acesso via SSH à instância:

   |Type       |Source    |
   |-----------|----------|
   |HTTP       |My Ip     |
   |SSH        |My Ip     |

6. Clique em ***"Save Rules"*** para aplicar as alterações.

### 1.3 Criando uma Instância EC2

1. No console principal da AWS, busque por **"EC2"**.
2. Clique em ***"Launch Instance"*** ou, alternativamente, no painel esquerdo, selecione ***"Instances"*** > ***"Launch Instance"***.
3. Será mostrado o seguinte painel:


![Captura de tela 2025-02-24 100629](https://github.com/user-attachments/assets/3f897264-aeb1-40e1-bf11-eb2e82c29373)


**Configurações**

Este projeto será feito baseado nas configurações a seguir.

- Adicione as Tags necessárias para o projeto
- **Application and OS Images (Amazon Machine Image - AMI)**: Selecione **Amazon Linux 2 AMI (HVM) - Kernel 5.10, SSD Volume Type**.
- **Instance Type**: Escolha **t2.micro**.

- **Par de Chaves (Key Pair)**: Utilize as configurações padrões deao criar uma nova Key Pair. Defina um nome (exemplo.pem) para a chave e faça o download após a criação. ⚠️ Nome do arquivo e diretório de download serão necessários para a **Etapa 2**

- **VPC**: Selecione a VPC criada anteriormente.
- **Subnet**: Escolha uma Subnet Pública.
- **Auto-assign public IP**: Habilite esta opção para atribuir um IP público automaticamente.
- **Firewall (security group)**: Selecione o security group vinculado à VPC escolhida.

- **Storage Type**: Altere para **gp3**.

Clicar em ***"Launch Instance"***. 

**Instância criada!** ✅

Assim, no console de instancias EC2 será mostrado a Instância craida, ou em criação pois leva alguns segundos para ser iniciada e estará pronta para uso.

## Etapa 2 - Configuração do Servidor Web **Nginx**

O Nginx é um servidor web de alto desempenho, conhecido por sua eficiência, escalabilidade e baixo consumo de recursos.

### 2.1 Conectar-se à Instância EC2

A princício será feita a conexão com a instância criada anteriormente via SSH (Source Shell), um protocolo de rede criptografado que permite acessar e gerenciar remotamente servidores, computadores ou dispositivos de forma segura. 

A partir do console AWS EC2 > Instances, selecionar a Instância craida e clicar em ***"Connect"***. Essa página irá disponibilizar opções de conexão, clique em ***"SSH Client"*** e já será mostrado as instruções de conexão, conforme a captura a seguir:

![Captura de tela 2025-02-24 105214](https://github.com/user-attachments/assets/302d2fa1-4edf-4955-bd80-9ceb26a4ea80)

Abra o terminal bash via Visual Studio Code, por exemplo.

Navegue até o diretório onde o arquivo da chave privada .pem foi baixado, Downloads por exemplo:

```
cd ~/Downloads
```

Altere as permissões do arquivo da chave privada para garantir que apenas você possa acessá-lo:

```
chmod 400 exemplo.pem
```

Conecte à instância com o seguinte comando:

```
ssh -i "exemplo.pem" ec2-user@Public_IPv4_address
```

**Instância conectada!** ✅

### 2.2 Instalação e Configuração do Nginx

A seguir será detalhado como instalar e configurar o servidor web Nginx na Instância criada.

No terminal conectado com acesso via SSH execute o seguinte comando:

```
sudo amazon-linux-extras install nginx1 -y
```

Após a instalação, inicie o serviço do Nginx e habilite-o para que ele inicie automaticamente na inicialização do sistema:

```
sudo systemctl start nginx
sudo systemctl enable nginx
```

Para verificar se o Nginx está funcionando corretamente, abra um navegador e acesse o endereço IP público da sua instância EC2:
**http://<Public_IPv4_address>**

Será mostrado a seguinte página:

![Captura de tela 2025-02-21 100644](https://github.com/user-attachments/assets/c9ba7868-010d-491d-96a2-21225bc162e3)


### 2.3 Personalizar a página do Nginx

Para personalizar a página inicial do Nginx será editado o arquivo index.html.

```
sudo nano /usr/share/nginx/html/index.html
```

Substitua o conteúdo pelo seguinte exemplo, ou personalize a sua maneira.

```<!DOCTYPE html>
<html>
<head>
    <title>Projeto AWS</title>
</head>
<body>
    <h1>Hello World!</h1>
</body>
</html>
```

Salve o arquivo com os comandos `Ctrl+X` e confirme com `Y`.

Recarregue o arquivo para aplicar as alterações.

```
sudo systemctl reload nginx
```

Ao entrar novamente no endereço de IP via navegador é esperado a seguinte resposta:

![Captura de tela 2025-02-23 134542](https://github.com/user-attachments/assets/fab6669e-7c41-4525-9e21-db2bdef112fb)

### 2.4 Configurar reinício automático do Nginx

É possível configurar um serviço para garantir que o Nginx reinicie automaticamente em caso de falha.

Edite o arquivo de serviço do Nginx:

```
sudo nano /usr/lib/systemd/system/nginx.service
```

Adicione ou modifique as seguintes linhas na seção [Service]:

```
Restart=always
RestartSec=5s
```
📌
- `Restart=always`: Faz com que o Nginx reinicie sempre que ele falhar.
- `RestartSec=5s`: Define o tempo de espera entre as tentativas de reinício (5 segundos neste caso).

Recarregue e reinicie o serviço Nginx:

```
sudo systemctl daemon-reload
sudo systemctl restart nginx
```

Habilite o serviço para iniciar com o sistema:

```
sudo systemctl enable nginx
```

**Nginx instalado e configurado!** ✅

## Etapa 3 - Monitoramento e Notificações

Nessa etapa será criado um script em Bash para monitorar a disponibilidade do site e enviar notificações para um canal do Discord caso o site esteja indisponível. O script é executado periodicamente através de um **Cron Job**, garantindo que o monitoramento seja contínuo.

### 3.1 Criação de um Servidor no Discord e Configuração de um Webhook

Para enviar notificações ao Discord, é necessário criar um servidor (caso você ainda não tenha um) e configurar um webhook no canal desejado.

**Criando um Servidor no Discord**
- Abra o Discord e clique no ícone "+" no canto esquerdo da interface.
- Selecione "Criar um Servidor".
- Escolha um template ou crie um servidor personalizado.
- Dê um nome ao servidor e clique em "Criar".

**Configurando um Webhook**
- No servidor criado, clique no canal onde deseja receber as notificações.
- Clique no ícone de engrenagem ao lado do nome do canal para acessar as Configurações do Canal.
- Navegue até a seção **Integrações** e clique em "Criar Webhook".
- Personalize o webhook com nome e imagem que desejar.
- Copie a URL do Webhook gerada. ⚠️Essa URL será usada no script de monitoramento para enviar mensagens ao canal.

![Captura de tela 2025-02-24 103418](https://github.com/user-attachments/assets/b428e270-8c33-4033-b98b-696f8126826d)

**Alternativa: Criando um Webhook em um Servidor Existente**
- Se você já possui um servidor no Discord e deseja adicionar um webhook a um canal existente:
- Acesse o canal desejado no servidor.
- Siga os passos 2 a 5 da seção Configurando um Webhook acima.

### 3.2 Criação do Diretório e Script de Monitoramento

Primeiro, crie um diretório para armazenar o script de monitoramento:

```
sudo mkdir -p /opt/monitoramento
```

Em seguida, crie o script monitor_site.sh dentro desse diretório:

```
sudo nano /opt/monitoramento/monitor_site.sh
```

Cole o script abaixo substituindo a **WEBHOOK_URL** do Discord pela sua:

```
#!/bin/bash

# Configurações
URL="http://localhost:80" 
LOG_FILE="/var/log/monitoramento.log"
WEBHOOK_URL="https://<webhook_URL>"

# Verifica se o diretório de logs existe, se não, cria
if [ ! -d "$(dirname "$LOG_FILE")" ]; then
    sudo mkdir -p "$(dirname "$LOG_FILE")"
    sudo chmod 755 "$(dirname "$LOG_FILE")"
fi

# Verifica se o site está acessível
STATUS_CODE=$(curl -o /dev/null -s -w "%{http_code}" --max-time 10 "$URL")  # Timeout de 10 segundos
DATE=$(date +"%Y-%m-%d %H:%M:%S")

if [ "$STATUS_CODE" -ne 200 ]; then
    echo "$DATE - ERRO: Site indisponível (Status: $STATUS_CODE)" | sudo tee -a "$LOG_FILE"
    
    # Enviar notificação para o Discord
    curl -H "Content-Type: application/json" -X POST -d "{\"content\": \"O site está fora do ar!\"}" "$WEBHOOK_URL"
else
    echo "$DATE - OK: Site acessível (Status: $STATUS_CODE)" | sudo tee -a "$LOG_FILE"
fi
```
📌 **Configurações Iniciais**
- `URL`: Armazena o endereço do site que será monitorado. No exemplo, o script verifica `http://localhost:80` que é a porta configurada na Instância EC2 que criamos, mas você pode alterar para qualquer URL.
- `LOG_FILE`: Define o caminho do arquivo de log onde os resultados do monitoramento serão armazenados.
- `WEBHOOK_URL`: Contém o URL do webhook do Discord, que será usado para enviar notificações quando o site estiver indisponível.

📌 **Verificar diretório de logs**
- `if [ ! -d "$(dirname "$LOG_FILE")" ]; then`: Verifica se o diretório onde o arquivo de log será salvo existe.
    - `dirname "$LOG_FILE"`: Extrai o diretório do caminho do arquivo de log.
    - `! -d`: Verifica se o diretório não existe.
- `sudo mkdir -p`: Cria o diretório de logs (e qualquer diretório pai necessário) caso ele não exista.
- `sudo chmod 755`: Define permissões para o diretório, garantindo que ele seja acessível.

📌 **Verificar status do site**
- `curl`: Comando usado para fazer uma requisição HTTP ao site.
    - `o /dev/null`: Descarta o conteúdo da resposta (não armazena o HTML ou outros dados).
    - `-s`: Executa o comando em modo silencioso (sem exibir saída no terminal).
    - `-w "%{http_code}"`: Extrai apenas o código de status HTTP da resposta (por exemplo, 200 para sucesso, 404 para não encontrado, etc.).
    - `--max-time 10`: Define um timeout de 10 segundos para a requisição. Se o site não responder nesse tempo, o comando falha.
- `STATUS_CODE`: Armazena o código de status HTTP retornado pelo curl.
- `DATE`: Armazena a data e hora atual no formato YYYY-MM-DD HH:MM:SS

📌 **Registro no Log**
- `if [ "$STATUS_CODE" -ne 200 ]; then`: Verifica se o código de status é diferente de 200 (OK).
    - `ne`: Operador que significa "não igual".
- `echo "$DATE - ERRO`: Site indisponível (Status: $STATUS_CODE)": Gera uma mensagem de erro com a data, hora e código de status.
    - `sudo tee -a "$LOG_FILE"`: Adiciona a mensagem ao arquivo de log (-a para append) e exibe no terminal.

📌 **Notificação Discord**
- `curl -H "Content-Type: application/json" -X POST -d "{\"content\": \"O site está fora do ar!\"}" "$WEBHOOK_URL"`: 
    - Envia uma requisição POST para o webhook do Discord.
    - `-H "Content-Type`: application/json": Define o cabeçalho da requisição como JSON.
    - `-d "{\"content\": \"O site está fora do ar!\"}"`: Envia a mensagem "O site está fora do ar!" no corpo da requisição.
- `else`: Caso o site esteja acessível (status 200), registra uma mensagem de sucesso no log.

Ative as permissões de execução e torne o script executável:

```
sudo chmod +x /opt/monitoramento/monitor_site.sh
```

### 3.3 Confirguração do Cron Job

O cron job garante que o script seja executado automaticamente a cada minuto.

Crie o Cron Job:

```
sudo crontab -e
```

Acrescente a linha:

```
* * * * * /opt/monitoramento/monitor_site.sh
```

📌

` * * * * * ` Define a execução do script a cada minuto.
   - Os campos ocupados por ` * ` representam: minuto, hora, dia do mês, mês, dia da semana.
   - ` * ` significa "qualquer valor".

Para salvar e sair pressione `ESC` e digite `:wq` (whrite and quit/salvar alterações e sair).

**Automação e notificação via Discord configurados!** ✅

## Etapa 4 - Testes

Esta etapa conta com os passos para testar o comportamento do Nginx em caso de falha, verificando se o serviço é reiniciado automaticamente e se o site monitorado volta a ficar acessível após a recuperação do Nginx. O teste utiliza o script de monitoramento criado anteriormente (**monitor_site.sh**) para verificar a disponibilidade do site.

### Teste de Auto restart do Nginx

Antes, verifique o status do Nginx:

```
sudo systemctl status nginx
```

Resposta esperada:

![nginx-running](https://github.com/user-attachments/assets/0d1aa325-ad5a-4eb1-9272-1cf22769e15f)


📌
- `Active: active (running)`: Indica que o Nginx está em execução.
- `Main PID`: Mostra o ID do processo principal do Nginx.

Execute o script de monitoramento para verificar se o site está acessível enquanto o Nginx está ativo e verifique o log de monituramento:

```
/opt/monitoramento/monitor_site.sh
```

```
sudo cat /var/log/monitoramento.log
```

Respostas esperadas:

![montitoramento-acessivel](https://github.com/user-attachments/assets/823879c7-01a4-4a94-ad7b-b3efa5961c42)


**Parar o Nginx forçadamente**

Para simular uma falha, pare o Nginx manualmente usando o comando `killall`:

```
sudo killall nginx
```

Verifique o status do Nginx logo após o comando, como a contagem de restar foi estabelecida em 5 segundos a verificação deve ser feita dentro desse tempo:

```
sudo systemctl status nginx
```

Enquanto o Nginx estiver parado, se necessário execute novamente o comando `killall`, verifique o monitoramento do site:

```
/opt/monitoramento/monitor_site.sh
```

Resposta esperada:

![sudo-killall](https://github.com/user-attachments/assets/1fbec654-8a0e-45ce-9caa-0cc525fec926)


📌
- `Active: activating (auto-restart)`: Indica que o Nginx está tentando reiniciar automaticamente após ser parado.

A noificação via Discord deverá chegar conforme esperado:

![Captura de tela 2025-02-21 112402](https://github.com/user-attachments/assets/fb3abce7-8ef3-445a-9322-1e5030869251)

Após o reinicio automático, verifique o status do serviço Nginx se necessário e execute o monitoramento novamente. 

A sguir uma captura completa do terminal com todos os comandos para mostrar a eficiência do que foi feito:

![Captura de tela 2025-02-21 113923](https://github.com/user-attachments/assets/2662f56c-f288-45df-a9dd-0af6956c7b6d)


## Etapa 5 - Desafio Extra

Como proposta de aprofundamento na execução do projeto, será implementado todas as etapas anteriores via `UserData` ao iniciar uma Instância EC2.

Um **UserData** é um script ou conjunto de comandos que pode ser fornecido ao iniciar uma instância, executado automaticamente **apenas uma vez**, geralmente utilizado para instalar softwares, configurar serviços e executar tarefas de inicialização personalizadas.

### 5.1 Iniciando uma Instância com UserData

Siga os passos na etapa **1.3** acima para criar uma instância, porém, após configurar o **Storage Type** para `gp3`, não inicie a instância (Launch Instance). Abaixo de **Configure Storage**, clique na aba **Advanced details** para expandi-la.

![Captura de tela 2025-02-25 151308](https://github.com/user-attachments/assets/02725a7c-fd92-43b4-8a49-a27c8464231b)

Na última configuração dessa aba está o local para inserir o `UserData`. Aqui reformulamos todos as etapas anteriores de modo que, ao iniciar a instância, já teremos esse projeto rodando.

Inclua o seguinte código no campo UserData:

```
#!/bin/bash
# Projeto AWS-Linux

# Redirecionar saída para log
exec > >(tee /var/log/user-data.log|logger -t user-data -s 2>/dev/console) 2>&1

# Atualizar pacotes e instalar EPEL
sudo yum update -y
sudo amazon-linux-extras install epel -y

# Instalar nginx
sudo yum install nginx -y

# Criar arquivo HTML de teste
sudo cat << 'EOF' > /usr/share/nginx/html/index.html
<!DOCTYPE html>
<html>
<body>

<h1>PROJETO AWS Compass.UOL</h1>
<h2>teste de instalacao e automacao (monitoramento) do Nginx via UserData</p> </h2>

<script>
let host = location.host;
document.getElementById("hostname").innerHTML = host;
</script>
</body>
</html>
EOF

# Configurar reinício automático nginx
sudo sed -i '/\[Service\]/a Restart=always\nRestartSec=5s' /usr/lib/systemd/system/nginx.service

# Recarregar o systemd para aplicar as alterações
sudo systemctl daemon-reload

# Habilitar e iniciar o nginx
sudo systemctl enable nginx --now

# Criação do Diretório e Script de Monitoramento
sudo mkdir -p /opt/monitoramento

# Criar o script monitor_site.sh dentro do diretório
sudo cat << 'EOF' > /opt/monitoramento/monitor_site.sh
#!/bin/bash

# Configurações
URL="http://localhost:80"
LOG_FILE="/var/log/monitoramento.log"
WEBHOOK_URL="https://<seu-webhook>"

# Verifica se o diretório de logs existe, se não, cria
if [ ! -d "$(dirname "$LOG_FILE")" ]; then
    sudo mkdir -p "$(dirname "$LOG_FILE")"
    sudo chmod 755 "$(dirname "$LOG_FILE")"
fi

# Verifica se o site está acessível
STATUS_CODE=$(curl -o /dev/null -s -w "%{http_code}" --max-time 10 "$URL")  # Timeout de 10 segundos
DATE=$(date +"%Y-%m-%d %H:%M:%S")

if [ "$STATUS_CODE" -ne 200 ]; then
    echo "$DATE - ERRO: Site indisponível (Status: $STATUS_CODE)" | sudo tee -a "$LOG_FILE"
    
    # Enviar notificação para o Discord
    curl -H "Content-Type: application/json" -X POST -d "{\"content\": \"O site está fora do ar!\"}" "$WEBHOOK_URL"
else
    echo "$DATE - OK: Site acessível (Status: $STATUS_CODE)" | sudo tee -a "$LOG_FILE"
fi
EOF

# permissão de execução ao script
sudo chmod +x /opt/monitoramento/monitor_site.sh

# Cron Job para monitorar a cada minuto
(sudo crontab -l 2>/dev/null; echo "* * * * * /opt/monitoramento/monitor_site.sh") | sudo crontab -

# Update final
sudo yum update -y
```

📌**Alterações feitas para funcionar como UserData**
- `exec > >(tee /var/log/user-data.log|logger -t user-data -s 2>/dev/console) 2>&1`: criação de um log de erros para verificação caso o script falhe.
- `sudo amazon-linux-extras install epel -y`: Instala o repositório EPEL (Extra Packages for Enterprise Linux), que fornece pacotes adicionais para instalar o Nginx.
- `sudo cat << 'EOF' > /usr/share/nginx/html/index.html`: criar uma página HTML personalizada, o comando `EOF` inclui o código no arquivo iniciado para não utilizar editor de texto nessa etapa.
- `sudo sed -i '/\[Service\]/a Restart=always\nRestartSec=5s' /usr/lib/systemd/system/nginx.service`: inclui as configurações `Restart=always` e `RestartSec=5s` no campo [Service] do arquivo de serviço do Nginx para o reinício automático.
- `sudo cat << 'EOF' > /opt/monitoramento/monitor_site.sh`: cria o script de monitoramento via Discord utilizando `EOF` para incluir o codigo bash.
- `(sudo crontab -l 2>/dev/null; echo "* * * * * /opt/monitoramento/monitor_site.sh") | sudo crontab -`: configura o CronJob para executar o script de monitoramento a cada minuto.

Após incluir o UserData no campo, clique em **Launch Instance**.

Repita a **Etapa 4** de testes para validar esse processo, os resultados esperados são os mesmos.

## Conclusão ✅

Este projeto prático demonstrou a integração de diversas tecnologias e práticas essenciais para a área de DevSecOps, com foco em Linux, AWS, automação e monitoramento. Através das etapas realizadas, foi possível configurar um ambiente seguro e escalável na AWS, implantar um servidor web com Nginx, e implementar um sistema de monitoramento automatizado com notificações em tempo real via Discord.

O desafio resume o projeto em uma solução simples e eficaz para automatizar a configurar o objetivo desse projeto em apenas uma etapa.

O sucesso deste projeto reforça a importância da automação, monitoramento contínuo e boas práticas de segurança na gestão de infraestruturas modernas.
