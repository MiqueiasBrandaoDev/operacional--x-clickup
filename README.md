
# 📂 Estrutura Operacional — Projeto X ClickUp  

## 🔧 Ferramentas  

- **ClickUp** → Gestão de tarefas, formulários e campos personalizados.  
- **Supabase/Postgres** → Banco de dados relacional e relatórios estratégicos.  
- **n8n** → Automação de processos e integrações entre ClickUp, Supabase e ZapSign.  
- **ZapSign** → Assinatura digital de documentos.  
- **Templates de documentos (Docx/HTML → PDF)** → Modelos padronizados com merge de campos.  
- **Webhooks (ClickUp ↔ n8n ↔ Supabase)** → Sincronização em tempo real entre as plataformas.  

---

## 🗂️ Tabelas — Supabase  

- **cmz_planos** → Cadastro de planos e limites contratados.  
- **cmz_cliente** → Registro de clientes vinculados aos planos.  
- **cmz_tipo_servico** → Catálogo de tipos de serviços (reunião, extrajudicial, judicial, etc.).  
- **cmz_solicitacao** → Registro principal das demandas (core da operação).  
- **cmz_parte** → Cadastro de partes (pessoas e empresas).  
- **cmz_solicitacao_parte** → Associação de partes às solicitações com definição de papéis (ex.: requerente, requerido).  
- **cmz_devolutiva** → Registros de devolutivas/comunicações.  
- **cmz_anexo** → Anexos vinculados a solicitações, partes ou devolutivas.  
- **cmz_terceiro** → Cadastro de terceiros (cartórios, correspondentes, etc.).  
- **cmz_solicitacao_terceiro** → Relação entre solicitações e terceiros.  
- **cmz_template_documento** → Modelos de documentos padronizados.  
- **cmz_documento_gerado** → Histórico de documentos gerados e enviados para assinatura.  
- **cmz_clickup_task_map** → Sincronização entre tarefas do ClickUp e registros do Supabase.  
- **cmz_event_log** → Log de eventos para auditoria e rastreabilidade.  

---

## 🏷️ Campos Personalizados — ClickUp  

### 📌 Identificação  
- **Cliente** (lookup/select) → Vinculação ao cadastro de clientes (**cmz_cliente**).  
- **Solicitante** (texto ou usuário responsável pela abertura).  
- **Tipo de serviço** (select) → Relacionado à tabela **cmz_tipo_servico**.  
- **Origem** (select: Reunião | Outro).  
- **Plano (cmz_plano)** (select/lookup) → Identificação do plano contratado vinculado ao cliente.  

### 📌 Comunicação & Documentos  
- **Última devolutiva em** (date) → Data da última comunicação registrada.  

---

## 📑 Tipos de Serviço — Catálogo Inicial  

- **Reunião** → Atendimentos presenciais ou virtuais.  
- **Extrajudicial** → Demandas administrativas, negociações, notificações, cartórios.  
- **Judicial** → Processos judiciais, petições, acompanhamento processual.  
- **Consultoria Empresarial** → Pareceres, análises e orientações estratégicas.  
- **Contratos** → Elaboração, revisão e gestão contratual.  
- **Compliance / LGPD** → Adequação regulatória e auditorias.  
- **Outro** → Demandas não enquadradas nas principais categorias.  

---

## 🔄 Fluxo por Demanda  

### 1. **Abertura (Controller)**  
- Registro inicial via formulário no ClickUp.  
- Criação da solicitação no Supabase e task vinculada no ClickUp.  

### 2. **Cadastro de Partes**  
- Inserção de pessoas/empresas com definição de papéis (Requerente, Requerido, Terceiro, etc.).  
- Associação entre solicitação e partes (**cmz_parte** ↔ **cmz_solicitacao_parte**).  

### 3. **Complementação Técnica**  
- Inclusão de campos específicos conforme o tipo de serviço.  
- Alimentação do campo **meta** com dados dinâmicos por serviço.  

### 4. **Terceiros / Diligências**  
- Registro de terceiros vinculados (**cmz_terceiro** ↔ **cmz_solicitacao_terceiro**).  
- Possível criação de subtarefas no ClickUp.  

### 5. **Devolutivas**  
- Registro estruturado no Supabase (**cmz_devolutiva**).  
- Atualização automática do campo **Última devolutiva em** no ClickUp.  

### 6. **Geração de Documentos e Assinaturas**  
- Seleção de template padronizado (**cmz_template_documento**).  
- Merge de dados da solicitação e partes.  
- Envio automático para **ZapSign**.  
- Registro no histórico (**cmz_documento_gerado**).  

### 7. **Encerramento & Relatórios**  
- Conclusão da task no ClickUp.  
- Consolidação em views estratégicas no Supabase:  
  - Produtividade por responsável.  
  - Status e SLA.  
  - Clientes e serviços.  
- Disponibilização para acompanhamento gerencial.  
