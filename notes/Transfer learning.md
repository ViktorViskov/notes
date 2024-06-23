
#### Libs
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
DATA_DIR = './potatoes'
IMAGE_SIZE = 224
BACH_SIZE = 32
EPOCHS = 5
```
#### Data loading
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
    DATA_DIR + '/valid',
    image_size=(IMAGE_SIZE, IMAGE_SIZE),
    batch_size=BACH_SIZE,
    label_mode='int',
    seed=42
)

class_names = train_data.class_names
class_names
```
#### Show data from dataset
```python
def show_random_image(target_dir: str, classes: list[str]):
    plt.figure(figsize=(14, 10))
    for num in range(4):
        selected_class = np.random.choice(classes)
        plt.subplot(2, 2, num+1)
        images = os.listdir(f"{target_dir}/{selected_class}")
        random_image = np.random.choice(images)
        img = plt.imread(f"{target_dir}/{selected_class}/{random_image}")
        plt.title(f"{selected_class} {img.shape}")
        plt.axis("off")
        plt.imshow(img)

for path, dir_name, file_name in os.walk(DATA_DIR):
    print(f"{len(dir_name)} directories, {len(file_name)} images in", path, sep="\t")

show_random_image(DATA_DIR + "/train", class_names)
```
#### Normalizing
```python
normalization_layer = keras.layers.Rescaling(1./255)

train_data_norm = train_data.map(lambda x, y: (normalization_layer(x), y))
test_data_norm = test_data.map(lambda x, y: (normalization_layer(x), y))

x, y = next(iter(train_data_norm.take(1))) # Only for example
x, y
```

#### Load model from tensorflow hub function
```python
def load_model(model_url:str, num_classes: int=10):
    feature_extractor_layer = hub.KerasLayer(
	    model_url,
        trainable=False, 
        input_shape=(IMAGE_SIZE, IMAGE_SIZE, 3))
    return feature_extractor_layer
```
#### Load model from hub and compile
```python
core_model = load_model("https://www.kaggle.com/models/google/mobilenet-v3/TensorFlow2/large-100-224-feature-vector/1", num_classes=len(class_names))

model_1 = keras.Sequential([
	#core_model,
    keras.layers.Lambda(lambda x: core_model(x)), # if ValueError: Only instances of `keras.Layer` can be added to a Sequential model.
    keras.layers.Dense(10, activation='softmax')
])

model_1.compile(
    loss=keras.losses.SparseCategoricalCrossentropy(),
    optimizer=keras.optimizers.Adam(),
    metrics=['accuracy']
)

model_1.summary()
```
#### Show loss curves function
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
```
#### Show loss curves
```python
plot_loss_curves(history_1)
```

#### Evaluate validating random image
```python
def evaluate_random_image(target_dir: str, classes: list[str]):
    selected_class = np.random.choice(classes)
    images = os.listdir(f"{target_dir}/{selected_class}")
    random_image = np.random.choice(images)
    
    img = plt.imread(f"{target_dir}/{selected_class}/{random_image}")
    norm_img = tf.image.resize(img, [IMAGE_SIZE, IMAGE_SIZE]) / 255.0
    
    predicted = model_1.predict(tf.expand_dims(norm_img, axis=0))
    print(np.argmax(predicted), class_names[np.argmax(predicted)])
    
    #show image
    plt.imshow(img)
    plt.title(f"{selected_class} {np.max(predicted) * 100:.2f}%")
    plt.axis("off")

evaluate_random_image(DATA_DIR + "/valid", class_names)
```

[[Tensor Flow]]