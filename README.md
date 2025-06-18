# SafeNotes - DevSecOps Demo Project

## Descripción

SafeNotes es una aplicación demo desarrollada para el aprendizaje y la implementación de buenas prácticas de seguridad en el ciclo DevOps (DevSecOps).

El objetivo del proyecto es mostrar cómo integrar pruebas de seguridad de dependencias y de imagen Docker utilizando pipelines automatizados en GitHub Actions.

## Tecnologías utilizadas

* **Node.js**
* **Express.js**
* **Docker**
* **OWASP Dependency Check**
* **Trivy (Aqua Security)**
* **GitHub Actions**

## Estructura del repositorio

```
/
├── .github/workflows/security.yml   --> Pipeline de seguridad automatizada
├── app.js                            --> Aplicación Node.js principal
├── Dockerfile                        --> Imagen Docker de la aplicación
├── package.json / package-lock.json  --> Dependencias de Node.js
├── node_modules                      --> Módulos instalados
├── trivy-report/                     --> Reporte de vulnerabilidades (Trivy)
├── dependency-check-report.html      --> Reporte generado por OWASP Dependency-Check
```

## Instalación local (para pruebas manuales)

1. Clonar el repositorio:

```bash
git clone https://github.com/DiegoYanezGonzalez/safenotes.git
cd safenotes
```

2. Instalar dependencias:

```bash
npm install
```

3. Levantar la aplicación:

```bash
npm start
```

Por defecto queda disponible en `http://localhost:3000`.

## Construir la imagen Docker

```bash
docker build -t safenotes:latest .
```

## Ejecutar con Docker

```bash
docker run -p 3000:3000 safenotes:latest
```

## Pipeline de Seguridad (GitHub Actions)

Cada vez que se realiza un *push* o *pull request* sobre la rama `main`, se ejecuta automáticamente el pipeline de seguridad definido en `.github/workflows/security.yml`.

### Controles de seguridad incluidos:

1. **OWASP Dependency Check**: Analiza las dependencias de `package.json` para detectar vulnerabilidades conocidas en librerías de terceros.

2. **Trivy Docker Image Scan**: Escanea la imagen Docker generada del proyecto buscando vulnerabilidades de sistema y paquetes en el contenedor.

3. **Reportes automáticos**:

   * `dependency-check-report.html`
   * `trivy-report/`

Estos reportes pueden ser descargados desde los artefactos del job de GitHub Actions.

### Fragmento del workflow:

```yaml
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Install dependencies
        run: npm install

      - name: OWASP Dependency-Check
        uses: dependency-check/Dependency-Check_Action@main

      - name: Upload Dependency Check report
        uses: actions/upload-artifact@v3
        with:
          name: dependency-check-report
          path: ./dependency-check-report.html

      - name: Build Docker image
        run: docker build -t safenotes:latest .

      - name: Trivy Docker Image Scan
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: safenotes:latest
          format: table
          exit-code: 0

      - name: Upload Trivy Report
        uses: actions/upload-artifact@v3
        with:
          name: trivy-report
          path: trivy-report.json
```

## Objetivo del ejercicio

Este proyecto es utilizado como ejercicio en formaciones de DevSecOps para demostrar:

* Integración de seguridad temprana (Shift-Left)
* Automatización de controles en pipelines CI/CD
* Detección proactiva de vulnerabilidades

---

**Diego Yánez Gonzalez** - Proyecto de formación Devops
