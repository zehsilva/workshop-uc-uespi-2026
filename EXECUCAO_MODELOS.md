# Instalação e testes dos modelos locais — UC × UESPI

## Caminho mais estável: Colab com Tucano2

1. Criar notebook em [colab.research.google.com](https://colab.research.google.com/) → File → Upload notebook → abrir `UESPI_Workshop_Bayes_LangChain_Modelos_Locais_2026.ipynb`.
2. Em Colab, Runtime → Change runtime type → GPU (se disponível). CPU pode executar Tucano2, mas será mais lento.
3. Executar numa célula de código:

```python
%pip install -qU langchain-core langchain-ollama langchain-huggingface "transformers>=4.55,<5" accelerate huggingface-hub
```

4. Na célula de seleção de backends, escolher `RUN_TUCANO = True` e manter as outras duas flags `False`.
5. Executar as células por ordem. O modelo é descarregado da Hugging Face no primeiro uso. **Pré-carregar antes da aula.**

Não há token de API comercial. Para pesos disponíveis publicamente, em geral não é necessário token Hugging Face.

## Computador local: Ollama com Clinical-BR

Instalar [Ollama](https://ollama.com/download), iniciar o serviço local e abrir um terminal:

```bash
ollama pull cabelo/clinical-br-llama-2-7b
ollama list
ollama run cabelo/clinical-br-llama-2-7b
```

O download é de cerca de **13 GB** (informação do catálogo Ollama), com necessidade adicional de RAM e memória durante a inferência. Recomenda-se testar o desempenho real antes do workshop. No Jupyter a correr **no mesmo computador**, instalar `langchain-ollama` e definir `RUN_OLLAMA_CLINICAL=True`. Se o serviço utilizar outro endereço, definir `OLLAMA_BASE_URL=http://127.0.0.1:11434` no ambiente.

**Importante para Colab:** `localhost` no Colab aponta para a máquina do Colab; não comunica diretamente com o Ollama do computador do apresentador. Para Ollama dentro do Colab seria necessário instalar e iniciar o serviço nessa máquina e descarregar o modelo, o que não é indicado para uma demonstração curta com 13 GB.

O Clinical-BR é destinado a **texto clínico** e está fora do domínio de bombas e sensores. O notebook inclui um caso clínico fictício **apenas para extração textual**. Não usar para diagnóstico médico.

## AMALIA opcional: 9B

Modelo oficial: <https://huggingface.co/amalia-llm/AMALIA-9B-0626-DPO>

O projeto AMALIA recomenda **A100 40 GB** no caso da execução completa via vLLM. O notebook usa uma estratégia **experimental** `bitsandbytes` 4-bit com `RUN_AMALIA=True`, não garantida em todas as GPUs; acrescentar à instalação:

```python
%pip install -qU bitsandbytes
```

Requer GPU CUDA e capacidade suficiente para carregar e executar o modelo. **Não ativar AMALIA simultaneamente** com modelos grandes no mesmo ambiente. Não usar a configuração 4-bit como afirmação de qualidade equivalente à inferência BF16.

Documentação: <https://amalia-llm.github.io/intro.html>

## Que testes são realizados?

O notebook mostra os dois exercícios ligados:

1. Beta-Binomial e risco posterior sob dados sintéticos;
2. LangChain LCEL para Hugging Face ou Ollama;
3. extrair JSON de uma narrativa e verificar campos por Pydantic;
4. verificar o suporte textual de cada valor e rejeitar contradições;
5. executar a função de risco (`estimate_pump_failure` em LangChain) apenas depois da validação;
6. mini-bateria com relato completo, incompleto, instrução maliciosa, negação, contradição e unidade diferente;
7. variante opcional `with_structured_output` do Ollama;
8. opcional: comparar uma nota clínica fictícia com modelo especializado.

**Métricas didáticas:** validade de JSON, confiança na evidência textual, decisão/abstenção, tempo de execução e robustez pt-BR/pt-PT. O modelo não calcula o risco numericamente: quem o faz é a parte probabilística, sob premissas explícitas. As frases de avaliação não constituem um benchmark estatístico nem comprovam superioridade de qualquer checkpoint.

## Limitações de verificação

Na elaboração foi possível executar integralmente o notebook **sem LLMs**, incluindo os testes do contrato e das decisões. Não havia binário Ollama nem GPU disponível, e as dependências LangChain/Transformers não puderam ser instaladas pela ausência de rede no ambiente de execução. Assim, os caminhos de inferência real **precisam ser testados no computador/Colab de destino**.
