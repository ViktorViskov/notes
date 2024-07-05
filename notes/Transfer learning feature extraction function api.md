#### Import libs
```python
import tensorflow as tf
import tensorflow_hub as hub
import numpy as np
import pandas as pd
import keras
import matplotlib.pyplot as plt
import os
```
#### Variables
```python
DATA_DIR = './10_food_classes_10_percent'
IMAGE_SIZE = 224
BACH_SIZE = 32
EPOCHS = 5
```
#### Load dataset
```python
tf.random.set_seed(42)

keras.utils.image_dataset_from_directory
train_data = keras.utils.image_dataset_from_directory(
    DATA_DIR + '/train',
    image_size=(IMAGE_SIZE, IMAGE_SIZE),
    batch_size=BACH_SIZE,
    label_mode='int',
    seed=42
)

test_data = keras.utils.image_dataset_from_directory(
    DATA_DIR + '/test',
    image_size=(IMAGE_SIZE, IMAGE_SIZE),
    batch_size=BACH_SIZE,
    label_mode='int',
    seed=42
)

class_names = train_data.class_names
class_names
```
#### Create and show model
```python
base_model = keras.applications.efficientnet_v2.EfficientNetV2B3(include_top=False)
base_model.trainable = False

inputs = keras.layers.Input(shape=(IMAGE_SIZE, IMAGE_SIZE, 3))

x = base_model(inputs)
x = keras.layers.GlobalAveragePooling2D()(x)

outputs = keras.layers.Dense(len(class_names), activation='softmax')(x)

model_1 = keras.Model(inputs, outputs)

model_1.compile(
    loss=keras.losses.SparseCategoricalCrossentropy(),
    optimizer=keras.optimizers.Adam(),
    metrics=['accuracy']
)

model_1.summary()
```
**==Model has preprocessing layers and it does not need data normalizing==**
#### Training
```python
history_1 = model_1.fit(
    train_data,
    epochs=EPOCHS,
    validation_data=test_data,
    validation_steps=int(0.1 * len(test_data)),
    callbacks=[create_tensorboard_callback('tensorboard', 'model_1')])
```
#### Show training history
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
#### Evaluate results
```python
model_mob.evaluate(test_data)
```