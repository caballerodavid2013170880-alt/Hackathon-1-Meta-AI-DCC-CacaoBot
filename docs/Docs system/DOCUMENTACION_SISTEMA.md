# 📚 DOCUMENTACIÓN TÉCNICA DEL SISTEMA
## Cacao Ancestral CDMX — Arquitectura, Componentes y Operación
**Hackathon 1 Meta AI — Módulo 1**

---

## 1. INTRODUCCIÓN Y VISIÓN GENERAL

Este documento describe la arquitectura técnica, diseño de componentes, flujos de datos y directrices operativas de la plataforma **Cacao Ancestral CDMX**, desarrollada en el marco del **Hackathon 1 de Meta AI**.

El proyecto une dos mundos tecnológicos complementarios:
1. **Un Frontend E-commerce de Alto Rendimiento:** Construido sin dependencias pesadas de frameworks, emulando la estética gourmet y minimalista de la chocolatería fina de aroma basada en la auditoría de *Aranda Chocolates*.
2. **Un Asistente de Operaciones Inteligente (CacaoBot):** Alimentado por un pipeline de inteligencia artificial entrenado con **PEFT / LoRA**, enriquecido con recuperación semántica **RAG** y redacción mediante **Meta Llama 3** a través de la infraestructura de **Groq**.

---

## 2. ARQUITECTURA DEL FRONTEND (SPA)

### 2.1. Gestión de Estado Global (`AppState`)
El archivo [`CacaoAncestral/public/js/app.js`](../CacaoAncestral/public/js/app.js) encapsula el estado reactivo de la aplicación en el objeto global `AppState`:

```javascript
const AppState = {
  activeSection: 'novedades',     // Sección visible actual
  products: [],                   // Catálogo de productos en memoria
  filteredProducts: [],           // Resultados filtrados
  selectedCacaoFilter: 'all',     // Filtro de pureza (% cacao)
  searchQuery: '',                // Término de búsqueda
  sortBy: 'featured',             // Criterio de ordenación
  currentPage: 1,                 // Página actual del catálogo
  itemsPerPage: 10,               // Paginación estricta de 10 por página
  cart: [],                       // Bolsa de compras temporal
  activeContactTab: 'colaboracion',// Pestaña de formulario activa
  gradioUrl: 'https://such-frays-overall.ngrok-free.dev' // Endpoint AI
};
```

### 2.2. Flujo de Navegación SPA
La función `switchSection(sectionId)` controla la visibilidad de los contenedores `<section>` identificados con la clase `.site-section`:
1. Retira la clase `.active` de todas las secciones.
2. Agrega `.active` a la sección seleccionada mediante transiciones suaves de opacidad (`fade-in`).
3. Actualiza las clases en los enlaces de la barra de navegación del Header y en el Footer.
4. Desplaza el viewport hacia la parte superior de la página de forma fluida (`window.scrollTo({ top: 0, behavior: 'smooth' })`).

### 2.3. Catálogo y Paginación Matemática
La función `renderCatalogGrid()` implementa una partición exacta de 10 productos:
```javascript
const startIndex = (AppState.currentPage - 1) * AppState.itemsPerPage;
const endIndex = Math.min(startIndex + AppState.itemsPerPage, totalItems);
const pageItems = AppState.filteredProducts.slice(startIndex, endIndex);
```
Los controles de navegación (`« Primera`, `‹ Anterior`, botones numéricos de página, `Siguiente ›`, `Última »`) se recalculan dinámicamente según la cantidad de resultados tras aplicar filtros por porcentaje de cacao o búsqueda por texto libre.

### 2.4. Modal de Ficha Técnica
Cada tarjeta de producto cuenta con el botón **"Ficha Técnica"**, el cual invoca `openProductModal(productId)` para renderizar una tabla técnica en una ventana modal emergente con los atributos clave de cada producto:
- Código SKU (ej. `CA-SOC-85`)
- Tipo de chocolate (ej. *Chocolate Oscuro de Origen*)
- Región de origen (ej. *Soconusco, Chiapas*)
- Gramaje neto (ej. *70 g*)
- Ingredientes completos y alérgenos
- Stock disponible en almacén

---

## 3. ARQUITECTURA DEL ASISTENTE CACAOBOT AI

### 3.1. Botón Flotante e Iluminación Reactiva
En la esquina inferior derecha se sitúa `#botTriggerBtn` con la imagen [`CacaoBot.webp`](../CacaoAncestral/public/img/CacaoBot.webp):
- Al hacer clic, se añade la clase `.illuminated` que activa un halo pulsante dorado (`box-shadow: 0 0 25px rgba(212, 163, 115, 0.95)`).
- Simultáneamente, el contenedor lateral deslizante `#chatDrawer` recibe la clase `.open`, desplazándose suavemente desde el borde derecho con una transición de 350 ms.

### 3.2. Integración de Red y Protocolo de Comunicación
La función `consultarAsistente(mensajeUsuario)` se conecta al servidor en Google Colab:

```javascript
async function consultarAsistente(mensajeUsuario) {
  // 1. Conexión nativa a Gradio 6+ mediante Server-Sent Events (SSE)
  // Endpoint: /gradio_api/call/ui_handler
  // Cabecera: ngrok-skip-browser-warning: true
  // 2. Fallback a endpoint clásico /api/predict (Gradio 3/4)
  // 3. Retorno estructurado: { respuesta, esEscalado, badge, contexto }
}
```

#### Esquema de Salida de Gradio:
El servidor en Colab retorna una tupla de 4 elementos:
1. `datos.data[0]` (**Badge HTML**): Etiqueta visual formateada con color temático (`🏷️ PEDIDO_VENTAS`, `🏷️ QUEJA_URGENTE`, `🏷️ BRAND_ASSETS`, `🏷️ OTRO`).
2. `datos.data[1]` (**Estado / Alerta Humano**): Notificación si el mensaje requiere escalado a soporte humano.
3. `datos.data[2]` (**Respuesta de Texto**): Texto generado por Meta Llama 3 con formato Markdown, notas de cata y respuesta institucional.
4. `datos.data[3]` (**Contexto RAG**): Fragmentos documentales recuperados de la base de conocimiento oficial.

### 3.3. Protocolo de Escalado Humano
Si `esEscalado` es verdadero (identificado automáticamente ante reclamaciones como producto derretido, paquetes dañados o solicitudes urgentes):
- El chat inserta un contenedor de advertencia prioritario:
  ```html
  <div class="escalado-alert-box">
    ⚠️ <strong>Escalado Prioritario Activado:</strong> Su caso fue transferido a atención humana especializada. Línea directa WhatsApp: +52 55 9876 5432.
  </div>
  ```

### 3.4. Motor RAG Local de Respaldo
Para prevenir fallos si el túnel de Ngrok o la sesión de Google Colab se desconectan:
- Si la llamada HTTP falla o supera el tiempo de espera (12 segundos), el sistema conmuta de forma transparente a `processCacaoBotMessage(text)`.
- Este motor local implementa un clasificador léxico de intenciones con respuestas predefinidas idénticas a las del notebook para cotizaciones, notas de cata de barras 85%, identidad visual de la marca y garantías de envío.

---

## 4. BASE DE DATOS Y MODELO DE DATOS

La persistencia de información de catálogo e institucional se basa en el archivo Excel [`cacao_ancestral_db.xlsx`](../CacaoAncestral/cacao_ancestral_db.xlsx):

| Hoja de Cálculo | Registros | Propósito en el Sistema |
| :--- | :---: | :--- |
| `Productos` | 16 | Alimenta el catálogo, carrusel y buscador. Incluye SKU, precio, gramaje y stock. |
| `Eventos_Noticias` | 3 | Alimenta las tarjetas de eventos y ferias en la sección Novedades. |
| `Empresa_Info` | 22 | Parámetros oficiales de marca: historia, misión, visión, showroom y políticas. |
| `Contacto_Colaboracion` | - | Estructura para registrar solicitudes de chefs y restaurantes aliados. |
| `Contacto_Cotizacion` | - | Estructura para registrar pedidos corporativos de mayoreo (500+ piezas). |
| `Contacto_QuejasSugerencias` | - | Estructura para registrar incidentes bajo la Garantía de Frescura 24h. |

---

## 5. DESPLIEGUE Y OPERACIÓN

### 5.1. Ejecución Local
Para pruebas en entorno local:
```powershell
cd "e:\Proyectos\Hackathon Meta IA 1\CacaoAncestral"
python -m http.server 8080 --directory public
```
El sitio estará disponible de forma inmediata en `http://localhost:8080`.

### 5.2. Despliegue en Firebase Hosting
El proyecto está preconfigurado con `firebase.json` y `.firebaserc`:
```bash
cd CacaoAncestral
firebase login
firebase deploy --only hosting
```
Las reglas de `firebase.json` garantizan reescrituras automáticas a `index.html` para todas las rutas y almacenamiento en caché eficiente para assets estáticos.
