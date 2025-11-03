# FirstLine Backend - API Documentation

## Visão Geral

FirstLine Backend é uma API REST robusta para análise de vendas com IA. A aplicação permite análise de áudios e textos de chamadas de vendas, gerenciamento de contextos, contatos e relatórios completos.

**DNS de Produção:** `api.firstlineai.com.br`

## Autenticação

Todos os endpoints requerem autenticação via token Bearer:

```
Authorization: Bearer {AUTH_TOKEN}
```

Para desenvolvimento, também é necessário o header:
```
X-User-ID: {USER_ID}
```

## Tecnologias Utilizadas

- **Flask** - Framework web Python
- **SQLAlchemy** - ORM para banco de dados
- **PostgreSQL** - Banco de dados principal
- **JWT** - Autenticação via tokens
- **Flask-CORS** - Configuração de CORS
- **Rate Limiting** - Controle de taxa de requisições
- **Logging** - Sistema de logs estruturado

## Endpoints da API

### 🔐 Autenticação

#### POST /auth/login
Autentica um usuário no sistema.

**Request:**
```json
{
  "email": "usuario@empresa.com",
  "password": "senha123"
}
```

**Response 200:**
```json
{
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "usuario@empresa.com",
    "created_at": "2025-07-14T10:30:00Z"
  },
  "message": "Login realizado com sucesso"
}
```

**Response 401:**
```json
{
  "error": "Credenciais inválidas"
}
```

#### POST /auth/register
Registra um novo usuário e cria seu perfil.

**Request:**
```json
{
  "email": "novo@empresa.com",
  "password": "senha123",
  "confirmPassword": "senha123",
  "companyName": "Empresa LTDA",
  "responsibleName": "João Silva",
  "phone": "+55 11 99999-9999",
  "cnpj": "12.345.678/0001-90"
}
```

**Response 201:**
```json
{
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "novo@empresa.com",
    "created_at": "2025-07-14T10:30:00Z"
  },
  "profile": {
    "id": "650e8400-e29b-41d4-a716-446655440000",
    "company_name": "Empresa LTDA",
    "responsible_name": "João Silva",
    "phone": "+55 11 99999-9999",
    "cnpj": "12.345.678/0001-90",
    "contact_email": "novo@empresa.com",
    "current_plan": "free"
  },
  "message": "Usuário registrado com sucesso"
}
```

**Response 400:**
```json
{
  "error": "E-mail já cadastrado"
}
```

#### GET /auth/me/{user_id}
Obtém informações do usuário autenticado.

**Response 200:**
```json
{
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "usuario@empresa.com",
    "created_at": "2025-07-14T10:30:00Z",
    "updated_at": "2025-07-14T10:30:00Z"
  },
  "profile": {
    "id": "650e8400-e29b-41d4-a716-446655440000",
    "company_name": "Empresa LTDA",
    "responsible_name": "João Silva",
    "phone": "+55 11 99999-9999",
    "cnpj": "12.345.678/0001-90",
    "contact_email": "usuario@empresa.com",
    "current_plan": "free",
    "plan_renewal_date": null,
    "created_at": "2025-07-14T10:30:00Z",
    "updated_at": "2025-07-14T10:30:00Z"
  }
}
```

### 👤 Perfil do Usuário

#### GET /profile/{user_id}
Obtém o perfil de um usuário específico.

**Response 200:**
```json
{
  "id": "650e8400-e29b-41d4-a716-446655440000",
  "user_id": "550e8400-e29b-41d4-a716-446655440000",
  "company_name": "Nome da Empresa Ltda",
  "responsible_name": "João Silva",
  "phone": "(11) 99999-9999",
  "cnpj": "12.345.678/0001-90",
  "contact_email": "contato@empresa.com",
  "current_plan": "free",
  "plan_renewal_date": "2025-08-15T10:30:00Z",
  "created_at": "2025-01-15T10:30:00Z",
  "updated_at": "2025-07-14T14:20:00Z"
}
```

**Response 404:**
```json
{
  "error": "Profile not found"
}
```

#### PUT /profile/{user_id}
Atualiza o perfil do usuário.

**Request:**
```json
{
  "company_name": "Nova Empresa Ltda",
  "responsible_name": "Maria Santos",
  "phone": "(11) 88888-8888",
  "cnpj": "98.765.432/0001-10",
  "contact_email": "novo@empresa.com",
  "current_plan": "premium"
}
```

**Response 200:**
```json
{
  "id": "650e8400-e29b-41d4-a716-446655440000",
  "user_id": "550e8400-e29b-41d4-a716-446655440000",
  "company_name": "Nova Empresa Ltda",
  "responsible_name": "Maria Santos",
  "phone": "(11) 88888-8888",
  "cnpj": "98.765.432/0001-10",
  "contact_email": "novo@empresa.com",
  "current_plan": "premium",
  "plan_renewal_date": "2025-08-15T10:30:00Z",
  "created_at": "2025-01-15T10:30:00Z",
  "updated_at": "2025-07-14T14:25:00Z"
}
```

**Response 400:**
```json
{
  "error": "Validation failed",
  "details": [
    {
      "field": "cnpj",
      "message": "CNPJ format is invalid"
    }
  ]
}
```

### 🎯 Contextos de Vendas

#### GET /contexts
Obtém todos os contextos do usuário.

**Query Parameters:**
- Nenhum parâmetro adicional

**Response 200:**
```json
{
  "data": [
    {
      "id": "ctx-550e8400-e29b-41d4-a716-446655440000",
      "user_id": "550e8400-e29b-41d4-a716-446655440000",
      "name": "Vendas de Software B2B",
      "description": "Contexto para vendas de software empresarial",
      "prompt": "Você é um especialista em análise de vendas...",
      "is_active": true,
      "vendedores": ["João Silva", "Maria Santos"],
      "vendedor_ativo": "João Silva",
      "created_at": "2025-01-15T10:00:00Z",
      "updated_at": "2025-01-15T10:00:00Z"
    }
  ],
  "total": 1
}
```

#### POST /contexts
Cria um novo contexto.

**Request (Simples):**
```json
{
  "name": "Vendas Imobiliárias",
  "description": "Contexto para análise de vendas de imóveis",
  "prompt": "Analise as chamadas considerando vendas imobiliárias...",
  "vendedores": ["Carlos Pereira"],
  "vendedor_ativo": "Carlos Pereira"
}
```

**Request (Completo com Geração Automática):**
```json
{
  "name": "Vendas SaaS Enterprise",
  "description": "Contexto completo para vendas SaaS",
  "productInfo": "Plataforma de CRM empresarial com IA",
  "targetAudience": "Empresas de médio e grande porte",
  "commonObjections": [
    {
      "objection": "Preço muito alto",
      "response": "Nosso ROI se paga em 3 meses"
    }
  ],
  "pricingStructure": "Plano Básico: R$ 199/mês, Plano Pro: R$ 399/mês",
  "playbook": "Descoberta -> Demonstração -> Proposta -> Fechamento",
  "mentalTriggers": "Escassez, prova social, autoridade",
  "competitors": ["Salesforce", "HubSpot"],
  "vendedores": ["Ana Costa", "Roberto Lima"],
  "vendedorAtivo": "Ana Costa"
}
```

**Response 201:**
```json
{
  "data": {
    "id": "ctx-550e8400-e29b-41d4-a716-446655440000",
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "name": "Vendas SaaS Enterprise",
    "description": "Contexto completo para vendas SaaS",
    "prompt": "Você é um especialista em análise de vendas. Analise as chamadas considerando...",
    "is_active": false,
    "vendedores": ["Ana Costa", "Roberto Lima"],
    "vendedor_ativo": "Ana Costa",
    "created_at": "2025-01-15T10:00:00Z",
    "updated_at": "2025-01-15T10:00:00Z"
  },
  "message": "Contexto criado com sucesso"
}
```

#### PUT /contexts/{context_id}
Atualiza um contexto existente.

**Request:**
```json
{
  "name": "Vendas SaaS Enterprise - Atualizado",
  "description": "Contexto atualizado para vendas SaaS",
  "prompt": "Prompt atualizado...",
  "vendedores": ["Ana Costa", "Roberto Lima", "Pedro Oliveira"],
  "vendedor_ativo": "Pedro Oliveira"
}
```

**Response 200:**
```json
{
  "data": {
    "id": "ctx-550e8400-e29b-41d4-a716-446655440000",
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "name": "Vendas SaaS Enterprise - Atualizado",
    "description": "Contexto atualizado para vendas SaaS",
    "prompt": "Prompt atualizado...",
    "is_active": true,
    "vendedores": ["Ana Costa", "Roberto Lima", "Pedro Oliveira"],
    "vendedor_ativo": "Pedro Oliveira",
    "created_at": "2025-01-15T10:00:00Z",
    "updated_at": "2025-01-15T12:00:00Z"
  },
  "message": "Contexto atualizado com sucesso"
}
```

#### DELETE /contexts/{context_id}
Remove um contexto.

**Response 200:**
```json
{
  "message": "Contexto removido com sucesso"
}
```

#### PUT /contexts/{context_id}/activate
Ativa um contexto (desativa todos os outros).

**Response 200:**
```json
{
  "data": {
    "id": "ctx-550e8400-e29b-41d4-a716-446655440000",
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "name": "Vendas SaaS Enterprise",
    "description": "Contexto para vendas SaaS",
    "prompt": "Prompt do contexto...",
    "is_active": true,
    "vendedores": ["Ana Costa", "Roberto Lima"],
    "vendedor_ativo": "Ana Costa",
    "created_at": "2025-01-15T10:00:00Z",
    "updated_at": "2025-01-15T12:00:00Z"
  },
  "message": "Contexto ativado com sucesso"
}
```

### 📞 Contatos

#### GET /contacts
Lista contatos do usuário com filtros e paginação.

**Query Parameters:**
- `page` (number): Página atual (default: 1)
- `limit` (number): Itens por página (default: 20, max: 100)
- `contact_type` (string): Filtrar por tipo ('lead', 'cliente', 'prospect')
- `search` (string): Buscar por nome ou email

**Response 200:**
```json
{
  "data": [
    {
      "id": "contact-550e8400-e29b-41d4-a716-446655440000",
      "name": "João Silva",
      "email": "joao@exemplo.com",
      "whatsapp": "(11) 99999-9999",
      "contact_type": "lead",
      "created_at": "2025-07-15T10:30:00Z"
    },
    {
      "id": "contact-650e8400-e29b-41d4-a716-446655440000",
      "name": "Maria Santos",
      "email": "maria@exemplo.com",
      "whatsapp": "(11) 88888-8888",
      "contact_type": "cliente",
      "created_at": "2025-07-15T09:15:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 45,
    "totalPages": 3
  }
}
```

#### POST /contacts
Cria um novo contato.

**Request:**
```json
{
  "name": "João Silva",
  "email": "joao@exemplo.com",
  "whatsapp": "(11) 99999-9999",
  "contact_type": "lead"
}
```

**Response 201:**
```json
{
  "data": {
    "id": "contact-550e8400-e29b-41d4-a716-446655440000",
    "name": "João Silva",
    "email": "joao@exemplo.com",
    "whatsapp": "(11) 99999-9999",
    "contact_type": "lead",
    "created_at": "2025-07-15T10:30:00Z"
  },
  "message": "Contato criado com sucesso"
}
```

**Response 400:**
```json
{
  "error": "Dados inválidos",
  "details": {
    "name": ["Nome é obrigatório"],
    "contact_type": ["Tipo de contato é obrigatório"]
  }
}
```

#### PUT /contacts/{contact_id}
Atualiza um contato existente.

**Request:**
```json
{
  "name": "João Silva Santos",
  "email": "joao.santos@exemplo.com",
  "whatsapp": "(11) 99999-9999",
  "contact_type": "cliente"
}
```

**Response 200:**
```json
{
  "data": {
    "id": "contact-550e8400-e29b-41d4-a716-446655440000",
    "name": "João Silva Santos",
    "email": "joao.santos@exemplo.com",
    "whatsapp": "(11) 99999-9999",
    "contact_type": "cliente",
    "created_at": "2025-07-15T10:30:00Z"
  },
  "message": "Contato atualizado com sucesso"
}
```

#### DELETE /contacts/{contact_id}
Remove um contato.

**Response 200:**
```json
{
  "message": "Contato deletado com sucesso"
}
```

### 🎵 Análise de Áudio

#### POST /analyze-audio
Analisa um arquivo de áudio de chamada de vendas.

**Request:**
- Content-Type: multipart/form-data
- Campo: `audio` (arquivo de áudio)

**Response 200:**
```json
{
  "id": "analysis-550e8400-e29b-41d4-a716-446655440000",
  "transcription": "Texto da transcrição...",
  "analysis": {
    "score_geral": 8.5,
    "resumo": "Conversa bem estruturada...",
    "pontos_positivos": ["Bom rapport", "Descobriu necessidades"],
    "pontos_atencao": ["Poderia criar mais urgência"],
    "objecoes_identificadas": ["Preço alto"],
    "sugestoes_melhoria": ["Usar prova social"],
    "proximos_passos": ["Enviar proposta"]
  },
  "created_at": "2025-07-15T10:30:00Z"
}
```

### 📝 Análise de Texto

#### POST /analyze-text
Analisa um texto de chamada de vendas.

**Request:**
```json
{
  "text": "Conversa completa da chamada de vendas...",
  "user_id": "550e8400-e29b-41d4-a716-446655440000",
  "contextId": "ctx-650e8400-e29b-41d4-a716-446655440000",
  "vendedor": "João Silva"
}
```

**Request (contextId opcional):**
```json
{
  "text": "Conversa completa da chamada de vendas...",
  "user_id": "550e8400-e29b-41d4-a716-446655440000",
  "vendedor": "João Silva"
}
```

**Campos:**
- `text` (string, obrigatório): Texto da conversa para análise
- `contextId` (UUID, opcional): ID do contexto para análise. Se não fornecido, usa o contexto ativo padrão do usuário
- `vendedor` (string, opcional): Nome do vendedor responsável pela chamada

**Response 200:**
```json
{
  "id": "analysis-550e8400-e29b-41d4-a716-446655440000",
  "analysis": {
    "score_geral": 7.5,
    "resumo": "Análise da conversa...",
    "pontos_positivos": ["Boa abertura", "Perguntas qualificadoras"],
    "pontos_atencao": ["Não tratou objeções"],
    "objecoes_identificadas": ["Prazo de decisão"],
    "sugestoes_melhoria": ["Criar urgência"],
    "proximos_passos": ["Follow-up em 2 dias"]
  },
  "created_at": "2025-07-15T10:30:00Z"
}
```

**Response 400:**
```json
{
  "error": "Text content is required"
}
```

**Response 400 (sem contexto):**
```json
{
  "error": "Context ID is required or no default context available"
}
```

**cURL Example:**
```bash
curl -X POST "http://localhost:5000/analyze-text" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "X-User-ID: 550e8400-e29b-41d4-a716-446655440000" \
  -d '{
    "text": "Olá, bom dia! Estou ligando para apresentar nossa solução...",
    "contextId": "ctx-650e8400-e29b-41d4-a716-446655440000",
    "vendedor": "João Silva"
  }'
```

### 📊 Análises (Gerenciamento)

#### GET /analyses
Lista análises do usuário com filtros e paginação.

**Query Parameters:**
- `limit` (number): Limite de resultados (default: 50, max: 100)
- `offset` (number): Offset para paginação (default: 0)
- `vendedor` (string): Filtrar por vendedor específico
- `context_id` (UUID): Filtrar por contexto específico
- `upload_type` (string): Filtrar por tipo ('audio' ou 'text')
- `sort` (string): Ordenação ('created_at' default descendente)

**Response 200:**
```json
{
  "success": true,
  "data": [
    {
      "id": "analysis-550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2025-07-15T10:30:00Z",
      "client_name": "Cliente Exemplo",
      "score_geral": 8.5,
      "upload_type": "audio",
      "resumo": "Conversa de vendas bem estruturada...",
      "pontos_positivos": ["Bom rapport", "Descobriu necessidades"],
      "pontos_atencao": ["Poderia ter criado mais urgência"],
      "objecoes_identificadas": ["Preço alto", "Prazo de decisão"],
      "sugestoes_melhoria": ["Usar prova social", "Criar deadline"],
      "proximos_passos": ["Enviar proposta", "Ligar em 2 dias"],
      "context_uuid": "ctx-650e8400-e29b-41d4-a716-446655440000",
      "transcription": "Texto completo da conversa...",
      "vendedor": "João Silva",
      "audio_file_name": "call_123.mp3",
      "notes": "Observações adicionais",
      "framework_analysis": {
        "spin": {
          "situacao": {"presente": true, "trecho": "...", "avaliacao": "..."},
          "problema": {"presente": true, "trecho": "...", "avaliacao": "..."}
        }
      },
      "coaching_insights": ["Insight 1", "Insight 2"],
      "performance_analysis": {
        "rapport": {"score": 8, "observacao": "...", "frase_real": "..."},
        "apresentacao_oferta": {"score": 7, "observacao": "..."}
      },
      "mental_triggers": {
        "urgencia": {"detectado": true, "frase_real": "...", "recomendacao_firstline": "..."}
      },
      "reformulacoes_pnl": [
        {"categoria": "Rapport", "frase_real": "...", "recomendacao_firstline": "..."}
      ],
      "plano_fechamento": ["Estratégia 1", "Estratégia 2"],
      "ia_preditiva": {
        "probabilidade_fechamento": 75,
        "principais_riscos": ["Risco 1", "Risco 2"],
        "fatores_positivos": ["Fator 1", "Fator 2"]
      }
    }
  ],
  "pagination": {
    "total": 45,
    "limit": 50,
    "offset": 0,
    "has_more": false
  }
}
```

#### GET /analyses/{analysis_id}
Obtém uma análise específica.

**Response 200:**
```json
{
  "success": true,
  "data": {
    "id": "analysis-550e8400-e29b-41d4-a716-446655440000",
    "created_at": "2025-07-15T10:30:00Z",
    "client_name": "Cliente Exemplo",
    "score_geral": 8.5,
    "upload_type": "audio",
    "resumo": "Conversa de vendas bem estruturada...",
    "pontos_positivos": ["Bom rapport", "Descobriu necessidades"],
    "pontos_atencao": ["Poderia ter criado mais urgência"],
    "objecoes_identificadas": ["Preço alto", "Prazo de decisão"],
    "sugestoes_melhoria": ["Usar prova social", "Criar deadline"],
    "proximos_passos": ["Enviar proposta", "Ligar em 2 dias"],
    "context_uuid": "ctx-650e8400-e29b-41d4-a716-446655440000",
    "transcription": "Texto completo da conversa transcrita...",
    "vendedor": "João Silva",
    "audio_file_name": "call_123.mp3",
    "notes": "Observações adicionais sobre a call"
  }
}
```

**Response 404:**
```json
{
  "success": false,
  "error": "Analysis not found"
}
```

#### PUT /analyses/{analysis_id}
Atualiza uma análise existente.

**Request:**
```json
{
  "client_name": "Cliente Atualizado",
  "vendedor": "Maria Silva",
  "notes": "Observações atualizadas",
  "resumo": "Novo resumo da análise",
  "score_geral": 8.5,
  "pontos_positivos": ["Excelente abertura", "Boa descoberta de necessidades"],
  "pontos_atencao": ["Poderia ter tratado melhor as objeções"],
  "objecoes_identificadas": ["Preço alto", "Prazo de decisão longo"],
  "sugestoes_melhoria": ["Usar social proof", "Criar mais urgência"],
  "proximos_passos": ["Enviar proposta personalizada", "Agendar follow-up"],
  "framework_analysis": {
    "spin": {
      "situacao": {"presente": true, "trecho": "Estamos usando sistema X", "avaliacao": "Bem identificado"},
      "problema": {"presente": true, "trecho": "Temos problemas com Y", "avaliacao": "Claramente exposto"}
    }
  },
  "coaching_insights": ["Trabalhar técnicas de fechamento", "Melhorar tratamento de objeções"],
  "performance_analysis": {
    "rapport": {"score": 8, "observacao": "Bom rapport inicial", "frase_real": "Como vai o seu dia?", "sugestao_firstline": "Continuar com abordagem empática"},
    "apresentacao_oferta": {"score": 7, "observacao": "Apresentação clara", "frase_real": "Nossa solução oferece...", "sugestao_firstline": "Focar mais nos benefícios"}
  }
}
```

**Campos atualizáveis:**
- `client_name` (string): Nome do cliente
- `vendedor` (string): Nome do vendedor
- `notes` (string): Observações adicionais
- `resumo` (string): Resumo da análise
- `score_geral` (number): Pontuação geral (0-10)
- `pontos_positivos` (array): Lista de pontos positivos
- `pontos_atencao` (array): Lista de pontos de atenção
- `objecoes_identificadas` (array): Lista de objeções identificadas
- `sugestoes_melhoria` (array): Lista de sugestões de melhoria
- `proximos_passos` (array): Lista de próximos passos
- `framework_analysis` (object): Análise de frameworks de vendas
- `coaching_insights` (array): Insights de coaching
- `performance_analysis` (object): Análise de performance
- `mental_triggers` (array): Gatilhos mentais utilizados
- `reformulacoes_pnl` (array): Reformulações PNL
- `plano_fechamento` (array): Plano de fechamento
- `ia_preditiva` (object): Análise preditiva com IA

**Response 200:**
```json
{
  "success": true,
  "message": "Analysis updated successfully",
  "data": {
    "id": "analysis-550e8400-e29b-41d4-a716-446655440000",
    "created_at": "2025-07-15T10:30:00Z",
    "client_name": "Cliente Atualizado",
    "score_geral": 8.5,
    "upload_type": "text",
    "resumo": "Novo resumo da análise",
    "pontos_positivos": ["Excelente abertura", "Boa descoberta de necessidades"],
    "pontos_atencao": ["Poderia ter tratado melhor as objeções"],
    "objecoes_identificadas": ["Preço alto", "Prazo de decisão longo"],
    "sugestoes_melhoria": ["Usar social proof", "Criar mais urgência"],
    "proximos_passos": ["Enviar proposta personalizada", "Agendar follow-up"],
    "context_uuid": "ctx-650e8400-e29b-41d4-a716-446655440000",
    "vendedor": "Maria Silva",
    "notes": "Observações atualizadas",
    "framework_analysis": {
      "spin": {
        "situacao": {"presente": true, "trecho": "Estamos usando sistema X", "avaliacao": "Bem identificado"},
        "problema": {"presente": true, "trecho": "Temos problemas com Y", "avaliacao": "Claramente exposto"}
      }
    },
    "coaching_insights": ["Trabalhar técnicas de fechamento", "Melhorar tratamento de objeções"],
    "performance_analysis": {
      "rapport": {"score": 8, "observacao": "Bom rapport inicial", "frase_real": "Como vai o seu dia?", "sugestao_firstline": "Continuar com abordagem empática"},
      "apresentacao_oferta": {"score": 7, "observacao": "Apresentação clara", "frase_real": "Nossa solução oferece...", "sugestao_firstline": "Focar mais nos benefícios"}
    }
  }
}
```

**Response 400:**
```json
{
  "success": false,
  "error": "No valid fields provided for update"
}
```

**Response 400 (score inválido):**
```json
{
  "success": false,
  "error": "score_geral must be between 0 and 10"
}
```

**Response 403:**
```json
{
  "success": false,
  "error": "Access denied - not your analysis"
}
```

**Response 404:**
```json
{
  "success": false,
  "error": "Analysis not found"
}
```

**cURL Example:**
```bash
curl -X PUT "http://localhost:5000/analyses/analysis-550e8400-e29b-41d4-a716-446655440000" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "X-User-ID: 550e8400-e29b-41d4-a716-446655440000" \
  -d '{
    "client_name": "Cliente Atualizado",
    "vendedor": "Maria Silva",
    "score_geral": 8.5,
    "notes": "Observações atualizadas após reunião"
  }'
```

#### DELETE /analyses/{analysis_id}
Remove uma análise.

**Response 200:**
```json
{
  "success": true,
  "message": "Analysis deleted successfully",
  "deleted_id": "analysis-550e8400-e29b-41d4-a716-446655440000"
}
```

### 📈 Relatórios

#### GET /reports/stats
Obtém estatísticas gerais do usuário.

**Response 200:**
```json
{
  "totalAnalyses": 45,
  "averageScore": 7.8,
  "recentAnalyses": 12,
  "vendedoresAtivos": 8,
  "vendedores": ["João", "Maria", "Pedro"]
}
```

#### GET /reports/analyses
Obtém relatório de análises com filtros.

**Query Parameters:**
- `search` (string): Buscar por cliente ou resumo
- `vendedor` (string): Filtrar por vendedor
- `page` (number): Página atual (default: 1)
- `limit` (number): Itens por página (default: 10, max: 100)

**Response 200:**
```json
{
  "data": [
    {
      "id": "analysis-550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2025-07-15T10:30:00Z",
      "client_name": "Cliente XYZ",
      "score_geral": 8.5,
      "upload_type": "audio",
      "resumo": "Boa conversa com objeções tratadas",
      "pontos_positivos": ["Rapport bem estabelecido"],
      "pontos_atencao": ["Não perguntou sobre budget"],
      "objecoes_identificadas": ["Preço alto"],
      "sugestoes_melhoria": ["Usar técnica de ancoragem"],
      "proximos_passos": ["Enviar proposta"],
      "context_uuid": "ctx-650e8400-e29b-41d4-a716-446655440000",
      "transcription": "texto da transcrição...",
      "vendedor": "João",
      "framework_analysis": {},
      "coaching_insights": ["Insight 1"],
      "performance_analysis": {},
      "mental_triggers": {},
      "reformulacoes_pnl": {},
      "plano_fechamento": ["Estratégia 1"],
      "ia_preditiva": {}
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 45,
    "pages": 5
  }
}
```

## Respostas de Erro Padrão

### 400 - Bad Request
```json
{
  "error": "Dados inválidos",
  "details": {
    "field": ["Mensagem de erro específica"]
  }
}
```

### 401 - Unauthorized
```json
{
  "error": "Não autorizado",
  "message": "Token inválido ou expirado"
}
```

### 403 - Forbidden
```json
{
  "error": "Acesso negado",
  "message": "Você não tem permissão para acessar este recurso"
}
```

### 404 - Not Found
```json
{
  "error": "Recurso não encontrado",
  "message": "O recurso solicitado não existe"
}
```

### 500 - Internal Server Error
```json
{
  "error": "Erro interno do servidor",
  "message": "Ocorreu um erro inesperado"
}
```

## Validações

### Tipos de Contato
- `lead`: Lead qualificado
- `cliente`: Cliente ativo
- `prospect`: Prospect em prospecção

### Formatos de Dados
- **Email**: Formato padrão de email
- **Telefone**: Formato brasileiro `(XX) XXXXX-XXXX`
- **CNPJ**: Formato brasileiro `XX.XXX.XXX/XXXX-XX`
- **UUID**: Formato UUID v4

### Limites
- **Paginação**: Máximo 100 itens por página
- **Upload**: Arquivos de áudio até 200MB
- **Texto**: Máximo 10.000 caracteres

## Exemplos de Uso

### Curl - Login
```bash
curl -X POST "http://ec2-18-231-192-82.sa-east-1.compute.amazonaws.com/auth/login" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "usuario@empresa.com",
    "password": "senha123"
  }'
```

### Curl - Criar Contexto
```bash
curl -X POST "http://ec2-18-231-192-82.sa-east-1.compute.amazonaws.com/contexts" \
  -H "Authorization: Bearer your-token-here" \
  -H "Content-Type: application/json" \
  -H "X-User-ID: user-123" \
  -d '{
    "name": "Vendas Imobiliárias",
    "description": "Contexto para análise de vendas de imóveis",
    "prompt": "Analise as chamadas considerando vendas imobiliárias...",
    "vendedores": ["Carlos Pereira"],
    "vendedor_ativo": "Carlos Pereira"
  }'
```

### Curl - Listar Análises
```bash
curl -X GET "http://ec2-18-231-192-82.sa-east-1.compute.amazonaws.com/analyses?limit=10&vendedor=João" \
  -H "Authorization: Bearer your-token-here" \
  -H "X-User-ID: user-123"
```

### Curl - Upload de Áudio
```bash
curl -X POST "http://ec2-18-231-192-82.sa-east-1.compute.amazonaws.com/analyze-audio" \
  -H "Authorization: Bearer your-token-here" \
  -H "X-User-ID: user-123" \
  -F "audio=@call_recording.mp3"
```

## Estrutura do Banco de Dados

### Tabelas Principais
- `users`: Usuários do sistema
- `profiles`: Perfis dos usuários empresariais
- `contexts`: Contextos de análise de vendas
- `contacts`: Contatos dos usuários
- `analyzes`: Análises realizadas (áudio e texto)

### Relacionamentos
- `profiles.user_id` → `users.id`
- `contexts.user_id` → `users.id`
- `contacts.user_id` → `users.id`
- `analyzes.user_id` → `users.id`
- `analyzes.context_uuid` → `contexts.id`

## Configuração

### Variáveis de Ambiente
```env
DB_URI=mysql://user:password@localhost/database
AUTH_TOKEN=your-auth-token-here
```

### Dependências
```bash
pip install flask sqlalchemy bcrypt python-dotenv flask-cors flask-limiter
```

## Rate Limiting

- **Padrão**: 100 requisições por minuto
- **Específico**: Alguns endpoints podem ter limites diferentes

## Logs

A aplicação registra logs em `app.log` com informações sobre:
- Requisições recebidas
- Erros de processamento
- Operações de banco de dados
- Autenticação e autorização

## Status Codes

- `200`: Sucesso
- `201`: Criado com sucesso
- `400`: Requisição inválida
- `401`: Não autorizado
- `403`: Acesso negado
- `404`: Recurso não encontrado
- `500`: Erro interno do servidor

---

**Desenvolvido por FirstLine AI**  
**Versão da API**: 1.0  
**Última atualização**: 15 de julho de 2025
