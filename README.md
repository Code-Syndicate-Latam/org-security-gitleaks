🔐 Configuración y uso de Gitleaks con pre-commit (Local)

Este repositorio utiliza Gitleaks como hook de pre-commit, centralizado desde el repositorio de seguridad de la organización, para prevenir la inclusión accidental de secretos (tokens, credenciales, claves, etc.) en el código.

📋 Prerrequisitos

Antes de comenzar, asegúrate de cumplir con lo siguiente:

Tener Python instalado

Tener acceso SSH a GitHub y a la organización

Tener los siguientes archivos en la raíz del repositorio (incluidos vía PR):

.pre-commit-config.yaml

.gitleaks.toml

🛠 Instalación de pre-commit

Verifica si pre-commit ya está instalado:

pre-commit --version


Si no está instalado, instálalo según tu sistema:

pip install pre-commit


o en sistemas Debian/Kali:

sudo apt install pre-commit

🔑 Verificar acceso SSH a GitHub

El hook se descarga desde un repositorio privado de la organización usando SSH.
Verifica que tu clave SSH tenga acceso:

ssh -T git@github.com


La respuesta esperada debe ser similar a:

Hi <usuario>! You've successfully authenticated...

⚙️ Instalación del hook en el repositorio

Desde la raíz del repositorio, ejecuta:

pre-commit install


Esto instalará el hook en .git/hooks/pre-commit y lo activará automáticamente para futuros commits.

▶️ Ejecución manual inicial (recomendado)

Para ejecutar Gitleaks sobre todos los archivos del repositorio y validar el estado actual:

pre-commit run gitleaks --all-files


En la primera ejecución, pre-commit:

Clonará el repositorio central de seguridad

Cacheará el hook localmente

Ejecutará Gitleaks usando el archivo .gitleaks.toml del repositorio

Si se detectan secretos, el comando fallará y mostrará el detalle.

🔄 Uso normal (automático)

A partir de este punto, no es necesario ejecutar nada manualmente.

Cada vez que ejecutes:

git commit -m "mensaje del commit"


Se ejecutará automáticamente Gitleaks:

✅ Si no se detectan secretos → el commit continúa

❌ Si se detectan secretos → el commit se bloquea

⬆️ Actualización del hook (opcional)

Si el repositorio central publica una nueva versión del hook:

pre-commit autoupdate


Esto actualizará la versión (rev) definida en .pre-commit-config.yaml.

🧯 Errores comunes

Error de permisos SSH

Permission denied (publickey)


➡ Tu clave SSH no tiene acceso al repositorio de la organización.

Error: no se encuentra .gitleaks.toml

error loading config: open .gitleaks.toml: no such file or directory


➡ El archivo debe existir en la raíz del repositorio.

✅ Resumen

El hook de Gitleaks está centralizado en un repositorio de seguridad

Cada repositorio solo define su configuración local

La validación se ejecuta automáticamente en cada commit

El mismo estándar se utiliza tanto localmente como en CI/CD

Resultado esperado: el commit será bloqueado indicando que se detectó un secreto.

Limpiar la prueba:

rm test-secret.txt
git reset HEAD test-secret.txt
