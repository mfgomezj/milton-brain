# FINANCIAL_SPECS.md — Arquitectura de Finanzas Local-First y Clasificación DIAN (V20)

Este documento contiene la especificación de diseño técnico y las reglas de negocio de la contabilidad **local-first** del ecosistema de Milton Gómez. Frankie y cualquier otro agente deben seguir estrictamente estas especificaciones para procesar, clasificar y registrar egresos.

---

## 1. Arquitectura Local-First con Espejo en la Nube

Nuestra filosofía contable prioriza la soberanía y resiliencia de los datos. La base de datos es local y privada, utilizando la nube (Notion) únicamente como un panel visual asíncrono.

```
+---------------------------------------------------------------------------------------+
| 1. ENTRADA (Telegram / Milton Brain)                                                  |
|    - Dictado de voz o texto por Milton.                                               |
|    - Frankie pre-procesa (corrección fonética, categoría inicial).                    |
+---------------------------------------------------------------------------------------+
                                        │
                                        ▼
+---------------------------------------------------------------------------------------+
| 2. VERIFICACIÓN (HITL Nivel 2 - Gatillo de un Toque)                                  |
|    - Mensaje corto confirmando monto, medio de pago, deducibilidad DIAN e imagen.     |
|    - Aprobación expresa con un simple: 👍 / OK / Si.                                  |
+---------------------------------------------------------------------------------------+
                                        │
                                        ▼
+---------------------------------------------------------------------------------------+
| 3. ORQUESTACIÓN Y PROCESAMIENTO (n8n Droplet Docker)                                  |
|    - Compresión de imagen a .webp (Calidad balanceada, peso máximo: 120 KB).          |
|    - Nomenclatura del archivo: FIN-[ID_EGRESO].webp (ej: FIN-0104.webp).              |
+---------------------------------------------------------------------------------------+
                                        │
                                        ▼
+---------------------------------------------------------------------------------------+
| 4. ALMACENAMIENTO LOCAL Y CDN (SSD Droplet / Excel SSD)                               |
|    - Base de Datos Física: /home/node/.n8n/data/Frankie_Master_Ledger.xlsx            |
|    - Almacenamiento CDN Nginx Alpine: /root/jobnearme-stack/n8n_data/soportes/egresos/|
|    - Acceso web seguro: media.jobnearme.online/soportes/egresos/FIN-[ID].webp         |
+---------------------------------------------------------------------------------------+
                                        │
                                        ▼
+---------------------------------------------------------------------------------------+
| 5. FRONT-END INTERFACE (Notion UI & Cloudflare)                                       |
|    - Notion API actualiza en segundo plano el Premium Dashboard.                      |
|    - Cloudflare Edge aplica "Cache Everything" para ahorrar 95% de ancho de banda.     |
+---------------------------------------------------------------------------------------+
```

---

## 2. Jerarquía de Almacenamiento de Soportes

Para mantener el orden de Notion-Mirror 1:1, los archivos de soporte físico se organizan estrictamente en el Droplet bajo `/root/jobnearme-stack/n8n_data/soportes/` con la siguiente estructura:

*   **`/soportes/egresos/`**: Contiene exclusivamente los tickets y facturas de gastos nombrados en formato `FIN-[ID_EGRESO].webp`.
*   **`/soportes/clientes/`**: Contiene la evidencia técnica de entrega o mantenimiento de máquinas con nomenclatura `[ID_MAQUINA]_[FECHA].webp`.
*   **`/soportes/legacy-repos/`**: Carpeta dedicada a resguardar archivos históricos no estandarizados de las fases anteriores del proyecto.

---

## 3. Reglas de Clasificación DIAN (Art. 107 Estatuto Tributario Colombiano)

Cualquier egreso procesado debe ser categorizado binariamente para optimizar la declaración de renta de Milton. La clasificación se basa en la **relación de causalidad, necesidad y proporcionalidad** con la actividad comercial productora de renta lícita:

### A. Gastos Deducibles de Renta (Costo Operativo)
Son aquellos gastos indispensables para que los negocios (Funnels Foundry o Confección Loys Jean's) operen y generen ingresos.
- **Tecnología e Infraestructura**: Servidores (DigitalOcean, Vercel), APIs (OpenAI, Anthropic), herramientas de desarrollo, hosting, dominios.
- **Marketing y Adquisición**: Publicidad en Google Ads, Meta Ads, software de SEO, automatizaciones.
- **Insumos de Confección (Loys Jean's)**: Telas, hilos, botones, mantenimiento mecánico de máquinas de coser, repuestos técnicos, agujas.
- **Mano de Obra y Servicios Directos**: Honorarios de operarios del taller, freelancers técnicos.

### B. Gastos Personales (No Deducibles)
Son egresos de la esfera privada de Milton que no influyen en la producción de renta de las empresas.
- **Alimentación y Entretenimiento**: Restaurantes personales, compras de supermercado para consumo personal, salidas de ocio.
- **Cuidado Personal y Salud**: Suscripciones médicas, gimnasio, vestuario no operativo.
- **Eje Espiritual y Donaciones**: Diezmos, aportes a la iglesia, donaciones de caridad (no certificadas bajo Art. 125 E.T.).
- **Transporte Personal**: Gastos de combustible o mantenimiento del vehículo de uso estrictamente doméstico.

---

## 4. Algoritmo Cognitivo para Frankie al Clasificar Egresos

Cuando Milton dicte un egreso, Frankie aplicará el siguiente algoritmo cognitivo antes de solicitar confirmación:

```
[Inicio: Egresos Dictado]
         │
         ▼
[Corrección Fonética V20]
(ej: "Louis Jeans" -> "Loys Jean's")
         │
         ▼
[Evaluación Causalidad Art. 107 E.T.]
¿El gasto es necesario para generar ingresos en Funnels Foundry o Loys Jean's?
         ├── SÍ ──> Clasificación: DEDUCIBLE DE RENTA
         └── NO ──> Clasificación: GASTO PERSONAL (NO DEDUCIBLE)
         │
         ▼
[Identificación de Medio de Pago y Caja]
(Deducir de la cuenta mencionada: Nequi, Caja, Bancolombia, etc.)
         │
         ▼
[HITL Nivel 2 - Confirmación de un Toque]
Formular mensaje resumen estructurado y solicitar ticket.
```

### Ejemplo de interacción ideal:
> **Milton**: *Gasto de $120.000 COP en hilos para Loys Jeans pagado con Nequi.*
>
> **Frankie**: 
> ```
> 📝 Registro de Egreso Propuesto:
> - Detalle: Hilos e insumos de confección (Loys Jean's)
> - Monto: $120.000 COP (Medio: Nequi)
> - Tipo DIAN: Deducible de Renta (Causalidad Operativa)
> 
> ¿Confirmás para proceder? Adjuntame el ticket para comprimirlo en milisegundos.
> ```
> **Milton**: 👍 *(Envía imagen del ticket)*

---
*Última actualización: 2026-05-22 — Autor: Frankie Arquitecto (Antigravity)*
