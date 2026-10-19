# Continuação pelo Codex CLI local

Este resumo transfere o contexto da conversa de preparação na nuvem. Leia também `AGENTS.md`, o [inventário e a arquitetura proposta](arquitetura-v1.md) e a [evidência SSH](evidencias/validacao-inicial-ssh.md). Confirme o estado atual antes de executar mudanças; os dados abaixo são observações anteriores fornecidas pelo usuário, não consultas atuais do novo agente.

## Objetivo e método

Construir um portfólio SOC/Blue Team júnior enquanto o usuário aprende. Avançar em português simples, com poucos comandos por etapa e indicação explícita do local de execução. Aguardar saídas necessárias. O usuário já confundiu comandos do host com comandos executados dentro do SSH e já colou três comandos sem separação; prefira um comando por vez.

A V1 deve demonstrar centralização Linux, quatro casos de detecção e investigação, regras justificadas, falso positivo, triagem e escalonamento. Windows fica para depois. Ainda não há Wazuh instalado ou validado nesta sequência.

## Ambiente e recursos observados

- Host: CachyOS, KVM/QEMU, libvirt e virt-manager. Ryzen 5 5500 e 16 GB de RAM informados pelo usuário.
- `free -h` no host mostrou 15 GiB utilizáveis, 9 GiB usados, 6,5 GiB disponíveis e swap de 15 GiB com uso muito pequeno. São valores de um momento específico, que precisam ser atualizados.
- Libvirt do sistema: `qemu:///system`. VM Ubuntu executando e VM Windows desligada na listagem fornecida.
- Pool `default`: ativo, caminho de imagens observado em `/var/lib/libvirt/images`; capacidade 740,47 GiB, alocação 293,82 GiB e disponível 446,65 GiB conforme `pool-info` fornecido. A disponibilidade atual deve ser reconfirmada.
- VM Linux atual: Ubuntu 24.04.5 LTS, KVM, 2 processadores disponíveis (`nproc`), RAM de 1,9 GiB e swap de 2 GiB.
- Disco virtual atual: 25 GB; `/boot` de 2 GB; partição LVM de 23 GB; raiz ext4 em volume lógico de 11,5 GB. `df` mostrou 5,6 GB disponíveis na raiz; `vgs` mostrou 11,50 GB livres no grupo `ubuntu-vg`. Nenhuma ampliação foi executada.
- SSH funciona entre o host e a VM via rede NAT do libvirt. Identifique os nomes e os endereços reais localmente; os identificadores das evidências públicas estão anonimizados.
- Codex CLI instalado no CachyOS: versão informada pelo usuário `0.159.3`. A transferência do contexto não implica que a sessão local já esteja iniciada.

## O que já foi comprovado

- Um login SSH por senha aceito a partir do host, conforme log fornecido às 20:05:48 UTC de 19/10/2026.
- Durante a preparação, houve conexões bem-sucedidas da VM para ela mesma. Não tratá-las como falhas, nem como sucesso posterior à tentativa rejeitada.
- Uma tentativa SSH rejeitada a partir do host, com falha de autenticação às 20:12:45 UTC e `Failed password` às 20:12:47 UTC, seguida de encerramento `[preauth]`.
- As três mensagens de falha/encerramento pertencem à mesma conexão e não comprovam falhas repetidas.
- Nenhum alerta Wazuh ou incidente foi comprovado. A tentativa antiga mencionada antes desta sequência permanece sem evidência; a nova tentativa tem transcrição anonimizada no repositório.

## Decisão de arquitetura

Proposta: manter a VM Linux como máquina monitorada e criar uma segunda VM Ubuntu para os componentes centrais do Wazuh, com 4 vCPU, 8 GiB de RAM e disco próprio de 80 GB. Planejar a raiz com espaço suficiente; o instalador Ubuntu pode deixar parte do disco sem atribuição ao volume lógico.

O Quickstart oficial consultado recomenda 4 vCPU, 8 GiB de RAM e 50 GB para 1–25 agentes e 90 dias de alertas. A fonte oficial exata está registrada no documento de arquitetura. Consultar novamente antes da instalação e escolher explicitamente a versão.

O usuário decidiu tentar com a carga atual do host, mesmo após ser informado de que havia apenas 6,5 GiB disponíveis para uma nova VM de 8 GiB. Não reduzir silenciosamente a memória do Wazuh para contornar os requisitos. A execução simultânea, desempenho e pressão de memória ainda não foram testados. Monitorar e ajustar com o usuário se necessário.

## Ponto exato de retomada: ISO Ubuntu

A ISO existente no diretório Downloads do usuário tem o nome `ubuntu-24.04.3-live-server-amd64.iso`. Essa ISO foi identificada como CD-ROM da VM atual. O caminho pessoal foi omitido aqui.

O usuário calculou o SHA-256 local:

```text
c3514bf0056180d09376462a7a1b4f213c1d6e8ea67fae5c25099c6fd3d8274b
```

**A integridade ainda não foi confirmada por comparação com a fonte oficial.** Não anunciar o hash como validado.

Uma consulta a `https://old-releases.ubuntu.com/releases/24.04.3/SHA256SUMS` no host falhou com `curl: (56) Recv failure: Conexão fechada pela outra ponta`. Depois foi sugerido este comando no host, mas nenhuma saída foi fornecida:

```bash
curl --http1.1 --fail --location --show-error --connect-timeout 10 --max-time 30 https://releases.ubuntu.com/24.04.3/SHA256SUMS
```

O próximo passo técnico é obter o manifesto oficial de forma confiável e comparar o hash completo da linha da ISO correta. Se a conexão falhar, diagnosticar a falha; não desabilitar TLS ou presumir correspondência. Não executar comandos de instalação do Wazuh nessa etapa.

## Depois da validação da ISO

1. Criar uma nova VM e um disco próprio, preservando a VM Linux atual. Confirmar como a memória se comporta com as duas ligadas.
2. Instalar Ubuntu e conferir sistema, rede, SSH e recursos efetivos, inclusive tamanho do sistema de arquivos da raiz.
3. Consultar a documentação Wazuh atual, instalar os componentes centrais e validar o funcionamento real.
4. Instalar e configurar a coleta no Linux; validar como os registros do SSH chegam ao Wazuh antes de testar detecções.
5. Executar os quatro casos gradualmente e documentar o que foi realmente observado. Incluir scripts em Bash/Python quando ajudarem a investigação ou anonimização, explicando o código ao usuário.

Não há evidência de que qualquer uma dessas cinco etapas tenha sido executada.
