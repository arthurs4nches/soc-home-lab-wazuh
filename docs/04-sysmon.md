# Lab: Instalação do Sysmon e coleta pelo agente Wazuh

## Objetivo
Enriquecer a telemetria do endpoint Windows com o Sysmon (com a configuração da comunidade SwiftOnSecurity) e fazer o agente Wazuh coletar o canal do Sysmon, para que eventos como criação de processos e arquivos gerem alertas.

## Metodologia
- Consulta à documentação oficial do Wazuh para o canal e o formato de coleta
- Download do Sysmon (Sysinternals) e da configuração SwiftOnSecurity
- Instalação do Sysmon com a configuração e validação dos eventos no Visualizador de Eventos
- Backup do `ossec.conf` e inclusão do bloco `localfile` para o canal `Microsoft-Windows-Sysmon/Operational`
- Reinício do agente, validação pelo log e busca dos eventos no dashboard

## Processo
Baixei o Sysmon e a configuração SwiftOnSecurity diretamente na VM e instalei com `Sysmon64.exe -accepteula -i`. Validei o serviço e os eventos de criação de processo (ID 1) com `Get-WinEvent`.

Em seguida, fiz backup do `C:\Program Files (x86)\ossec-agent\ossec.conf` e adicionei, antes de `</ossec_config>`, o bloco `localfile` com `location` igual a `Microsoft-Windows-Sysmon/Operational` e `log_format` igual a `eventchannel`. Reiniciei o serviço `WazuhSvc` e confirmei no `ossec.log` a mensagem "Analyzing event log". No dashboard, a busca `rule.groups: sysmon` passou a retornar eventos do endpoint.

### Problemas encontrados
- **Serviço do agente parou após editar o `ossec.conf`.** Havia uma tag `</logging>` duplicada, deixada na edição manual, que invalidou o XML. Removi a linha extra e o serviço voltou a iniciar.
- **Configuração desfeita sem querer.** Ao restaurar o backup por engano, o bloco do Sysmon foi removido e o agente parou de coletar o canal. Refiz a edição e validei o log com horário posterior ao reinício.

## Observações e aprendizado
A edição de XML exige conferir abertura e fechamento de tags, e o backup antes de alterar um arquivo de configuração evita perda de tempo. Também confirmei que o nome do serviço do agente é `WazuhSvc`, e não o nome usado em exemplos da documentação. A primeira rajada de alertas (regra 92217) foi triada e classificada como falso positivo (ver `incident-reports/01-ngen-falso-positivo.md`).

## Resultado
Agente coletando o canal do Sysmon e eventos visíveis no dashboard, com snapshot `03-sysmon-ok` criado.
