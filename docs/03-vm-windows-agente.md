# Lab: Criação da VM Windows 11 e conexão do agente Wazuh

## Objetivo
Criar a VM que será o endpoint monitorado do laboratório e conectá-la ao servidor Wazuh por meio do agente, confirmando o recebimento de eventos pela rede Host-Only.

## Metodologia
- Criação da VM Windows 11 Enterprise Evaluation no VirtualBox (4 GB de RAM, 2 vCPU, 80 GB, UEFI, TPM 2.0)
- Configuração de dois adaptadores de rede (NAT e Host-Only)
- Instalação do sistema com conta local e criação do snapshot `01-so-limpo`
- Geração do comando de instalação do agente pelo próprio dashboard
- Instalação do agente via PowerShell e inicialização do serviço
- Validação do status do agente no dashboard

## Processo
Criei a VM `win11-endpoint` com 80 GB de disco, acima do mínimo de 64 GB exigido pelo Windows 11, com UEFI e TPM 2.0 habilitados. Após a instalação, validei a rede (NAT para internet e Host-Only para comunicação com o servidor Wazuh) e criei o snapshot `01-so-limpo`.

No dashboard, usei a função **Deploy new agent**, informando o IP do manager na rede Host-Only (`192.168.56.102`) e o nome do agente `win11-endpoint`. Executei o comando gerado no PowerShell da VM e iniciei o serviço `WazuhSvc`. Em seguida, o dashboard passou a mostrar o agente como **Active**.

### Problemas encontrados
- **Instalação falhou sem mensagem de erro.** O serviço do agente não aparecia. Reexecutei o instalador com log detalhado (`/l*v`) e código de saída. O log indicou `Error 1925` (privilégios insuficientes) e o código de saída foi `1603`. A causa era o PowerShell aberto sem privilégios de administrador. Reabrindo como administrador, o código de saída foi `0` e o serviço foi criado.
- **Nome do serviço.** O comando `NET START Wazuh` retornou "service name is invalid". Descobri o nome correto com `Get-Service *wazuh*`: `WazuhSvc`.
- **Login recusado no dashboard dentro da VM.** A senha foi digitada à mão, pois o clipboard não é compartilhado entre host e VM, e o caractere `O` (letra) foi trocado por `0` (zero). Comparando com a saída do servidor, encontrei a diferença.

## Observações e aprendizado
Instaladores em modo silencioso (`/q`) podem falhar sem exibir erro, então o código de saída e o log do instalador são o caminho para diagnosticar. Também aprendi que o status do serviço e o nome real do serviço devem ser conferidos no sistema, e não assumidos. Por fim, copiar credenciais em vez de digitá-las evita erros com caracteres parecidos.

## Resultado
Agente `win11-endpoint` conectado e com status Active no Wazuh, com snapshot `02-agente-ok` criado. Próxima etapa: instalar o Sysmon para enriquecer a telemetria do Windows.



<img width="2559" height="943" alt="print wazuh dashboard conectado" src="https://github.com/user-attachments/assets/57e9d025-eeb9-4193-8ff3-42b8c169556a" />
