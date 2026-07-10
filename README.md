# 🐾 NutriVet AI v3.0 - Edición Consola Observable y Segura

> **Asistente Clínico Autónomo para Asesoría Nutricional Veterinaria con Monitoreo de Métricas y Trazabilidad en Tiempo Real**

NutriVet AI v3.0 es una solución de software basada en Inteligencia Artificial diseñada para el ámbito clínico veterinario. Su objetivo principal es proveer recomendaciones nutricionales de precisión adaptadas a predisposiciones genéticas de razas de mascotas (perros y gatos). 

En esta versión 3.0, el sistema integra una arquitectura **RAG Híbrida**, controles de caída (*failover*) dinámicos, y cumple estrictamente con los lineamientos de la **Unidad 3 (Observabilidad, Trazabilidad y Seguridad)** mediante un entorno interactivo en consola.

---

## 🏗️ Arquitectura del Sistema
El sistema implementa un flujo de control determinista y secuencial que combina la potencia del modelo de lenguaje con repositorios de datos indexados locales y remotos.

### 🧠 Componentes Core
* **Cerebro (LLM):** `gpt-4o-mini` (vía GitHub Models API / Azure Inference).
* **Estrategia RAG Híbrida:** 1. *Capa Vectorial Principal:* `ChromaDB` cargada con las guías oficiales de la WSAVA (*Nutrition Assessment Guidelines*).
  2. *Capa Estructurada Local (Respaldo):* Módulo analítico basado en `datos_razas.json` que actúa automáticamente si ocurren pérdidas de conexión o excepciones en el embedding remoto.
* **Presentación:** Interfaz de consola dinámica con refresco de pantalla estructurado mediante `tabulate`.

### 🛡️ Capa de Observabilidad y Seguridad (Exigencias de la Evaluación)
* **Trazabilidad de Ejecución (Logs):** Registro persistente de transacciones, invocaciones y latencias en el archivo local `nutrivet_agente.log` utilizando la librería nativa `logging`.
* **Dashboard de Producción Integrado:** Cuadro de mando en vivo en la cabecera de la consola que expone KPIs críticos de rendimiento.
* **Políticas de Seguridad e Inyección:** Un *System Prompt* blindado que intercepta intentos de inyección y consultas ajenas al dominio veterinario (consultas de salud humana).

---

## 📊 Métricas de Observabilidad Implementadas

El sistema recopila de forma continua y automática los siguientes indicadores de desempeño en cada interacción:

| Métrica | Descripción | Mecanismo de Medición |
| :--- | :--- | :--- |
| **Total Consultas** | Volumen total de requerimientos recibidos por sesión. | Contador incremental síncrono. |
| **Consultas Exitosas** | Transacciones completadas con estado `200 OK` por la API. | Captura selectiva en bloque `try`. |
| **Errores Críticos** | Fallas de red, problemas de cuotas de API o excepciones de Chroma. | Captura de excepciones en bloque `except`. |
| **Filtros de Seguridad** | Cantidad de intentos de vulneración o desvío humano bloqueados. | Auditoría de respuestas mediante coincidencia semántica. |
| **Latencia Transaccional** | Tiempo exacto de procesamiento del LLM por inferencia. | Cálculo diferencial de delta de tiempo con librería `time`. |

---

## 🛡️ Protocolo de Uso Responsable y Ético

Para asegurar un despliegue seguro en producción, el asistente cuenta con mitigaciones integradas:
1. **Denegación de Consultas Humanas:** Si el usuario solicita diagnósticos médicos para personas o dosificación de fármacos humanos (ej. paracetamol), el agente frena la inferencia y entrega un mensaje estandarizado de rechazo.
2. **Mitigación de Prompt Injection:** Instrucciones explícitas de bajo nivel impiden que el usuario altere las directivas base, extraiga las reglas internas o fuerce al modelo a actuar como una IA distinta.
3. **Consistencia Veterinaria:** Configuración de la temperatura del LLM en `0.1` para erradicar alucinaciones y garantizar respuestas predecibles respaldadas por el protocolo WSAVA.

---

## 🛠️ Guía de Despliegue y Uso

### 1. Requisitos Previos
Asegúrate de contar con Python 3.10, 3.11 o 3.12 instalado y las credenciales correspondientes cargadas en tu archivo `.env`:
```env
OPENAI_BASE_URL=[https://models.inference.ai.azure.com](https://models.inference.ai.azure.com)
GITHUB_TOKEN=tu_token_aqui
