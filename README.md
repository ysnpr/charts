# charts

Repositório GitOps com manifests do Argo CD, seguindo o padrão **App of Apps**.

## Estrutura

```bash
charts/
├── bootstrap/          # Ponto de entrada — única app registrada manualmente no Argo CD
├── apps/               # Apps filhas gerenciadas pelo bootstrap
│   └── airflow3/       # Apache Airflow — app.yaml + values.yaml
│
├── airflow/            # [LEGADO] Teste standalone do Airflow
└── trino/              # [LEGADO] Teste standalone do Trino
```

## Como funciona

O padrão App of Apps centraliza o deploy em uma única Application raiz:

```bash
bootstrap/root-app.yaml   →   aponta para apps/
                                └── apps/kustomization.yaml lista cada app filha
                                      └── cada app.yaml instala o Helm chart no cluster
```

Basta registrar `bootstrap/root-app.yaml` no Argo CD. As demais apps são sincronizadas automaticamente.

## Adicionando uma nova aplicação

1. Crie o diretório `apps/<nome-da-app>/`
2. Adicione `app.yaml` (Application do Argo CD) e `values.yaml` (valores Helm)
3. Registre em `apps/kustomization.yaml`:

   ```yaml
   resources:
     - airflow3/app.yaml
     - <nome-da-app>/app.yaml # adicionar aqui
   ```

4. Faça o push — o Argo CD sincroniza automaticamente.

## Apps em produção

| App     | Namespace | Chart                  | Versão |
| ------- | --------- | ---------------------- | ------ |
| airflow | airflow3  | apache-airflow/airflow | 1.19.0 |

## Legado

`airflow/` e `trino/` são manifests de testes anteriores ao padrão App of Apps. Funcionam se aplicados diretamente no Argo CD, mas não fazem parte do fluxo automatizado.
