1. Create a vector, scalar, matrix and tensor with values of your choosing using `tf.constant()`.
2. Find the shape, rank and size of the tensors you created in 1.
3. Create two tensors containing random values between 0 and 1 with shape `[5, 300]`.
4. Multiply the two tensors you created in 3 using matrix multiplication.
5. Multiply the two tensors you created in 3 using dot product.
6. Create a tensor with random values between 0 and 1 with shape `[224, 224, 3]`.
7. Find the min and max values of the tensor you created in 6 along the first axis.
8. Created a tensor with random values of shape `[1, 224, 224, 3]` then squeeze it to change the shape to `[224, 224, 3]`.
9. Create a tensor with shape `[10]` using your own choice of values, then find the index which has the maximum value.
10. One-hot encode the tensor you created in 9.

### 📖 00. TensorFlow Fundamentals Extra-curriculum

[](https://github.com/mrdbourke/tensorflow-deep-learning#-00-tensorflow-fundamentals-extra-curriculum)

- Read through the [list of TensorFlow Python APIs](https://www.tensorflow.org/api_docs/python/), pick one we haven't gone through in this notebook, reverse engineer it (write out the documentation code for yourself) and figure out what it does.
- Try to create a series of tensor functions to calculate your most recent grocery bill (it's okay if you don't use the names of the items, just the price in numerical form).
    - How would you calculate your grocery bill for the month and for the year using tensors?
- Go through the [TensorFlow 2.x quick start for beginners](https://www.tensorflow.org/tutorials/quickstart/beginner) tutorial (be sure to type out all of the code yourself, even if you don't understand it).
    - Are there any functions we used in here that match what's used in there? Which are the same? Which haven't you seen before?
- Watch the video ["What's a tensor?"](https://www.youtube.com/watch?v=f5liqUk0ZTw) - a great visual introduction to many of the concepts we've covered in this notebook.