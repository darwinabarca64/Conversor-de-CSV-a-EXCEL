# Guía de Inicio Rápido - Conversor de CSV a Excel

Esta guía te guiará paso a paso para configurar y ejecutar localmente el proyecto de conversión de CSV a XLSX, además de preparar el control de versiones con Git.

---

## 1. Análisis del Proyecto y Dependencias

Al revisar el archivo [package.json](file:///d:/programs/cuidar_el_alma/csv2xlsx-main/package.json), notarás que **no hay dependencias de producción o de desarrollo preconfiguradas**. Esto se debe a que la librería está diseñada como un módulo ES6 puro (`ES Modules`) que se ejecuta directamente en el navegador de manera nativa (ver [src/Csv2Xlsx.js](file:///d:/programs/cuidar_el_alma/csv2xlsx-main/src/Csv2Xlsx.js)).

### El Desafío del CORS en Navegadores
Debido a que se importan archivos Javascript localmente usando `import` (como se ve en [demo.html](file:///d:/programs/cuidar_el_alma/csv2xlsx-main/_demo/demo.html#L33)), si intentas abrir el archivo `demo.html` haciendo doble clic en él (`file:///`), el navegador lo bloqueará por políticas de seguridad (CORS).

Por lo tanto, **necesitas un servidor web local sencillo** para servir los archivos a través de `http://localhost`.

### Instalación de Herramientas Recomendadas
Te recomendamos utilizar la herramienta ligera de Node.js llamada `http-server`. No es necesario instalarla de forma global; puedes ejecutarla directamente usando `npx`.

---

## 2. Instrucciones de Arranque y Ejecución

Sigue estos pasos en tu terminal (PowerShell o CMD en Windows):

### Paso 1: Abrir la terminal en la raíz del proyecto
Asegúrate de estar en la carpeta raíz del proyecto:
`d:\programs\cuidar_el_alma\csv2xlsx-main`

### Paso 2: Arrancar el Servidor Local
Para levantar el servidor web en la raíz del proyecto, ejecuta el siguiente comando:

```bash
npx http-server -p 8080
```

> **¿Dónde ejecutarlo?**
> Este comando se debe ejecutar en la **raíz del proyecto** (`d:\programs\cuidar_el_alma\csv2xlsx-main`).

### Paso 3: Probar la Demo en el Navegador
Una vez que el comando esté corriendo, abre tu navegador favorito y accede a la siguiente dirección para probar la demo interactiva:

👉 **[http://localhost:8080/_demo/demo.html](http://localhost:8080/_demo/demo.html)**

### Novedades en esta versión
- **Soporte para Objetos `File` nativos:** Ahora el método `Csv2Xlsx.convertCsv` acepta archivos directamente del navegador (del input file o eventos de soltado drag-and-drop), además de URLs remotas.
- **Nueva interfaz de usuario Microsoft Excel 365:** La página de demostración cuenta con una zona de arrastrar y soltar interactiva, panel de configuración de separador/totales y spinner de carga, diseñada bajo los lineamientos visuales de Microsoft Office.

---

## 3. Pasos para Inicializar Git (git init)

Si deseas añadir este proyecto a un repositorio Git propio, sigue estos pasos desde la **raíz del proyecto**:

### Paso 1: Inicializar el repositorio
```bash
git init
```

### Paso 2: Verificar el archivo .gitignore
El proyecto ya cuenta con un archivo [.gitignore](file:///d:/programs/cuidar_el_alma/csv2xlsx-main/.gitignore). Puedes verificar o editar qué archivos excluir antes de realizar tu primer commit.

### Paso 3: Agregar todos los archivos al área de preparación (staging)
```bash
git add .
```

### Paso 4: Realizar el primer commit
```bash
git commit -m "Initial commit: Importación de csv2xlsx"
```

### Paso 5: (Opcional) Conectar a tu repositorio remoto (GitHub/GitLab/etc)
Si creas un repositorio vacío en GitHub, puedes vincularlo y subir el código con:
```bash
git branch -M main
git remote add origin TU_URL_DEL_REPOSITORIO_DE_GITHUB.git
git push -u origin main
```
