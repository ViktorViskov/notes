### Imports
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
from sklearn.datasets import make_circles
```
### Dataset
```python
x, y = make_circles(1000,
                    noise=0.03,
                    random_state=42)
```
### Split by train / test
```python
x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.2, random_state=42)
```
### Create model
Need to use specific activation to ==**non linear**== data for hidden tensors (classification):
- relu
- sigmoid
- tanh

Output tensor must use ==**sigmoid**== for binary classification
```python
model_1 = keras.Sequential([
    keras.layers.Dense(16, activation="relu"),
    keras.layers.Dense(4, activation="relu"),
    keras.layers.Dense(1, activation="sigmoid")
])
```
### Compiling
For loss method ==**BinaryCrossentropy**==
For metrics ==**accuracy**==
```python
model_1.compile(
    loss=keras.losses.BinaryCrossentropy(),
    optimizer=keras.optimizers.Adam(learning_rate=0.01),
    metrics=["accuracy"]
)
```
### Training
```python
history = model_1.fit(x_train, y_train, epochs=25, verbose=1)
```
### Show training
```python
pd.DataFrame(history.history).plot(title="Model 6 loss and accuracy", xlabel="epochs", ylabel="loss/accuracy", figsize=(10, 7))
```

[[Tensor Flow]]
[[ML]]