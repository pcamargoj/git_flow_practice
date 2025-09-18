# 🚀 Automatización de Release con GitHub Actions

Este repositorio incluye workflows automatizados para gestionar el proceso de release siguiendo Git Flow.

## 📋 Workflows Disponibles

### 1. 🎯 Create Release Branch (Manual)
**Archivo:** `.github/workflows/create_release_branch.yml`

Permite crear manualmente una rama release desde la interfaz de GitHub.

**Cómo usar:**
1. Ve a Actions → Create Release Branch
2. Haz clic en "Run workflow"
3. Ingresa la versión (ej: `1.2.0`)
4. Selecciona la rama base (`develop` o `main`)
5. Ejecuta el workflow

**Qué hace:**
- ✅ Valida el formato de versión
- ✅ Verifica que la rama no exista
- ✅ Crea la rama `release/x.y.z`
- ✅ Crea un issue de seguimiento
- ✅ Proporciona enlaces útiles

### 2. 🤖 Auto Create Release Branch (Automático)
**Archivo:** `.github/workflows/auto_release_branch.yml`

Se ejecuta automáticamente cuando haces push a `develop` con un mensaje específico.

**Cómo usar:**
```bash
# Formato del commit:
git commit -m "feat: nueva funcionalidad [create-release:1.2.0]"
git push origin develop
```

**Qué hace:**
- ✅ Extrae la versión del mensaje del commit
- ✅ Crea la rama release automáticamente
- ✅ Crea un Pull Request a `main`
- ✅ Agrega comentario al commit original
- ✅ Notifica en Slack (opcional)

### 3. 🏁 Finalize Release (Automático)
**Archivo:** `.github/workflows/finalize_release.yml`

Se ejecuta cuando se hace merge de una PR de release a `main`.

**Qué hace:**
- ✅ Crea tag de versión (`v1.2.0`)
- ✅ Crea GitHub Release con notas
- ✅ Hace merge de vuelta a `develop`
- ✅ Elimina la rama release
- ✅ Comenta en la PR original

## 🔄 Flujo Completo de Release

### Opción 1: Manual
```bash
# 1. Ir a GitHub Actions → Create Release Branch
# 2. Ejecutar con versión 1.2.0
# 3. Trabajar en la rama release/1.2.0
# 4. Crear PR a main manualmente
# 5. Hacer merge → Se ejecuta finalize_release automáticamente
```

### Opción 2: Automática
```bash
# 1. Hacer commit con trigger automático
git commit -m "feat: preparar release [create-release:1.2.0]"
git push origin develop

# 2. Se crea automáticamente:
#    - Rama release/1.2.0
#    - PR a main

# 3. Revisar y hacer merge de la PR
# 4. Se ejecuta finalize_release automáticamente
```

## 📝 Formatos de Commit

### Para ejecutar tests:
```bash
git commit -m "feat: nueva funcionalidad [run-release]"
```

### Para crear release automático:
```bash
git commit -m "feat: preparar release [create-release:1.2.0]"
git commit -m "chore: bump version [create-release:2.1.0]"
```

### Para saltar CI:
```bash
git commit -m "docs: actualizar README [skip-ci]"
```

## ⚙️ Configuración Requerida

### Secrets de GitHub (opcional):
- `SLACK_WEBHOOK_URL`: Para notificaciones en Slack

### Permisos requeridos:
- `contents: write`: Para crear ramas y tags
- `pull-requests: write`: Para crear PRs
- `issues: write`: Para crear issues de seguimiento

## 🎨 Personalización

### Cambiar el formato de versión:
Edita la regex en `auto_release_branch.yml`:
```yaml
# Actual: [create-release:1.2.0]
if [[ $COMMIT_MSG =~ \[create-release:([0-9]+\.[0-9]+\.[0-9]+)\] ]]; then

# Para formato con prefijo v: [create-release:v1.2.0]
if [[ $COMMIT_MSG =~ \[create-release:v([0-9]+\.[0-9]+\.[0-9]+)\] ]]; then
```

### Cambiar ramas base:
Modifica las opciones en `create_release_branch.yml`:
```yaml
options:
  - develop
  - main
  - staging  # Agregar nueva opción
```

### Agregar validaciones adicionales:
En cualquier workflow, agrega steps como:
```yaml
- name: Ejecutar tests antes de release
  run: |
    python -m pytest
    npm test
    # Otros comandos de validación
```

## 🔍 Troubleshooting

### Error: "La rama release ya existe"
```bash
# Eliminar rama local y remota
git branch -D release/1.2.0
git push origin --delete release/1.2.0
```

### Error: "Formato de versión inválido"
Asegúrate de usar el formato `x.y.z`:
- ✅ Correcto: `1.2.0`, `2.10.5`
- ❌ Incorrecto: `v1.2.0`, `1.2`, `1.2.0-beta`

### Error de permisos:
Verifica que el token tenga los permisos necesarios en Settings → Actions → General.

## 📊 Monitoreo

### Ver ejecuciones:
- Ve a Actions en GitHub
- Filtra por workflow name
- Revisa logs detallados

### Notificaciones:
- Issues de seguimiento automáticos
- Comentarios en PRs y commits
- Slack (si está configurado)

## 🚀 Próximos Pasos

1. **Configurar notificaciones:** Agregar webhook de Slack
2. **Validaciones adicionales:** Tests, linting, security scans
3. **Deploy automático:** Conectar con CD pipeline
4. **Rollback automático:** En caso de fallos en producción
5. **Changelog automático:** Generar desde commits convencionales