# OWASP LLM01 Guardrails Template & Blueprint de Segurança para IA

[![OWASP LLM01](https://img.shields.io/badge/OWASP-LLM01%20Hardened-ff0055.svg)](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
[![AI Diagnostics](https://img.shields.io/badge/TAVARDT-AI%20Evaluator-5b3cf5.svg)](https://tools.tavardt.com/pt/avaliador-robustez-llm/)
[![Licence MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

***

Middleware de código aberto e blueprint de segurança para mitigação de Injeção de Prompt (OWASP LLM01), extração de prompts de sistema e jailbreaks adversariais, desenvolvido pela **TAVARDT**, em estrito alinhamento com as diretrizes do CNCS, o Regulamento Europeu de IA e o RGPD.

Modelos de Linguagem de Grande Porte processam instruções de governança e dados fornecidos pelo usuário no mesmo espaço contextual. Sem higienização de entrada na camada de aplicação e validação de saída baseada em tokens canário, agentes autônomos e esteiras de RAG permanecem expostos a sequestro de contexto e vazamento de credenciais.

***

## Capacidades Centrais e Arquitetura Defensiva

* **Higienização Determinística de Entrada**: Inspeciona prompts de entrada contra expressões regulares compiladas para bloquear anulação de instruções e manipulação de delimitadores antes do envio ao modelo.
* **Verificação Criptográfica por Token Canário**: Injeta identificadores imprevisíveis (`TAVARDT_CANARY_<hex16>`) via biblioteca `secrets` para detectar e interromper sessões que tentem extrair o prompt de sistema.
* **Cobertura Abrangente de Ameaças**: Neutraliza riscos descritos em OWASP-LLM01 (Injeção de Prompt), OWASP-LLM06 (Divulgação de Informações Sensíveis) e OWASP-LLM07 (Design Inseguro de Plugins).
* **Zero Dependências Externas**: Opera estritamente com a biblioteca padrão do Python, garantindo latência de execução inferior a 2 milissegundos sem chamadas de rede externas.
* **Arquitetura de Integração Transparente**: Envolve qualquer provedor de modelo (OpenAI, Anthropic, Google Gemini, modelos locais Llama) em agentes autônomos, chatbots e sistemas RAG.

***

## Vetores de Risco e Impacto Operacional

Disponibilizar modelos fundacionais em produção sem barreiras de proteção expõe dados confidenciais e ferramentas corporativas a manipulações hostis. O template neutraliza quatro vetores críticos de risco:

* **Injeção Direta de Prompt (OWASP LLM01)**: Usuários mal-intencionados enviando comandos de anulação (`ignore previous instructions`) para forçar o modelo a executar ações não autorizadas.
* **Exfiltração de Prompt de Sistema (OWASP LLM06)**: Perguntas elaboradas para coagir o modelo a exibir suas instruções originais e regras internas de governança.
* **Escape de Delimitadores e Jailbreak por Personas**: Manipulação de marcadores markdown ou XML concebida para alterar o comportamento do modelo para modos sem restrições.
* **Injeção Indireta via Bases de Conhecimento**: Cargas adversariais camufladas em documentos de terceiros que sequestram o fluxo de raciocínio em esteiras de RAG.

***

## Instalação e Execução CLI

Integre o middleware de guardrails em sua aplicação Python ou execute localmente o script de demonstração incluído:

```bash
# Clonar o repositório oficial
git clone https://github.com/TAVARDT-Portugal/owasp-llm-guardrails-template.git

# Acessar o diretório do projeto
cd owasp-llm-guardrails-template

# Executar o script de validação rápida
python examples/quickstart.py
```

### Python Integration Example

Envolva chamadas de inferência com verificações determinísticas de entrada e saída:

```python
from guardrails.validator import LLMGuardrail

guard = LLMGuardrail()

# 1. Ingress Validation (Input Guardrail)
is_safe, error = guard.validate_input(user_input)
if not is_safe:
    raise ValueError(f"Input blocked due to suspicious adversarial pattern: {error}")

# 2. Inject Canary Token into System Prompt
hardened_system_prompt = guard.get_canary_injection_prompt(system_prompt)

# 3. Egress Validation (Output Guardrail)
is_valid, leak_error = guard.validate_output(completion)
if not is_valid:
    raise SecurityError("Critical alert: System prompt leak detected via canary token!")
```

***

## Perguntas Técnicas Frequentes

### Quais vetores de risco o template de guardrails OWASP LLM neutraliza nas esteiras de modelos fundacionais?

O template aplica controles determinísticos na camada de aplicação contra injeções diretas de prompt, tentativas de jailbreak e exfiltração de diretrizes de sistema. Ao integrar a filtragem de entrada por expressões regulares com a verificação de saída por tokens canário, o middleware protege as cargas de trabalho sem exigir ajustes finos de modelo.

### Como a validação dinâmica por token canário impede o vazamento de instruções de sistema?

A classe LLMGuardrail gera um token criptográfico imprevisível e o incorpora às instruções de sistema acompanhado de ordens rigorosas de confidencialidade. Durante a validação de saída, o componente inspeciona as respostas do modelo em busca do token secreto, interceptando e descartando imediatamente respostas comprometidas.

### Por que a sanitização determinística na entrada é necessária se o modelo já possui alinhamento de segurança?

Alinhamentos de segurança do modelo podem ser burlados por formulações adversariais complexas, codificações especiais ou instruções em cenários fictícios de encenação. O filtro de expressões regulares bloqueia padrões hostis conhecidos antes que os tokens atinjam o contexto, eliminando riscos de inferência e poupando custos computacionais.

### Como este template de guardrails se integra a fluxos de agentes autônomos e arquiteturas de RAG?

O guardrail protege os ciclos de inferência ao higienizar fragmentos de documentos recuperados de terceiros e mensagens de usuários antes da montagem final do prompt. Essa validação impede que injeções indiretas ocultas em bancos vetoriais manipulem a tomada de decisão do modelo ou disparem ferramentas corporativas indevidas.

### Qual é a sobrecarga computacional e o impacto de latência introduzidos pela camada de guardrails?

O validador em Python opera exclusivamente em memória utilizando expressões regulares pré-compiladas e comparações nativas de strings, concluindo a checagem em menos de 2 milissegundos. Por não depender de APIs externas nem de modelos avaliadores secundários, a solução adiciona zero latência de rede aos ambientes de produção.

***

## Links Úteis

* **Portal Corporativo:** https://ag.tavardt.com/pt/
* **Suíte de Diagnósticos:** https://tools.tavardt.com/pt/
* **Contacto Técnico:** https://ag.tavardt.com/pt/contacto/

***

### Licença
Distribuído sob a Licença MIT. Consulte [LICENSE](LICENSE) para obter detalhes completos.

***

© 2026 TAVARDT. Todos os direitos reservados.
