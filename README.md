# YoloV5-Robomaster

## Run the YoloV5 and train your own Dateset

### Python environment

In this project, We use python3.9 interpreter at local system without using virtual python environment or Anaconda environment.

YoloV5 can be downloaded from `git@github.com:ultralytics/yolov5.git` and make sure that you have downloaded the package in `requirements.txt`.

```commandline
pip install -r requirements.txt
```

After then, you can open the terminal at YoloV5 direction and run `detect.py`

```commandline
python detects.py
```

Then open the direction `runs->detect` and open the dome image to see whether your 
Yolo has been configured well. 

### Prepare the Dataset

#### Create a folder structure

The folder structure is shown below:

```text
--YoloV5
    ---Dataset
        ---images
           ----train
           ----val
        ---labels
           ----train
           ----val
```

In the `images` folder, all the `.jpeg` should be saved in it. Into the `images` folder, 
`train` is for the training images and `val` is for the validation images. When you label the image,
 remember to select the `SaveLabelDirection` in `\DateSet\labels\train` or `\DateSet\labels\val`.

#### Label Image

You can find a project called `labelImg` in `git@github.com:heartexlabs/labelImg.git`.
If you wait to train your own data with your own labels, pay attention to the file `/labelImg/data/predefined_classes.txt`. 
You may delete the content and replace it with your own label. Then open the terminal under the `labelImg` direction and run

```commandline
sudo apt-get install pyqt5-dev-tools
sudo pip3 install -r requirements/requirements-linux-python3.txt
make qt5py3
python3 labelImg.py
```

After you finish all the labeling work. You can find your label folder is full of `imagename.txt`and one `class.txt`.

### Train and Detecting

#### Edit the .yaml

Open the fold `\YoloV5\data` and create a `yaml` file which you can use your own name. 
Here is an example:

```yaml
# Example usage: python train.py --data name.yaml
# parent
# └──yolov5
#    └──datasets
#       ├── images
#       └── labels



# Train/val/test sets as 1) dir: path/to/imgs, 2) file: path/to/imgs.txt, or 3) list: [path/to/imgs1, path/to/imgs2, ..]
path: .../Yolo-RoboMaster/YoloV5/DataSet  # dataset root dir
train: images/train  # train images (relative to 'path')
val: images/val  # val images (relative to 'path')
test:  # test images (optional)

# Classes
names:
  0: Bound_Red
  1: Bound_Blue  # class names
```

#### Configurate train.py and detect.py

##### For train.py

There are few places need to be noticed.

```python
# Config
    plots = not evolve and not opt.noplots 
    cuda = device.type != 'cpu'  # choose cpu or gpu

def parse_opt(known=False):
    parser = argparse.ArgumentParser()
    # choose the model which the program learns from: [yolov5s.pt/yolov5m.pt/yolov5l.pt/yolov5n.pt/yolov5x.pt]
    parser.add_argument('--weights', type=str, default=ROOT / 'yolov5s.pt', help='initial weights path')
    # Choose your .yaml file
    parser.add_argument('--data', type=str, default=ROOT / 'data/Bound.yaml', help='dataset.yaml path')
    # Set the number of epochs (recommend 100-250)
    parser.add_argument('--epochs', type=int, default=200, help='total training epochs')
    # cuda device, i.e. 0 or 0,1,2,3 or cpu
    parser.add_argument('--device', default='cpu', help='cuda device, i.e. 0 or 0,1,2,3 or cpu')
    # choose the output direction
    parser.add_argument('--project', default=ROOT / 'runs/train', help='save to project/name')
```

Then change the `.yaml` in `models` folder. If you choose to use `yolov5s.pt`, you should open `yolov5s.yaml`

```yaml
# Parameters
nc: 2  # number of classes, upon your own dataset's classes
```

Run `train.py`and a folder called `\train\exp+{num}` in `runs` folders will be created. Open the direction `YoloV5\runs\train\exp\weights\` 
and you can find two training result `best.pt` and `last.pt`, copy them in the direction `\YoloV5`

##### For detect.py

Copy your test images in `YoloV5/data/images`.

There are few places need to be noticed.

```python
def parse_opt():
    parser = argparse.ArgumentParser()
    # select .pt file
    parser.add_argument('--weights', nargs='+', type=str, default=ROOT / 'best.pt', help='model path or triton URL')
    # select the testing image
    parser.add_argument('--source', type=str, default=ROOT / 'data/images', help='file/dir/URL/glob/screen/0(webcam)')
    # select your .yaml
    parser.add_argument('--data', type=str, default=ROOT / 'data/Bound.yaml', help='(optional) dataset.yaml path')
    # select cuda device
    parser.add_argument('--device', default='cpu', help='cuda device, i.e. 0 or 0,1,2,3 or cpu')
    # select output direction
    parser.add_argument('--name', default='exp', help='save results to project/name')
    print_args(vars(opt))
    return opt
```

Then run it, the rusult can be seen in `YoloV5/runs/detect/exp/`