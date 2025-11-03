# API - Call Integration

Endpoint para análise automática de áudio via integração com API4Com.

## Endpoint

```
POST https://api.firstlineai.com.br/analyze-audio/call-integration/{user_email}
```

## Autenticação
- Necessário autenticar via **Basic Auth** (usuário e senha) no header da requisição.

## Parâmetros

### Path Parameters
| Nome       | Tipo   | Obrigatório | Descrição           |
|------------|--------|-------------|---------------------|
| user_email | string | Sim         | E-mail do usuário   |

### Query Parameters
| Nome             | Tipo     | Obrigatório | Descrição                                 |
|------------------|----------|-------------|-------------------------------------------|
| analysis_name    | string   | Não         | Nome personalizado para a análise         |
| speakers_expected| inteiro  | Não         | Número de participantes esperados na call |

### Body (JSON)
O body deve conter os dados da chamada da API4Com no formato JSON:

```json
{
  "version": "v1.4",
  "eventType": "channel-hangup",
  "id": "2ee13fa4-975c-499d-bbb8-5177ff418316",
  "domain": "seudominio.api4com.com",
  "direction": "outbound",
  "caller": "1000",
  "called": "04833328530",
  "startedAt": "2025-01-01 00:00:00",
  "answeredAt": "2025-01-01 00:00:05",
  "endedAt": "2025-01-01 00:00:10",
  "duration": 5,
  "hangupCause": "NORMAL_CLEARING",
  "hangupCauseCode": "16",
  "recordUrl": "https://listener.api4com.com/files/listen/2ee13fa4-975c-499d-bbb8-5177ff418316.mp3",
  "metadata": {
    "gateway": "integration-test",
    "userId": 123,
    "entityId": 456
  }
}
```

**Campo obrigatório:**
- `recordUrl`: URL do arquivo de áudio para download

## Exemplo de requisição (curl)

```bash
curl -X POST "https://api.firstlineai.com.br/analyze-audio/call-integration/lucas@gmail.com?analysis_name=analisexpto&speakers_expected=2" \
  -u usuario:senha \
  -H "Content-Type: application/json" \
  -d '{
    "version": "v1.4",
    "eventType": "channel-hangup",
    "id": "2ee13fa4-975c-499d-bbb8-5177ff418316",
    "domain": "seudominio.api4com.com",
    "direction": "outbound",
    "caller": "1000",
    "called": "04833328530",
    "startedAt": "2025-01-01 00:00:00",
    "answeredAt": "2025-01-01 00:00:05",
    "endedAt": "2025-01-01 00:00:10",
    "duration": 5,
    "hangupCause": "NORMAL_CLEARING",
    "hangupCauseCode": "16",
    "recordUrl": "https://listener.api4com.com/files/listen/2ee13fa4-975c-499d-bbb8-5177ff418316.mp3",
    "metadata": {
      "gateway": "integration-test",
      "userId": 123,
      "entityId": 456
    }
  }'
```

## Resposta de Sucesso
```json
{
  "success": true,
  "message": "Análise concluída com sucesso."
}
```

## Resposta para Admins
Se o usuário autenticado for um admin, o endpoint retorna todos os dados completos da análise, no seguinte formato:

```json
{
  "success": true,
  "analysis": {
    "id": "uuid-da-analise",
    "client_name": "Nome do cliente",
    "seller_name": "Nome do vendedor identificado",
    "analysis_name": "Nome da análise",
    "transcription": "Transcrição do áudio com nomes substituídos",
    "score_geral": 8,
    "pontos_positivos": ["Exemplo de ponto positivo"],
    "pontos_atencao": ["Exemplo de ponto de atenção"],
    "objecoes_identificadas": [],
    "sugestoes_melhoria": [],
    "proximos_passos": [],
    "resumo": "Resumo da conversa",
    "context_uuid": "id-do-contexto",
    "upload_type": "audio",
    "vendedor": "id do vendedor",
    "user_id": "id do usuário",
    "framework_analysis": "...",
    "coaching_insights": [],
    "performance_analysis": "...",
    "mental_triggers": "...",
    "reformulacoes_pnl": "...",
    "plano_fechamento": [],
    "ia_preditiva": "...",
    "created_at": "2024-06-07 15:00:00",
    "audio_duration_seconds": 123.45
  },
  "transcription": "Transcrição bruta do áudio",
  "contextUsed": "Nome do contexto utilizado",
  "processing_time": 12.34
}
```

**Observações:**
- O conteúdo e os campos podem variar conforme o processamento do áudio e o contexto.
- Todos os campos relevantes da análise são retornados, igual ao endpoint autenticado por JWT.
- Para usuários comuns, o retorno permanece apenas a mensagem genérica de sucesso.
- O sistema automaticamente baixa o arquivo de áudio da URL fornecida no `recordUrl`.

## Respostas de Erro Comuns
- 400: Parâmetros obrigatórios ausentes, JSON inválido ou falha no download do áudio
- 404: Usuário ou contexto não encontrado
- 500: Erro interno ao processar a integração

---
Dúvidas? Entre em contato com o suporte FirstLine AI. 