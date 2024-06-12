Here is example how to define best learning rate
```python
model_1 = keras.Sequential([
    keras.layers.Dense(16, activation="relu"),
    keras.layers.Dense(4, activation="relu"),
    keras.layers.Dense(1, activation="sigmoid")
])

model_1.compile(
    loss=keras.losses.BinaryCrossentropy(),
    optimizer=keras.optimizers.Adam(learning_rate=0.01),
    metrics=["accuracy"]
)

# Create a learning rate scheduler callback
lr_scheduler = keras.callbacks.LearningRateScheduler(lambda epoch: 1e-4 * 10**(epoch/25))

history_1 = model_1.fit(x_train, y_train, epochs=100, callbacks=[lr_scheduler])
```

### Show history
```python
pd.DataFrame(history_1.history).plot(title="Model 1 loss and accuracy", xlabel="epochs", ylabel="loss/accuracy", figsize=(16, 7), grid=True)
```
![[Pasted image 20240612194058.png]]
### Show special for learn rate
```python
lrs = 1e-4 * (10 ** (np.arange(100)/25))
plt.figure(figsize=(16, 7))
plt.semilogx(lrs, history_1.history["loss"]) # we want the x-axis (learning rate) to be log scale
plt.xlabel("Learning Rate")
plt.ylabel("Loss")
plt.title("Learning rate vs. loss")
plt.grid(True)
```
![[Pasted image 20240612194730.png]]
==**BEST LEARNING RATE BEFORE INCREASING==**

[[Tensor Flow]]
[[ML]]