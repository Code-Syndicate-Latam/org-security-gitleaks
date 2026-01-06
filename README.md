# Org Security Gitleaks - Pre-Commit Hook

Este repositorio contiene la configuración corporativa de **Gitleaks** para prevenir la inclusión de secretos en los repositorios de la organización.  
Todos los desarrolladores deben usar este hook antes de enviar cualquier cambio a repositorios Java o Angular.

---

## 🔹 Requisitos

- Git >= 2.20  
- [Pre-commit](https://pre-commit.com/) >= 3.3  
- SSH configurado para GitHub (`git@github.com:Code-Syndicate-Latam/...`)  

---

## 🔹 Flujo para los desarrolladores

Si aceptas el PR que contiene los archivos `.pre-commit-config.yaml` y `.gitleaks.toml`, **no necesitas copiar nada manualmente**.  
Solo sigue estos pasos:

1. **Instalar los hooks locales:**

```bash
pre-commit install
pre-commit autoupdate
```

Probar que Gitleaks funciona (opcional pero recomendado):

echo "TEST_SECRET=123456" > test-secret.txt
git add test-secret.txt
git commit -m "test gitleaks hook"


Resultado esperado: el commit será bloqueado indicando que se detectó un secreto.

Limpiar la prueba:

rm test-secret.txt
git reset HEAD test-secret.txt
