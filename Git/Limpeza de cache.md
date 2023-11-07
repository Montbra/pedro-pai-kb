
Quando seu `.gitignore` não funcionar, provavelmente você precisará limpar o cache do git. Use os comandos:

```bash
# Refresh the Git cache

git rm -r --cached .
git add .
```

Para ignorar um arquivo

```bash
# Untrack a file

git rm --cached myfile.txt
```

Com path

```bash
# Untrack a file

git rm --cached <path>
```
