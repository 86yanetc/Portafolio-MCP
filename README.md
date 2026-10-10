# 🤖 Enterprise BI Agent <img src="./imagenes/mcp_agent_logo.jpg" width="100" align="right">

**Ecosistema de Inteligencia Artificial Local (Qwen2.5 7B) conectado en el borde a sistemas de datos corporativos y navegación híbrida asíncrona.**

<div align="right">
  <em>"Más allá de las respuestas rígidas: Un cerebro agéntico local diseñado con la capacidad autónoma de seleccionar sus propias herramientas, auditar el inventario y navegar la web en el momento exacto en que el negocio lo requiere."</em>
</div>


**Desarrollado por: Msc. Yanet Cesaire Velazquez**

## 🚀 Demo Interactiva en Vivo

Para facilitar la auditoría visual y técnica de la arquitectura de la interfaz del agente, el entorno dinámico ha sido empaquetado y desplegado de forma estática en el borde (Edge):

👉 **[Descargar Informe Técnico Completo (PDF)](./documentos/Informe_Tecnico_Final.pdf)**

👉 **[Probar el Demo del Agente en Vivo](https://86yanetc.github.io/gobernanza_guardrails/demo-mcp/)**

## 🧠 Visión Arquitectónica

El **Enterprise BI Agent** no es una simple interfaz de chat, es una infraestructura cognitiva de grado industrial diseñada para el procesamiento y mutación de datos en el borde (Edge Isolation). Ha sido concebida bajo la premisa de descentralización tecnológica, permitiendo que un modelo de lenguaje de código abierto interactúe de forma segura y directa con sistemas de archivos empresariales y la red pública mediante protocolos desacoplados. Este ecosistema resuelve de raíz los cuatro grandes desafíos de la Inteligencia Artificial en entornos corporativos: **Privacidad Absoluta (Zero-Trust), Latencia de Inferencia, Control de Costos (FinOps) y Confiabilidad Transaccional.**

---

## 💡 Decisiones de Ingeniería y Preguntas Claves

En la construcción de este ecosistema agéntico, se aplicaron principios estrictos de arquitectura de software para transformar modelos generativos locales en una pasarela operativa de grado empresarial. Estas son las respuestas de ingeniería a los pilares de la plataforma:

### 1. ¿Por qué una Arquitectura Desacoplada basada en MCP en lugar de una Integración Monolítica?
Ejecutar inferencias lógicas pesadas junto con manipulación física de bases de datos locales y navegación web asíncrona requiere un aislamiento estricto de responsabilidades:
* **Desacoplamiento Tecnológico:** El uso de **Model Context Protocol (MCP)** mediante transporte `stdio` / JSON-RPC 2.0 permite separar por completo el motor de inferencia (el cliente) de las fuentes de datos (el servidor `FastMCP`). Si la empresa decide migrar su almacenamiento de un archivo CSV local a un clúster de PostgreSQL o SQLite, el código del cliente y el prompt de la IA permanecen intactos; solo se escala el servidor MCP por debajo.

* **Seguridad de Memoria y Procesos:** Al correr las herramientas en un subproceso de terminal aislado (`sys.executable -u`), se mitigan los riesgos de inyección de código o corrupción de memoria en la interfaz gráfica del usuario (Streamlit), garantizando un entorno operativo tolerante a fallos.

### 2. ¿Cómo se optimizó el Retorno de Inversión (FinOps) y la Latencia de Respuesta?
El consumo de APIs comerciales en la nube y los tiempos de espera de red representan los mayores obstáculos económicos y operativos para la IA empresarial. La solución implementada fue una **Infraestructura de Costo Marginal Cero (0\$ Infra)**:
* **Local Edge Inference:** La integración nativa del motor de inferencia `llama-cpp-python` permite ejecutar modelos en formato GGUF (**Qwen2.5-7B / Llama-3.1-8B**) aprovechando el hardware local (aceleración por CPU/GPU). Esto reduce a cero el costo operativo por token, eliminando la dependencia de suscripciones o pagos por uso de proveedores externos.

* **Carga Persistente con Caché de Recursos:** Utilizando el decorador `@st.cache_resource`, los gigabytes del weights del modelo se inicializan en la memoria RAM una única vez al arrancar la infraestructura. Esto reduce la latencia de inicialización a cero milisegundos en consultas subsecuentes, manteniendo un flujo de tokens por segundo altamente competitivo (`Metal/CUDA FP16`).

### 3. ¿Cómo se garantiza la Privacidad, Seguridad y Gobernanza de los Datos Confidenciales?
La filtración de secretos comerciales o datos de clientes a través de APIs en la nube es un riesgo inaceptable para el gobierno corporativo. El framework implementa:
* **Zero-Trust Edge Isolation:** Al ejecutarse el 100% de la cañería de datos en local, la información sensible de ventas, stock y facturación empresarial jamás abandona el perímetro físico de la organización.

* **Type-Safe Validation (Pydantic Guardrails):** Las llamadas autónomas generadas por la IA no impactan directamente en las bases de datos. Pasan por una capa intermedia de validación de esquemas estrictos donde el servidor MCP inspecciona los tipos de datos en el JSON. Si el modelo alucina un parámetro inválido o una columna inexistente, el sistema rechaza la transacción de forma segura y reeduca al modelo mediante mensajes de error del sistema.

* **Manejo de Transacciones Seguro:** Las operaciones de escritura (`registrar_venta_simulada` o `agregar_nuevo_producto`) incorporan mecanismos de validación previa en Pandas (filtros inteligentes por raíces de tokens). Esto previene la duplicación accidental de catálogos y asegura que no se registren transacciones inválidas (como ventas con stock negativo o precios nulos).

### 4. ¿Cuál fue el desafío técnico más complejo?
El reto principal del proyecto fue el diseño del **Bucle de Control Asíncrono Multi-Paso Tolerante a Errores (ReAct)**. Lograr que un modelo local de escala intermedia (7B parámetros) decida de forma autónoma cuándo frenar su generación de texto, inyecte un bloque XML válido (`<tool_call>`), y que el cliente de Python intercepte ese payload en caliente para desviar el flujo hacia el servidor MCP sin romper el hilo de ejecución síncrono de Streamlit, representó el mayor hito de ingeniería de este desarrollo. Se implementó una lógica de persistencia visual basada en `st.session_state` y refrescos limpios (`st.rerun`) para congelar los datos analíticos y renderizar gráficos de barra nativos en pantalla únicamente cuando la transacción de datos local concluye con éxito.

