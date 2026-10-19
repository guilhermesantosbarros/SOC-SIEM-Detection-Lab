# 🛡️ SOC / SIEM Detection Lab

> 🚧 Projeto em desenvolvimento

Laboratório de cibersegurança defensiva criado para praticar conceitos utilizados em ambientes SOC e Blue Team.

O projeto utilizará meu Home Lab como ambiente para coleta e centralização de logs, monitoramento de eventos de segurança, detecção de atividades suspeitas e investigação de alertas.

## 🎯 Objetivos

- Aprender os fundamentos de um SOC
- Entender o funcionamento de um SIEM
- Coletar e analisar logs de sistemas Linux e Windows
- Monitorar eventos de segurança
- Criar e analisar alertas
- Praticar triagem e investigação de eventos
- Documentar investigações realizadas no laboratório
- Conhecer conceitos relacionados ao MITRE ATT&CK

## 🧪 Ambiente planejado

O laboratório será construído utilizando máquinas virtuais no meu Home Lab.

Estrutura inicial planejada:

- Host Linux
- Ubuntu Server
- Windows
- SIEM
- Wazuh
- KVM/QEMU
- libvirt

## 🔎 Casos de estudo

Durante o desenvolvimento serão documentados casos de investigação realizados exclusivamente dentro do próprio laboratório.

Os casos serão adicionados conforme forem implementados e testados.

## 📚 Aprendizados

Esta seção será atualizada durante o desenvolvimento com os principais conceitos e práticas aprendidos no projeto.

## 📌 Status

🚧 Em desenvolvimento

### Progresso verificado da V1 Linux

- [x] Identificação do host CachyOS e da VM Ubuntu 24.04.5 LTS pelas saídas compartilhadas.
- [x] Consulta dos registros originais de autenticação SSH na VM.
- [x] Uma tentativa SSH rejeitada confirmada nos registros do servidor: [evidência anonimizada e análise](docs/evidencias/validacao-inicial-ssh.md).
- [x] Verificar memória, processadores, disco e espaço disponível na VM Linux.
- [x] Consultar o Quickstart oficial e documentar a [arquitetura proposta, inventário e diagrama](docs/arquitetura-v1.md).
- [x] Consultar RAM do host e armazenamento do libvirt; registrar a decisão de tentar a VM Wazuh proposta.
- [ ] Validar a ISO do Ubuntu, criar a VM Wazuh e testar a execução simultânea com a VM Linux.
- [ ] Instalar e validar a centralização de logs Linux.
- [ ] Investigar falhas repetidas de login SSH.
- [ ] Investigar falhas seguidas de login bem-sucedido.
- [ ] Investigar criação de usuário.
- [ ] Investigar alteração de privilégios.
- [ ] Validar regras existentes e justificar qualquer regra personalizada.
- [ ] Incluir atividade legítima para discutir falso positivo e concluir o procedimento de triagem e escalonamento.

Windows fica como expansão após a V1 Linux. Um registro original de autenticação não equivale a um alerta de SIEM; a tentativa rejeitada já observada não conclui o caso de falhas repetidas nem demonstra um incidente.

Para continuar o trabalho no computador do laboratório, leia as [instruções de retomada local](docs/continuar-local.md) e o [AGENTS.md](AGENTS.md).
