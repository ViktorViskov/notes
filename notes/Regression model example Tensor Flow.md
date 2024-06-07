### requirements.txt
```
tensorflow-cpu
keras
matplotlib
pandas
scikit-learn
```

##### Imports
```python
import tensorflow as tf
import numpy as np
import pandas as pd
import keras
from sklearn.compose import make_column_transformer
from sklearn.preprocessing import MinMaxScaler, OneHotEncoder
from sklearn.model_selection import train_test_split
```

##### Data loading
```python
insurance = pd.read_csv("https://raw.githubusercontent.com/stedy/Machine-Learning-with-R-datasets/master/insurance.csv")
```

##### Create column transformer
```python
column_transformer = make_column_transformer(
    (MinMaxScaler(), ["age", "bmi", "children"]),
    (OneHotEncoder(handle_unknown="ignore"), ["sex", "smoker", "region"])
)
```

##### Data selecting (X - inputs, y - outputs)
```python
x = insurance.drop("charges", axis=1)
y = insurance["charges"]
```

##### Data spliting for traing and test
```python
x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.2, random_state=42)
```

##### Fit column transformer on train data and transform data
```python
column_transformer.fit(x_train)

x_train_normal = column_transformer.transform(x_train)
x_test_normal = column_transformer.transform(x_test)
```

##### Model creating
```python
tf.random.set_seed(42)

model_three = keras.Sequential([
  keras.layers.Dense(150, activation="relu"),
  keras.layers.Dense(100, activation="relu"),
  keras.layers.Dense(30, activation="relu"),
  keras.layers.Dense(1)
])

model.compile(
  loss=['mae'],
  optimizer=keras.optimizers.Adam(learning_rate=0.003),
  metrics=['mae']
)
```

##### Model fitting and evaluating
```python
history = model.fit(x_train_normal, y_train, epochs=100, verbose=1)
pd.DataFrame(history.history).plot()

predicted = model.predict(x_test_normal)
print(model.evaluate(x_test_normal, y_test))
pd.DataFrame(predicted.squeeze() - y_test).plot(kind='bar')
```

##### Saving
```python
model.save("model.keras")
```

##### Loading
```python
model = keras.models.load_model("model.h5")
```

[[Tensor Flow]]
[[ML]]
