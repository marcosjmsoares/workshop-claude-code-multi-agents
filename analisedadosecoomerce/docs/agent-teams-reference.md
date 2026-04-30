# Equipes de Agentes — Referência Claude Code

> **Fonte:** https://code.claude.com/docs/pt/agent-teams  
> **Descrição:** Coordene múltiplas instâncias Claude Code trabalhando juntas como uma equipe, com tarefas compartilhadas, mensagens entre agentes e gerenciamento centralizado.

---

## Índice

- [Quando usar equipes de agentes](#quando-usar-equipes-de-agentes)
- [Comparar com subagents](#comparar-com-subagents)
- [Ativar equipes de agentes](#ativar-equipes-de-agentes)
- [Inicie sua primeira equipe de agentes](#inicie-sua-primeira-equipe-de-agentes)
- [Controle sua equipe de agentes](#controle-sua-equipe-de-agentes)
  - [Escolha um modo de exibição](#escolha-um-modo-de-exibição)
  - [Especifique companheiros de equipe e modelos](#especifique-companheiros-de-equipe-e-modelos)
  - [Exigir aprovação de plano para companheiros de equipe](#exigir-aprovação-de-plano-para-companheiros-de-equipe)
  - [Fale com companheiros de equipe diretamente](#fale-com-companheiros-de-equipe-diretamente)
  - [Atribuir e reivindicar tarefas](#atribuir-e-reivindicar-tarefas)
  - [Encerrar companheiros de equipe](#encerrar-companheiros-de-equipe)
  - [Limpar a equipe](#limpar-a-equipe)
  - [Aplicar gates de qualidade com hooks](#aplicar-gates-de-qualidade-com-hooks)
- [Como funcionam as equipes de agentes](#como-funcionam-as-equipes-de-agentes)
  - [Como Claude inicia equipes de agentes](#como-claude-inicia-equipes-de-agentes)
  - [Arquitetura](#arquitetura)
  - [Usar definições de subagent para companheiros de equipe](#usar-definições-de-subagent-para-companheiros-de-equipe)
  - [Permissões](#permissões)
  - [Context e comunicação](#context-e-comunicação)
  - [Uso de tokens](#uso-de-tokens)
- [Exemplos de casos de uso](#exemplos-de-casos-de-uso)
  - [Executar uma revisão de código paralela](#executar-uma-revisão-de-código-paralela)
  - [Investigar com hipóteses concorrentes](#investigar-com-hipóteses-concorrentes)
- [Melhores práticas](#melhores-práticas)
- [Troubleshooting](#troubleshooting)
- [Limitações](#limitações)
- [Próximos passos](#próximos-passos)

---

## Quando usar equipes de agentes

Equipes de agentes são mais eficazes quando o trabalho pode ser dividido em partes independentes e executadas em paralelo. Exemplos de bons casos de uso:

- **Pesquisa e revisão:** múltiplos companheiros de equipe podem investigar diferentes aspectos de um problema simultaneamente, depois compartilhar e desafiar as descobertas uns dos outros.
- **Novos módulos ou recursos:** companheiros de equipe podem possuir cada um uma peça separada sem se atrapalharem.
- **Depuração com hipóteses concorrentes:** companheiros de equipe testam diferentes teorias em paralelo e convergem para a resposta mais rapidamente.
- **Coordenação entre camadas:** mudanças que abrangem frontend, backend e testes, cada uma de propriedade de um companheiro de equipe diferente.

---

## Comparar com subagents

| Característica | Subagents | Equipes de Agentes |
|---|---|---|
| Execução | Sequencial / dentro da sessão | Paralela / sessões independentes |
| Coordenação | Automática pelo líder | Lista de tarefas compartilhada |
| Melhor para | Tarefas auxiliares simples | Trabalho de grande escala dividível |

Veja a comparação completa em [subagent vs agent team](https://code.claude.com/docs/pt/features-overview#compare-similar-features).

---

## Ativar equipes de agentes

Equipes de agentes são um **recurso experimental**. Para ativar, defina a variável de ambiente:

```
CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

Ou adicione ao seu `settings.json`:

```json
{ "env": { "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1" } }
```

---

## Inicie sua primeira equipe de agentes

Dê ao Claude uma tarefa que se beneficie de trabalho paralelo e peça explicitamente uma equipe:

```
I'm designing a CLI tool that helps developers track TODO comments across their codebase.
Create an agent team to explore this from different angles:
one teammate on UX, one on technical architecture, one playing devil's advocate.
```

Claude criará a equipe com base nas suas instruções, criará uma **lista de tarefas compartilhada** e distribuirá o trabalho entre os companheiros de equipe.

> Ao finalizar o trabalho, não se esqueça de [limpar a equipe](#limpar-a-equipe).

---

## Controle sua equipe de agentes

### Escolha um modo de exibição

Existem dois modos disponíveis:

| Modo | Descrição | Requisitos |
|---|---|---|
| **In-process** | Todos os companheiros de equipe são executados dentro do terminal principal. Use `Shift+Down` para percorrê-los. | Qualquer terminal |
| **Split panes** | Cada companheiro de equipe recebe seu próprio painel. Você vê a saída de todos ao mesmo tempo. | tmux ou iTerm2 |

Configure o modo no `~/.claude/settings.json`:

```json
{ "teammateMode": "in-process" }
```

Ou via linha de comando:

```bash
claude --teammate-mode in-process
```

**Instalando as dependências para split panes:**

- **tmux:** instale através do gerenciador de pacotes do seu sistema. Veja o [wiki tmux](https://github.com/tmux/tmux/wiki/Installing) para instruções específicas da plataforma.
- **iTerm2:** instale o CLI [it2](https://github.com/mkusaka/it2), depois ative a API Python em *iTerm2 → Settings → General → Magic → Enable Python API*.

---

### Especifique companheiros de equipe e modelos

Você pode instruir o Claude a usar um modelo específico para os companheiros de equipe:

```
Create a team with 4 teammates to refactor these modules in parallel. Use Sonnet for each teammate.
```

---

### Exigir aprovação de plano para companheiros de equipe

Para maior controle, peça que o companheiro de equipe apresente um plano antes de fazer mudanças:

```
Spawn an architect teammate to refactor the authentication module.
Require plan approval before they make any changes.
```

---

### Fale com companheiros de equipe diretamente

- **Modo in-process:** use `Shift+Down` para percorrer os companheiros de equipe, depois digite para enviar uma mensagem. Pressione `Enter` para visualizar a sessão do companheiro de equipe, depois `Escape` para interromper seu turno atual. Pressione `Ctrl+T` para alternar a lista de tarefas.
- **Modo split-pane:** clique em um painel de companheiro de equipe para interagir com sua sessão diretamente. Cada companheiro de equipe tem uma visualização completa de seu próprio terminal.

---

### Atribuir e reivindicar tarefas

Existem duas formas de distribuir trabalho:

- **Líder atribui:** diga ao líder qual tarefa dar a qual companheiro de equipe.
- **Auto-reivindicar:** após terminar uma tarefa, um companheiro de equipe pega a próxima tarefa não atribuída e desbloqueada por conta própria.

---

### Encerrar companheiros de equipe

Para encerrar um companheiro de equipe específico, instrua o líder:

```
Ask the researcher teammate to shut down
```

---

### Limpar a equipe

Quando o trabalho estiver concluído, limpe a equipe para liberar recursos:

```
Clean up the team
```

---

### Aplicar gates de qualidade com hooks

Use [hooks](https://code.claude.com/docs/pt/hooks) para aplicar verificações automáticas de qualidade:

| Hook | Quando é executado | Como usar |
|---|---|---|
| `TeammateIdle` | Quando um companheiro de equipe está prestes a ficar ocioso | Saia com código `2` para enviar feedback e manter o companheiro de equipe trabalhando |
| `TaskCreated` | Quando uma tarefa está sendo criada | Saia com código `2` para evitar criação e enviar feedback |
| `TaskCompleted` | Quando uma tarefa está sendo marcada como concluída | Saia com código `2` para evitar conclusão e enviar feedback |

---

## Como funcionam as equipes de agentes

### Como Claude inicia equipes de agentes

Há duas formas de iniciar uma equipe:

1. **Você solicita uma equipe:** dê ao Claude uma tarefa que se beneficie do trabalho paralelo e peça explicitamente uma equipe de agentes. Claude cria uma com base em suas instruções.
2. **Claude propõe uma equipe:** se Claude determinar que sua tarefa se beneficiaria do trabalho paralelo, pode sugerir criar uma equipe. Você confirma antes que ele proceda.

---

### Arquitetura

As equipes são gerenciadas por arquivos de configuração locais:

| Arquivo | Descrição |
|---|---|
| `~/.claude/teams/{team-name}/config.json` | Configuração da equipe |
| `~/.claude/tasks/{team-name}/` | Lista de tarefas compartilhada |
| `.claude/teams/teams.json` | Definições de membros da equipe |

---

### Usar definições de subagent para companheiros de equipe

Você pode usar um [subagent](https://code.claude.com/docs/pt/sub-agents) existente como base para um companheiro de equipe:

```
Spawn a teammate using the security-reviewer agent type to audit the auth module.
```

As definições de subagent podem incluir:
- `tools` — ferramentas disponíveis
- `model` — modelo a ser usado
- `SendMessage` — permissões de mensagem
- `skills` — skills associadas
- `mcpServers` — servidores MCP configurados

---

### Permissões

Por padrão, os companheiros de equipe herdam o modo de permissão do líder. O flag `--dangerously-skip-permissions` pode ser usado com cuidado para fluxos de trabalho automatizados.

---

### Context e comunicação

- **Entrega automática de mensagens:** quando os companheiros de equipe enviam mensagens, elas são entregues automaticamente aos destinatários. O líder não precisa fazer polling para atualizações.
- **Notificações de ociosidade:** quando um companheiro de equipe termina e para, ele notifica automaticamente o líder.
- **Lista de tarefas compartilhada:** todos os agentes podem ver o status da tarefa e reivindicar trabalho disponível.
- **Mensagens de companheiros de equipe:** envie uma mensagem para um companheiro de equipe específico pelo nome. Para alcançar todos, envie uma mensagem por destinatário.

---

### Uso de tokens

Cada companheiro de equipe tem sua própria context window e consome tokens independentemente. Consulte [custos de token de equipe de agentes](https://code.claude.com/docs/pt/costs#agent-team-token-costs) para detalhes.

---

## Exemplos de casos de uso

### Executar uma revisão de código paralela

```
Create an agent team to review PR #142. Spawn three reviewers:
- One focused on security implications
- One checking performance impact
- One validating test coverage
Have them each review and report findings.
```

---

### Investigar com hipóteses concorrentes

```
Users report the app exits after one message instead of staying connected.
Spawn 5 agent teammates to investigate different hypotheses.
Have them talk to each other to try to disprove each other's theories, like a scientific debate.
Update the findings doc with whatever consensus emerges.
```

---

## Melhores práticas

### Dê aos companheiros de equipe contexto suficiente

Forneça instruções detalhadas ao gerar cada companheiro de equipe:

```
Spawn a security reviewer teammate with the prompt:
"Review the authentication module at src/auth/ for security vulnerabilities.
Focus on token handling, session management, and input validation.
The app uses JWT tokens stored in httpOnly cookies.
Report any issues with severity ratings."
```

---

### Escolha um tamanho de equipe apropriado

- **Custos de token escalam linearmente:** cada companheiro de equipe tem sua própria context window e consome tokens independentemente.
- **Sobrecarga de coordenação aumenta:** mais companheiros de equipe significa mais comunicação, coordenação de tarefas e potencial para conflitos.
- **Retornos decrescentes:** além de um certo ponto, companheiros de equipe adicionais não aceleram o trabalho proporcionalmente.

---

### Dimensione tarefas apropriadamente

| Tamanho | Problema |
|---|---|
| **Muito pequeno** | Sobrecarga de coordenação excede o benefício |
| **Muito grande** | Companheiros de equipe trabalham muito tempo sem check-ins, aumentando o risco de esforço desperdiçado |
| **Bem dimensionado** | Unidades auto-contidas que produzem um entregável claro (ex.: uma função, um arquivo de teste ou uma revisão) |

---

### Espere os companheiros de equipe terminarem

Instrua o líder explicitamente quando necessário:

```
Wait for your teammates to complete their tasks before proceeding
```

---

### Comece com pesquisa e revisão

Antes de implementar, use companheiros de equipe para investigar o problema e reunir informações. Isso reduz o risco de esforço duplicado ou mal direcionado.

---

### Evite conflitos de arquivo

Atribua arquivos ou módulos distintos a cada companheiro de equipe para evitar que dois agentes editem o mesmo arquivo simultaneamente.

---

### Monitore e direcione

Acompanhe o progresso da equipe regularmente usando o modo de exibição escolhido. Intervenha quando um companheiro de equipe estiver preso ou tomando uma direção errada.

---

## Troubleshooting

### Companheiros de equipe não aparecem

- No modo in-process, os companheiros de equipe podem já estar em execução, mas não visíveis. Pressione `Shift+Down` para percorrer os companheiros de equipe ativos.
- Verifique se a tarefa que você deu ao Claude era complexa o suficiente para justificar uma equipe. Claude decide se deve gerar companheiros de equipe com base na tarefa.
- Se você explicitamente solicitou split panes, certifique-se de que tmux está instalado e disponível no seu PATH:

```bash
which tmux
```

- Para iTerm2, verifique se o CLI `it2` está instalado e a API Python está ativada nas preferências do iTerm2.

---

### Muitos prompts de permissão

Revise suas [configurações de permissão](https://code.claude.com/docs/pt/permissions) para reduzir o número de confirmações necessárias durante a execução da equipe.

---

### Companheiros de equipe parando em erros

- Dê a eles instruções adicionais diretamente via mensagem.
- Gere um companheiro de equipe de substituição para continuar o trabalho.

---

### Líder encerra antes do trabalho estar pronto

Instrua o líder explicitamente para aguardar a conclusão de todos os companheiros de equipe antes de encerrar a sessão.

---

### Sessões tmux órfãs

Se sessões tmux ficarem ativas após o encerramento da equipe, limpe-as manualmente:

```bash
tmux ls
tmux kill-session -t <session-name>
```

---

## Limitações

| Limitação | Detalhes |
|---|---|
| **Sem retomada de sessão (in-process)** | `/resume` e `/rewind` não restauram companheiros de equipe in-process. Após retomar, o líder pode tentar enviar mensagens para companheiros que não existem mais — gere novos companheiros se isso acontecer. |
| **Status de tarefa pode atrasar** | Os companheiros de equipe às vezes falham em marcar tarefas como concluídas, bloqueando tarefas dependentes. Verifique o trabalho manualmente e atualize o status ou instrua o líder a pressionar o companheiro de equipe. |
| **Encerramento pode ser lento** | Os companheiros de equipe terminam a solicitação ou chamada de ferramenta atual antes de encerrar. |
| **Uma equipe por sessão** | Um líder pode gerenciar apenas uma equipe por vez. Limpe a equipe atual antes de iniciar uma nova. |
| **Sem equipes aninhadas** | Os companheiros de equipe não podem gerar suas próprias equipes ou companheiros. Apenas o líder pode gerenciar a equipe. |
| **Líder é fixo** | A sessão que cria a equipe é o líder por sua vida útil. Não é possível promover um companheiro de equipe a líder. |
| **Permissões definidas no tempo de geração** | Todos os companheiros de equipe começam com o modo de permissão do líder. Você pode alterar modos individualmente após gerar, mas não pode definir modos por companheiro no tempo de geração. |
| **Split panes requerem tmux ou iTerm2** | O modo in-process funciona em qualquer terminal. O modo split-pane **não é suportado** no terminal integrado do VS Code, Windows Terminal ou Ghostty. |

---

## Próximos passos

- **Delegação leve:** [subagents](https://code.claude.com/docs/pt/sub-agents) geram agentes auxiliares para pesquisa ou verificação dentro de sua sessão — melhor para tarefas que não precisam de coordenação entre agentes.
- **Sessões paralelas manuais:** [Git worktrees](https://code.claude.com/docs/pt/common-workflows#run-parallel-claude-code-sessions-with-git-worktrees) permitem executar múltiplas sessões Claude Code sem coordenação de equipe automatizada.
- **Comparar abordagens:** veja a comparação [subagent vs agent team](https://code.claude.com/docs/pt/features-overview#compare-similar-features) para um detalhamento lado a lado.
