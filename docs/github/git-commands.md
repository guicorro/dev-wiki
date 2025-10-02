# Comandos de Git

## 12 comandos esenciales de Git

### git init

Inicializa un nuevo repositorio Git en el directorio actual.

```bash
mkdir railsapp
cd railsapp
git init
```

### git add

Prepara los cambios en el directorio actual y subdirectorios.

```bash
echo "# RAILS APP" > README.md
echo "Demo" >> README.md
git add README.md
```

### git commit

Guarda los cambios preparados con un mensaje.

```bash
git add app.css
git commit -m "Initial commit"
```

### git push

Envía los cambios locales al repositorio remoto.

```bash
git add .
git commit -m "Update files"
git push origin main
```

### git pull

Obtiene los cambios de un repositorio remoto y los fusiona en el local.

```bash
git status
git pull origin main
git log --oneline
```

### git remote

Agrega, visualiza y renombra repositorios remotos.

```bash
git remote add origin <url>
git remote -v
git remote rename origin upstr
```

### git branch

Lista todas las ramas, crea una nueva y confirma su creación.

```bash
git branch
git branch feature-login
git branch
```

### git fetch

Obtiene los últimos datos de un repositorio remoto, pero no los integra en el área de trabajo.

```bash
git fetch origin
```

### git checkout

Cambia a la rama especificada.

```bash
git branch
git checkout feature-login
git branch
```

### git merge

Fusiona la rama especificada en la rama actual (en este caso, main).

```bash
git checkout main
git merge feature-login
git log --oneline
```

### git status

Muestra el estado del repositorio, incluyendo cambios no confirmados.

```bash
git status
echo "Data Added" >> hello.txt
git status
```

### git reset

Restaura la rama actual al commit especificado.

```bash
git log --oneline
git reset --hard <commit-hash>
```

---

Fuente: [sysxplore.com](https://sysxplore.com)
