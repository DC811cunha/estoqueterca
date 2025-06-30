````markdown
# 1. CAPA

- **Nome do Grupo**: André Miquelino Campos, Diego Cunha  
- **Matrículas**: 1326703 / 1324564  
- **Disciplina**: Qualidade de Software  
- **Professor**: Diego Sauter Possamai  
- **Sistema**: estoqueterca  
- **Data de Entrega**: 01 de julho de 2025  

# 2. INTRODUÇÃO

## 2.1 RESUMO DOS RESULTADOS DA N2

Na auditoria N2, validamos cinco métricas críticas de qualidade:

- **Cobertura de Código**: 88% (meta ≥ 85%)  
- **Tempo Médio de Resposta (GET)**: 130 ms (meta < 150 ms)  
- **Complexidade Ciclomática**: 7 por função (meta ≤ 8)  
- **Vulnerabilidades Críticas**: 0 (meta = 0)  
- **Health-Check (uptime)**: 100% (10/10)  

Foi identificado que, embora todas as metas tenham sido cumpridas, faltaram anexos detalhados, registros de versões de ferramentas e um plano de ação com cronograma.

## 2.2 OBJETIVO DA N3

- Revisar e atualizar as métricas de qualidade definidas na N1, incorporando o aprendizado da execução real.  
- Propor uma estratégia de automatização para coleta, análise e acompanhamento contínuo dessas métricas.  
- Criar um plano técnico que integre pipelines CI/CD, ferramentas de análise e dashboards para monitoramento em tempo real.  

## 2.3 ESCOPO ANALISADO

- **Módulos**: clientes, produtos, usuarios  
- **Endpoints**: todas as rotas CRUD e `/health`  
- **Ambiente de teste**: Node.js v18.16.0, MySQL 8.0, Express 4.18.2  

# 3. ATUALIZAÇÃO DAS MÉTRICAS DE QUALIDADE

| Código | Métrica                              | Meta Anterior | Resultado N2 | Meta Atualizada | Ferramenta               | Justificativa                                                       |
| ------ | ------------------------------------ | ------------- | ------------ | --------------- | ------------------------ | ------------------------------------------------------------------- |
| M-01   | Cobertura de Código                  | ≥ 85%         | 88%          | ≥ 90%           | Jest + nyc               | Atingir 90% para cobrir módulos críticos.                          |
| M-02   | Tempo Médio de Resposta (GET)        | < 150 ms      | 130 ms       | < 120 ms        | Artillery                | Reduzir tempo para melhorar UX.                                     |
| M-03   | Complexidade Ciclomática (por fn)    | ≤ 8           | 7            | ≤ 7             | SonarQube                | Meta mais rígida para refatoração contínua.                        |
| M-04   | Vulnerabilidades Críticas            | 0             | 0            | 0               | npm audit & OWASP ZAP    | Alerta imediato em caso de nova vulnerabilidade.                   |
| M-05   | Health-Check (uptime)                | 100% (10/10)  | 100%         | 100%            | curl + Prometheus        | Monitoramento contínuo com alertas.                                 |
| M-06   | Bugs por Sprint                      | ≤ 2           | 4            | ≤ 1             | Jira                     | Inclusão de métrica para qualidade de entrega por sprint.          |
| M-07   | Vulnerabilidades de Nível Médio/Baixo | ≤ 5          | 3            | ≤ 3             | SonarQube + OWASP ZAP    | Controlar vulnerabilidades secundárias antes que se tornem críticas.|

# 4. PROPOSTA DE AUTOMATIZAÇÃO

## 4.1 Objetivo da Automação

- **Eficiência**: reduzir esforço manual.  
- **Alertas**: notificar imediatamente sobre falhas.  
- **Reprodutibilidade**: garantir processos idênticos em cada build.  
- **Visibilidade**: dashboards atualizados em tempo real.  

## 4.2 Ferramentas Escolhidas

- **CI/CD**: GitHub Actions + Jenkins  
- **Testes Unitários**: Jest + nyc  
- **Performance**: Artillery  
- **Análise de Código**: SonarQube Community Edition  
- **Security**: npm audit, OWASP ZAP  
- **Monitoramento**: Prometheus + Grafana Cloud  
- **Issue Tracker**: Jira  
- **Comunicação**: Slack Webhooks  

## 4.3 Fluxo Automatizado Proposto

1. **Push na branch `main`** → GitHub Actions dispara pipeline.  
2. **Setup**: `npm ci`, configura variáveis de ambiente a partir de Secrets.  
3. **Lint & Testes Unitários**:
   ```yaml
   - name: Run ESLint
     run: npm run lint
   - name: Run Jest Tests
     run: npm test -- --coverage
````

4. **SonarQube Scan**:

   ```yaml
   - name: SonarQube Scan
     uses: sonarsource/sonarcloud-github-action@v1
     with:
       projectKey: estoqueterca
       organization: org-demo
       token: ${{ secrets.SONAR_TOKEN }}
   ```
5. **Performance Tests**:

   ```yaml
   - name: Run Artillery
     run: artillery run scripts/artillery/load-test.yaml --output report.json
   ```
6. **Security Audit**:

   ```yaml
   - name: npm audit
     run: npm audit --json > audit.json
   - name: OWASP ZAP Scan
     uses: zaproxy/action-full-scan@v0.6.0
   ```
7. **Notifications**: webhook para Slack em caso de falha.
8. **Dashboard Update**: artefatos enviados a S3 e coletados pelo Prometheus.
9. **Jenkins**: job agendado para Artillery + Health-Check, com publicação em Grafana Cloud.

## 4.4 Visualização dos Dados

* **Grafana Cloud**: dashboards para cobertura, latência, complexidade, vulnerabilidades.
* **Jira Dashboard**: burndown de bugs por sprint (M-06).
* **README.md**: badges de coverage e status do build.

## 4.5 Desenho da Arquitetura da Automação

![Figura 1 – Diagrama da arquitetura CI/CD e Monitoramento](./diagram_ci_cd.png)

*Figura 1 – Diagrama da arquitetura CI/CD e Monitoramento*

## 4.6 Benefícios Esperados e Limitações

| Benefício                         | Limitação                            |
| --------------------------------- | ------------------------------------ |
| Execução automática 24/7          | Custo de execução de VMs em nuvem    |
| Embasamento para decisões rápidas | Curva de aprendizado das ferramentas |
| Visibilidade em tempo real        | Manutenção de pipelines e scripts    |
| Notificações imediatas            | Falsos positivos em testes           |

# 5. CONCLUSÃO

* **Nível atual da qualidade**: muito satisfatório, com todas as métricas críticas dentro das metas.
* **Pontos fortes**: cobertura alta, tempos de resposta baixos, zero vulnerabilidades críticas.
* **Pontos fracos**: falta de métricas de bugs por sprint e vulnerabilidades secundárias.
* **Viabilidade**: GitHub Actions + Jenkins + Prometheus/Grafana é viável com baixo custo incremental.
* **Recomendações**:

  1. Implementar o pipeline proposto nos próximos 15 dias.
  2. Treinar a equipe no uso de Grafana e SonarQube.
  3. Revisar métricas semestrais e ajustar metas conforme evolução do projeto.

# 6. ANEXOS

* **Anexo A**: Prints de dashboards (coverage, Sonar, Grafana).
* **Anexo B**: Relatórios exportados: `coverage/lcov-report/index.html`, `report.json`, `audit.json`.
* **Anexo C**: Scripts de automação (GitHub Actions YAML e Jenkinsfile).
* **Anexo D**: Pipeline-as-Code (Jenkinsfile exemplar).

```
```
