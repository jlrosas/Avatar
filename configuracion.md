# Git Subtree Workflow 🚀

Este documento explica cómo trabajar con **Git Subtree** para dividir un proyecto en subcarpetas, mantenerlas sincronizadas como repositorios independientes y colaborar en equipo.

Ideal para proyectos que necesitan separar **módulos o componentes** pero seguir trabajando desde un repositorio central.

---

## 📂 Estructura inicial

Supongamos que tienes un repositorio principal con la siguiente estructura:

```
repo-principal/
│── cartridges/sfra-base
│── cloud-code/
│── mobile-app/
│── pwa/
```

Queremos que `sfra-base/` y `cloud-code/` vivan en sus propios repositorios, pero sin dejar de formar parte del `repo-principal`.

---

## 1️⃣ Administrador - Crear el repo principal y subrepos

1. Crear un repositorio principal en GitHub → `repo-principal` y rama principal `master`
2. Crear repositorios independientes para los submódulos → `sfra-base` y `cloud-code` y rama principal `main`

Clonamos el principal:

```bash
git clone git@github.com:TU_USUARIO/repo-principal.git
cd repo-principal
```

---

## 2️⃣ Administrador - Agregar los subtrees

Agregamos los remotos:

```bash
git remote add sfra-base git@github.com:TU_USUARIO/sfra-base.git
git remote add cloud-code git@github.com:TU_USUARIO/cloud-code.git
```

Creamos los subtrees:

```bash
git subtree add --prefix=cartridges/sfra-base sfra-base main --squash
git subtree add --prefix=cloud-code cloud-code main --squash
```

Descargar cambios del subrepo

```bash
git subtree pull --prefix=cartridges/sfra-base sfra-base main --squash
git subtree pull --prefix=cloud-code cloud-code main --squash
```

Subimos los subtrees a master:

```bash
git push origin master
```

Tambien puede Subir cambios de los subtrees a una rama de integracion para luego enviarlo al master con pull Request de `merge-sfra-fix` a `master`:

```bash
git checkout -b merge-sfra-fix
git push origin merge-sfra-fix
```

Si deseas subir cambios directo al subrepo hechos en tu carpeta local

```bash
git subtree push --prefix=cartridges/sfra-base sfra-base main
git subtree push --prefix=cloud-code cloud-code main
```

---

##  3️⃣ Trabajar con los subtrees

### 🔹 clonar subrepo

```bash
git clone git@github.com:TU_USUARIO/sfra-base.git
cd sfra-base
```

### 🔹 Crear rama de trabajo

```bash
git checkout -b feature/fix-readme
```

### 🔹 Subir cambios al subrepo

```bash
echo "// hotfix readme" >> readme.md
git add readme.md
git commit -m "Actualización en readme"
git push origin feature/fix-readme
```

---

## 4️⃣ Hacer Pull Request en GitHub

1. Ve al subrepo (`sfra-base`) en GitHub.
2. Crea un **Pull Request** desde la rama `fix-readme` hacia la rama principal `main` del proyecto.
3. Los cambios estarán disponibles de manera aislada y ordenada.

---

## 📊 Ventajas y desventajas

| Aspecto        | Subtree                                                         |
| -------------- | --------------------------------------------------------------- |
| **Ventaja**    | Todo el código en un repo central pero con repos modulares.     |
| **Ventaja**    | Más simple que `submodule` (no hay `.gitmodules`).              |
| **Ventaja**    | PRs y releases pueden gestionarse en repos separados.           |
| **Desventaja** | Los pushes/pulls deben hacerse manualmente.                     |
| **Desventaja** | Historial unificado, puede ser confuso si no se usa `--squash`. |

---

## 📈 Flujo completo resumido

1. Admin repositorio principal crea `repo-principal` y repositorios `independientes`, agrega repositorios `independientes` como subtree en `repo-principal`.

2. Developer crea ramas de desarrollo hace cambios en repo independiente (ejemplo: `sfra-base`).

3. PR en repo independiente `sfra-base` → rama de desarrollo a rama `main`.

4. Admin repositorio principal sincroniza cambios en monorepo con subtree pull.

5. Admin repositorio principal, abre PR en `repo-principal` para revisión y merge, de rama de integracion a rama `master`.

---

## ✅ Conclusión

* Útil cuando un equipo necesita dividir responsabilidades.
* Mantiene independencia en repos críticos (ej: frontend y backend).
* Permite crecer y escalar módulos sin romper el repo principal.
