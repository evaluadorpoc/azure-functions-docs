
# Guía Paso a Paso: Despliegue Manual de Azure Function con GitHub Actions

## 🧱 Paso 1: Preparación del entorno

1. Abrir VS Code en una carpeta vacía
2. Abrir terminal integrada (`Ctrl + ñ`)
3. Confirmar ruta válida y vacía

```bash
PS C:\...\azurefunction>
```

## 🧰 Paso 2: Inicializar Git y conectar con GitHub

```bash
git init
git remote add origin https://github.com/<usuario>/<repositorio>.git
git remote -v
```

## 🧪 Paso 3: Crear estructura manual de Azure Function

```bash
mkdir HttpTrigger
mkdir -p .github/workflows
```

### `HttpTrigger/__init__.py`

```python
import azure.functions as func
import logging

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info("✅ Webhook recibido")
    return func.HttpResponse("Hello from manual Azure Function!", status_code=200)
```

### `HttpTrigger/function.json`

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

## ⚙️ Paso 4: Archivos base del proyecto

### `host.json`

```json
{
  "version": "2.0"
}
```

### `requirements.txt`

```
azure-functions
```

### `.funcignore`

```
.git/
.github/
.vscode/
```

## 💾 Paso 5: Commit inicial

```bash
git add .
git commit -m "🔧 PoC3 - Azure Function manual inicial"
git push -u origin master
```

## 🧠 Paso 6: Crear workflow `.github/workflows/azure-function.yml`

```yaml
name: 🚀 Deploy Azure Function

on:
  push:
    branches:
      - master

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repo
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.10'

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Zip deployment package
        run: zip -r function.zip .

      - name: Deploy to Azure Function App
        uses: azure/functions-action@v1
        with:
          app-name: <NOMBRE_FUNCTION_APP>
          package: ./function.zip
          publish-profile: ${{ secrets.AZURE_PUBLISH_PROFILE }}
```

## 🔒 Paso 7: Crear el secreto en GitHub

1. Ir a GitHub > Settings > Secrets > Actions
2. Crear `AZURE_PUBLISH_PROFILE` con contenido del archivo `.PublishSettings` descargado de Azure

## 🔁 Paso 8: Push del workflow

```bash
git add .github/workflows/azure-function.yml
git commit -m "⚙️ Agrega workflow de despliegue automático con GitHub Actions"
git push
```

## ✅ Paso 9: Verificar despliegue y probar función

- Ir a [https://<app>.azurewebsites.net/api/HttpTrigger](https://<app>.azurewebsites.net/api/HttpTrigger)
- Confirmar mensaje: `"Hello from manual Azure Function!"`

---

## ❓ ¿Se puede usar otro nombre que "HttpTrigger"?

¡Sí! El nombre de la carpeta es el nombre del endpoint. Puedes usar cualquier nombre como:

```bash
mkdir AnalizadorHU
```

Y luego acceder con:

```
https://<app>.azurewebsites.net/api/AnalizadorHU
```

Solo asegúrate que:

- El nombre de carpeta es único
- Contiene `__init__.py` y `function.json`
- El `function.json` está bien formado

---

Guía generada automáticamente desde una PoC real exitosa 💡
