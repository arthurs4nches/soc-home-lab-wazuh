# Incidente 01: Executáveis criados na pasta do Windows (regra 92217)

## Resumo
Após instalar o Sysmon no endpoint `win11-endpoint`, o Wazuh gerou 40 alertas da regra 92217 ("Executable dropped in Windows root folder", nível 6), mapeada ao ATT&CK T1570 (Lateral Tool Transfer). A investigação concluiu que a atividade é legítima: o processo `mscorsvw.exe` (.NET NGEN) gerando imagens nativas de bibliotecas do .NET.

## Linha do tempo (horário do dashboard)
- Instalação do Sysmon e início da coleta no agente.
- Cerca de 22:23: rajada de 40 alertas em aproximadamente 2 minutos, todos da mesma regra.
- Sem novos alertas depois da rajada.

## Evidências
- Evento Sysmon ID 11 (File Created), regra Wazuh 92217, nível 6.
- Processo: `C:\Windows\Microsoft.NET\Framework\v4.0.30319\mscorsvw.exe`, usuário `NT AUTHORITY\SYSTEM`.
- Arquivos criados em `C:\Windows\assembly\NativeImages_v4.0.30319_32\Temp\...`: DLLs do próprio .NET (`System.Xml.Linq.dll`, `System.Core.dll`, `PresentationFramework`, `WindowsBase.dll` e outras).
- Assinatura digital do `mscorsvw.exe` verificada com `Get-AuthenticodeSignature`: **Valid**.
- Origem do evento: local (agente 001); nenhuma transferência vinda de outro host.

## IOCs
Nenhum indicador malicioso identificado. Artefatos observados e classificados como legítimos: `mscorsvw.exe` (assinado) e DLLs do .NET na pasta temporária do NGEN.

## Técnicas ATT&CK
T1570 (Lateral Tool Transfer), atribuída pela regra. Não confirmada: a regra é genérica e o comportamento observado não envolve movimentação entre máquinas.

## Veredito
**Falso positivo.** Atividade esperada do .NET NGEN em um sistema recém-instalado.

## Resposta recomendada
- Nenhuma contenção necessária.
- Solicitar tuning: criar regra filha que ignore apenas o conjunto `mscorsvw.exe` + caminho completo + usuário SYSTEM + pasta `NativeImages*\Temp`, sem desativar a regra 92217.
- Evitar exceção apenas por nome de processo, que um atacante poderia imitar.



<img width="1568" height="637" alt="image_2820261028-000817_29" src="https://github.com/user-attachments/assets/a122b323-e197-44f0-9920-e611d7c78381" />
