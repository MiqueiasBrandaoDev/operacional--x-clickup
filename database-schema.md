# 🗄️ Documentação do Banco de Dados - Operacional x ClickUp

## 📋 Visão Geral
Este documento apresenta a estrutura completa do banco de dados para o projeto operacional integrado com ClickUp, incluindo todas as tabelas, colunas, tipos de dados e relacionamentos.

---

## 🏗️ Estrutura das Tabelas

### 1. **cmz_planos**
Cadastro de planos contratuais e seus limites.

| Coluna | Tipo | Restrições | Descrição |
|--------|------|------------|-----------|
| `id` | SERIAL | PRIMARY KEY | Identificador único do plano |
| `nome` | VARCHAR(100) | NOT NULL | Nome do plano |
| `descricao` | TEXT | | Descrição detalhada do plano |
| `limite_solicitacoes_mes` | INTEGER | | Limite mensal de solicitações |
| `limite_usuarios` | INTEGER | | Limite de usuários |
| `valor_mensal` | DECIMAL(10,2) | | Valor mensal do plano |
| `ativo` | BOOLEAN | DEFAULT TRUE | Status ativo/inativo |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Data de criação |
| `updated_at` | TIMESTAMP | DEFAULT NOW() | Data de atualização |

---

### 2. **cmz_cliente**
Registro de clientes vinculados aos planos.

| Coluna | Tipo | Restrições | Descrição |
|--------|------|------------|-----------|
| `id` | SERIAL | PRIMARY KEY | Identificador único do cliente |
| `nome` | VARCHAR(150) | NOT NULL | Nome/Razão social |
| `email` | VARCHAR(100) | UNIQUE | Email principal |
| `telefone` | VARCHAR(20) | | Telefone de contato |
| `documento` | VARCHAR(20) | UNIQUE | CPF/CNPJ |
| `tipo_pessoa` | VARCHAR(10) | CHECK IN ('FISICA', 'JURIDICA') | Tipo de pessoa |
| `endereco` | JSONB | | Endereço completo |
| `plano_id` | INTEGER | FK: cmz_planos(id) | Plano contratado |
| `data_contrato` | DATE | | Data do contrato |
| `status` | VARCHAR(20) | DEFAULT 'ATIVO' | Status do cliente |
| `observacoes` | TEXT | | Observações gerais |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Data de criação |
| `updated_at` | TIMESTAMP | DEFAULT NOW() | Data de atualização |

---

### 3. **cmz_tipo_servico**
Catálogo de tipos de serviços oferecidos.

| Coluna | Tipo | Restrições | Descrição |
|--------|------|------------|-----------|
| `id` | SERIAL | PRIMARY KEY | Identificador único |
| `nome` | VARCHAR(100) | NOT NULL UNIQUE | Nome do tipo de serviço |
| `descricao` | TEXT | | Descrição detalhada |
| `categoria` | VARCHAR(50) | | Categoria do serviço |
| `tempo_estimado_dias` | INTEGER | | Tempo médio em dias |
| `valor_sugerido` | DECIMAL(10,2) | | Valor sugerido |
| `campos_especificos` | JSONB | | Campos específicos por tipo |
| `template_default` | INTEGER | FK: cmz_template_documento(id) | Template padrão |
| `ativo` | BOOLEAN | DEFAULT TRUE | Status ativo/inativo |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Data de criação |
| `updated_at` | TIMESTAMP | DEFAULT NOW() | Data de atualização |

---

### 4. **cmz_solicitacao** 
Registro principal das demandas (core da operação).

| Coluna | Tipo | Restrições | Descrição |
|--------|------|------------|-----------|
| `id` | SERIAL | PRIMARY KEY | Identificador único |
| `numero_protocolo` | VARCHAR(20) | UNIQUE NOT NULL | Protocolo único da solicitação |
| `cliente_id` | INTEGER | FK: cmz_cliente(id) NOT NULL | Cliente solicitante |
| `tipo_servico_id` | INTEGER | FK: cmz_tipo_servico(id) NOT NULL | Tipo de serviço |
| `titulo` | VARCHAR(200) | NOT NULL | Título da solicitação |
| `descricao` | TEXT | | Descrição detalhada |
| `prioridade` | VARCHAR(10) | CHECK IN ('BAIXA', 'MEDIA', 'ALTA', 'URGENTE') | Prioridade |
| `status` | VARCHAR(20) | DEFAULT 'ABERTA' | Status atual |
| `origem` | VARCHAR(20) | CHECK IN ('REUNIAO', 'EMAIL', 'TELEFONE', 'OUTRO') | Origem da demanda |
| `responsavel` | VARCHAR(100) | | Responsável pela solicitação |
| `solicitante` | VARCHAR(100) | | Nome do solicitante |
| `valor_servico` | DECIMAL(10,2) | | Valor do serviço |
| `prazo_estimado` | DATE | | Prazo estimado |
| `prazo_real` | DATE | | Prazo real de conclusão |
| `meta` | JSONB | | Metadados específicos por tipo |
| `observacoes` | TEXT | | Observações gerais |
| `clickup_task_id` | VARCHAR(50) | UNIQUE | ID da task no ClickUp |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Data de criação |
| `updated_at` | TIMESTAMP | DEFAULT NOW() | Data de atualização |
| `closed_at` | TIMESTAMP | | Data de encerramento |

---

### 5. **cmz_parte**
Cadastro de partes (pessoas e empresas).

| Coluna | Tipo | Restrições | Descrição |
|--------|------|------------|-----------|
| `id` | SERIAL | PRIMARY KEY | Identificador único |
| `nome` | VARCHAR(150) | NOT NULL | Nome completo/Razão social |
| `tipo_pessoa` | VARCHAR(10) | CHECK IN ('FISICA', 'JURIDICA') | Tipo de pessoa |
| `documento` | VARCHAR(20) | | CPF/CNPJ |
| `rg` | VARCHAR(20) | | RG (para pessoa física) |
| `data_nascimento` | DATE | | Data de nascimento |
| `estado_civil` | VARCHAR(20) | | Estado civil |
| `profissao` | VARCHAR(100) | | Profissão |
| `nacionalidade` | VARCHAR(50) | | Nacionalidade |
| `email` | VARCHAR(100) | | Email |
| `telefone` | VARCHAR(20) | | Telefone |
| `endereco` | JSONB | | Endereço completo |
| `dados_adicionais` | JSONB | | Dados específicos por contexto |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Data de criação |
| `updated_at` | TIMESTAMP | DEFAULT NOW() | Data de atualização |

---

### 6. **cmz_solicitacao_parte**
Associação de partes às solicitações com definição de papéis.

| Coluna | Tipo | Restrições | Descrição |
|--------|------|------------|-----------|
| `id` | SERIAL | PRIMARY KEY | Identificador único |
| `solicitacao_id` | INTEGER | FK: cmz_solicitacao(id) NOT NULL | Solicitação |
| `parte_id` | INTEGER | FK: cmz_parte(id) NOT NULL | Parte envolvida |
| `papel` | VARCHAR(50) | NOT NULL | Papel (REQUERENTE, REQUERIDO, TESTEMUNHA, etc.) |
| `observacoes` | TEXT | | Observações específicas |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Data de criação |

**Constraint:** `UNIQUE(solicitacao_id, parte_id, papel)`

---

### 7. **cmz_devolutiva**
Registros de devolutivas/comunicações.

| Coluna | Tipo | Restrições | Descrição |
|--------|------|------------|-----------|
| `id` | SERIAL | PRIMARY KEY | Identificador único |
| `solicitacao_id` | INTEGER | FK: cmz_solicitacao(id) NOT NULL | Solicitação relacionada |
| `tipo` | VARCHAR(50) | NOT NULL | Tipo de devolutiva |
| `titulo` | VARCHAR(200) | NOT NULL | Título da devolutiva |
| `conteudo` | TEXT | NOT NULL | Conteúdo da comunicação |
| `destinatario` | VARCHAR(100) | | Destinatário |
| `meio_envio` | VARCHAR(50) | | Email, WhatsApp, Presencial, etc. |
| `data_envio` | TIMESTAMP | | Data de envio |
| `data_leitura` | TIMESTAMP | | Data de leitura/confirmação |
| `status` | VARCHAR(20) | DEFAULT 'ENVIADA' | Status da devolutiva |
| `anexos` | JSONB | | Referências a anexos |
| `responsavel` | VARCHAR(100) | | Responsável pelo envio |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Data de criação |
| `updated_at` | TIMESTAMP | DEFAULT NOW() | Data de atualização |

---

### 8. **cmz_anexo**
Anexos vinculados a solicitações, partes ou devolutivas.

| Coluna | Tipo | Restrições | Descrição |
|--------|------|------------|-----------|
| `id` | SERIAL | PRIMARY KEY | Identificador único |
| `nome_arquivo` | VARCHAR(255) | NOT NULL | Nome original do arquivo |
| `nome_fisico` | VARCHAR(255) | NOT NULL | Nome físico no storage |
| `caminho` | VARCHAR(500) | NOT NULL | Caminho completo |
| `tipo_mime` | VARCHAR(100) | | Tipo MIME do arquivo |
| `tamanho` | BIGINT | | Tamanho em bytes |
| `hash_arquivo` | VARCHAR(64) | | Hash MD5/SHA256 |
| `entidade_tipo` | VARCHAR(50) | NOT NULL | Tipo da entidade vinculada |
| `entidade_id` | INTEGER | NOT NULL | ID da entidade |
| `categoria` | VARCHAR(50) | | Categoria do anexo |
| `descricao` | TEXT | | Descrição do anexo |
| `publico` | BOOLEAN | DEFAULT FALSE | Visível para cliente |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Data de upload |
| `created_by` | VARCHAR(100) | | Usuário que fez upload |

---

### 9. **cmz_terceiro**
Cadastro de terceiros (cartórios, correspondentes, etc.).

| Coluna | Tipo | Restrições | Descrição |
|--------|------|------------|-----------|
| `id` | SERIAL | PRIMARY KEY | Identificador único |
| `nome` | VARCHAR(150) | NOT NULL | Nome/Razão social |
| `tipo` | VARCHAR(50) | NOT NULL | CARTORIO, CORRESPONDENTE, PERITO, etc. |
| `documento` | VARCHAR(20) | | CPF/CNPJ |
| `registro_profissional` | VARCHAR(50) | | OAB, CRC, etc. |
| `email` | VARCHAR(100) | | Email de contato |
| `telefone` | VARCHAR(20) | | Telefone |
| `endereco` | JSONB | | Endereço completo |
| `especialidades` | TEXT[] | | Especialidades/Áreas de atuação |
| `observacoes` | TEXT | | Observações gerais |
| `ativo` | BOOLEAN | DEFAULT TRUE | Status ativo/inativo |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Data de criação |
| `updated_at` | TIMESTAMP | DEFAULT NOW() | Data de atualização |

---

### 10. **cmz_solicitacao_terceiro**
Relação entre solicitações e terceiros.

| Coluna | Tipo | Restrições | Descrição |
|--------|------|------------|-----------|
| `id` | SERIAL | PRIMARY KEY | Identificador único |
| `solicitacao_id` | INTEGER | FK: cmz_solicitacao(id) NOT NULL | Solicitação |
| `terceiro_id` | INTEGER | FK: cmz_terceiro(id) NOT NULL | Terceiro envolvido |
| `funcao` | VARCHAR(100) | NOT NULL | Função do terceiro |
| `valor_honorario` | DECIMAL(10,2) | | Valor dos honorários |
| `status` | VARCHAR(20) | DEFAULT 'PENDENTE' | Status da participação |
| `data_inicio` | DATE | | Data de início |
| `data_conclusao` | DATE | | Data de conclusão |
| `observacoes` | TEXT | | Observações específicas |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Data de criação |
| `updated_at` | TIMESTAMP | DEFAULT NOW() | Data de atualização |

---

### 11. **cmz_template_documento**
Modelos de documentos padronizados.

| Coluna | Tipo | Restrições | Descrição |
|--------|------|------------|-----------|
| `id` | SERIAL | PRIMARY KEY | Identificador único |
| `nome` | VARCHAR(150) | NOT NULL | Nome do template |
| `descricao` | TEXT | | Descrição do template |
| `tipo_servico_id` | INTEGER | FK: cmz_tipo_servico(id) | Tipo de serviço relacionado |
| `categoria` | VARCHAR(50) | | Categoria do documento |
| `formato` | VARCHAR(10) | CHECK IN ('DOCX', 'HTML', 'PDF') | Formato do template |
| `caminho_arquivo` | VARCHAR(500) | NOT NULL | Caminho do arquivo template |
| `variaveis` | JSONB | | Variáveis disponíveis |
| `requer_assinatura` | BOOLEAN | DEFAULT FALSE | Requer assinatura digital |
| `ativo` | BOOLEAN | DEFAULT TRUE | Status ativo/inativo |
| `versao` | VARCHAR(10) | DEFAULT '1.0' | Versão do template |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Data de criação |
| `updated_at` | TIMESTAMP | DEFAULT NOW() | Data de atualização |

---

### 12. **cmz_documento_gerado**
Histórico de documentos gerados e enviados para assinatura.

| Coluna | Tipo | Restrições | Descrição |
|--------|------|------------|-----------|
| `id` | SERIAL | PRIMARY KEY | Identificador único |
| `solicitacao_id` | INTEGER | FK: cmz_solicitacao(id) NOT NULL | Solicitação relacionada |
| `template_id` | INTEGER | FK: cmz_template_documento(id) NOT NULL | Template utilizado |
| `nome_documento` | VARCHAR(255) | NOT NULL | Nome do documento gerado |
| `caminho_arquivo` | VARCHAR(500) | NOT NULL | Caminho do arquivo gerado |
| `dados_merge` | JSONB | | Dados utilizados no merge |
| `zapsign_documento_id` | VARCHAR(100) | | ID no ZapSign |
| `status_assinatura` | VARCHAR(20) | DEFAULT 'PENDENTE' | Status da assinatura |
| `url_assinatura` | VARCHAR(500) | | URL para assinatura |
| `data_envio_assinatura` | TIMESTAMP | | Data de envio para assinatura |
| `data_assinatura` | TIMESTAMP | | Data da assinatura |
| `signatarios` | JSONB | | Lista de signatários |
| `observacoes` | TEXT | | Observações |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Data de geração |
| `updated_at` | TIMESTAMP | DEFAULT NOW() | Data de atualização |

---

### 13. **cmz_clickup_task_map**
Sincronização entre tarefas do ClickUp e registros do Supabase.

| Coluna | Tipo | Restrições | Descrição |
|--------|------|------------|-----------|
| `id` | SERIAL | PRIMARY KEY | Identificador único |
| `clickup_task_id` | VARCHAR(50) | UNIQUE NOT NULL | ID da task no ClickUp |
| `solicitacao_id` | INTEGER | FK: cmz_solicitacao(id) UNIQUE | Solicitação relacionada |
| `clickup_list_id` | VARCHAR(50) | | ID da lista no ClickUp |
| `clickup_space_id` | VARCHAR(50) | | ID do espaço no ClickUp |
| `status_clickup` | VARCHAR(50) | | Status atual no ClickUp |
| `ultimo_sync` | TIMESTAMP | | Última sincronização |
| `dados_clickup` | JSONB | | Dados completos da task |
| `sync_ativo` | BOOLEAN | DEFAULT TRUE | Sincronização ativa |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Data de criação |
| `updated_at` | TIMESTAMP | DEFAULT NOW() | Data de atualização |

---

### 14. **cmz_event_log**
Log de eventos para auditoria e rastreabilidade.

| Coluna | Tipo | Restrições | Descrição |
|--------|------|------------|-----------|
| `id` | SERIAL | PRIMARY KEY | Identificador único |
| `evento_tipo` | VARCHAR(50) | NOT NULL | Tipo do evento |
| `entidade_tipo` | VARCHAR(50) | NOT NULL | Tipo da entidade afetada |
| `entidade_id` | INTEGER | NOT NULL | ID da entidade |
| `usuario` | VARCHAR(100) | | Usuário responsável |
| `descricao` | TEXT | NOT NULL | Descrição do evento |
| `dados_antes` | JSONB | | Estado anterior |
| `dados_depois` | JSONB | | Estado posterior |
| `ip_origem` | INET | | IP de origem |
| `user_agent` | TEXT | | User agent |
| `origem` | VARCHAR(50) | | CLICKUP, SUPABASE, N8N, etc. |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Data do evento |

---

### 15. **tasks** 
Tabela de integração ClickUp com contexto e RAG.

| Coluna | Tipo | Restrições | Descrição |
|--------|------|------------|-----------|
| `id` | SERIAL | PRIMARY KEY | Identificador único |
| `clickup_id` | VARCHAR(50) | UNIQUE NOT NULL | ID da task no ClickUp |
| `name` | VARCHAR(500) | NOT NULL | Nome da task |
| `description` | TEXT | | Descrição da task |
| `status` | VARCHAR(50) | | Status atual no ClickUp |
| `priority` | INTEGER | | Prioridade (1-4 no ClickUp) |
| `list_id` | VARCHAR(50) | | ID da lista no ClickUp |
| `folder_id` | VARCHAR(50) | | ID da pasta no ClickUp |
| `space_id` | VARCHAR(50) | | ID do espaço no ClickUp |
| `assignees` | JSONB | | Lista de responsáveis |
| `watchers` | JSONB | | Lista de observadores |
| `creator_id` | VARCHAR(50) | | ID do criador no ClickUp |
| `time_estimate` | BIGINT | | Estimativa de tempo em ms |
| `time_spent` | BIGINT | | Tempo gasto em ms |
| `custom_fields` | JSONB | | Campos personalizados |
| `tags` | TEXT[] | | Tags da task |
| `parent_task_id` | VARCHAR(50) | | ID da task pai (subtasks) |
| `order_index` | VARCHAR(50) | | Índice de ordenação |
| `team_id` | VARCHAR(50) | | ID do time |
| `url` | VARCHAR(500) | | URL da task no ClickUp |
| `date_created` | TIMESTAMP | | Data de criação no ClickUp |
| `date_updated` | TIMESTAMP | | Data de atualização no ClickUp |
| `date_closed` | TIMESTAMP | | Data de fechamento |
| `date_done` | TIMESTAMP | | Data de conclusão |
| `archived` | BOOLEAN | DEFAULT FALSE | Task arquivada |
| `context_summary` | TEXT | | Resumo do contexto para RAG |
| `key_entities` | JSONB | | Entidades-chave extraídas |
| `related_documents` | TEXT[] | | Documentos relacionados |
| `embedding_vector` | VECTOR(1536) | | Vetor de embedding para RAG |
| `last_embedding_update` | TIMESTAMP | | Última atualização do embedding |
| `solicitacao_id` | INTEGER | FK: cmz_solicitacao(id) | Solicitação relacionada |
| `sync_status` | VARCHAR(20) | DEFAULT 'SYNCED' | Status de sincronização |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Data de criação local |
| `updated_at` | TIMESTAMP | DEFAULT NOW() | Data de atualização local |

---

## 🔗 Relacionamentos Principais

### **Hierarquia Cliente → Solicitação**
```
cmz_cliente (1) ←→ (N) cmz_solicitacao
```

### **Plano → Cliente**
```
cmz_planos (1) ←→ (N) cmz_cliente
```

### **Tipo de Serviço → Solicitação**
```
cmz_tipo_servico (1) ←→ (N) cmz_solicitacao
```

### **Solicitação ↔ Partes (Many-to-Many)**
```
cmz_solicitacao (N) ←→ cmz_solicitacao_parte ←→ (N) cmz_parte
```

### **Solicitação → Devolutivas**
```
cmz_solicitacao (1) ←→ (N) cmz_devolutiva
```

### **Solicitação ↔ Terceiros (Many-to-Many)**
```
cmz_solicitacao (N) ←→ cmz_solicitacao_terceiro ←→ (N) cmz_terceiro
```

### **Anexos Polimórficos**
```
cmz_anexo (N) → (1) [cmz_solicitacao | cmz_parte | cmz_devolutiva]
```

### **Templates → Documentos Gerados**
```
cmz_template_documento (1) ←→ (N) cmz_documento_gerado
```

### **Sincronização ClickUp**
```
cmz_solicitacao (1) ←→ (1) cmz_clickup_task_map ←→ (1) tasks
```

### **Integração RAG**
```
tasks (1) ←→ (1) cmz_solicitacao
tasks.embedding_vector → Sistema RAG
```

---

## 📊 Índices Recomendados

### **Índices de Performance**
```sql
-- Busca de solicitações por cliente
CREATE INDEX idx_solicitacao_cliente ON cmz_solicitacao(cliente_id);

-- Busca por status e prioridade
CREATE INDEX idx_solicitacao_status ON cmz_solicitacao(status, prioridade);

-- Busca de tasks por ClickUp ID
CREATE INDEX idx_tasks_clickup_id ON tasks(clickup_id);

-- Busca vetorial para RAG
CREATE INDEX idx_tasks_embedding ON tasks USING ivfflat (embedding_vector vector_cosine_ops);

-- Log de eventos por entidade
CREATE INDEX idx_event_log_entidade ON cmz_event_log(entidade_tipo, entidade_id);

-- Anexos por entidade
CREATE INDEX idx_anexo_entidade ON cmz_anexo(entidade_tipo, entidade_id);
```

### **Índices de Integridade**
```sql
-- Sincronização ClickUp
CREATE UNIQUE INDEX idx_unique_clickup_sync ON cmz_clickup_task_map(clickup_task_id);

-- Protocolo único
CREATE UNIQUE INDEX idx_unique_protocolo ON cmz_solicitacao(numero_protocolo);
```

---

## 🚀 Extensões Necessárias

```sql
-- Para busca vetorial (RAG)
CREATE EXTENSION IF NOT EXISTS vector;

-- Para campos JSONB otimizados
CREATE EXTENSION IF NOT EXISTS btree_gin;

-- Para funções de texto e busca
CREATE EXTENSION IF NOT EXISTS pg_trgm;
```

---

## 📈 Views Estratégicas Sugeridas

### **Dashboard Operacional**
- Solicitações por status e responsável
- SLA por tipo de serviço
- Produtividade mensal
- Pipeline de documentos

### **Relatórios Gerenciais**
- Faturamento por cliente/plano
- Performance por terceiro
- Análise de tipos de demanda
- Satisfação por devolutivas

### **Análise RAG**
- Contextos mais consultados
- Padrões de solicitações similares
- Recomendações automáticas
- Base de conhecimento evolutiva