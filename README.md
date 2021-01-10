# Análisis discriminante cuadrtico QDA con RStudio

QDA no es realmente muy diferente de LDA, excepto que asume que la matriz de covarianza puede ser diferente para cada clase y, por lo tanto, estimaremos la matriz de covarianza por separado para cada clase $k$ , $k = 1, 2, ..., K$ .

# **Paso 1. Carga de paquetes R requeridos.**

```{r message=FALSE}
library(tidyverse)
library(caret)
library(klaR)
theme_set(theme_classic())
```

# **Paso 2. Preparando los datos.**

**2.1. Divida los datos en entrenamiento y conjunto de prueba:**
  
  ```{r}
# Cargamos la data
data("iris")
# Dividimos la data para entrenamiento en un (80%) y para la prueba en un (20%)
set.seed(123)
training.samples <- iris$Species %>%
  createDataPartition(p = 0.8, list = FALSE)
train.data <- iris[training.samples, ]
test.data <- iris[-training.samples, ]
```

**2.2 Normaliza los datos. Las variables categóricas se ignoran automáticamente.**
  
  ```{r}
# Estimar parámetros de preprocesamiento
preproc.param <- train.data %>% 
  preProcess(method = c("center", "scale"))
# Transformar los datos usando los parámetros estimados
train.transformed <- preproc.param %>% predict(train.data)
test.transformed <- preproc.param %>% predict(test.data)
```
# **Paso 3. Creación del Modelo QDA**
 QDA se puede calcular usando la función R `qda()`[paquete MASS]

```{r}
library(MASS)
# Creando el modelo
modelqda <- qda(Species~., data = train.transformed)
modelqda

```
# paso 4.**Gráficos de partición QDA**
```{r} 
partimat(Species ~ Sepal.Length + Sepal.Width + Petal.Length + Petal.Width, data=train.transformed, method="qda")
```

# **Paso 5: use el modelo para hacer predicciones**

```{r}
# haciendo predicciones
predictionsqda <- modelqda %>% predict(test.transformed)
names(predictionsqda)
```

# **Paso 6: evaluar el modelo**
```{r}
# Precision del Modelo
mean(predictionsqda$class == test.transformed$Species)
```
