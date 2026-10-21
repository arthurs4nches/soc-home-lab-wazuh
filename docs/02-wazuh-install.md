# Lab: Instalação do Wazuh (all-in-one) no Ubuntu Server

## Objetivo
Instalar a plataforma Wazuh (manager, indexer e dashboard) na VM `wazuh-server`, para que ela receba logs de endpoints monitorados e gere alertas de segurança no mini SOC.

## Metodologia
- Conferência da documentação oficial para usar o método e a versão estável atual (Wazuh 4.14)
- Acesso à VM por SSH a partir do host, para colar comandos com facilidade
- Instalação all-in-one com o assistente oficial (`wazuh-install.sh -a`)
- Armazenamento da senha de admin fora do repositório
- Acesso ao dashboard pela rede Host-Only
- Desativação do repositório do Wazuh e criação de snapshot

## Processo
Consultei a página de Quickstart da documentação oficial do Wazuh e usei a versão estável, ignorando as páginas de pré-lançamento (5.0 beta/rc). Conectei à VM por SSH usando o IP da rede Host-Only (`192.168.56.101`) e executei o assistente de instalação oficial, que implantou manager, indexer e dashboard no mesmo servidor em cerca de 10 a 20 minutos.

Ao final, o assistente exibiu a mensagem `Installation finished` e as credenciais do usuário `admin`, que guardei fora do GitHub. Acessei o dashboard em `https://192.168.56.101`, aceitando o aviso de certificado, já que o próprio Wazuh gera um certificado autoassinado.

Seguindo a recomendação da documentação, comentei a linha do repositório do Wazuh em `/etc/apt/sources.list.d/wazuh.list`, evitando que uma atualização automática quebre o ambiente. Em seguida, desliguei a VM e criei o snapshot `02-wazuh-ok`.

## Observações e aprendizado
Entendi o papel de cada componente: o manager analisa os eventos com base em regras e gera alertas, o indexer armazena e permite pesquisar, e o dashboard é a interface visual. O dashboard mostra 0 agentes porque ainda não há endpoints conectados. Também aprendi que o certificado autoassinado gera aviso no navegador e que, em ambiente de laboratório isolado, isso é esperado. Congelar as versões do repositório é uma boa prática para manter o ambiente reproduzível.

## Resultado
Wazuh instalado e acessível pelo dashboard, com snapshot `02-wazuh-ok` criado. Próxima etapa: criar a VM Windows 11 e conectar o agente.



<img width="2553" height="986" alt="image" src="https://github.com/user-attachments/assets/3959d392-d4da-452a-8b28-c07b6f5c6874" />
