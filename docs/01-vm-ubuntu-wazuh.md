# Lab: Criação da VM Ubuntu Server (Wazuh) com rede NAT + Host-Only

## Objetivo
Criar e instalar a máquina virtual que vai hospedar o Wazuh (manager, indexer e dashboard) no mini SOC, com dois adaptadores de rede: um para acesso à internet e outro para comunicação privada entre as VMs do laboratório.

## Metodologia
- Verificação da virtualização (VT-x/AMD-V) habilitada no processador
- Download das ISOs (Ubuntu Server, Windows 11 Enterprise Evaluation e Kali) a partir dos sites oficiais
- Criação da VM no VirtualBox com 6 GB de RAM, 2 vCPU e 50 GB de disco, armazenada em SSD
- Configuração de dois adaptadores de rede (NAT e Host-Only)
- Instalação manual do Ubuntu Server 24.04.5 LTS com OpenSSH
- Teste de conectividade e criação de snapshot

## Processo
Confirmei que a virtualização estava habilitada no Gerenciador de Tarefas e instalei o VirtualBox 7.2. Baixei a ISO do Ubuntu Server 24.04.5 LTS via torrent oficial e organizei as ISOs em uma pasta dedicada, mantendo as VMs em SSD e as ISOs no HD.

Criei a VM `wazuh-server` com **6 GB de RAM, 2 vCPU e 50 GB de disco** (alocação dinâmica) e desativei a instalação automática (unattended) para executar cada etapa do instalador manualmente.

Configurei dois adaptadores de rede:
- **Adaptador 1 (NAT):** dá acesso à internet para baixar pacotes. IP recebido: `10.0.2.15/24`.
- **Adaptador 2 (Host-Only):** rede privada entre o host e as VMs do lab, sem acesso externo. IP recebido: `192.168.56.101/24`.

Após a instalação, validei as interfaces com `ip a` e a saída para a internet com `ping`. Por fim, desliguei a VM e criei o snapshot `01-so-limpo`.

## Observações e aprendizado
Separar as redes por função é uma boa prática: o NAT serve apenas para instalação e atualização, enquanto a Host-Only mantém o tráfego do lab isolado, o que é essencial quando, mais adiante, houver simulações de ataque. Também ficou clara a utilidade do snapshot: ele permite voltar ao estado limpo em segundos e repetir testes sem reinstalar o sistema.

Nota: não consegui conferir o hash da ISO do Ubuntu contra o valor oficial; o download foi feito pelo torrent oficial, que verifica a integridade dos pedaços.

## Resultado
VM `wazuh-server` instalada e funcional, com NAT + Host-Only operando e snapshot `01-so-limpo` criado.


<img width="2558" height="1033" alt="01-vm-wazuh-server" src="https://github.com/user-attachments/assets/dbdb844f-3965-433a-851c-3a91a0cf9451" />
