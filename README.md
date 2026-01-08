1. Prerrequisitos obligatorios

Antes de cualquier cosa, tu entorno local debe cumplir estos tres puntos:

1.1 Tener pre-commit instalado
pre-commit --version


Si no está instalado:

pip install pre-commit


o en Kali / Debian:

sudo apt install pre-commit

1.2 Tener acceso SSH al repo de la organización

Tu hook usa:

repo: git@github.com:Code-Syndicate-Latam/org-security-gitleaks.git


Eso requiere SSH, no HTTPS.

Verifica acceso:

ssh -T git@github.com


Debe responder algo como:

Hi <usuario>! You've successfully authenticated...


Si esto falla, pre-commit no podrá clonar el repo.

1.3 Estar dentro de un repo que ya tenga el PR mergeado

Es decir, en el repo ms-usuario, ms-auth o Front-End, debe existir:

.pre-commit-config.yaml
.gitleaks.toml


Verifica:

ls -a

2. Tu .pre-commit-config.yaml (correcto)

El que tienes es válido:

repos:
  - repo: git@github.com:Code-Syndicate-Latam/org-security-gitleaks.git
    rev: v8.21.0
    hooks:
      - id: gitleaks
        name: Gitleaks - Prevent secrets
        args:
          - "--redact"
          - "--config=.gitleaks.toml"


Punto clave:

rev apunta a un tag/version del repo central

args usa el .gitleaks.toml local al repo consumidor

3. Paso a paso para ejecutarlo localmente
Paso 1 – Instalar los hooks en ese repositorio

Desde la raíz del repo:

pre-commit install


Esto:

Crea .git/hooks/pre-commit

Conecta Git con pre-commit

Paso 2 – Descargar y preparar el hook centralizado

Ejecuta:

pre-commit run gitleaks --all-files


En la primera ejecución, pre-commit:

Clonará org-security-gitleaks

Cacheará el hook en:

~/.cache/pre-commit/


Si hay secretos → falla
Si no hay secretos → pasa

Paso 3 – Flujo normal de trabajo (automático)

A partir de ahora, cada vez que hagas:

git commit -m "mensaje"


Ocurre automáticamente:

Se ejecuta Gitleaks

Si detecta secretos → el commit se bloquea

Si pasa → el commit continúa

No tienes que correr nada manualmente.

4. (Opcional) Actualizar la versión del hook central

Si el repo central publica un nuevo tag:

pre-commit autoupdate


Esto:

Actualiza rev: en .pre-commit-config.yaml

Solo si el repo central es accesible

5. Errores comunes y cómo identificarlos
Error: no puede clonar el repo
Permission denied (publickey).


👉 Tu SSH key no tiene acceso a la org.

Error: no encuentra .gitleaks.toml
error loading config: open .gitleaks.toml: no such file or directory


👉 El archivo debe existir en el repo consumidor, no en el repo central.

Error: hook no encontrado
Hook 'gitleaks' not found


👉 El repo org-security-gitleaks no expone un hook con ese id.
Debe existir un .pre-commit-hooks.yaml en ese repo.

6. Resumen ejecutivo

✔ No copias nada manualmente
✔ El hook vive en el repo central
✔ El config vive en cada repo
✔ Solo ejecutas localmente:

pre-commit install
pre-commit run gitleaks --all-files


Y desde ese punto queda 100% automático.

Si quieres, en el siguiente mensaje puedo:

Revisar el org-security-gitleaks y validar que el hook esté bien definido

Simular un fallo real de Gitleaks y cómo se corrige

Alinear este pre-commit con el workflow de GitHub Actions para que ambos usen exactamente la misma lógica
