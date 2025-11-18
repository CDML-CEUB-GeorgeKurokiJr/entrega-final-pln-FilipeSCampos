# 🚀 Guia Rápido: Persistência de Dados

## ✅ Sim, seus dados vão persistir!

Seu `docker-compose.yml` está configurado corretamente com volumes mapeados para a pasta `volumes/` do projeto:

- **Qdrant** (collections e vetores) → `./volumes/qdrant`
- **n8n** (workflows, incluindo agente-cozinha.json) → `./volumes/n8n`
- **PostgreSQL** (dados do banco) → `./volumes/postgres`
- **MinIO** (arquivos) → `./volumes/minio`

## 📋 Comandos Corretos

### Para parar os containers (mantém dados):
```bash
docker compose down
```

### ⚠️ NUNCA use:
```bash
docker compose down -v    # ❌ Isso APAGA todos os dados!
```

### Para subir novamente:
```bash
docker compose up -d
```

## 🔄 Fluxo Completo

```bash
# 1. Parar containers (dados ficam na pasta volumes/)
docker compose down

# 2. Fazer o que precisar (atualizar código, etc.)

# 3. Subir novamente
docker compose up -d

# 4. Aguardar containers iniciarem (30-60 segundos)
docker compose ps

# 5. Verificar se tudo está funcionando
# - Qdrant: http://localhost:6333/dashboard
# - n8n: http://localhost:5678
# - Sua aplicação: http://localhost:5000
```

## ✅ O que será preservado:

- ✅ **Todas as collections do Qdrant** (dados populados)
- ✅ **Workflow do n8n** (agente-cozinha.json)
- ✅ **Dados do PostgreSQL** (memória do chat)
- ✅ **Arquivos no MinIO**

## 🔍 Verificar se os dados estão lá:

### Qdrant:
```bash
# Ver collections
curl http://localhost:6333/collections

# Ou via Python
python -c "from src.vector_store import QdrantVectorStore; vs = QdrantVectorStore(); print([c['name'] for c in vs.list_collections()])"
```

### n8n:
- Acesse http://localhost:5678
- Faça login (admin/admin123)
- Seu workflow `agente-cozinha.json` deve estar lá

### PostgreSQL:
```bash
docker compose exec postgres psql -U chat_user -d chat_memory -c "\dt"
```

## 💡 Dica de Segurança (Backup)

Antes de fazer `docker compose down`, é recomendado fazer backup:

```bash
# Backup do Qdrant
python scripts/backup_qdrant_collections.py

# Backup do n8n (a pasta volumes/n8n já é seu backup)
# Mas você pode copiar manualmente se quiser:
# cp -r volumes/n8n backups/n8n_backup_$(date +%Y%m%d)
```

## 🚨 Se algo der errado:

1. **Verificar se a pasta volumes/ existe:**
   ```bash
   ls -la volumes/
   ```

2. **Verificar permissões (Linux/Mac):**
   ```bash
   chmod -R 755 volumes/
   ```

3. **Restaurar do backup:**
   ```bash
   python scripts/restore_qdrant_backup.py backups/qdrant/qdrant_backup_*.json
   ```

## 📝 Resumo

- ✅ Use `docker compose down` (sem `-v`)
- ✅ Dados ficam em `volumes/`
- ✅ Ao subir novamente com `docker compose up -d`, tudo volta como estava
- ✅ Faça backup antes de mudanças importantes

