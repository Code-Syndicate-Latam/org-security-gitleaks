# 🔐 Gitleaks + pre-commit (Configuración Local)

Este repositorio utiliza **Gitleaks** integrado con **pre-commit** para prevenir la inclusión accidental de secretos (API keys, tokens, credenciales, etc.) antes de que el código sea versionado.

La lógica de seguridad está **centralizada en un repositorio de la organización**, mientras que cada repositorio consumidor define su configuración local.

---

## 📋 Prerrequisitos

Antes de comenzar, asegúrate de cumplir con lo siguiente:

- Python instalado
- Acceso SSH a GitHub y a la organización
- Los siguientes archivos deben existir en la raíz del repositorio:
  - `.pre-commit-config.yaml`
  - `.gitleaks.toml`

---

## 🛠 Instalación de pre-commit

Verifica si `pre-commit` ya está instalado:

```bash
pre-commit --version

Si no está instalado, puedes instalarlo con:

pip install pre-commit

O en sistemas Debian/Kali:

sudo apt install pre-commit

🔑 Verificar acceso SSH a GitHub

El hook de Gitleaks se obtiene desde un repositorio privado de la organización usando SSH.

Verifica tu acceso ejecutando:

ssh -T git@github.com

La salida esperada debe ser similar a:

Hi <usuario>! You've successfully authenticated...

⚙️ Instalación del hook en el repositorio

Desde la raíz del repositorio, ejecuta:

pre-commit install

Esto:

    Instala el hook en .git/hooks/pre-commit

    Activa la ejecución automática de Gitleaks antes de cada commit

▶️ Ejecución manual inicial (recomendado)

Para validar el estado actual del repositorio y escanear todos los archivos, ejecuta:

pre-commit run gitleaks --all-files

En la primera ejecución, pre-commit:

    Clonará el repositorio central de seguridad

    Cacheará el hook localmente

    Ejecutará Gitleaks utilizando el archivo .gitleaks.toml del repositorio

Si se detectan secretos, el comando fallará y mostrará el detalle correspondiente.
🔄 Uso normal (automático)

A partir de este punto, no es necesario ejecutar nada manualmente.

Cada vez que ejecutes:

git commit -m "mensaje del commit"

Se ejecutará automáticamente Gitleaks:

    ✅ Si no se detectan secretos, el commit continúa

    ❌ Si se detectan secretos, el commit se bloquea

⬆️ Actualización del hook (opcional)

Si el repositorio central publica una nueva versión del hook, puedes actualizarlo con:

pre-commit autoupdate

Esto actualizará la versión (rev) definida en .pre-commit-config.yaml.
🧯 Errores comunes
❌ Error de permisos SSH

Permission denied (publickey)

Causa: tu clave SSH no tiene acceso al repositorio de la organización.
❌ Error: no se encuentra .gitleaks.toml

error loading config: open .gitleaks.toml: no such file or directory

Causa: el archivo .gitleaks.toml no existe en la raíz del repositorio.
✅ Resumen

    Gitleaks se ejecuta localmente antes de cada commit

    El hook está centralizado en un repositorio de seguridad de la organización

    Cada repositorio define su configuración mediante .gitleaks.toml

    El mismo estándar de seguridad se utiliza localmente y en CI/CD
