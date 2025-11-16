# Flujo de trabajo recomendado con Git (GitFlow simplificado)

> Pensado para proyectos reales, equipos chicos/medianos.  
> Ramas principales: `main`, `develop`, `feature/*`, `hotfix/*`.

---

## 1. Ramas principales

- **`main`**
  - Siempre deployable.
  - Solo contiene versiones estables (releases).
- **`develop`**
  - Rama donde se integran las features.
  - De acá salen las versiones que luego pasan a `main`.

```bash
# Crear develop a partir de main (solo una vez)
git checkout main
git pull origin main
git branch develop
git push -u origin develop
```

---

## 2. Ramas de feature

**Nombre recomendado:** `feature/nombre-claro`  
Ej: `feature/login-usuarios`, `feature/checkout`, `feature/reportes-pdf`.

### Crear una feature

```bash
# Partir SIEMPRE desde develop
git checkout develop
git pull origin develop

# Crear la rama
git checkout -b feature/login-usuarios

# Trabajar normalmente
git status
git add .
git commit -m "Agregar formulario de login con validación"
git push -u origin feature/login-usuarios
```

### Mantener la feature actualizada con develop

Cada tanto:

```bash
# Estando en la feature
git checkout feature/login-usuarios
git fetch origin
git merge origin/develop
# (resolver conflictos si aparecen, luego)
git push
```

*Opcional:* si preferís rebase:

```bash
git checkout feature/login-usuarios
git fetch origin
git rebase origin/develop
git push --force-with-lease
```

---

## 3. Integrar una feature (merge a develop)

Cuando terminás la feature:

1. Asegurate de estar actualizado con `develop`.
2. Hacés el merge hacia `develop`.

```bash
# 1. Actualizar feature con develop
git checkout feature/login-usuarios
git fetch origin
git merge origin/develop
# resolver conflictos, commit, push

# 2. Merge en develop
git checkout develop
git pull origin develop
git merge feature/login-usuarios
git push origin develop
```

En plataformas como GitHub/GitLab normalmente:
- creás un **Pull Request / Merge Request** desde `feature/...` → `develop`,
- lo revisan,
- y se mergea desde la interfaz web.

---

## 4. Preparar una release (opcional, pero prolijo)

Cuando `develop` está estable y listo para sacar una versión:

- Podés usar una rama `release/x.y.z` **o** mergear directo a `main`.

### Opción simple (sin rama release)

```bash
git checkout main
git pull origin main

git merge develop   # integrar todo lo probado
git tag v1.0.0      # marcar versión
git push origin main --tags
```

---

## 5. Hotfix (arreglos urgentes en producción)

Para bugs críticos que están en producción (en `main`).

**Flujo:**

1. Crear rama desde `main`.
2. Arreglar.
3. Merge a `main` y a `develop`.

```bash
# 1. Crear hotfix
git checkout main
git pull origin main
git checkout -b hotfix/fix-login-crash

# 2. Arreglar, commitear, subir
git add .
git commit -m "Fix: evitar crash al loguear sin contraseña"
git push -u origin hotfix/fix-login-crash

# 3. Merge a main
git checkout main
git pull origin main
git merge hotfix/fix-login-crash
git push origin main

# 4. Merge a develop (para que también tenga el fix)
git checkout develop
git pull origin develop
git merge hotfix/fix-login-crash
git push origin develop
```

---

## 6. Resumen rápido del flujo

1. **`main`** → producción, estable.
2. **`develop`** → integración de features.
3. **`feature/*`** → nuevas funcionalidades:
   - crear desde `develop`,
   - mergear de vuelta a `develop` con PR/MR.
4. **`hotfix/*`** → bugs urgentes:
   - crear desde `main`,
   - merge a `main` y también a `develop`.

---

## 7. Comandos clave (mini cheat sheet)

```bash
# Ver ramas
git branch
git branch -a

# Cambiar de rama
git checkout nombre-rama
git switch nombre-rama

# Crear rama nueva y cambiar
git checkout -b feature/nueva-feature
git switch -c feature/nueva-feature

# Ver estado
git status

# Agregar cambios
git add .
git commit -m "Mensaje descriptivo"

# Sincronizar con remoto
git pull origin nombre-rama
git push origin nombre-rama

# Merge
git checkout destino
git merge origen
```

---

## 8. Reglas sanas de trabajo

- No commitear directo en `main`.
- Evitar commits gigantes, mejor varios pequeños y claros.
- Siempre hacer `pull` antes de empezar a trabajar.
- Usar nombres de ramas descriptivos.
- Para trabajo en equipo: usar PR/MR siempre para revisar código.
