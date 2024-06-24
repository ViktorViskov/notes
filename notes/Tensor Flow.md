#### Libs
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

#### Model
```python
model = keras.Sequential([
    keras.layers.Dense(81, input_shape=[1]),
    keras.layers.Dense(1)
])

model.compile(
    loss=['mae'],
    optimizer=keras.optimizers.Adam(0.001),
    metrics=['mae']
)

history = model.fit(x_train, y_train, epochs=200, verbose=1)
```
#### Learning history
```python
pd.DataFrame(history.history).plot()
```

[[python]]
[[ML]]