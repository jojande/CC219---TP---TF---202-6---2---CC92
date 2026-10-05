# Análisis de Sentimientos Basado en Aspectos en Reseñas de Restaurantes

## Curso

**CC219 – Aplicaciones de Data Science**  
**Periodo:** 2026-02  
**Trabajo Parcial / Final**

## Objetivo del proyecto

El objetivo del proyecto es desarrollar y evaluar un enfoque de **Análisis de Sentimientos Basado en Aspectos (Aspect-Based Sentiment Analysis, ABSA)** aplicado a reseñas de restaurantes en español.

El estudio busca comparar modelos tradicionales de clasificación de textos con un modelo basado en Transformers, considerando tanto el rendimiento predictivo como la capacidad de analizar aspectos específicos presentes en una opinión.

Las principales tareas planteadas son:

1. **Clasificación de sentimiento por aspecto**, determinando si la opinión asociada a un aspecto es positiva, negativa o neutral.
2. **Clasificación de categorías de aspecto**, identificando qué categorías están presentes en una oración.
3. **Extracción de Opinion Target Expressions (OTE)** como extensión experimental, identificando el fragmento textual que representa el objetivo de una opinión.

## Integrantes

- [Nombre del integrante 1]
- [Nombre del integrante 2]
- [Nombre del integrante 3]

## Dataset

Se utiliza el dataset oficial de **SemEval-2016 Task 5: Aspect-Based Sentiment Analysis**, específicamente:

- **Dominio:** Restaurants
- **Idioma:** Spanish
- **Subtask:** Subtask 1 – Sentence-level ABSA

Los archivos utilizados son:

```text
dataset/
├── SemEval-2016ABSA Restaurants-Spanish_Train_Subtask1.xml
└── SP_REST_SB1_TEST.xml.gold
```

El corpus está compuesto por reseñas de restaurantes organizadas en formato XML. Cada reseña contiene una o varias oraciones y cada oración puede contener ninguna, una o múltiples opiniones anotadas.

Cada opinión puede incluir los siguientes atributos:

- `target`: expresión textual sobre la que se emite la opinión.
- `category`: categoría del aspecto en formato `ENTITY#ATTRIBUTE`.
- `polarity`: polaridad del sentimiento.
- `from`: posición inicial del target dentro del texto.
- `to`: posición final del target dentro del texto.

El valor `target="NULL"` representa un aspecto implícito que no aparece expresado mediante un fragmento textual concreto.

### Dimensiones del dataset

| Elemento | Train | Test | Total |
|---|---:|---:|---:|
| Reseñas | 627 | 268 | 895 |
| Oraciones | 2,070 | 881 | 2,951 |
| Opiniones | 2,720 | 1,072 | 3,792 |

El dataset contiene 12 categorías de aspectos y presenta un desbalance importante entre las clases de sentimiento, con predominio de opiniones positivas.

## Preguntas de investigación

El proyecto busca responder las siguientes preguntas:

1. **¿Es posible identificar automáticamente la categoría de aspecto sobre la cual se expresa una opinión en una reseña de restaurante?**
2. **¿Es posible identificar automáticamente la expresión textual que representa el objetivo de una opinión dentro de una reseña?**
3. **¿Es posible clasificar automáticamente la polaridad del sentimiento expresado respecto de un aspecto determinado?**

## Análisis Exploratorio de Datos

El EDA contempla:

- carga e inspección de los archivos XML;
- transformación de la estructura jerárquica a representaciones tabulares;
- análisis de reseñas, oraciones y opiniones;
- análisis de polaridades;
- análisis de categorías de aspectos;
- análisis de Opinion Targets;
- identificación de targets implícitos;
- análisis de longitud de textos;
- inspección de valores faltantes y duplicados;
- comparación entre Train y Test;
- normalización conservadora del texto;
- visualización de distribuciones y frecuencias.

Entre los principales hallazgos se encuentran:

- predominio de la clase `positive`;
- baja representación de la clase `neutral`;
- existencia de una única instancia `conflict` en Train;
- desbalance entre categorías de aspectos;
- aproximadamente 30 % de targets implícitos (`NULL`);
- presencia de múltiples aspectos dentro de una misma oración;
- ausencia de errores en los offsets de los targets explícitos;
- distribuciones similares, aunque no idénticas, entre Train y Test.

## Propuesta de modelización

La comparación principal del proyecto se centrará en la **clasificación de sentimiento por aspecto**.

Se proponen los siguientes modelos:

### Modelos tradicionales

- **TF-IDF + Logistic Regression**
- **TF-IDF + Linear SVM**

Estos modelos funcionarán como baselines para evaluar el rendimiento de representaciones léxicas tradicionales.

### Transformer

- **BETO (`dccuchile/bert-base-spanish-wwm-cased`)**

BETO será utilizado como modelo contextual preentrenado para español.

### Opinion Target Extraction

Como extensión experimental, la extracción de targets se plantea como un problema de **token classification / sequence labeling**, utilizando un esquema BIO:

- `B-ASP`: inicio de un aspecto.
- `I-ASP`: continuación del aspecto.
- `O`: token que no pertenece a un aspecto.

## Representación de los datos

Para Logistic Regression y Linear SVM se utilizará **TF-IDF**, considerando:

- unigramas;
- unigramas + bigramas.

Para BETO se utilizará el tokenizador propio del modelo y representaciones contextuales.

En la clasificación de sentimiento por aspecto se proporcionará información correspondiente a:

```text
oración + categoría del aspecto + target
```

cuando el target sea explícito.

## Partición de los datos

Se respetará la división oficial de SemEval:

- **Train oficial:** utilizado para entrenamiento y validación.
- **Test Gold oficial:** reservado exclusivamente para evaluación final.

El Train oficial se dividirá internamente en:

- **80 % Training**
- **20 % Validation**

La partición se realizará agrupando por `review_id` para evitar fuga de información entre reseñas relacionadas.

El conjunto Test no será utilizado para selección de hiperparámetros, ajuste de modelos ni construcción del vocabulario TF-IDF.

## Métricas propuestas

Debido al desbalance de clases, **Accuracy no será utilizada como única métrica**.

### Clasificación de sentimiento

Métrica principal:

- **Macro F1**

Métricas complementarias:

- Precision
- Recall
- F1-score por clase
- Weighted F1
- Accuracy
- Matriz de confusión

### Clasificación de categorías

Al tratarse de un problema multietiqueta:

- Macro F1
- Micro F1
- Precision macro/micro
- Recall macro/micro
- Weighted F1
- F1 por categoría

### Opinion Target Extraction

- Precision a nivel de span
- Recall a nivel de span
- F1 a nivel de span
- Exact Match

## Estructura del repositorio

```text
.
├── dataset/
│   ├── SemEval-2016ABSA Restaurants-Spanish_Train_Subtask1.xml
│   └── SP_REST_SB1_TEST.xml.gold
├── code/
│   └── TrabajoParcial.ipynb
├── README.md
└── [otros archivos del proyecto]
```

## Estado actual

El proyecto se encuentra actualmente en la etapa de **Trabajo Parcial**.

Hasta el momento se ha completado:

- definición del caso de uso;
- descripción y análisis del dataset;
- análisis exploratorio de datos;
- propuesta de modelización;
- definición de estrategias de representación;
- definición de particiones Train / Validation / Test;
- selección de métricas de evaluación.

El entrenamiento y comparación experimental de los modelos corresponde a la siguiente etapa del proyecto.

## Conclusiones

Las conclusiones finales se incorporarán una vez completada la etapa de modelización, entrenamiento y evaluación de los modelos.

Por el momento, el análisis exploratorio muestra que el dataset presenta desbalance tanto en las polaridades como en las categorías de aspectos, además de una proporción relevante de targets implícitos. Estas características deberán considerarse durante la selección de métricas y la evaluación de los modelos.

## Referencias

- Pontiki, M., Galanis, D., Papageorgiou, H., Androutsopoulos, I., Manandhar, S., Al-Smadi, M., Al-Ayyoub, M., Zhao, Y., Qin, B., De Clercq, O., Hoste, V., Apidianaki, M., Tannier, X., Loukachevitch, N., Kotelnikov, E., Bel, N., Jiménez-Zafra, S. M., & Eryiğit, G. (2016). *SemEval-2016 Task 5: Aspect Based Sentiment Analysis*. Proceedings of the 10th International Workshop on Semantic Evaluation (SemEval-2016), 19–30.
- SemEval-2016 Task 5: Aspect-Based Sentiment Analysis.

## Licencia

**Por definir.**

Antes de publicar el repositorio, se debe seleccionar y añadir una licencia apropiada para el código desarrollado por el equipo. El uso y redistribución del dataset debe respetar las condiciones establecidas por sus autores y por SemEval.
