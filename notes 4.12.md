#### Q1：train_cmvae中去打印img_recon的值返回的是Tensor("",shape=(32,64,64,3))，想要取出第一张数据。

```python
img_recon, gate_recon, means, stddev, z = model(img_gt, mode)
indices = tf.constant([[0]])
a = tf.gather_nd(params=img_recon, indices=indices)
print(a)
```

#### Q2: tensorboard起不了，不显示数据

* 在回调的时候创建文件夹：
```python
tf_callback = tf.keras.callbacks.TensorBoard(log_dir="./logs")
```

* 在命令行中，首先进入logs所在位置的上级目录

```latex
cd tensorflow2.0
```

* 再执行tensorboard命令

```latex
tensorboard --logdir=logs  
```

* 最后注意在谷歌浏览器中去访问接口

![image-20230412143546082](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230412143546082.png)

启动成功。

#### Q3：eager tensor 和 symbolic tensor的区别和数据解决方法，其在数据输出的表现为，tf.Tensor(xxx)和Tensor(xxx)

* EagerTensor有`numpy()`属性， 而Tensor则没有。所以静态图运行下的Tensor无法通过numpy()获取数值。

* **tf.print()**: tf.print(tensor)能够无论在动态图还是静态图下都能够打印出张量的内容，即既可以`tf.print(tensor)`，也可以`tf.print(EagerTensor)`。

* **print()**: 而`print(EagerTensor.numpy())`只能在动态图下即Eager Execution使用，而且只能够对EagerTensor使用。
* EagerTensor可以直接与python代码进行交互的，也可以进行迭代遍历操作，而Tensor则不支持与Python直接进行交互。

#### Q4：@tf.function 下面的计算值为中间值，在计算图中，所以直接在函数中去输出是有问题的，可以让函数具有返回值，在主函数中去打印输出操作。
```python
@tf.function
def train(img_gt, gate_gt, epoch, mode):
    # freeze the non-utilized weights
    if mode == 0:
        model.q_img.trainable = True
        model.p_img.trainable = True
        # model.p_gate.trainable = True
    elif mode == 1:
        model.q_img.trainable = True
        model.p_img.trainable = True
        # model.p_gate.trainable = False
    elif mode == 2:
        model.q_img.trainable = True
        model.p_img.trainable = False
        # model.p_gate.trainable = True
    with tf.GradientTape() as tape:
        img_recon, gate_recon, means, stddev, z = model(img_gt, mode)
        # result=img_recon.eval()
        img_loss, gate_loss, kl_loss = compute_loss_unsupervised(img_gt, gate_gt, img_recon, gate_recon, means, stddev,
                                                                 mode)
        img_loss = tf.reduce_mean(img_loss)
        gate_loss = tf.reduce_mean(gate_loss)
        beta, w_img, w_gate = regulate_weights(epoch)
        # weighted_loss_img = calc_weighted_loss_img(img_recon, img_gt)
        if mode == 0:
            total_loss = w_img * img_loss + w_gate * gate_loss + beta * kl_loss
            # total_loss = w_img * img_loss + beta * kl_loss
            # total_loss = weighted_loss_img + gate_loss + beta * kl_loss
            # total_loss = img_loss
            train_loss_rec_img.update_state(img_loss)
            train_loss_rec_gate.update_state(gate_loss)
            train_loss_kl.update_state(kl_loss)
        # TODO: later create structure for other training modes -- for now just training everything together
        elif mode == 1:
            total_loss = img_loss + beta * kl_loss
            # train_kl_loss_m1(kl_loss)
        elif mode == 2:
            total_loss = gate_loss + beta * kl_loss
    gradients = tape.gradient(total_loss, model.trainable_variables)
    optimizer.apply_gradients(zip(gradients, model.trainable_variables))
    # indices = tf.constant([[0]])
    # a = tf.gather_nd(params=img_recon, indices=indices)
    # tf.print(a)
    return img_recon
```

```python
for epoch in range(epochs):
    # print('MODE NOW: {}'.format(mode))
    for train_images, train_labels in train_ds:
        train(train_images, train_labels, epoch, mode)
        if epoch == 49:
            result = train(train_images, train_labels, epoch, mode)
```

![image-20230413093615495](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230413093615495.png)

再对返回的图像做相应的操作。

### Q5!!!!!:超级大问题，加载模型时注意模型文件的加载问题。.ckpt,.ckpt.index,.ckpt-data-000001-000002

* 注意加载模型文件时一定不要改动，弄清模型结构。
