# Diagnóstico: NASA DONKI/CME retornando HTTP 503

> Documento para orientar o GitHub Copilot na investigação e correção do workflow.
> Data de referência: 03/11/2026.

## 1. Contexto do problema

- Endpoint atualmente utilizado: `https://api.nasa.gov/DONKI/CME`
- O workflow funcionou até **29/09/2026**, retornando **119 registros**.
- A partir de **30/09/2026**, começou a retornar `HTTP 503 Service Unavailable`.
- A falha persistiu até **03/11/2026**.
- O endpoint NASA APOD respondeu `HTTP 200` no mesmo período.
- O erro também ocorre com `DEMO_KEY`; não presumir que a chave privada seja a causa.
- Timeout foi aumentado de 30 para 80 segundos sem resolver.
- O código já realiza três tentativas antes de falhar.

## 2. Descoberta relevante: migração oficial em 30/09/2026

A NASA Community Coordinated Modeling Center (CCMC) anunciou mudanças de infraestrutura e migração de DONKI para novas URLs em **30/09/2026**. A data coincide com o início da falha observada.

Fontes oficiais:

- Anúncio de atualizações/migração: https://ccmc.gsfc.nasa.gov/news/major-updates/
- DONKI CCMC: https://ccmc.gsfc.nasa.gov/tools/DONKI/
- Referência da API: https://ccmc.gsfc.nasa.gov/DONKI/api/
- Guia DONKI: https://ccmc.gsfc.nasa.gov/DONKI/guide/

Segundo a documentação/aviso de migração, a nova base da API pública é:

`https://ccmc.gsfc.nasa.gov/DONKI-API/get/`

Endpoint CME correspondente:

`https://ccmc.gsfc.nasa.gov/DONKI-API/get/CME`

A migração informa que parâmetros de entrada e formatos de resposta permanecem essencialmente inalterados. Verificar a documentação atual antes de implementar autenticação ou parâmetros.

## 3. Evidências e grau de confiança

### Confirmado/documentado

- CCMC publicou migração de DONKI para novas URLs em 30/09/2026.
- O endpoint CCMC novo é documentado oficialmente.
- O início do problema do workflow coincide com a data da migração (informação fornecida pelo usuário).

### Evidência independente / corroborativa

- Relato técnico GitHub menciona `HTTP 503 upstream connect error` no gateway `api.nasa.gov` para DONKI, enquanto endpoints CCMC responderam HTTP 200 durante aquela investigação:
  https://github.com/iamthegreatdestroyer/aether/issues/5601
- Monitoramento independente:
  https://flarient.com/status
- Outro consumidor de DONKI:
  https://asteriastar.com/space-weather/events

Esses relatos são fontes comunitárias/independentes, não comunicados oficiais NASA. Não generalizar para todos os usuários, regiões ou recursos.

### Não confirmado

- Não há confirmação de que a NASA tenha anunciado descontinuação de DONKI.
- Não há previsão oficial encontrada para normalização do gateway antigo `api.nasa.gov/DONKI/CME`.
- A causa interna exata do 503 persistente não foi confirmada (gateway, upstream ou outro componente).

## 4. Hipótese principal

**Hipótese de alta confiança:** o workflow está usando uma URL antiga/gateway que foi afetada pela migração DONKI de 30/09/2026. O caminho mais promissor é migrar/testar a API CCMC oficial.

Não tratar como problema de API key ou timeout sem evidências.

## 5. Tarefas para o Copilot

1. Localizar no repositório todas as referências a:
   - `api.nasa.gov/DONKI`
   - `/DONKI/CME`
   - configuração da NASA API key
   - timeout e política de retries.
2. Identificar linguagem, framework, workflow e testes existentes.
3. Não alterar comportamento antes de apresentar diagnóstico e plano conciso.
4. Criar um teste/diagnóstico que compare:
   - endpoint antigo `https://api.nasa.gov/DONKI/CME`
   - endpoint novo `https://ccmc.gsfc.nasa.gov/DONKI-API/get/CME`
5. Para a mesma janela de datas, registrar:
   - status HTTP;
   - duração;
   - `Content-Type`;
   - headers relevantes (`Date`, `Retry-After`, `Server`, rate-limit headers se presentes);
   - corpo de erro limitado e sanitizado;
   - quantidade de registros JSON, quando válido.
6. Validar se a nova API requer chave ou aceita consulta sem `api_key`, consultando a documentação oficial. Não presumir.
7. Comparar schema/campos da resposta nova com o formato esperado pelo código atual.
8. Se compatível e o teste confirmar sucesso, propor migração configurável para a URL CCMC.
9. Preservar logs úteis, tratamento de falhas e testes automatizados.
10. Não remover retries sem justificativa; para 503/502/504, usar backoff exponencial com jitter e limite razoável.
11. Não imprimir nem expor API keys nos logs.
12. Não implementar fallback silencioso para dados vazios como se fosse sucesso.

## 6. Testes manuais sugeridos

### Testar endpoint novo

```bash
curl -i --max-time 80 \
  'https://ccmc.gsfc.nasa.gov/DONKI-API/get/CME?startDate=2026-09-29&endDate=2026-09-29'
```

### Testar endpoint antigo

```bash
curl -i --max-time 80 \
  'https://api.nasa.gov/DONKI/CME?startDate=2026-09-29&endDate=2026-09-29&api_key=DEMO_KEY'
```

Comparar status, conteúdo e número de registros. Os comandos são testes propostos; não representam resultados já executados.

## 7. Exemplo de teste Python (diagnóstico, não produção)

```python
import time
import requests

tests = [
    (
        "gateway_nasa",
        "https://api.nasa.gov/DONKI/CME",
        {
            "startDate": "2026-09-29",
            "endDate": "2026-09-29",
            "api_key": "DEMO_KEY",
        },
    ),
    (
        "ccmc_donki",
        "https://ccmc.gsfc.nasa.gov/DONKI-API/get/CME",
        {
            "startDate": "2026-09-29",
            "endDate": "2026-09-29",
        },
    ),
]

for name, url, params in tests:
    started = time.monotonic()
    try:
        response = requests.get(url, params=params, timeout=80)
        elapsed = time.monotonic() - started

        print(f"\n[{name}] HTTP {response.status_code} ({elapsed:.2f}s)")
        print("URL:", response.url)
        print("Content-Type:", response.headers.get("Content-Type"))
        print("Date:", response.headers.get("Date"))
        print("Retry-After:", response.headers.get("Retry-After"))

        if response.ok:
            payload = response.json()
            if isinstance(payload, list):
                print("Registros:", len(payload))
            else:
                print("JSON raiz:", type(payload).__name__)
        else:
            print("Erro (limitado):", response.text[:1000])
    except requests.RequestException as exc:
        print(f"\n[{name}] Exceção: {exc}")
```

**Segurança:** usar `DEMO_KEY` somente para diagnóstico; não incluir chave privada no código, commit ou logs.

## 8. Critérios de aceite para uma correção

- [ ] Endpoint CCMC validado com resposta HTTP bem-sucedida.
- [ ] Requisição equivalente retorna dados CME válidos.
- [ ] Campos necessários ao workflow permanecem compatíveis.
- [ ] Configuração da URL fica centralizada (variável/config).
- [ ] Erros HTTP são tratados explicitamente.
- [ ] Retry com backoff não gera tempestade de requisições.
- [ ] Logs não contêm segredo/API key.
- [ ] Testes automatizados cobrem sucesso, 503, timeout e JSON inválido.
- [ ] Falha de API não é confundida com lista vazia de eventos.
- [ ] README/configuração documenta endpoint e autenticação.

## 9. Orientação final

Prioridade: **diagnosticar e validar a URL CCMC oficial antes de mexer em API keys, timeouts ou lógica de negócio**.

O Copilot deve inspecionar o repositório e propor alterações pequenas, com testes e explicação do impacto. Não afirmar que a migração resolveu até que uma chamada real e os testes do projeto confirmem.
