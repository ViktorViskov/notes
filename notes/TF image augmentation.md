### Simple example for image augmentation befor training
```python
data_augmentation = keras.Sequential([
    keras.layers.Rescaling(1./255), # dont need to use in imagenet
    keras.layers.RandomRotation(0.2), # rotate the image between 0 and 20 deg
    keras.layers.RandomTranslation(0.2, 0.2), # shift image width and height way 
    keras.layers.RandomZoom(0.2), # zoom into the image
    keras.layers.RandomZoom(height_factor=(-0.2, 0.2), width_factor=(0.0, 0.0)), 
    keras.layers.RandomZoom(height_factor=(0.0, 0.0), width_factor=(-0.2, 0.2)), 
    keras.layers.RandomFlip('horizontal_and_vertical'), # flip the image
])
```

```python
# Step 3: Apply the rescaling and augmentation
train_data_augmented = train_data.map(lambda x, y: (data_augmentation(x, training=True), y))
```