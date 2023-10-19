# Business Intelligence Lab Submission Markdown

<Specify your group name here> <Specify the date when you submitted the lab>

-   [Student Details](#student-details)
-   [Setup Chunk](#setup-chunk)

# Student Details {#student-details}

+---------------------------------------------------+---------------------------------------------+
| **Student ID Numbers and Names of Group Members** | 1\. 135232 - Sadiki Hamisi                  |
|                                                   |                                             |
|                                                   | 2\. 134782 - Yasmin Choma                   |
|                                                   |                                             |
|                                                   | 3\. 134783 - Moses mbugua                   |
|                                                   |                                             |
|                                                   | 4\. 122998 - Glenn Oloo                     |
+---------------------------------------------------+---------------------------------------------+
| **GitHub Classroom Group Name**                   | Starfield                                   |
+---------------------------------------------------+---------------------------------------------+
| **Course Code**                                   | BBT4206                                     |
+---------------------------------------------------+---------------------------------------------+
| **Course Name**                                   | Business Intelligence II                    |
+---------------------------------------------------+---------------------------------------------+
| **Program**                                       | Bachelor of Business Information Technology |
+---------------------------------------------------+---------------------------------------------+
| **Semester Duration**                             | 21^st^ August 2023 to 28^th^ November 2023  |
+---------------------------------------------------+---------------------------------------------+

# Setup Chunk {#setup-chunk}

**Note:** the following "*KnitR*" options have been set as the defaults:\
`knitr::opts_chunk$set(echo = TRUE, warning = FALSE, eval = TRUE, collapse = FALSE, tidy.opts = list(width.cutoff = 80), tidy = TRUE)`.

More KnitR options are documented here <https://bookdown.org/yihui/rmarkdown-cookbook/chunk-options.html> and here <https://yihui.org/knitr/options/>.

**Note:** the following "*R Markdown*" options have been set as the defaults:

Installing and loading of required packages

``` code
# Language server
if (require("languageserver")) {
  require("languageserver")
} else {
  install.packages("languageserver", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}

# ggplot2 
if (require("ggplot2")) {
  require("ggplot2")
} else {
  install.packages("ggplot2", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}

# caret 
if (require("caret")) {
  require("caret")
} else {
  install.packages("caret", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}

# mlbench 
if (require("mlbench")) {
  require("mlbench")
} else {
  install.packages("mlbench", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}

# pROC 
if (require("pROC")) {
  require("pROC")
} else {
  install.packages("pROC", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}

# dplyr 
if (require("dplyr")) {
  require("dplyr")
} else {
  install.packages("dplyr", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}
```

Loading and viewing of the dataset

``` code

# Loading the dataset
data(iris)

# Viewing the dataset
View(iris)
iris_freq <- iris$Species
cbind(frequency =
        table(iris_freq),
      percentage = prop.table(table(iris_freq)) * 100)
```

Splitting of the dataset into training and test data

``` code

train_index <- createDataPartition(iris$Species,
                                   p = 0.75,
                                   list = FALSE)

iris_train <- iris[train_index, ]
iris_test <- iris[-train_index, ]
```

Training the Model using the 5-fold cross validation re-sampling method

``` code

train_control <- trainControl(method = "cv", number = 5)
```

Training a Generalized Linear Model for prediction

``` code

set.seed(7)
iris_model_rf  <-
  train(Species ~ ., data = iris_train, method = "rf",
        metric = "Accuracy", trControl = train_control)
```

Displaying the model performance

``` code

print(iris_model_rf)
```

Predictions of the model presented in a Confusion Matrix

``` code

confusion_matrix <- confusionMatrix(predictions, iris_test$Species)
print(confusion_matrix)

confusion_data <- as.data.frame(as.table(confusion_matrix))
```

Graphical representation of the Confusion Matrix

``` code
ggplot(data = confusion_data, aes(x = Reference, y = Prediction, fill = Freq)) +
  geom_tile() +
  geom_text(aes(label = Freq), vjust = 1) +
  scale_fill_gradient(low = "lightblue", high = "blue") +
  theme_minimal() +
  labs(title = "Confusion Matrix")
```

Training the model using bootstrapping with 1000 repetitions

``` code

train_control <- trainControl(method = "boot", number = 1000)
```

Training the linear regression model the value of diabetes

``` code

Pima_model_logistic <-
  train(diabetes ~ ., data = Pima_train,
        method = "glm", family = binomial,
        metric = "MSE",  
        trControl = train_control)
```

Printing the predictions

``` code

print(predictions)
```

Calculating the mean squared error (MSE)

``` code
mse <- mean((as.numeric(predictions) - as.numeric(Pima_test$diabetes))^2)
```

Calculating and printing RMSE by taking the square root of MSE

``` code

rmse <- sqrt(mse)
print(paste("RMSE (MSE) =", rmse))
```

Training the k Nearest Neighbours Model

``` code

iris_model_knn <- train(Species ~ ., data = iris_train, method = "knn",
                        metric = "Accuracy", trControl = train_control)
```

Displaying the models performance

``` code

print(iris_model_knn)
```

Computing the ROC curves for each class

``` code

roc_curves <- lapply(unique(iris$Species), function(class_name) {
  class_predictions <- as.numeric(predictions[class_name] > 0)
  class_roc <- roc(iris_test$Species == class_name, class_predictions)
  return(class_roc)
})
```

Plot the ROC curves for each class

``` code

colors <- rainbow(length(roc_curves))
for (i in 1:length(roc_curves)) {
  plot(roc_curves[[i]], col = colors[i], print.auc = TRUE, print.auc.x = 0.6, print.auc.y = 0.6, lwd = 2.5, add = (i > 1))
}

legend("bottomright", legend = unique(iris$Species), col = colors, lwd = 2.5)
```
