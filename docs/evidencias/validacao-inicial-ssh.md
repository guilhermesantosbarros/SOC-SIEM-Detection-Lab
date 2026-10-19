# Validação inicial: falha de autenticação SSH

Status: uma tentativa rejeitada confirmada no servidor. Esta validação é um pré-requisito da V1; não conclui o caso de falhas repetidas.

## Contexto e objetivo

Verificar como o Ubuntu registra uma autenticação SSH rejeitada antes de centralizar registros em um SIEM. O teste foi realizado pelo responsável, exclusivamente no próprio laboratório.

Ambiente informado e confirmado pelas saídas compartilhadas: host CachyOS, KVM/libvirt e VM Ubuntu 24.04.5 LTS. Na coleta inicial, o banner de login indicava uma raiz de aproximadamente 11 GB. Consultas posteriores confirmaram os recursos e foram registradas no [inventário e proposta de arquitetura](../arquitetura-v1.md).

## Origem e anonimização da evidência

Transcrição fornecida pelo responsável no chat. Não houve acesso direto do assistente ao host ou à VM, nem coleta independente ou verificação de integridade dos registros originais.

Somente os identificadores abaixo foram substituídos nos trechos publicados. Horários em UTC, mensagens, PIDs e portas foram preservados.

| Identificador | Substituto público |
| --- | --- |
| Hostname da VM | `vm-linux` |
| Conta de teste | `usuario-lab` |
| IP do host na rede do laboratório | `192.0.2.10` |
| IP da VM | `192.0.2.20` |

Os endereços `192.0.2.0/24` são reservados para documentação. Não são os endereços reais do laboratório e não devem ser usados para conectar à VM.

## Execução

No host CachyOS, foi executado o comando abaixo contra o endereço real da VM. O endereço publicado está anonimizado:

```bash
ssh -o PubkeyAuthentication=no -o PreferredAuthentications=password -o NumberOfPasswordPrompts=1 usuario-lab@192.0.2.20
```

O teste solicitava o uso de uma senha propositalmente incorreta. O conteúdo digitado não foi coletado. O cliente retornou:

```text
usuario-lab@192.0.2.20: Permission denied (publickey,password).
```

Na sessão já aberta na VM Ubuntu, o responsável consultou:

```bash
sudo journalctl -u ssh --since "5 minutes ago" -n 40 --utc -o short-iso --no-pager
```

Essa janela foi usada durante o teste. Reexecutar o comando em outra ocasião consulta outro intervalo e pode não recuperar os mesmos eventos.

## Logs efetivamente observados

```text
2026-10-19T20:12:45+00:00 vm-linux sshd[11987]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.0.2.10  user=usuario-lab
2026-10-19T20:12:47+00:00 vm-linux sshd[11987]: Failed password for usuario-lab from 192.0.2.10 port 44842 ssh2
2026-10-19T20:12:47+00:00 vm-linux sshd[11987]: Connection closed by authenticating user usuario-lab 192.0.2.10 port 44842 [preauth]
```

As três mensagens pertencem ao mesmo processo `sshd[11987]`. A mensagem de falha de senha e a de fechamento compartilham origem e porta. Elas descrevem uma tentativa rejeitada e seu encerramento, não três tentativas.

`[preauth]` indica fechamento antes de completar a autenticação. `uid=0 euid=0` descreve o contexto privilegiado do processo de autenticação; não demonstra que a conta de teste obteve privilégios de administrador.

## Hipóteses e verificações

| Hipótese | Evidência e avaliação |
| --- | --- |
| A autenticação por senha falhou | Confirmada por `Failed password`, coerente com a recusa do cliente. |
| A origem foi o host do laboratório | O endereço registrado corresponde ao host conforme o contexto fornecido pelo responsável. O endereço interno, isoladamente, não estabelece legitimidade. |
| Houve várias tentativas ou força bruta | Não demonstrado por essa conexão. Uma falha isolada não sustenta essa conclusão. |
| Houve login bem-sucedido após essa falha | Não demonstrado no trecho fornecido. |
| A tentativa antiga já havia sido comprovada | Não. Esta evidência corresponde ao novo teste de 19/10/2026 às 20:12 UTC. |

O trecho consultado também continha um login aceito anterior, às 20:10:56 UTC, com origem no endereço da própria VM. Conforme a sequência informada pelo responsável, foi uma conexão da VM para si mesma durante a preparação. Ele não demonstra sucesso posterior à falha de 20:12:47.

## Conclusão, triagem e decisão

Conclusão sustentada: uma autenticação SSH por senha foi rejeitada para a conta de teste; a conexão foi encerrada antes da autenticação. A origem registrada corresponde ao host do laboratório.

No contexto informado, trata-se de um teste controlado e autorizado. A decisão para este exercício é encerrar a triagem desta tentativa como atividade de teste, sem contenção e sem escalonamento. Isso não conclui a análise de outros eventos ou de todo o ambiente.

Sem esse contexto, seria necessário verificar com o responsável pela conta, analisar recorrência e outros acessos e seguir o procedimento de escalonamento. Desativar contas ou desligar máquinas não é uma ação automática do analista N1; qualquer contenção depende de autorização e procedimento.

## Alertas e limitações

- Registro original observado: sim, pelo journal do SSH, conforme transcrição compartilhada.
- Alerta de SIEM: não demonstrado. Nenhuma saída do Wazuh foi fornecida.
- Regra de detecção aplicada ou validada: nenhuma nesta etapa.
- Incidente confirmado: não há evidência de incidente neste teste.
- Caso de falhas repetidas: pendente; foi confirmada apenas uma tentativa rejeitada.
- Não foram produzidos prints, arquivos originais exportados ou hashes das evidências.
- O inventário da VM e a proposta de arquitetura foram documentados posteriormente. Ainda falta confirmar a capacidade disponível no host, definir a alocação final e instalar e validar a centralização de logs antes de testar as quatro detecções da V1.
