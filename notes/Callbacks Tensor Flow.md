### Save during training
#### For automatic save weight during training for best validation results
```python
checkpoint_path = "model_checkpoint.weights.h5"
checkpoint_callback = keras.callbacks.ModelCheckpoint(
    filepath=checkpoint_path,
    save_weights_only=True,
    save_best_only=True,
    monitor="val_accuracy",
    verbose=1
)
```

#### For load weights
```python
model_2.load_weights(checkpoint_path)
```