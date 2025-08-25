# 🌳 Mapeamento Hierárquico – Operacional X

```
📦 Espaço: OPERACIONAL X
│
├── 📁 Pasta: Solicitações
│   │
│   ├── 📋 Lista: Abertas
│   ├── 📋 Lista: Em andamento
│   ├── 📋 Lista: Em devolutiva
│   └── 📋 Lista: Concluídas
│
│   🔖 Custom Fields:
│     • Tipo de serviço (dropdown / cmz_tipo_servico)
│     • Cliente (lookup → Clientes)
│     • Responsável interno (people)
│     • Origem da solicitação (Reunião / Outro)
│     • Última devolutiva em (date)
│     • SLA previsto (date)
│     • Status jurídico (etapas)
│
├── 📁 Pasta: Partes & Terceiros
│   │
│   ├── 📋 Lista: Partes vinculadas
│   └── 📋 Lista: Terceiros envolvidos
│
│   🔖 Custom Fields:
│     • Tipo (Requerente / Requerido / Terceiro)
│     • Solicitação vinculada (lookup)
│
├── 📁 Pasta: Devolutivas
│   │
│   └── 📋 Lista: Histórico de devolutivas
│
│   🔖 Custom Fields:
│     • Solicitação vinculada
│     • Tipo de devolutiva (Texto / Documento / Reunião)
│     • Responsável
│
└── 📁 Pasta: Documentos & Templates
    │
    ├── 📋 Lista: Templates de documentos
    ├── 📋 Lista: Documentos gerados
    └── 📋 Lista: Assinaturas em andamento (ZapSign)
    
    🔖 Custom Fields:
      • Vinculação com Solicitação
      • Status assinatura (Pendente / Assinado / Cancelado)
      • Data de envio
```

---

# 🌳 Mapeamento Hierárquico – Clientes

```
📦 Espaço: CLIENTES
│
└── 📋 Lista: Cadastro de Clientes
    🔖 Custom Fields:
      • Nome / Razão social
      • Plano (dropdown / cmz_planos)
      • Data de início
      • Documentos principais
```

---

# 🔗 Integrações (visão paralela)

```
ClickUp (interface)
   │
   ▼
n8n (automação/orquestração)
   │
   ├── Sincroniza Solicitações, Partes, Devolutivas → Supabase (cmz_* tables)
   ├── Gera Documento → ZapSign
   └── Atualiza Custom Field "Última devolutiva em"
   │
   ▼
Supabase (base central)
   │
   ▼
Dashboards (Metabase / Superset)
```

---

👉 Essa árvore deixa claro **quem opera no ClickUp** (e vê uma estrutura simples) e **quem usa o Supabase/n8n** (infra invisível, mas com controle total).

Quer que eu **transforme esse mapeamento num diagrama visual (tipo organograma/fluxograma)** ou você prefere manter nesse formato hierárquico de árvore para já aplicar no ClickUp?