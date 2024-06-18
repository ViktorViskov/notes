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
import os
```
### Data loading
#### Variables
```python
data_dir = "10_food_classes_all_data"

train_data_dir = data_dir + "/train"
test_data_dir = data_dir + "/test"

class_names = np.array(sorted(os.listdir(train_data_dir)))
```
#### Read dataset
```python
tf.random.set_seed(42)

keras.utils.image_dataset_from_directory
train_data = keras.utils.image_dataset_from_directory(
    train_data_dir,
    image_size=(224, 224),
    batch_size=32,
    label_mode='int',
    seed=42
)

test_data = keras.utils.image_dataset_from_directory(
    test_data_dir,
    image_size=(224, 224),
    batch_size=32,
    label_mode="int",
    seed=42
)
```
#### Normalizing
```python
normalization_layer = keras.layers.Rescaling(1./255)

train_data_norm = train_data.map(lambda x, y: (normalization_layer(x), y))
test_data_norm = test_data.map(lambda x, y: (normalization_layer(x), y))

x, y = next(iter(train_data_norm.take(1))) # Only for example
```
#### Augmentation
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
### Show dataset
```python
for path, dir_name, file_name in os.walk(data_dir):
    print(f"{len(dir_name)} directories, {len(file_name)} images in", path, sep="\t")

def show_random_image(target_dir: str, target_class: str):
    images = os.listdir(f"{target_dir}/{target_class}")
    random_image = np.random.choice(images)
    img = plt.imread(f"{target_dir}/{target_class}/{random_image}")
    plt.title(f"{target_class} {img.shape}")
    plt.axis("off")
    plt.imshow(img)

show_random_image(train_data_dir, np.random.choice(class_names))
```

#### Model
```python
model_1 = keras.Sequential([
    keras.layers.Conv2D(10, 3, activation="relu", input_shape=(224, 224, 3)),
    keras.layers.Conv2D(10, 3, activation="relu"),
    keras.layers.MaxPool2D(),
    keras.layers.Conv2D(10, 3, activation="relu"),
    keras.layers.Conv2D(10, 3, activation="relu"),
    keras.layers.MaxPool2D(),
    keras.layers.Flatten(),
    keras.layers.Dense(10, activation="softmax")
])

model_1.compile(
    loss=keras.losses.SparseCategoricalCrossentropy(),
    optimizer=keras.optimizers.Adam(),
    metrics=["accuracy"]
)

history_1 = model_1.fit(train_data_norm, epochs=5, validation_data=test_data_norm)  
```
### Evaluate learning
```python
def plot_loss_curves(history):
    loss = history.history["loss"]
    val_loss = history.history["val_loss"]

    accuracy = history.history["accuracy"]
    val_accuracy = history.history["val_accuracy"]

    plt.figure(figsize=(14, 7))
    plt.subplot(1, 2, 1)
    plt.plot(loss, label="loss")
    plt.plot(val_loss, label="val_loss")
    plt.title("Loss")
    plt.xlabel("epochs")
    plt.ylabel("loss")
    plt.grid(True)
    plt.legend()

    plt.subplot(1, 2, 2)
    plt.plot(accuracy, label="accuracy")
    plt.plot(val_accuracy, label="val_accuracy")
    plt.title("Accuracy")
    plt.xlabel("epochs")
    plt.ylabel("accuracy")
    plt.grid(True)
    plt.legend()

plot_loss_curves(history_1)
```