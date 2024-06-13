### Example of multi-class clasification
```python
import tensorflow as tf
import numpy as np
import pandas as pd
import keras
import matplotlib.pyplot as plt
from sklearn.compose import make_column_transformer
from sklearn.preprocessing import MinMaxScaler, OneHotEncoder
from sklearn.model_selection import train_test_split
from sklearn.metrics import median_absolute_error
```
#### Data loading
```python
(train_data, train_labels), (test_data, test_labels) = keras.datasets.fashion_mnist.load_data()
```

#### Show loaded data (image)
```python
numb = 232
plt.imshow(train_data[numb], cmap=plt.cm.binary)
plt.title(class_names[train_labels[numb]])
```
#### Create labels (getted from git)
```python
class_names = [
    "T-shirt/top",
    "Trouser",
    "Pullover",
    "Dress",
    "Coat",
    "Sandal",
    "Shirt",
    "Sneaker",
    "Bag",
    "Ankle boot",
]
```
#### Normalize
```python
print(np.min(train_data), np.max(train_data)) # Max is 255 and min 0
train_data_norm = train_data / 255.0 
test_data_norm = test_data / 255.0
np.min(train_data_norm), np.max(test_data_norm)
```
#### Model ==not hot decoded== labels
```python
model_2 = keras.Sequential([
    keras.layers.Flatten(input_shape=(28, 28)),
    keras.layers.Dense(4, activation="relu"),
    keras.layers.Dense(4, activation="relu"),
    keras.layers.Dense(10, activation="softmax"),
])

model_2.compile(
    loss=keras.losses.SparseCategoricalCrossentropy(),
    optimizer=keras.optimizers.Adam(),
    metrics=["accuracy"],
)

history_2 = model_2.fit(train_data_norm, train_labels, epochs=10, validation_data=(test_data_norm, test_labels))
```
#### Model ==hot decoded== labels
```python
tf.random.set_seed(42)
model_2 = keras.Sequential([
    keras.layers.Flatten(input_shape=(28, 28)),
    keras.layers.Dense(4, activation="relu"),
    keras.layers.Dense(4, activation="relu"),
    keras.layers.Dense(10, activation="softmax"),
])

model_2.compile(
    loss=keras.losses.CategoricalCrossentropy(),
    optimizer=keras.optimizers.Adam(),
    metrics=["accuracy"]
)

history_2 = model_2.fit(train_data, tf.one_hot(train_labels, depth=10), epochs=10, validation_data=(test_data, tf.one_hot(test_labels, depth=10)))
```
#### Show learn history
```python
pd.DataFrame(history_2.history).plot(title="Normalized data", figsize=(12, 7), grid=True, xlabel="epochs", ylabel="loss/accuracy")
```
#### Convert predictions from 10 outputs to 1
```python
pred_probs = model_4.predict(test_data_norm) # output is [[0.1, 0.01, ..., 0.6], [0.01, 0.9, ..., 0.01], ...
pred_classes = pred_probs.argmax(axis=1) # get the highest value index of each array in pred_probs ([0.1, 0.01, ..., 0.6] -> 9)
```
#### Confusion matrix not prettier
```python
from sklearn.metrics import confusion_matrix
confusion_matrix(y_true=test_labels,
                 y_pred=pred_classes)
```
####
```python
```
####
```python
```
####
```python
```
####
```python
```
####
```python
```
####
```python
```