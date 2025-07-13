# ☁️ Arquitetura Bancária Moderna com AWS - Fellipe Roveri Custodio

---

## ☁️ Serviços AWS Utilizados

| Serviço                 | Finalidade                                                                 |
|-------------------------|----------------------------------------------------------------------------|
| S3 + CloudFront         | Hospedagem de microfrontends com caching, performance e alta disponibilidade |
| API Gateway (Externo e Interno) | Interface de entrada unificada para microsserviços internos e expostos         |
| Cognito + RHSSO         | Autenticação segura com SSO corporativo                                     |
| Lambda                  | Orquestração leve, processamento de eventos e integração com sistemas externos |
| ECS Fargate             | Execução dos microsserviços por domínio (Crédito, Documentos, Financeiro)     |
| Amazon RDS PostgreSQL   | Banco relacional com transações ACID e consistência forte                     |
| RDS Proxy               | Multiplexação segura de conexões com banco relacional                         |
| Amazon MSK (Kafka)      | Comunicação assíncrona, CDC e backbone de eventos                             |
| Kafka Connect + Debezium| Leitura de alterações no banco legado via CDC                                 |
| Step Functions          | Orquestração transacional (padrão SAGA) entre débito e crédito                 |
| Amazon SQS              | Fila intermediária para desacoplamento e resiliência                          |
| Amazon S3 (Data Lake)   | Armazenamento de logs, documentos, payloads e dados históricos                 |
| ElastiCache (Redis)     | Cache de informações de conta e limites com baixa latência                    |
| CloudWatch + X-Ray      | Logs, métricas, tracing distribuído e alarmes                                 |
| WAF + Shield + ACM      | Proteção contra ataques e TLS para APIs e frontend                            |
| Route 53                | DNS gerenciado com roteamento inteligente                                     |

---

## 🏦 Alinhamento com o Modelo Arquitetural Bancário

A arquitetura foi desenhada respeitando três camadas bem definidas, inspiradas nos modelos de grandes bancos como o Itaú:

---

### 🔶 Core Bancário — *“Motor transacional e regras críticas”*

- Regras de negócio como transferências, cadastros e validações vivem nessa camada.
- Garantia de consistência transacional com PostgreSQL (Multi-AZ + Proxy).
- Processamento desacoplado com Kafka + Step Functions + Lambda para garantir atomicidade.
- Integração com sistema legado por CDC sem dependência direta.

**🔧 Engenharia aplicada:**
- PostgreSQL com RDS Proxy para concorrência alta.
- Step Functions com padrão SAGA para segurança em falhas (rollback/compensações).
- Kafka para comunicação confiável entre domínios críticos.

---

### 🟧 Experiência — *“Onde a jornada digital acontece”*

- ECS + API Gateway (BFF) conectam frontend com backend de forma organizada e segura.
- Microsserviços por domínio com deploys independentes e versionamento via pipelines.
- APIs REST bem definidas com contratos desacoplados dos canais.

**🚀 Pilar de transformação digital:**
- Backend desacoplado permite evolução contínua.
- Microsserviços focados em regras específicas (e.g. `/credito`, `/transferencia`, `/documento`).
- Caching com Redis para minimizar chamadas ao banco.

---

### 🟨 Canais Digitais — *“Interação direta com o cliente”*

- MFE hospedado em S3 com entrega global via CloudFront.
- Autenticação unificada via Cognito + integração com RHSSO (OpenID/OAuth2).
- Proteção com WAF, Shield e TLS automático via ACM.

**👥 Pensado para pessoas:**
- Frontend seguro, escalável e de rápida distribuição.
- Preparado para múltiplos dispositivos (web/mobile).
- Fácil deploy e baixo custo operacional.

---

## 🔁 Integração com Sistema Legado via CDC

- Kafka Connect + Debezium replicam alterações do banco legado para Kafka Topics.
- Lambdas consomem os tópicos e atualizam o sistema moderno.
- S3 recebe cópias brutas dos dados para histórico e governança.

**Por que essa escolha?**
- Não exige alterações no sistema legado.
- Permite integração contínua e em tempo real.
- Suporta estratégia híbrida e migração progressiva.

---

## 🔍 Observabilidade e Operações

- **CloudWatch Logs**: Armazenamento centralizado de logs (ECS, Lambda, API Gateway).
- **CloudWatch Metrics**: Acompanhamento de throughput, latência e erros.
- **X-Ray + OpenTelemetry**: Tracing distribuído ponta-a-ponta.
- **Alarms**: Detecção de falhas (e.g. alto tempo de resposta ou códigos 5xx).
- **Buckets S3** segregados para logs, documentos e arquivos fiscais.

**Resultado:** Monitoramento confiável e rastreabilidade em toda a jornada.

---

## 🧾 Fluxos Críticos Descoplados

### ✅ Transferência entre Contas (SAGA)
- **Step Function** coordena o fluxo: validação → débito → crédito.
- Em falha no crédito, realiza compensação no débito.
- Total rastreabilidade com logs e estados de execução.

### ✅ Documentos (PDFs, comprovantes)
- Recebidos e armazenados em S3.
- Processamento posterior por ECS ou Lambda.
- Controle de acesso e versionamento ativado.

### ✅ Cache e Performance
- Redis reduz latência na consulta de saldo e limites.
- TTL controlado por domínio.

---

## ✨ Decisões Arquiteturais Estratégicas

| Decisão                                | Justificativa                                                                 |
|----------------------------------------|-------------------------------------------------------------------------------|
| ECS Fargate para microsserviços        | Permite deploy modular, controle de rede, escalabilidade e versionamento     |
| Kafka como backbone de eventos         | Padrão em sistemas bancários, garante desacoplamento e auditabilidade        |
| Step Functions para transações         | Facilita rollback e visão de estado em tempo real (sem boilerplate)          |
| CDC com Kafka Connect + Debezium       | Evita acoplamento com sistemas legados e permite evolução gradual            |
| S3 como data lake e repositório geral  | Custo baixo, versionamento, governança e integração analítica futura         |
| Redis para caching                     | Reduz uso do RDS e melhora performance perceptível pelo cliente              |
| CloudWatch + X-Ray                     | Observabilidade distribuída ponta-a-ponta                                    |
| Cognito + RHSSO                        | Segurança centralizada, login único e governança de identidade               |

---

## 📌 Conclusão

Essa arquitetura é **moderna, resiliente, escalável e preparada para nuvem híbrida**, mantendo compatibilidade com sistemas legados, sem perder as capacidades de inovação.

- ✅ **Alto throughput com controle de estado**
- ✅ **Governança e rastreabilidade completas**
- ✅ **Preparada para expansão e integrações futuras (ex: IA, analytics)**
- ✅ **Focada em segurança, modularidade e autonomia de times**

---

## ✍️ Autor

**Fellipe Roveri Custodio**  
Especialista em Arquitetura de Sistemas Bancários  
🔗 [linkedin.com/in/felliperoveri](https://www.linkedin.com/in/felliperoveri/)
