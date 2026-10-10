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

El verdadero reto fue **hacer convivir el cerebro asíncrono de la IA con la interfaz visual de Streamlit sin que la pantalla se congele o entre en un bucle infinito de recargas.** 

Los modelos locales a veces cometen errores al formatear etiquetas o tardan unos segundos en procesar los datos. Lograr que el cliente de Python intercepte en caliente el bloque `<tool_call>` generado por Qwen, desvíe de forma segura el flujo hacia el servidor MCP y guarde los datos del gráfico en la memoria de la sesión (`st.session_state`) de manera totalmente invisible para el usuario, representó el mayor hito de ingeniería de este desarrollo. Se diseñó un bucle tolerante a fallos que congela los resultados analíticos en pantalla y redibuja los gráficos nativos únicamente cuando la transacción de datos local concluye con éxito.

## 📐 Arquitectura del Sistema & Flujo Transaccional

La solución rompe con los diseños tradicionales rígidos de "chatbots" e implementa una arquitectura híbrida modular basada en componentes desacoplados que se comunican de forma asíncrona local a través del estándar abierto **Model Context Protocol (MCP)**:

```mermaid
graph TD
    %% Estilos de Nodos y Paleta Corporativa
    classDef usuario fill:#e3f2fd,stroke:#1565c0,stroke-width:2px,color:#0d47a1;
    classDef frontend fill:#fff3e0,stroke:#ef6c00,stroke-width:2px,color:#e65100;
    classDef cliente fill:#ede7f6,stroke:#4a148c,stroke-width:2px,color:#4a148c;
    classDef backend fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px,color:#1b5e20;
    classDef datos fill:#eceff1,stroke:#37474f,stroke-width:2px,color:#263238;

    %% Bloque de Interacción de Usuario
    U[👤 Usuario Final]:::usuario
    
    subgraph UI_Streamlit [💻 Capa de Presentación - app.py]
        ST_UI[Streamlit Frontend]:::frontend
        ST_STATE{st.session_state<br>Persistencia Visual}:::frontend
    end

    subgraph Orquestacion_Local [🤖 Engine Agéntico Asíncrono - app.py]
        CLI_MCP[Cliente MCP<br>Bucle Asyncio]:::cliente
        QWEN[Cerebro Local<br>Qwen2.5 GGUF]:::cliente
        FILTER{¿Detecta XML<br>tool_call?}:::cliente
    end

    subgraph Servidor_FastMCP [🔌 Pasarela Operativa - server.py]
        SRV_MCP[Servidor FastMCP<br>Transporte stdio]:::backend
        VAL_PYD[Validación Type-Safe<br>Guardrails Pydantic]:::backend
        ROUTER{Router de<br>Herramientas}:::backend
    end

    subgraph Almacenamiento_y_Red [💾 Capa de Datos & Servicios Externos]
        CSV[(datos_ventas.csv<br>Pandas Engine)]:::datos
        DDG[API DuckDuckGo<br>Navegación Web]:::datos
    end

    %% Flujo de Comunicación e Intercambio de Payloads (Handshake)
    U -->|1. Consulta Lenguaje Natural| ST_UI
    ST_UI -->|2. Inicia Hilo Async| CLI_MCP
    CLI_MCP -->|3. prompt + messages| QWEN
    QWEN -->|4. Genera Tokens| FILTER
    
    %% Ramificación del Borde Condicional (ReAct Loop)
    FILTER -->|NO: Texto Libre| ST_STATE
    FILTER -->|SI: tool_call XML| SRV_MCP
    
    %% Flujo Servidor MCP (JSON-RPC 2.0)
    SRV_MCP -->|5. Payload JSON-RPC| VAL_PYD
    VAL_PYD -->|6. Esquema Validado| ROUTER
    
    %% Selección Autónoma de Herramientas
    ROUTER -->|obtener_stock / registrar_venta| CSV
    ROUTER -->|buscar_en_internet| DDG
    
    %% Retorno Asíncrono y Pasamanos de Memoria
    CSV -->|7. Dataframe / JSON| SRV_MCP
    DDG -->|7. Web Snippets| SRV_MCP
    SRV_MCP -->|8. tool_response XML| CLI_MCP
    
    %% Intercepción de Datos del Gráfico
    CLI_MCP -->|9. Intercepta datos_grafico| ST_STATE
    ST_STATE -->|10. Renderiza st.bar_chart| ST_UI
    CLI_MCP -->|11. Re-inyecta Contexto Fresh| QWEN

    %% Aplicación de Clases
    class U usuario;
    class ST_UI,ST_STATE frontend;
    class CLI_MCP,QWEN,FILTER cliente;
    class SRV_MCP,VAL_PYD,ROUTER backend;
    class CSV,DDG datos;
```
