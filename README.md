# Fine-Tuning Falcon-RW-1B con LoRA para Recomendación de Tarifas Eléctricas

Este proyecto tiene como objetivo el fine-tuning de un modelo de lenguaje para adaptarlo a una tarea específica: **recomendar la tarifa eléctrica más adecuada en función de una descripción de los hábitos de consumo del usuario**.

Se parte del modelo [Falcon-RW-1B](https://huggingface.co/tiiuae/falcon-rw-1b), el cual ha sido afinado con datos sintéticos para que, dado un prompt como:

> “Tengo consumo alto por la noche, ¿qué tarifa recomiendas?”

El modelo sea capaz de responder con:

> “La tarifa nocturna es la más adecuada para ti, ya que aprovecha precios reducidos en horario nocturno.”

## 📂 Contenido del repositorio

- `PracticaLLM.ipynb`: Notebook con todo el proceso de entrenamiento, validación y subida del modelo a Hugging Face.
- `tarifas_finetune_clean.jsonl`: Dataset sintético utilizado para el fine-tuning.
- `tarifas_reales.csv`: CSV con tarifas reales usado como referencia para generar los datos de entrenamiento.
- `modelo-finetuneado/`: Carpeta generada por Hugging Face Transformers con los pesos LoRA entrenados.

## 🧠 Modelo base

- **Nombre**: `tiiuae/falcon-rw-1b`
- **Parámetros**: 1.3B
- **Tipo**: Causal Language Model (CLM)
- **Token especial de padding**: igual al token de fin de secuencia (`eos_token`)

## ⚙️ Técnica de fine-tuning

Se ha utilizado **LoRA (Low-Rank Adaptation)**, una técnica de entrenamiento eficiente para adaptar modelos grandes sin modificar directamente sus pesos base. Configuración utilizada:

- `r=8`
- `alpha=32`
- `target_modules=["query_key_value"]`
- `dropout=0.05`

## 🧪 Validación

El modelo fue entrenado con un conjunto sintético de 100 ejemplos, y posteriormente validado con una fracción separada del dataset. Los resultados mostraron una mejora constante y reducción del error:

| Epoch | Training Loss | Validation Loss |
|-------|----------------|------------------|
| 1     | 0.1934         | 0.1721           |
| 2     | 0.0233         | 0.0217           |
| 3     | 0.0142         | 0.0127           |
| 4     | 0.0126         | 0.0126           |

⚠️ **Nota**: Los resultados son prometedores, pero deben tomarse con cautela. El dataset es muy homogéneo, lo que facilita que el modelo aprenda rápidamente. En un entorno real, se requeriría un dataset más diverso y representativo de casos reales.

## 📊 Dataset

- `tarifas_finetune_clean.jsonl`: contiene 500 ejemplos sintéticos del formato:
  ```json
  {
    "prompt": "Tengo consumo alto por la noche, ¿qué tarifa recomiendas?",
    "completion": "La tarifa nocturna es la más adecuada para ti, ya que aprovecha precios reducidos en horario nocturno."
  }
  ```

- `tarifas_reales.csv`: contiene las tarifas eléctricas reales que sirvieron como inspiración para generar el dataset.

🔧 **Pendiente**: El objetivo inicial era construir el dataset a partir de scraping de páginas web de comercializadoras eléctricas (como Iberdrola, Repsol o TotalEnergies). Por falta de tiempo, se optó por crear datos sintéticos como sustituto. Esta funcionalidad queda pendiente para una futura versión del proyecto.

## 🔐 Requisitos de autenticación

Este proyecto requiere dos claves de acceso:

- **Token de Hugging Face**: para descargar el modelo base `falcon-rw-1b`. Debe tener permisos para acceder a modelos protegidos. Puedes obtenerlo en [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens).
- **Token de OpenAI**: si planeas integrar este modelo o hacer comparativas con modelos OpenAI (opcional pero útil en evaluaciones). Puede obtenerse desde [platform.openai.com/account/api-keys](https://platform.openai.com/account/api-keys).

## 🚀 Próximos pasos

- Ampliar el dataset sintético para mayor robustez.
- Desarrollar el sistema de scraping para obtener información actualizada directamente desde webs oficiales.
- Evaluar métricas específicas de generación (e.g., Rouge, BLEU).
- Probar modelos más eficientes como `mistralai/Mistral-7B-Instruct` o `microsoft/phi-2`.
- Implementar una API o frontend para recomendaciones en tiempo real.