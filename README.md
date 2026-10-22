<div align="center">

# Anthony Yariff Huice Martínez
### Full-Stack & Mobile Engineer | Next.js · React Native (Expo) · Node.js · PostgreSQL (Supabase) · AI

[![LinkedIn](https://img.shields.io/badge/LinkedIn-anthony--huice-0A66C2?style=flat&logo=linkedin)](https://www.linkedin.com/in/anthony-huice)
[![Email](https://img.shields.io/badge/Email-anthonyhuice92%40gmail.com-EA4335?style=flat&logo=gmail)](mailto:anthonyhuice92@gmail.com)
[![Modalidad](https://img.shields.io/badge/Modalidad-Remoto_Full--Time_%2F_Contractor-2E7D32?style=flat)](mailto:anthonyhuice92@gmail.com)
[![Ubicación](https://img.shields.io/badge/Ubicación-Anaco%2C_Venezuela-555?style=flat&logo=google-maps)](https://www.linkedin.com/in/anthony-huice)

<p align="center">
  Desarrollador Full-Stack y Móvil (+3 años) especializado en el diseño arquitectónico y puesta en producción de sistemas empresariales de misión crítica (<b>CRM, ERP, WMS, Control de Accesos IoT y E-Commerce transaccional</b>). Enfoque estricto en aislamiento multi-tenant de baja latencia, consistencia transaccional en PostgreSQL y automatización operativa B2B con LLMs.
</p>

---

</div>

## 🛠️ Stack Tecnológico

| Dominio | Tecnologías & Arquitectura |
| :--- | :--- |
| **Frontend & Móvil** | `TypeScript (Strict)` · `Next.js (App Router, RSC, SSR/ISR)` · `React 19` · `React Native` · `Expo SDK` · `PWA` · `TanStack Query` · `Zustand` · `Tailwind CSS` · `Zod` |
| **Backend & Datos** | `Node.js` · `Express` · `PostgreSQL (Supabase)` · `Row-Level Security (RLS)` · `Funciones RPC Transaccionales` · `Triggers & Índices GIN` · `REST APIs` · `WebSockets (Realtime)` |
| **IA, IoT & Cloud** | `Google Gemini API` · `Meta WhatsApp Cloud API` · `Webhooks` · `Python (Edge/Hardware Bridge)` · `Vercel Serverless` · `Linux` · `Git / GitHub` |

---

## 🚀 Sistemas en Producción & Arquitectura de Ingeniería

> *Nota: Por acuerdos de confidencialidad y protección de propiedad intelectual comercial, los repositorios de producción se mantienen privados. A continuación se documenta la arquitectura técnica implementada en cada sistema.*

### 📡 1. Metricall — Suite Operativa Multi-Tenant CRM, ERP & WMS para ISPs (Telecom FTTH)
Ecosistema multiplataforma (**Web, PWA y Android**) en producción para operadoras de fibra óptica (FTTH). Reemplaza la gestión fragmentada en hojas de cálculo por flujos estructurados de campo, trazabilidad de inventario y atención automatizada.

* **Arquitectura Multi-Tenant de Latencia $O(1)$:** Modelo jerárquico de 5 niveles (*Empresas → Sucursales → Tableros → Listas JSONB → Tarjetas JSONB*) con propagación denormalizada de `empresa_id`, superando las **100 políticas RLS sin JOINs**, índices GIN sobre columnas JSONB y funciones RPC con `SECURITY DEFINER`.
* **Bot Transaccional con IA (WhatsApp Cloud API + Gemini 2.0 Flash):** Webhook serverless en Node.js con extracción determinista (temperatura `0.1–0.2`) y fallback heurístico local en JS (0% downtime ante caída de API externa). Automatiza:
  1. **Captura de Prospectos:** Extracción estructurada de datos de contratación y enrutamiento directo al tablero de *Factibilidad*.
  2. **Conciliación de Cobranza en 2 Pasos:** Validación interactiva de datos bancarios (Pago Móvil/Transferencia), descarga de comprobantes desde Meta hacia Supabase Storage y generación de tickets auditables.
  3. **Triaje NOC de Soporte Técnico:** Clasificación automatizada de 5 categorías de fallas ópticas/red y auditoría en vivo mediante `webhook_logs`.
* **WMS y Operaciones de Campo:** Trazabilidad de equipos serializados (ONUs/Routers) a través de 6 estados de custodia, validación geográfica obligatoria (coordenadas GPS + evidencia fotográfica de caja NAP/residencia) y firma digital manuscrita en dispositivo móvil.

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

### 🏋️ 2. TitanClub — Sistema Híbrido Cloud + Edge de Control de Accesos Biométrico y ERP
Migración y modernización de un sistema legacy local hacia una arquitectura desacoplada **Cloud + Edge** para control de acceso físico y analítica operativa en tiempo real.

* **Daemon Local Hardware-to-Cloud (Python Edge Bridge):** Servicio local en Python que integra el hardware de control de acceso / torniquete en red local (`TCP/IP` / Raspberry Pi) con **Supabase PostgreSQL**, sincronizando aperturas y bloqueos en tiempo real.
* **Dashboard Analítico en Tiempo Real (Next.js App Router):** Interfaz administrativa con **Supabase Realtime (WebSockets)**, mapas de calor de afluencia horaria con **Recharts**, control de solvencia de membresías, cómputo automatizado de horas de personal y exportación de auditorías en Excel (`.xlsx`).
* **Ingeniería de Migración de Datos:** Scripts automatizados de extracción, transformación y carga (ETL) desde bases de datos locales **SQLite** hacia **PostgreSQL** en la nube sin pérdida de historial operativo.

---

### 🚗 3. Plataforma E-Commerce Transaccional Automotriz (Vehículos y Repuestos)
Marketplace transaccional con catálogo de alta concurrencia y arquitectura de renderizado híbrido.

* **Control de Concurrencia y Stock:** Prevención de condiciones de carrera (*race conditions*) y sobre-venta (*overselling*) mediante transacciones atómicas y bloqueo pesimista de filas en PostgreSQL (`SELECT FOR UPDATE`) expuestas vía RPCs.
* **Rendimiento y Estado Desacoplado:** Catálogo público acelerado con **ISR (Incremental Static Regeneration)** y **React Server Components (RSC)**; autenticación en servidor con `@supabase/ssr` (cero parpadeo FOUC), validación de esquemas con **Zod** y separación estricta entre caché asíncrono (**TanStack Query v5**) y estado de interfaz (**Zustand v5**).

---

### 🛒 4. Mercatodo 8000 — Sistema Web de Gestión de Pedidos y Facturación Digital
* Digitalización del flujo de toma de pedidos comerciales y emisión automatizada de formatos digitales de recibos y facturación mediante arquitectura **Node.js, Express y API REST**, desplegada en entornos **Linux** de alta disponibilidad.

---

## 🎓 Educación & Idiomas

* **Ingeniería en Informática** — *Instituto Universitario Adventista de Venezuela (IUNAV)* · **En curso**
* **T.S.U. en Informática** — *Instituto Universitario Adventista de Venezuela (IUNAV)* · **Culminado**
* **Idiomas:** Español (Nativo) · Inglés (Lectura técnica de documentación y APIs)
