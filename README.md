# OWASP LLM01 Guardrails Template & Blueprint de Segurança de IA

[![Licence MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![OWASP LLM01](https://img.shields.io/badge/OWASP-LLM01%20Hardened-ff0055.svg)](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
[![Language](https://img.shields.io/badge/Language-Português%20(Portugal)-green.svg)](README.md)
[![AI Diagnostics](https://img.shields.io/badge/TAVARDT-AI%20Evaluator-5b3cf5.svg)](https://tools.tavardt.com/pt/avaliador-robustez-llm/)

Blueprint declarativo e middleware de código aberto para mitigação de Prompt Injection (OWASP LLM01), fuga de system prompt e manipulação adversarial de agentes de IA, em conformidade com o RGPD e o Regulamento Europeu de IA (AI Act).

Desenvolvido e mantido pela equipa de engenharia da **TAVARDT**.

***

## 1. Visão Geral da Arquitetura de Segurança para IA

Este blueprint disponibiliza uma estrutura modular de barreiras de proteção (**guardrails**) concebida para agentes autónomos, arquiteturas RAG e serviços empresariais que comunicam com Large Language Models (OpenAI, Anthropic, Gemini, Llama, Mistral).

### Salvaguarda Essencial na Camada de Aplicação
Os LLMs não distinguem nativamente diretrizes internas de sistema de entradas submetidas pelo utilizador. Sem validação ativa na entrada e na saída, as plataformas corporativas expõem-se à manipulação de contexto, extração indevida de dados confidenciais e violação das diretivas de conformidade europeias.

***

## 2. Vetores de Risco Cobertos (OWASP Top 10 for LLMs)

| Camada de Defesa | Vetor Mitigado | Descrição da Proteção |
| :- | :- | :- |
| **Input Sanitizer** | Injeção Direta de Prompt (Jailbreak) | Deteção e bloqueio de instruções adversariais (`ignora as instruções anteriores`, `DAN`, tags XML fictícias). |
| **Canary Token Verifier** | Fuga de System Prompt | Rastreio de tokens canário invioláveis na resposta para evitar a exfiltração do prompt de sistema. |
| **RAG Boundary Guard** | Injeção Indireta de Prompt | Controlo rigoroso de documentos recuperados para bases vetoriais e fontes externas de terceiros. |
| **Structural Output Filter** | Execução Não Autorizada de Tools | Validação estrita de esquemas JSON antes do acionamento de chamadas de funções e conectores MCP. |

***

## 3. Diagnóstico Prévio de Robustez do Modelo

Antes de disponibilizar agentes de inteligência artificial em canais operacionais ou de apoio ao cliente, valide a solidez do seu sistema com a suíte de auditoria da TAVARDT:

* **Auditoria de Robustez de IA:** Avaliador de Robustez de LLMs TAVARDT
* **Catálogo Completo de Diagnósticos:** **TAVARDT Tools Portugal**

***

## 4. Instalação e Guia de Utilização Rápida

### Clonagem & Execução

```bash
git clone https://github.com/TAVARDT-Portugal/owasp-llm-guardrails-template.git
cd owasp-llm-guardrails-template
python examples/quickstart.py
```

### Exemplo de Integração em Python

```python
from guardrails.validator import LLMGuardrail

guard = LLMGuardrail()

# 1. Validação de Entrada (Input Guardrail)
is_safe, error = guard.validate_input(user_input)
if not is_safe:
    raise ValueError(f"Entrada bloqueada por padrão suspeito: {error}")

# 2. Injeção de Token Canário nas Instruções de Sistema
hardened_system_prompt = guard.get_canary_injection_prompt(system_prompt)

# 3. Validação de Saída (Output Guardrail)
is_valid, leak_error = guard.validate_output(completion)
if not is_valid:
    raise SecurityError("Falha crítica: Fuga de prompt de sistema detetada!")
```

***

## 5. Consultoria Especializada em Agentes de IA & Cibersegurança

Para organizações que necessitam de implementação de agentes autónomos com segurança rigorosa, conformidade com o Regulamento de IA da UE e suporte de engenharia contínuo:

* **Portal Corporativo:** **TAVARDT**
* **Contacto Técnico Dedicado:** [Apoio de Engenharia Especializada](https://ag.tavardt.com/pt/contactos/)

***

## Licença

Distribuído sob a licença de código aberto MIT. Consulte o ficheiro [`LICENSE`](LICENSE) para mais detalhes.  
Copyright

***

© 2026 TAVARDT. Todos os direitos reservados.
