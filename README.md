# Hackathon Meta IA 1 — Cacao Ancestral CDMX

Repositorio completo del proyecto desarrollado para el **Hackathon 1 de Meta AI**, que integra una solución integral de inteligencia artificial con un sitio web responsivo e-commerce para la marca de chocolate artesanal **"Cacao Ancestral CDMX"**.

---

## 📂 Organización del Repositorio

El proyecto se estructura en dos módulos principales e independientes:

```text
Hackathon Meta IA 1/
├── CacaoAncestral/                  # Aplicación Web E-commerce & Landing Page
│   ├── public/                      # Archivos públicos para Firebase Hosting
│   │   ├── index.html               # Estructura SPA con widget CacaoBot embebido
│   │   ├── css/styles.css           # Estilos editoriales Aranda Chocolates
│   │   ├── js/app.js                # Lógica SPA, catálogo y conexión con el modelo
│   │   ├── img/                     # CacaoBot.webp e ilustraciones de productos
│   │   └── cacao_ancestral_db.xlsx  # Hoja de cálculo descargable
│   ├── cacao_ancestral_db.xlsx      # Base de datos maestra en Excel
│   ├── firebase.json                # Configuración de Firebase Hosting
│   ├── .env.example                 # Plantilla de variables de entorno seguras
│   ├── .gitignore                   # Reglas de protección de secretos
│   └── README.md                    # Documentación específica del sitio web
│
├── H1 Meta IA Proyecto DCC/         # Pipeline de IA & Asistente de Operaciones
│   ├── H1_DCC_...ipynb              # Notebook de Colab (LoRA, RAG y Gradio)
│   ├── CacaoBot.webp                # Gráfico oficial del asistente
│   └── WIDGET CACAO ANCESTRAL AI.html # Lógica de conexión Ngrok / Gradio
│
├── .gitignore                       # Reglas de exclusión para todo el repositorio
└── README.md                        # Documentación general del repositorio
```

---

## 🍫 Módulos del Proyecto

### 1. [`/CacaoAncestral`](./CacaoAncestral) — Plataforma Web E-commerce
Sitio web interactivo desarrollado con HTML5 semántico, CSS3 modular y JavaScript ES6+ vanilla, optimizado para despliegue en **Firebase Hosting**:
- **Navegación SPA sin Recargas**: 4 secciones principales (Novedades con carrusel dinámico, Catálogo con 10 productos por página y filtros por intensidad de cacao, Nosotros con historia y showroom, y Contacto con formularios especializados para alianzas, cotizaciones y quejas).
- **Asistente Inteligente CacaoBot**: Botón flotante persistente en la esquina inferior derecha con iluminación dorada reactiva al clic (`#D4A373`) y panel lateral deslizante (*drawer*) conectado a Google Colab vía Ngrok.
- **Base de Datos Excel**: Cada producto enlaza con la hoja [`cacao_ancestral_db.xlsx`](./CacaoAncestral/cacao_ancestral_db.xlsx), desplegando fichas técnicas con SKU, notas de cata, alérgenos, ingredientes y existencias en almacén.
- **Identidad Visual**: Basada en la auditoría UI/UX de Aranda Chocolates con paleta `#3B2219`, `#FAEDCD`, `#D4A373` y `#283618`.

### 2. [`/H1 Meta IA Proyecto DCC`](./H1%20Meta%20IA%20Proyecto%20DCC) — Asistente de Operaciones y BrandCore
Pipeline completo de inteligencia artificial entrenado para clasificar consultas, consultar bases de conocimiento con RAG y generar respuestas institucionales:
- **Notebook de Google Colab**: `H1_DCC_Cacao_Ancestral_CDMX_—_Asistente_de_Operaciones.ipynb` con fine-tuning LoRA sobre DistilBERT, recuperación semántica con Sentence Transformers, redacción con Llama en Groq y evaluación con Juez LLM.
- **Despliegue con Gradio y Ngrok**: Servidor web interactivo expuesto en el túnel permanente `such-frays-overall.ngrok-free.dev`.
- **Canal de Escalado Humano**: Detección automática de quejas e incidentes con enrutamiento prioritario a WhatsApp.

---

## 🚀 Guía de Inicio Rápido

### Ejecución Local del Sitio Web
```powershell
# 1. Navegar a la carpeta del sitio web
cd CacaoAncestral

# 2. Levantar el servidor web local con Python
python -m http.server 8080 --directory public

# 3. Abrir en el navegador:
# http://localhost:8080
```

### Despliegue a Firebase Hosting
```bash
cd CacaoAncestral
firebase login
firebase deploy --only hosting
```

---

## 🛡️ Seguridad y Buenas Prácticas

- **Cero Claves Expuestas**: El proyecto utiliza variables de entorno parametrizadas con la sintaxis `${SECRET_...}` en `.env.example` y `config.example.js`.
- **Protección con `.gitignore`**: Los archivos `.env*`, credenciales de servicio `*serviceAccountKey*.json`, llaves privadas, registros de depuración y hojas de cálculo con datos reales de clientes están estrictamente excluidos de Git.
- **Acceso a APIs en Colab**: Las claves de Groq y Ngrok se leen en tiempo de ejecución mediante `google.colab.userdata` o `getpass`, sin almacenarse en texto plano en el notebook.
