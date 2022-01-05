# A simple Unet3d implementation with Torchio

## Unet3D
[Unet](https://arxiv.org/abs/1505.04597) is a classical medicial image segmentation model.
Personally, I think there is no need to re-introduce this great structure in this repo.

## Torchio
[Torchio](https://torchio.readthedocs.io/), relatively less popular compared with Unet, is an SITK based medical image processing library.
In fact, this repo is a reproduced implementation of the original Torchio tutorial project.

Basically, I did not make any great changes but only ameliorated the prediction(inference) part of the original code.
The reason why I think these changes are essential is that the original tutorial does not return a perfect mask file because the spacing, the orientation and the size of the original prediction are all different from the input image.

## How to use
Come on, you know how to train and predict using a deep learning model.

To start training, 
```shell
python train.py
```
You can modify the training parameters as you wish. 
I have tried my best to use variables rather than direct constants in my code, so you should be able to easily find the parameters you want to modify.

I have also avoided using absolute paths in the code, so there shouldn't be any problem even if you run this code on your own device.

To predict,
```shell
python pred.py
```
In fact, I have only inserted the following part into the original code:
```python
spatial_transform = tio.Compose([
        tio.ToCanonical(),
        tio.Resample(4)
])
resample_shape = spatial_transform(tio.ScalarImage(img_file)).shape[-3:]

prediction = tio.CropOrPad(resample_shape)(prediction)
prediction = tio.Resample(img_file, image_interpolation='nearest')(prediction)
```
so that the `prediction` can have the same spacing as the input image.


It is worth noting that this is not a generic method of restoration. 
Because the transforms you use may be completely different for different datasets. 
The current restoration only applies to the current transforms.

I have tried to use the official `apply_inverse_transform()` method but the `ToCanonical()` and `Resample()` are irreversible, so I have to use this compromised solution.

If you have any better idea, do not hesitate to share it!