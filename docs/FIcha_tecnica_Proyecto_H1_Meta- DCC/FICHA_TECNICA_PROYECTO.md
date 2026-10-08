# 📑 FICHA TÉCNICA OFICIAL DEL PROYECTO
## Cacao Ancestral CDMX — E-commerce & Asistente Ejecutivo CacaoBot AI
**Hackathon 1 Meta AI — Módulo 1: Pipeline Integral, LoRA, RAG y Plataforma Web**

---

### 1. INFORMACIÓN GENERAL Y METADATOS

| Campo | Valor / Especificación |
| :--- | :--- |
| **Nombre del Proyecto** | Cacao Ancestral CDMX — Plataforma Web E-commerce & Asistente Inteligente |
| **Identificador del Sistema** | `cacao-ancestral-cdmx-h1` |
| **Versión del Sistema** | 1.2.0 (Producción / Firebase Ready) |
| **Marco de Desarrollo** | Hackathon 1 Meta AI — Redacción, Fine-Tuning LoRA y Despliegue Web |
| **Entidad / Marca** | Cacao Ancestral® (Chocolatería fina de aroma con origen agroforestal) |
| **Ubicación Showroom** | Calle Colima 142, Col. Roma Norte, Cuauhtémoc, CDMX, C.P. 06700 |
| **Fecha de Publicación** | Octubre 2026 |
| **Licencia** | MIT License / Uso Comercial para Hackathon Meta AI |

---

### 2. ARQUITECTURA GENERAL DEL SISTEMA

El sistema se compone de una arquitectura híbrida desacoplada, orientada a alta disponibilidad, baja latencia y consumo eficiente de recursos:

```
┌────────────────────────────────────────────────────────────────────────┐
│                        CAPA DE PRESENTACIÓN (SPA)                      │
│   HTML5 Semántico + CSS3 Editorial (Aranda Chocolates) + JS Vanilla   │
│   ├── Novedades (Carrusel interactivo & Eventos)                       │
│   ├── Catálogo (10 prod/pág, Filtros Cacao %, Búsqueda & Modal)        │
│   ├── Nosotros (Historia Roma Norte, Misión, Visión & Showroom)        │
│   └── Contacto (Alianzas, Cotizaciones Corporativas & Reclamaciones)   │
└───────────────────▲────────────────────────────────────▲───────────────┘
                    │                                    │
                    │ HTTP REST / SSE                    │ Sincronización
                    │                                    │
┌───────────────────▼──────────────────┐   ┌─────────────▼───────────────┐
│       ASISTENTE CACAOBOT AI          │   │     BASE DE DATOS EXCEL     │
│  Botón Flotante con Halo Dorado      │   │  cacao_ancestral_db.xlsx    │
│  ├── Live: Ngrok + Gradio 6+ SSE     │   │  ├── Productos (16 SKUs)    │
│  └── Offline: Motor RAG Local JS     │   │  ├── Eventos y Noticias     │
└───────────────────▲──────────────────┘   │  ├── Ficha de Empresa       │
                    │                      │  └── Bandejas de Contacto   │
                    │ Webhook / Ngrok      └─────────────────────────────┘
┌───────────────────▼────────────────────────────────────────────────────┐
│                    PIPELINE DE IA (GOOGLE COLAB)                       │
│  1. Clasificador LoRA (DistilBERT Fine-Tuned): 91.7% Precisión         │
│  2. Módulo de Enrutamiento: PEDIDO_VENTAS, BRAND_ASSETS, QUEJA, OTRO   │
│  3. Motor RAG Semántico: Sentence-Transformers (Embeddings)            │
│  4. Generador LLM: Llama 3 vía Groq Cloud Ingestion                    │
│  5. Juez Evaluador: Validación de factualidad y tono institucional     │
│  6. Protocolo de Escalado Humano: Enlace directo a WhatsApp            │
└────────────────────────────────────────────────────────────────────────┘
```

---

### 3. ESPECIFICACIÓN DEL STACK TECNOLÓGICO

#### A. Frontend y Experiencia de Usuario
- **Lenguajes:** HTML5 semántico, CSS3 modular (con CSS Custom Properties / Variables de Diseño), JavaScript ES6+ nativo sin frameworks pesados (0 dependencias externas tipo React/Vue para carga ultra-rápida en menos de 1.2 segundos).
- **Sistema de Diseño:** Inspirado en la ficha técnica de auditoría UI/UX de *Aranda Chocolates*:
  - **Marrón Cacao Puro:** `#3B2219` (Textos de lectura, botones principales, fondos oscuros).
  - **Crema Vainilla:** `#FAEDCD` (Fondos principales, sensación cálida artesanal).
  - **Ocre Maíz:** `#D4A373` (Acentos dorados, bordes elegantes, halos reactivos).
  - **Verde Selva Húmeda:** `#283618` (Badges ecológicos, sellos orgánicos y sustentabilidad).
- **Tipografías:**
  - *Playfair Display* (Google Fonts, pesos 700 / Bold) para encabezados editoriales.
  - *Inter* y *Montserrat* (Google Fonts, pesos 400 / 600) para interfaces y cuerpo de texto.
- **Iconografía:** SVGs lineales vectoriales optimizados incrustados directamente (sin fuentes de íconos pesadas).

#### B. Backend de Inteligencia Artificial (Notebook Google Colab)
- **Entorno:** Python 3.10+ en entorno acelerado por GPU (NVIDIA T4 / A100).
- **Clasificador Supervisado:** `distilbert-base-uncased` adaptado mediante PEFT / LoRA (*Low-Rank Adaptation*):
  - Rango LoRA ($r$): 8
  - Alpha LoRA: 16
  - Módulos objetivo: `q_lin`, `v_lin`
  - Precisión inicial sin entrenar: **25.0%**
  - Precisión final fine-tuned: **91.7%**
- **Recuperación Semántica (RAG):** `sentence-transformers/all-MiniLM-L6-v2` con similitud coseno para recuperación de micro-fragmentos documentales.
- **Modelo Generativo:** Meta Llama 3 (70B / 8B) servido a través de la API ultrarrápida de **Groq Cloud**.
- **Servidor Interactivo:** Gradio versión `>=6.0.0` exponiendo endpoints nativos con Server-Sent Events (SSE).
- **Túnel de Conexión Pública:** `pyngrok` con dominio reservado `such-frays-overall.ngrok-free.dev`.

#### C. Capa de Datos y Persistencia
- **Base de Datos Maestra:** Hoja de cálculo OpenXML [`cacao_ancestral_db.xlsx`](./cacao_ancestral_db.xlsx) estructurada en 6 hojas temáticas:
  1. `Productos`: Catálogo comercial con 16 SKUs, % cacao, precios, origen y notas de cata.
  2. `Eventos_Noticias`: Calendario de festivales gastronómicos y talleres de cata.
  3. `Empresa_Info`: Parámetros de marca, historia, horarios y políticas de frescura.
  4. `Contacto_Colaboracion`: Bandeja de alianzas con chefs y reposteros.
  5. `Contacto_Cotizacion`: Solicitudes de mayoreo y eventos corporativos.
  6. `Contacto_QuejasSugerencias`: Casos de garantía de frescura y atención al cliente.
- **Almacenamiento Local del Cliente:** `localStorage` para persistencia en navegador de carritos de compra y formularios.

---

### 4. COMPONENTES Y FUNCIONALIDADES CLAVE

| Módulo | Descripción Funcional | Comportamiento |
| :--- | :--- | :--- |
| **Navegación SPA** | Conmutación instantánea entre 4 vistas. | No recarga la página; actualiza indicador visual en Header y hash de URL. |
| **Carrusel de Novedades** | Desfile de 4 productos estrella. | Autoplay cada 5 segundos, botones anterior/siguiente y puntos de salto directo. |
| **Catálogo Paginado** | Muestra exactamente 10 productos por vista. | Controles « Primera, ‹ Anterior, 1, 2, Siguiente ›, Última ». Filtro por % cacao. |
| **Ficha Técnica Modal** | Ventana emergente editorial por producto. | Muestra SKU, tipo, origen, gramaje, ingredientes, alérgenos y stock. |
| **Formularios Dinámicos** | 3 pestañas (Alianzas, Cotizaciones, Quejas). | Campos contextuales (ej. número de orden en quejas, piezas en mayoreo). |
| **Botón CacaoBot** | Mazorca oficial en esquina inferior derecha. | Halo resplandeciente dorado al clic (`#D4A373`) y despliegue del drawer. |
| **Chat Drawer** | Panel lateral deslizante con modelo AI. | Modo streaming SSE, badge oficial, acordeón RAG y escalado a WhatsApp. |
| **Fallback RAG Local** | Motor inteligente de respaldo en JavaScript. | Si Colab se apaga, responde preguntas frecuentes sin arrojar error al usuario. |

---

### 5. PARÁMETROS DE SEGURIDAD Y CONFIGURACIÓN

- **Protección de Credenciales:**
  - Archivos `.env*`, claves de servicio `*serviceAccountKey*.json` y llaves criptográficas bloqueadas por `.gitignore`.
  - Uso estricto de plantillas con sintaxis de referencia segura: `${SECRET_...}` en `.env.example` y `${FIREBASE_...}` en `config.example.js`.
  - En Google Colab, las claves de Groq y Ngrok se leen exclusivamente en memoria mediante `google.colab.userdata.get()` o `getpass()`.
- **Cabeceras de Red:**
  - `ngrok-skip-browser-warning: "true"` para evitar intercepciones HTML de Ngrok.
  - Políticas de caché optimizadas en `firebase.json` (7 días para imágenes, 1 día para CSS/JS).

---

### 6. REQUISITOS DE ENTORNO Y EJECUCIÓN

- **Para el Sitio Web:**
  - Cualquier navegador moderno con soporte para ES6+ (Chrome 90+, Edge 90+, Firefox 88+, Safari 14+).
  - Servidor HTTP estático básico (Python `http.server`, Node.js `serve`, Nginx o Apache).
  - Conexión a internet para tipografías de Google Fonts y llamadas al túnel de Ngrok.
- **Para el Pipeline de Colab:**
  - Cuenta de Google Colab (GPU estándar T4 suficiente).
  - Cuenta gratuita de Groq Cloud (API Key para Llama 3).
  - Cuenta de Ngrok con Authtoken y dominio asignado.
