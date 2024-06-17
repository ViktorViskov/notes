### Libs
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
import os
```
### Show dataset (simple)
```python
for path, dir_name, file_name in os.walk("pizza_steak"):
    print(f"{len(dir_name)} directories, {len(file_name)} images in", path, sep="\t")
```
### Data preparing

#### Get classes
```python
class_names = np.array(sorted(os.listdir("pizza_steak/train/")))
class_names
```

#### Load dataset
```python
tf.random.set_seed(42)

train_dir = "pizza_steak/train"
test_dir = "pizza_steak/test"

keras.utils.image_dataset_from_directory
train_data = keras.utils.image_dataset_from_directory(
    train_dir,
    image_size=(224, 224),
    batch_size=32,
    label_mode="binary",
    seed=42
)

test_data = keras.utils.image_dataset_from_directory(
    test_dir,
    image_size=(224, 224),
    batch_size=32,
    label_mode="binary",
    seed=42
)
```
#### Simple dataset augmentation
```python
normalization_layer = keras.layers.Rescaling(1./255)

train_data_simp_aug = train_data.map(lambda x, y: (normalization_layer(x), y))
test_data_simp_aug = test_data.map(lambda x, y: (normalization_layer(x), y))
```

#### Complex dataset augmentation
```python
data_augmentation = keras.Sequential([
    keras.layers.Rescaling(1./255),
    keras.layers.RandomRotation(0.2), # rotate the image slightly between 0 and 20 degrees
    keras.layers.RandomTranslation(0.2, 0.2), # shift the image width and height ways
    keras.layers.RandomZoom(0.2), # zoom into the image
    keras.layers.RandomZoom(height_factor=(-0.2, 0.2), width_factor=(0.0, 0.0)), # аналог RandomWidth
    keras.layers.RandomZoom(height_factor=(0.0, 0.0), width_factor=(-0.2, 0.2)), # аналог RandomHeight
    keras.layers.RandomFlip('horizontal_and_vertical'), # flip the image on the horizontal and vertical axis
])

# Step 3: Apply the rescaling and augmentation
train_data_augmented = train_data.map(lambda x, y: (data_augmentation(x, training=True), y))
```

#### CNN model
```python
model_1 = keras.Sequential([
    keras.layers.Conv2D(10, 3, activation="relu", input_shape=(224, 224, 3)),
    keras.layers.MaxPool2D(),
    keras.layers.Conv2D(10, 3, activation="relu"),
    keras.layers.MaxPool2D(),
    keras.layers.Conv2D(10, 3, activation="relu"),
    keras.layers.MaxPool2D(),
    keras.layers.Flatten(),
    keras.layers.Dense(1, activation="sigmoid")
])

model_1.compile(
    loss=keras.losses.BinaryCrossentropy(),
    optimizer=keras.optimizers.Adam(),
    metrics=["accuracy"]
)

history_1 = model_4.fit(
    train_data_augmented,
    epochs=5,
    validation_data=test_data,
)
```
#### Make prediction
```python
image = keras.utils.load_img("800px-Pizza_Margherita_stu_spivack.jpg", target_size=(224, 224))
image_array = keras.utils.img_to_array(image)
image_array = image_array / 255
image_array = tf.expand_dims(image_array, axis=0)
prediction = model_4.predict(image_array)
prediction, class_names[int(prediction > 0.5)]
```

[[Tensor Flow]]
[[ML]]