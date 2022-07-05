# im2rec-tutorial

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

## AWS Image Classification

Pretrained models can use only a fixed 224 x 224 image size. Typical image dimensions for image classification are '3, 224, 224'. This is similar to the ImageNet dataset.
