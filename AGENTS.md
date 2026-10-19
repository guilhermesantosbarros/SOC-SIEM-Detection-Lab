# Instruções para trabalhar neste laboratório

## Objetivo

Desenvolver um portfólio para SOC/Blue Team júnior. Simular uma pequena empresa que precisa centralizar logs Linux, detectar atividades suspeitas e investigar alertas. Demonstrar raciocínio de analista N1, além da instalação de ferramentas.

## Forma de trabalhar

- Use português simples e explique os termos técnicos e o propósito dos comandos.
- Trabalhe em etapas curtas, com poucos comandos por vez. O usuário está aprendendo e tem confundido o terminal do host com sessões SSH da VM.
- Identifique explicitamente o local de execução: host CachyOS, VM Linux monitorada ou futura VM Wazuh.
- Confirme o ambiente real antes de agir. A máquina onde o agente está executando não é necessariamente o host do laboratório. Não tente acessar os endereços privados do laboratório a partir da nuvem.
- Quando depender de uma saída do usuário, aguarde antes da etapa dependente. Se houver ferramentas locais disponíveis e autorização para consultas, explique a consulta e use resultados efetivamente obtidos.
- Preserve alterações existentes e use o checkout atual. Não crie outro worktree por padrão.
- Consulte a documentação oficial atual antes de definir requisitos, versão e comandos de instalação do Wazuh. Distinga recomendação de recursos, requisito mínimo e consumo medido.

## Evidências e segurança

- Não invente resultados, prints, logs, alertas ou etapas concluídas. Identifique evidências fornecidas pelo usuário e consultas realizadas diretamente.
- Diferencie registro original, alerta, suspeita e incidente confirmado. Não conte várias mensagens de uma mesma conexão como várias tentativas.
- Faça simulações somente no laboratório do usuário. IP interno pode pertencer a máquina comprometida e IP externo pode representar acesso autorizado.
- Não solicite nem publique senhas, chaves ou tokens no chat, nos arquivos ou nos comandos. Não despeje variáveis de ambiente ou arquivos de credenciais.
- Anonimize dados pessoais e endereços nas evidências públicas, preservando correlação. Use a convenção em `docs/evidencias/validacao-inicial-ssh.md`; os IPs públicos desse documento são substitutos e não destinos reais.
- Preserve TLS, assinaturas e verificação de checksums. Um hash calculado localmente não valida uma ISO sem comparação confiável.
- Não desative contas, desligue servidores ou aplique contenção automaticamente como ação de N1. Vincule contenção a autorização e procedimento.

## Escopo da V1

Linux primeiro; Windows fica para uma expansão. Investigar quatro casos controlados: falhas repetidas de SSH, falhas seguidas de sucesso, criação de usuário e alteração de privilégios. Verificar regras existentes antes de justificar regras personalizadas. Incluir atividade legítima para discutir falso positivo.

Para cada caso, registrar contexto, execução, logs/alertas observados, conta/máquina/origem/horários, hipóteses e verificações, conclusão, decisão de encerrar/escalar e contenção possível conforme autorização. Manter uma lista honesta de concluído e pendente.

## Retomada

Leia `docs/continuar-local.md`, o README e os documentos relacionados antes de continuar. Não instale o Wazuh na VM Linux atual: a proposta aceita para teste usa uma segunda VM central, ainda não criada, sujeita à verificação de recursos no momento da execução.
