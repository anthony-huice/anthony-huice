<div align="center">

# Anthony Yariff Huice Martínez
### Full-Stack & Mobile Engineer | Next.js · React Native (Expo) · Node.js · Python · PostgreSQL (Supabase) · AI

[![LinkedIn](https://img.shields.io/badge/LinkedIn-anthony--huice-0A66C2?style=flat&logo=linkedin)](https://www.linkedin.com/in/anthony-huice)
[![Email](https://img.shields.io/badge/Email-anthonyhuice92%40gmail.com-EA4335?style=flat&logo=gmail)](mailto:anthonyhuice92@gmail.com)
[![Modalidad](https://img.shields.io/badge/Modalidad-Remoto_Full--Time_%2F_Contractor-2E7D32?style=flat)](mailto:anthonyhuice92@gmail.com)
[![Ubicación](https://img.shields.io/badge/Ubicación-Anaco%2C_Venezuela-555?style=flat&logo=google-maps)](https://www.linkedin.com/in/anthony-huice)

<p align="center">
  Desarrollador Full-Stack y Móvil (<b>+3 años de experiencia</b>) especializado en arquitectura y puesta en producción de sistemas empresariales de misión crítica (<b>CRM, ERP, WMS, Control de Accesos Biométrico y E-Commerce transaccional</b>). Enfoque estricto en aislamiento multi-tenant de baja latencia ($O(1)$ RLS), consistencia transaccional en PostgreSQL, integración con hardware en red local y automatización operativa B2B con LLMs.
</p>

---

</div>

## 📦 Repositorios Públicos Destacados (Portafolio de Ingeniería & Blueprints)

| Repositorio Público | Arquitectura & Dominio Técnico | Stack Clave |
| :--- | :--- | :--- |
| **[`autoparts-atomic-ecommerce`](https://github.com/anthony-huice/autoparts-atomic-ecommerce)** | **High-Concurrency Automotive Store:** Marketplace transaccional con 42 migraciones SQL, bloqueo pesimista de stock (`SELECT ... FOR UPDATE`), renderizado híbrido SSR/ISR y *Recruiter Sandbox Mode*. | `Next.js 16 (App Router)` · `React 19` · `TypeScript` · `TanStack Query` · `Supabase` |
| **[`whatsapp-gemini-conciliator`](https://github.com/anthony-huice/whatsapp-gemini-conciliator)** | **B2B Payment Conciliator & NOC Triage Engine:** Motor serverless de extracción estructurada con IA, idempotencia de webhooks (`wamid`) y *Zero-Downtime Heuristic Fallback* (+200 operaciones/mes). | `Node.js` · `TypeScript` · `Google Gemini 2.0 Flash` · `WhatsApp Cloud API` · `Zod` |
| **[`fieldops-isp-blueprint`](https://github.com/anthony-huice/fieldops-isp-blueprint)** | **Multi-Tenant FTTH & Serialized WMS Blueprint:** Kanban con candados de campo, cálculo geodésico Haversine (NAP a hogar), firma vectorial SVG, WMS de ONUs GPON y RLS en $O(1)$ sin JOINs. | `React Native` · `Expo SDK` · `TypeScript` · `PostgreSQL (O(1) RLS + GIN)` |
| **[`titan-architecture-case-study`](https://github.com/anthony-huice/titan-architecture-case-study)** | **Biometric Access Control & RTC Drift Engine:** Orquestación multihilo (4 demonios), bloqueo físico en torniquete (`/su`), compensación de reloj RTC (`epoch 2000`) y *Hot Backups* con `PRAGMA integrity_check`. | `Python (Multithreading)` · `SQLite` · `TCP/IP Hardware Bridge` · `Supabase Realtime` |

---

## 🛠️ Stack Tecnológico

| Dominio | Tecnologías & Arquitectura |
| :--- | :--- |
| **Frontend & Móvil** | `TypeScript (Strict)` · `Next.js (App Router, RSC, SSR/ISR)` · `React 19` · `React Native` · `Expo SDK` · `PWA` · `TanStack Query` · `Zustand` · `Tailwind CSS` · `Zod` |
| **Backend & Datos** | `Node.js` · `Express` · `PostgreSQL (Supabase)` · `Row-Level Security (RLS)` · `Funciones RPC Transaccionales` · `Triggers & Índices GIN` · `SQLite` · `REST APIs` · `WebSockets` |
| **IA, Hardware & Cloud** | `Google Gemini API` · `Meta WhatsApp Cloud API` · `Webhooks` · `Python (Multithreading & Hardware Bridge)` · `Vercel Serverless` · `Linux` · `Git / GitHub` |

---

## 🚀 Arquitectura de Sistemas en Producción

### 📡 1. Suite Operativa Multi-Tenant CRM, ERP & WMS para ISPs (Telecom FTTH)
Ecosistema multiplataforma (**Web, PWA y Android**) en producción activa para operadoras de fibra óptica (FTTH).
* **Arquitectura Multi-Tenant de Latencia $O(1)$:** Modelo jerárquico de 5 niveles con propagación denormalizada de tenant, superando las **100 políticas RLS sin JOINs**, índices GIN sobre columnas JSONB y funciones RPC con `SECURITY DEFINER` ([ver blueprint público `fieldops-isp-blueprint`](https://github.com/anthony-huice/fieldops-isp-blueprint)).
* **Bot Transaccional con IA (+200 conciliaciones y tickets/mes):** Webhook serverless en Node.js con extracción determinista en **Google Gemini 2.0 Flash** y fallback heurístico local en JS para **0% caída de servicio** ([ver motor público `whatsapp-gemini-conciliator`](https://github.com/anthony-huice/whatsapp-gemini-conciliator)).

```mermaid
flowchart TD
    subgraph Campo["Clientes & Cuadrillas"]
        WA["WhatsApp Clientes"] -->|Mensajes / Comprobantes| MetaAPI["Meta Cloud API"]
        App["App Móvil Expo / PWA"] -->|GPS + GeoFotos + WMS| SupaAPI["Supabase Client"]
    end

    subgraph Cloud["Capa Serverless & IA"]
        MetaAPI -->|Webhook HMAC| VercelFunc["Node.js Serverless (Vercel)"]
        VercelFunc -->|Extracción JSON / Fallback JS| Gemini["Google Gemini 2.0 Flash"]
        Gemini -->|Payload Validado| VercelFunc
        VercelFunc -->|RPC Security Definer| SupaDB[("PostgreSQL Multi-Tenant")]
        SupaAPI -->|Auth + RLS O(1) + Triggers| SupaDB
    end

    subgraph Realtime["Sincronización & Almacenamiento"]
        SupaDB -->|WebSockets| Kanban["Tableros Kanban Operativos"]
        SupaDB -->|Buckets Seguros| Storage["Supabase Storage (Evidencias)"]
    end
```

---

### 🏋️ 2. Sistema de Control de Accesos Biométrico, Auditoría de Caja y Solvencia (`TitanClub`)
Sistema en producción local que automatiza la comunicación con hardware biométrico/torniquete en red local (`LAN`), el control financiero por turnos (mañana/tarde) y la solvencia de membresías ([ver caso de estudio y motor `titan-architecture-case-study`](https://github.com/anthony-huice/titan-architecture-case-study)).

---

### 🚗 3. Plataforma E-Commerce Transaccional Automotriz (`AutoParts Pro`)
Marketplace transaccional con catálogo de alta concurrencia, 42 migraciones SQL y prevención de *overselling* mediante bloqueo pesimista de filas (`SELECT ... FOR UPDATE`) ([ver repositorio completo `autoparts-atomic-ecommerce`](https://github.com/anthony-huice/autoparts-atomic-ecommerce)).

---

## 🎓 Educación & Idiomas

* **Ingeniería en Informática** — *Instituto Universitario Adventista de Venezuela (IUNAV)* · **En curso**
* **T.S.U. en Informática** — *Instituto Universitario Adventista de Venezuela (IUNAV)* · **Culminado**
* **Idiomas:** Español (Nativo) · Inglés (Lectura técnica — Nivel A2/B1)
