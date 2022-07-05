# im2rec-tutorial

(Reference: [MXNet blog](https://arthurcaillau.com/image-record-iter/), [GLUON](https://cv.gluon.ai/build/examples_datasets/recordio.html))

There are two input image formats supported by SageMaker Image Classification Algorithm: **RecordIO Format** and **Image Format**. The recommended input format for the Amazon SageMaker image classification algorithms is Apache MXNet [RecordIO](https://mxnet.apache.org/versions/1.9.1/api/faq/recordio.html).

![image](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2019/02/21/identify-birds-2.gif)


## Running im2rec

To clone the MXNet repository. The im2rec.py is in tools folder.
```git
$ git clone https://github.com/apache/incubator-mxnet.git
```

Create mxnet opencv python environment
```conda
$ conda create -n clojure-mxnet mxnet opencv python=3
```

`im2rec` is used to first create a `.lst` file that will then be used to package the data in a binary format. The `.lst` file follows this format:
```
integer_image_index | label_index | path_to_image

417	8.000000	plastic_flexible\Plastic_Flexible-24-05-2022-17041653383097h.jpg
483	9.000000	plastic_single_use\Plastic_single_use-24-05-2022-19251653391500h.jpg
```

- prefix: prefix of input/output lst and rec files
- root: path to folder containing images
```bash
  python $MXNET_HOME/tools/im2rec.py \
    --list \
    --train-ratio 0.8 \
    --recursive \
    $data_path/data (prefix) $data_path (root)

  python $MXNET_HOME/tools/im2rec.py \
    --resize 224 \
    --center-crop \
    --num-thread 4 \
    $data_path/data $data_path
```

```
$ MXNET_HOME/tools/im2rec.py --help
usage: im2rec.py [-h] [--list] [--exts EXTS [EXTS ...]] [--chunks CHUNKS]
                 [--train-ratio TRAIN_RATIO] [--test-ratio TEST_RATIO]
                 [--recursive] [--no-shuffle] [--pass-through]
                 [--resize RESIZE] [--center-crop] [--quality QUALITY]
                 [--num-thread NUM_THREAD] [--color {-1,0,1}]
                 [--encoding {.jpg,.png}] [--pack-label]
                 prefix root

Create an image list or make a record database by reading from an image list

positional arguments:
  prefix                prefix of input/output lst and rec files.
  root                  path to folder containing images.

optional arguments:
  -h, --help            show this help message and exit

Options for creating image lists:
  --list                If this is set im2rec will create image list(s) by
                        traversing root folder and output to <prefix>.lst.
                        Otherwise im2rec will read <prefix>.lst and create a
                        database at <prefix>.rec (default: False)
  --exts EXTS [EXTS ...]
                        list of acceptable image extensions. (default:
                        ['.jpeg', '.jpg', '.png'])
  --chunks CHUNKS       number of chunks. (default: 1)
  --train-ratio TRAIN_RATIO
                        Ratio of images to use for training. (default: 1.0)
  --test-ratio TEST_RATIO
                        Ratio of images to use for testing. (default: 0)
  --recursive           If true recursively walk through subdirs and assign an
                        unique label to images in each folder. Otherwise only
                        include images in the root folder and give them label
                        0. (default: False)
  --no-shuffle          If this is passed, im2rec will not randomize the image
                        order in <prefix>.lst (default: True)

Options for creating database:
  --pass-through        whether to skip transformation and save image as is
                        (default: False)
  --resize RESIZE       resize the shorter edge of image to the newsize,
                        original images will be packed by default. (default:
                        0)
  --center-crop         specify whether to crop the center image to make it
                        rectangular. (default: False)
  --quality QUALITY     JPEG quality for encoding, 1-100; or PNG compression
                        for encoding, 1-9 (default: 95)
  --num-thread NUM_THREAD
                        number of thread to use for encoding. order of images
                        will be different from the input list if >1. the input
                        list will be modified to match the resulting order.
                        (default: 1)
  --color {-1,0,1}      specify the color mode of the loaded image. 1: Loads a
                        color image. Any transparency of image will be
                        neglected. It is the default flag. 0: Loads image in
                        grayscale mode. -1:Loads image as such including alpha
                        channel. (default: 1)
  --encoding {.jpg,.png}
                        specify the encoding of the images. (default: .jpg)
  --pack-label          Whether to also pack multi dimensional label in the
```

## AWS Image Classification

Pretrained models can use only a fixed 224 x 224 image size. Typical image dimensions for image classification are '3, 224, 224'. This is similar to the ImageNet dataset.

**Training** and **Validation** data channels are compulsory.
```
train_data = sagemaker.session.s3_input(
    s3train_path, 
    distribution='FullyReplicated', 
    content_type='application/x-recordio', 
    s3_data_type='S3Prefix'
)

validation_data = sagemaker.session.s3_input(
    s3validation_path, 
    distribution='FullyReplicated', 
    content_type='application/x-recordio', 
    s3_data_type='S3Prefix'
)

data_channels = {'train': train_data, 'validation': validation_data}
```
