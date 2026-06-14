# Entrega DevSecOps - Task Manager Flask

## Execução local

```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt -r requirements-dev.txt
pytest
```

## Pipeline proposto

O workflow `.github/workflows/devsecops-ci-cd.yml` executa:

1. Testes unitários com cobertura mínima.
2. SAST com Bandit.
3. SCA com OWASP Dependency-Check via Docker.
4. Build de imagem Docker.
5. DAST com OWASP ZAP via Docker contra a aplicação em execução.
6. Gates de deploy para desenvolvimento, homologação e produção.

## GitFlow recomendado

- `main`: versão estável e pronta para produção.
- `develop`: integração contínua das funcionalidades aprovadas.
- `feature/*`: novas funcionalidades, como observabilidade ou melhorias de autenticação.
- `bugfix/*`: correções de defeitos durante desenvolvimento.
- `release/*`: estabilização e homologação antes do merge em `main`.
- `hotfix/*`: correções urgentes originadas de produção.

Para esta aplicação pequena, um GitFlow enxuto é suficiente: `main`, `develop`,
`feature/*`, `bugfix/*` e `hotfix/*`. A branch `release/*` deve ser usada quando
houver uma janela formal de homologação.

## Monitoramento

O `docker-compose.yml` inclui Prometheus, Grafana e cAdvisor para acompanhar
métricas de contêiner. Para uma evolução de produção, recomenda-se instrumentar
a aplicação com `/metrics` usando `prometheus-flask-exporter`, além de logs
estruturados e alertas no Grafana.

## Principais hardenings recomendados

- Remover o `SECRET_KEY` padrão e exigir segredo por variável de ambiente.
- Atualizar dependências vulneráveis indicadas por SCA.
- Trocar rotas destrutivas por `POST`/`DELETE` com CSRF.
- Garantir autorização por dono da tarefa nas rotas de update/delete.
- Adicionar cabeçalhos de segurança como CSP, HSTS, X-Frame-Options e X-Content-Type-Options.
