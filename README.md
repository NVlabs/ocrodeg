
# ocrodeg

The `ocrodeg` package is a small Python library implementing document image degradation for data augmentation for handwriting recognition and OCR applications.

The following illustrates the kinds of degradations available from `ocrodeg`.


```python
%pylab inline
```

    Populating the interactive namespace from numpy and matplotlib



```python
rc("image", cmap="gray", interpolation="bicubic")
figsize(10, 10)
import scipy.ndimage as ndi
import ocrodeg

image = imread("testdata/W1P0.png")
imshow(image)
```




    <matplotlib.image.AxesImage at 0x7f06e26352d0>




![png](README_files/README_2_1.png)


# PAGE ROTATION


```python
for i, angle in enumerate([0, 90, 180, 270]):
    subplot(2, 2, i+1)
    imshow(ndi.rotate(image, angle))
```


![png](README_files/README_4_0.png)


# RANDOM GEOMETRIC TRANSFORMATIONS


```python
for i, angle in enumerate([-2, -1, 0, 1]):
    subplot(2, 2, i+1)
    imshow(ocrodeg.transform_image(image, angle=angle*pi/180))
```


![png](README_files/README_6_0.png)



```python
for i, angle in enumerate([-2, -1, 0, 1]):
    subplot(2, 2, i+1)
    imshow(ocrodeg.transform_image(image, angle=angle*pi/180)[1000:1500, 750:1250])
```


![png](README_files/README_7_0.png)



```python
for i, aniso in enumerate([0.5, 1.0, 1.5, 2.0]):
    subplot(2, 2, i+1)
    imshow(ocrodeg.transform_image(image, aniso=aniso))
```


![png](README_files/README_8_0.png)



```python
for i, aniso in enumerate([0.5, 1.0, 1.5, 2.0]):
    subplot(2, 2, i+1)
    imshow(ocrodeg.transform_image(image, aniso=aniso)[1000:1500, 750:1250])
```


![png](README_files/README_9_0.png)



```python
for i, scale in enumerate([0.5, 0.9, 1.0, 2.0]):
    subplot(2, 2, i+1)
    imshow(ocrodeg.transform_image(image, scale=scale))
```


![png](README_files/README_10_0.png)



```python
for i, scale in enumerate([0.5, 0.9, 1.0, 2.0]):
    subplot(2, 2, i+1)
    h, w = image.shape
    imshow(ocrodeg.transform_image(image, scale=scale)[h//2-200:h//2+200, w//3-200:w//3+200])
```


![png](README_files/README_11_0.png)


# RANDOM DISTORTIONS


```python
for i, sigma in enumerate([1.0, 2.0, 5.0, 20.0]):
    subplot(2, 2, i+1)
    noise = ocrodeg.bounded_gaussian_noise(image.shape, sigma, 5.0)
    distorted = ocrodeg.distort_with_noise(image, noise)
    h, w = image.shape
    imshow(distorted[h//2-200:h//2+200, w//3-200:w//3+200])
```


![png](README_files/README_13_0.png)


# RULED SURFACE DISTORTIONS


```python
for i, mag in enumerate([5.0, 20.0, 100.0, 200.0]):
    subplot(2, 2, i+1)
    noise = ocrodeg.noise_distort1d(image.shape, magnitude=mag)
    distorted = ocrodeg.distort_with_noise(image, noise)
    h, w = image.shape
    imshow(distorted[:1500])
```


![png](README_files/README_15_0.png)


# BLUR, THRESHOLDING, NOISE


```python
patch = image[1900:2156, 1000:1256]
imshow(patch)
```




    <matplotlib.image.AxesImage at 0x7f06daae51d0>




![png](README_files/README_17_1.png)



```python
for i, s in enumerate([0, 1, 2, 4]):
    subplot(2, 2, i+1)
    blurred = ndi.gaussian_filter(patch, s)
    imshow(blurred)
```


![png](README_files/README_18_0.png)



```python
for i, s in enumerate([0, 1, 2, 4]):
    subplot(2, 2, i+1)
    blurred = ndi.gaussian_filter(patch, s)
    thresholded = 1.0*(blurred>0.5)
    imshow(thresholded)
```


![png](README_files/README_19_0.png)



```python
reload(ocrodeg)
for i, s in enumerate([0.0, 1.0, 2.0, 4.0]):
    subplot(2, 2, i+1)
    blurred = ocrodeg.binary_blur(patch, s)
    imshow(blurred)

```


![png](README_files/README_20_0.png)



```python
for i, s in enumerate([0.0, 0.1, 0.2, 0.3]):
    subplot(2, 2, i+1)
    blurred = ocrodeg.binary_blur(patch, 2.0, noise=s)
    imshow(blurred)
```


![png](README_files/README_21_0.png)


# MULTISCALE NOISE


```python
reload(ocrodeg)
for i in range(4):
    noisy = ocrodeg.make_multiscale_noise_uniform((512, 512))
    subplot(2, 2, i+1); imshow(noisy, vmin=0, vmax=1)
```


![png](README_files/README_23_0.png)


# RANDOM BLOBS


```python
for i, s in enumerate([2, 5, 10, 20]):
    subplot(2, 2, i+1)
    imshow(ocrodeg.random_blobs(patch.shape, 3e-4, s))
```


![png](README_files/README_25_0.png)



```python
reload(ocrodeg)
blotched = ocrodeg.random_blotches(patch, 3e-4, 1e-4)
#blotched = minimum(maximum(patch, ocrodeg.random_blobs(patch.shape, 30, 10)), 1-ocrodeg.random_blobs(patch.shape, 15, 8))
subplot(121); imshow(patch); subplot(122); imshow(blotched)
```




    <matplotlib.image.AxesImage at 0x7f06dab07ad0>




![png](README_files/README_26_1.png)


# FIBROUS NOISE


```python
imshow(ocrodeg.make_fibrous_image((256, 256), 700, 300, 0.01))
```




    <matplotlib.image.AxesImage at 0x7f06da6953d0>




![png](README_files/README_28_1.png)


# FOREGROUND / BACKGROUND SELECTION


```python
subplot(121); imshow(patch); subplot(122); imshow(ocrodeg.printlike_multiscale(patch))
```




    <matplotlib.image.AxesImage at 0x7f06daa4c790>




![png](README_files/README_30_1.png)



```python
subplot(121); imshow(patch); subplot(122); imshow(ocrodeg.printlike_fibrous(patch))
```




    <matplotlib.image.AxesImage at 0x7f06dab42ed0>




![png](README_files/README_31_1.png)

