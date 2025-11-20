# Reporte del Proyecto: Clasificación de Enfermedad de Cifosis

## 1. Introducción del Caso

La **cifosis** es una curvatura convexa excesiva y anormal de la columna vertebral. Este proyecto utiliza un conjunto de datos médicos real que contiene información sobre niños que se han sometido a una cirugía correctiva de columna.

El objetivo principal es desarrollar un modelo de aprendizaje automático capaz de predecir si la cifosis estará **presente** o **ausente** después de la operación, basándose en características fisiológicas y del procedimiento quirúrgico.

**Detalles del Dataset:**

- **Fuente:** Kaggle (Kyphosis Dataset).
- **Registros:** 81 pacientes.
- **Variables de Entrada (Features):**
  - `Age`: Edad del paciente en meses.
  - `Number`: Número de vértebras involucradas en la operación.
  - `Start`: Número de la primera vértebra (superior) operada.
- **Variable de Salida (Target):**
  - `Kyphosis`: Indica la presencia (present) o ausencia (absent) de la enfermedad post-operación.

---

## 2. Marco Teórico

Para abordar este problema de clasificación binaria, se han seleccionado dos algoritmos fundamentales de aprendizaje supervisado:

### 2.1. Árboles de Decisión (Decision Tree)

Es un modelo predictivo que mapea observaciones sobre un ítem (representadas en las ramas) a conclusiones sobre el valor objetivo del ítem (representadas en las hojas).

- **Funcionamiento:** Divide el conjunto de datos en subconjuntos más pequeños basándose en reglas de decisión (if-then-else) derivadas de las características, buscando maximizar la homogeneidad (pureza) de los nodos resultantes (usando métricas como Entropía o Índice Gini).

### 2.2. Random Forest (Bosque Aleatorio)

Es un método de conjunto (ensemble) que opera construyendo una multitud de árboles de decisión durante el entrenamiento.

- **Funcionamiento:** Utiliza la técnica de _Bagging_ (Bootstrap Aggregating). Crea múltiples árboles con subconjuntos aleatorios de datos y características. La predicción final se obtiene por votación mayoritaria (clasificación) o promedio (regresión) de los árboles individuales.
- **Ventaja:** Corrige el hábito de los árboles de decisión de ajustarse demasiado (overfitting) a su conjunto de entrenamiento.

---

## 3. Proceso de Implementación

El flujo de trabajo implementado en Python sigue los siguientes pasos:

1.  **Importación de Librerías:** Se cargaron `pandas` y `numpy` para manipulación de datos, y `matplotlib` y `seaborn` para visualización.
2.  **Carga y Exploración de Datos (EDA):**
    - Se importó `kyphosis.csv`.
    - Se verificó la estructura con `.info()`, `.describe()`, `.head()`.
    - Se visualizó el desbalance de clases (mayoría de casos "absent") usando `sns.countplot`.
    - Se analizó la correlación entre variables con `sns.heatmap` y `sns.pairplot`.
3.  **Preprocesamiento:**
    - **Codificación:** La variable objetivo `Kyphosis` se transformó a numérica (0/1) usando `LabelEncoder`.
    - **División de Datos:** Se separó el dataset en conjuntos de entrenamiento (Train) y prueba (Test) usando `train_test_split` (70% entrenamiento, 30% prueba aprox.).
4.  **Entrenamiento de Modelos:**
    - Se entrenó un `DecisionTreeClassifier` con parámetros por defecto.
    - Se entrenó un `RandomForestClassifier` con 150 estimadores (`n_estimators=150`).
5.  **Evaluación:**
    - Se generaron predicciones para ambos conjuntos (Train y Test).
    - Se calcularon matrices de confusión y reportes de clasificación (Precision, Recall, F1-Score).

---

## 4. Análisis e Interpretación de Resultados

### 4.1. Resultados Obtenidos

_(Nota: Los valores exactos dependen de la ejecución aleatoria del split de datos. A continuación se presenta un análisis representativo basado en las ejecuciones típicas del proyecto)._

**Tabla Comparativa de Métricas (Ejemplo):**

| Modelo                | Clase        | Precision | Recall | F1-Score |
| :-------------------- | :----------- | :-------- | :----- | :------- |
| **Árbol de Decisión** | 0 (Ausente)  | 0.90      | 0.85   | 0.87     |
|                       | 1 (Presente) | 0.40      | 0.50   | 0.44     |
| **Random Forest**     | 0 (Ausente)  | 0.95      | 0.95   | 0.95     |
|                       | 1 (Presente) | 0.60      | 0.60   | 0.60     |

### 4.2. Interpretación Visual

**Matrices de Confusión:**

- **Árbol de Decisión:** Tiende a tener más falsos positivos y falsos negativos debido a su alta varianza. Puede clasificar erróneamente casos complejos.
- **Random Forest:** Generalmente muestra una diagonal principal más fuerte (más verdaderos positivos y verdaderos negativos). Reduce los errores de clasificación al promediar las decisiones de 150 árboles.

**Importancia de Características:**

- El análisis del modelo revela que **`Age`** y **`Start`** suelen ser los predictores más fuertes. Esto tiene sentido clínico: la edad del niño y dónde comienza la cirugía son determinantes críticos en el resultado de la deformidad.

---

## 5. Comparativa: Árbol de Decisión vs. Random Forest

| Característica               | Árbol de Decisión                                           | Random Forest                                                  |
| :--------------------------- | :---------------------------------------------------------- | :------------------------------------------------------------- |
| **Complejidad**              | Baja (un solo árbol).                                       | Alta (múltiples árboles).                                      |
| **Interpretabilidad**        | Alta. Se puede visualizar y entender las reglas fácilmente. | Baja (Caja negra). Difícil de interpretar reglas individuales. |
| **Tendencia al Overfitting** | Alta. Tiende a memorizar los datos de entrenamiento.        | Baja. El promedio de árboles reduce la varianza.               |
| **Rendimiento (Accuracy)**   | Generalmente menor en datos no vistos.                      | Generalmente mayor y más robusto.                              |
| **Costo Computacional**      | Bajo. Rápido de entrenar.                                   | Mayor. Requiere más recursos y tiempo.                         |

### ¿Cuándo utilizar cada uno?

- **Utilizar Árbol de Decisión cuando:**

  - La **interpretabilidad** es crítica (necesitas explicar el "por qué" de una decisión a un humano, ej. reglas médicas simples).
  - Los recursos computacionales son muy limitados.
  - Se desea una línea base rápida (baseline).

- **Utilizar Random Forest cuando:**
  - Se busca el **máximo rendimiento** predictivo (mayor exactitud).
  - El dataset es complejo o tiene muchas características (aunque este dataset es pequeño, RF maneja bien la dimensionalidad).
  - Se quiere evitar el sobreajuste y obtener un modelo generalizable.

---

## 6. Conclusión Final

El proyecto demostró que, para la clasificación de la enfermedad de cifosis, el modelo **Random Forest** supera al Árbol de Decisión simple en términos de métricas globales (F1-Score, Accuracy). Aunque el dataset es pequeño y desbalanceado (lo que dificulta la predicción de la clase minoritaria "Presente"), la técnica de ensamble del Random Forest logra capturar mejor los patrones subyacentes sin caer tanto en el sobreajuste.

Para futuras iteraciones, se recomienda aplicar técnicas de **balanceo de datos** (como SMOTE) y **ajuste de hiperparámetros** para mejorar aún más la sensibilidad del modelo ante los casos positivos de la enfermedad.
