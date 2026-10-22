<div align="center">

# Anthony Yariff Huice Martínez
### Full-Stack & Mobile Engineer | Next.js · React Native (Expo) · Node.js · PostgreSQL (Supabase) · AI

[![LinkedIn](https://img.shields.io/badge/LinkedIn-anthony--huice-0A66C2?style=flat&logo=linkedin)](https://www.linkedin.com/in/anthony-huice)
[![Email](https://img.shields.io/badge/Email-anthonyhuice92%40gmail.com-EA4335?style=flat&logo=gmail)](mailto:anthonyhuice92@gmail.com)
[![Modalidad](https://img.shields.io/badge/Modalidad-Remoto_Full--Time_%2F_Contractor-2E7D32?style=flat)](mailto:anthonyhuice92@gmail.com)
[![Ubicación](https://img.shields.io/badge/Ubicación-Anaco%2C_Venezuela-555?style=flat&logo=google-maps)](https://www.linkedin.com/in/anthony-huice)

<p align="center">
  Desarrollador Full-Stack y Móvil (+3 años) especializado en arquitectura y puesta en producción de sistemas empresariales (<b>CRM, ERP, WMS, Control de Accesos Biométrico y E-Commerce transaccional</b>). Enfoque estricto en aislamiento multi-tenant de baja latencia, consistencia transaccional en PostgreSQL, integración con hardware en red local y automatización operativa B2B con LLMs.
</p>

---

</div>

## 🛠️ Stack Tecnológico

| Dominio | Tecnologías & Arquitectura |
| :--- | :--- |
| **Frontend & Móvil** | `TypeScript (Strict)` · `Next.js (App Router, RSC, SSR/ISR)` · `React 19` · `React Native` · `Expo SDK` · `PWA` · `TanStack Query` · `Zustand` · `Tailwind CSS` · `Zod` |
| **Backend & Datos** | `Node.js` · `Express` · `PostgreSQL (Supabase)` · `Row-Level Security (RLS)` · `Funciones RPC Transaccionales` · `Triggers & Índices GIN` · `SQLite` · `REST APIs` · `WebSockets` |
| **IA, Hardware & Cloud** | `Google Gemini API` · `Meta WhatsApp Cloud API` · `Webhooks` · `Python (Multithreading & Hardware Bridge)` · `Vercel Serverless` · `Linux` · `Git / GitHub` |

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

### 🏋️ 2. TitanClub — Sistema de Control de Accesos Biométrico, Auditoría de Caja y Solvencia
Sistema en producción local (con arquitectura de expansión Cloud + Edge) que automatiza la comunicación con hardware de control de acceso biométrico/torniquete en red local (`LAN`), el control financiero por turnos y la solvencia de membresías.

* **Orquestación Multihilo y Hardware Bridge (`Python`):** Arquitectura concurrente con 4 demonios en segundo plano:
  1. *Monitor Keep-Alive:* Escucha continua del controlador biométrico en red local (`192.168.1.x`), capturando fichajes de socios y cómputo exacto de horas trabajadas de entrenadores.
  2. *Sincronizador de Bloqueo Físico:* Cola de tareas asíncrona que envía comandos HTTP directos al lector para bloquear o habilitar el paso físico en el torniquete según el estado de pago.
  3. *Guardián de Vencimientos y Cierre:* Verificación automática de membresías expiradas para bloqueo inmediato en hardware y cierre forzado diario de operaciones (9:00 PM).
  4. *Motor de Respaldo Transaccional:* Backups automáticos en caliente cada 2 horas mediante `sqlite3.backup()` previa validación de corrupción con `PRAGMA integrity_check` y rotación de snapshots.
* **Control de Caja por Turnos y Analítica Operativa:** Cuadre financiero segregado por jornada (mañana/tarde), auditoría de clientes solventes vs. vencidos, mapas de calor de afluencia horaria, exportación de reportes en Excel (`.xlsx`) y suite web espejo en **Next.js (App Router) + Supabase Realtime**.

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
* **Idiomas:** Español (Nativo) · Inglés (Lectura técnica)
