# 🔁 Actividad 6.9 – `git revert`: deshacer cambios de forma segura

> En la Actividad 6.8 has visto cómo `git reset` reescribe historia local.
> Ahora vas a aprender a usar **`git revert` para deshacer commits de forma segura**, creando nuevos commits que invierten cambios sin borrar el historial.

---

## 🎯 Objetivos de la actividad

Al finalizar esta actividad serás capaz de:

- Explicar qué hace `git revert` y por qué es seguro en ramas compartidas.
- Revertir un commit concreto sin eliminarlo del historial.
- Revertir varios commits (uno a uno o con `--no-commit`).
- Comparar `git reset` y `git revert` y saber cuándo usar cada uno.

---

## 🧠 Conceptos clave: reset vs revert

- `git reset` mueve la rama hacia atrás y puede **reescribir historia**, eliminando commits de la rama actual. Es peligroso si ya has hecho push.
- `git revert` no borra commits, sino que crea un **nuevo commit** que invierte los cambios de uno anterior. El historial queda intacto: A‑B‑C‑D‑(revert de B).

Por eso:

- `git revert` es la forma **recomendada para deshacer commits en ramas compartidas** (por ejemplo, `main` en remoto).

---

## 🧩 Parte 1 – Preparar un historial con un “commit problemático”

### Reto 1.1 – Crear el escenario

1. Crea una carpeta para esta actividad:

   ```bash
   mkdir actividad-6-9-git-revert
   cd actividad-6-9-git-revert
   ```

2. Inicializa el repositorio:

   ```bash
   git init
   ```

3. Crea `config.txt` con:

   ```text
   modo=produccion
   nivel_log=info
   ```

4. Añade y haz commit:

   ```bash
   git add config.txt
   git commit -m "Config inicial para producción"
   ```

5. Crea un commit correcto:

   ```bash
   echo "cache=activada" >> config.txt
   git add config.txt
   git commit -m "Activa cache por defecto"
   ```

6. Crea un commit “malo” (bug):

   ```bash
   echo "modo=debug" >> config.txt
   git add config.txt
   git commit -m "Cambia a modo debug en producción (bug)"
   ```

7. Crea otro commit correcto:

   ```bash
   echo "limite_conexiones=100" >> config.txt
   git add config.txt
   git commit -m "Ajusta limite de conexiones"
   ```

8. Revisa el historial:

   ```bash
   git log --oneline
   cat config.txt
   ```

### Checkpoint 1 – Evidencia

- Crea `cp1-historial-inicial-revert.txt` con:
  - La salida de `git log --oneline`.
  - El contenido actual de `config.txt`.

---

## 🧩 Parte 2 – Revertir un commit concreto

### Reto 2.1 – Revertir el commit que introdujo el bug

Tu objetivo: **deshacer solo el cambio “modo=debug”**, sin tocar el resto de commits.

1. Identifica el hash del commit “Cambia a modo debug en producción (bug)”:

   ```bash
   git log --oneline
   ```

2. Ejecuta:

   ```bash
   git revert <hash_del_bug>
   ```

   Esto creará un nuevo commit que invierte los cambios del commit con bug.

3. Comprueba:

   ```bash
   git log --oneline
   cat config.txt
   ```

   Verás un commit nuevo con mensaje tipo `Revert "Cambia a modo debug..."` y el archivo sin la línea problemática (o con ella corregida según el diff).

### Checkpoint 2 – Evidencia

- Crea `cp2-revert-simple.txt` con:
  - La salida de `git log --oneline` después del revert.
  - El contenido de `config.txt` después del revert.
  - Una frase explicando por qué este método es **seguro** incluso si el historial ya se ha compartido.

---

## 🧩 Parte 3 – Revertir el último commit (HEAD)

### Reto 3.1 – Revertir HEAD

1. Añade un cambio más:

   ```bash
   echo "nivel_log=debug" >> config.txt
   git add config.txt
   git commit -m "Sube nivel de log a debug (también bug)"
   ```

2. Sin buscar hash, revierte directamente el último commit:

   ```bash
   git revert HEAD
   ```

   Esto revertirá el commit actual (HEAD).

3. Comprueba:

   ```bash
   git log --oneline
   cat config.txt
   ```

### Checkpoint 3 – Evidencia

- Crea `cp3-revert-head.txt` con:
  - La salida de `git log --oneline`.
  - Una explicación de la diferencia entre `git revert HEAD` y `git reset --hard HEAD~1` en un contexto colaborativo.

---

## 🧩 Parte 4 – Revertir varios commits (con `--no-commit`)

### Reto 4.1 – Deshacer un rango de commits en un único revert

Imagina que quieres revertir **dos commits concretos** en un solo commit de revert.

1. Crea un par de commits pequeños:

   ```bash
   echo "experimento_1=activo" >> config.txt
   git add config.txt
   git commit -m "Activa experimento 1"

   echo "experimento_2=activo" >> config.txt
   git add config.txt
   git commit -m "Activa experimento 2"
   ```

2. Comprueba el log:

   ```bash
   git log --oneline
   ```

3. Ahora usa `git revert` **sin crear commits intermedios**:

   ```bash
   git revert --no-commit <hash_experimento_2>
   git revert --no-commit <hash_experimento_1>
   ```

   Esto aplica los “reverts” al staging area sin cerrar el commit.

4. Crea **un único commit** que revierta ambos:

   ```bash
   git commit -m "Revierte experimentos 1 y 2"
   ```

5. Comprueba:

   ```bash
   git log --oneline
   cat config.txt
   ```

### Checkpoint 4 – Evidencia

- Crea `cp4-multiples-revert.txt` con:
  - Los comandos usados con `--no-commit`.
  - La salida de `git log --oneline` final.
  - Una explicación de por qué a veces interesa agrupar varios reverts en un solo commit.

---

## 🧩 Parte 5 – Manejo de conflictos al revertir

### Reto 5.1 – Provocar un conflicto de revert

1. Crea una rama nueva:

   ```bash
   git checkout -b rama-conflicto
   ```

2. En esta rama, modifica la misma parte de `config.txt` donde actúan algunos commits anteriores (por ejemplo, cambia `modo=produccion` a otra cosa) y haz commit.

3. Vuelve a la rama donde estaban los commits originales (por ejemplo, `main`) y trata de hacer `git revert` sobre uno de los commits que tocaban la misma línea.

   ```bash
   git checkout main
   git revert <hash_que_modifica_modo>
   ```

   Git puede generar un conflicto si el contexto ha cambiado.

4. Ejecuta:

   ```bash
   git status
   ```

5. Abre el archivo en conflicto y resuélvelo eliminando los marcadores (`<<<<<<<`, `=======`, `>>>>>>>`) y dejando una versión coherente.

6. Marca como resuelto y termina el revert:

   ```bash
   git add config.txt
   git revert --continue
   ```

### Checkpoint 5 – Evidencia

- Crea `cp5-conflicto-revert.txt` con:
  - La salida de `git status` durante el conflicto.
  - Una breve descripción de cómo lo has resuelto.

---

## 🧩 Parte 6 – Reflexión final: revert vs reset

Crea `reflexion-6-9.md` y responde:

1. Explica con tus palabras la diferencia conceptual entre `git reset` y `git revert`.
2. Indica **dos situaciones** en las que preferirías usar `git revert` y no `git reset`.
3. ¿Por qué se dice que `git revert` es un “forward‑moving undo” y por qué esto es importante en un proyecto con más personas?

---

## 📦 Entrega esperada en el repositorio

Tu repositorio de la Actividad 6.9 debe incluir:

- `config.txt` (versión final).
- `cp1-historial-inicial-revert.txt`
- `cp2-revert-simple.txt`
- `cp3-revert-head.txt`
- `cp4-multiples-revert.txt`
- `cp5-conflicto-revert.txt`
- `reflexion-6-9.md`

---

## 🏁 Evaluación sugerida

- 4 pts – Uso correcto de `git revert` (commit concreto, HEAD, múltiples commits con `--no-commit`).
- 3 pts – Gestión adecuada de conflictos durante revert.
- 3 pts – Calidad de la reflexión en `reflexion-6-9.md` (comprensión profunda de revert vs reset y su uso en colaboración).

**Puntuación máxima: 10 puntos.**

---

## 🤖 Evaluación automática y redacción de informe

Esta actividad incluye una evaluación automática similar a la de la actividad anterior, adaptada a `git revert` (revert simple, `HEAD`, múltiples commits y conflictos):

- Workflow: `.github/workflows/evaluacion.yml`
- Script de evaluación: `tools/evaluar_repo.py`
- Script de redacción IA: `tools/ai_redactor.py`

### ¿Qué genera?

Al ejecutarse en GitHub Actions, se generan los siguientes archivos en `reportes/`:

- `informe.md` (resumen técnico de evaluación)
- `informe.json` (métricas en formato estructurado)
- `metricas.csv` (puntuaciones por criterio)
- `arbol.txt` (resumen del árbol del repositorio)

Si hay credenciales de IA configuradas en Secrets/Variables, también se generan:

- `retro.md` (retroalimentación redactada por IA)
- `retro.docx` (versión formal en Word)

### Parámetros del workflow (`workflow_dispatch`)

- `min_commits` (por defecto: `6`)
- `required_files` (por defecto: `config.txt,cp1-historial-inicial-revert.txt,cp2-revert-simple.txt,cp3-revert-head.txt,cp4-multiples-revert.txt,cp5-conflicto-revert.txt,reflexion-6-9.md`)

---
