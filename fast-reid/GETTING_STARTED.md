# Getting Started with Fastreid

## Prepare pretrained model | 准备预训练模型

If you use backbones supported by fastreid, you do not need to do anything. It will automatically download the pre-train models.
But if your network is not connected, you can download pre-train models manually and put it in `~/.cache/torch/checkpoints`.

If you want to use other pre-train models, such as MoCo pre-train, you can download by yourself and set the pre-train model path in `configs/Base-bagtricks.yml`.

如果要使用诸如`MoCo pre-train`这样的第三方模型，需要手动在`configs/Base-bagtricks.yml`中设置路径.

如果使用的骨干网络是由本项目支持的则会自动下载预训练模型，支持的预训练模型要放在`~/.cache/torch/checkpoints`.

## Compile with cython to accelerate evalution | 利用cython编译以加速评估

```bash
cd fastreid/evaluation/rank_cylib; make all
```

## Training & Evaluation in Command Line | 在命令行中评估&训练模型

We provide a script in "tools/train_net.py", that is made to train all the configs provided in fastreid.
You may want to use it as a reference to write your own training script.

To train a model with "train_net.py", first setup up the corresponding datasets following [datasets/README.md](https://github.com/JDAI-CV/fast-reid/tree/master/datasets), then run:

我们在`tools/train_net.py`中提供了训练本项目中各种设置的脚本，您可以以此为参考写出您自己的脚本.

要用这个脚本(`train_net.py`)训练模型，首先要遵循[datasets/README.md](https://github.com/JDAI-CV/fast-reid/tree/master/datasets)设定相应的数据集,再执行下方的脚本。

```bash
python3 tools/train_net.py --config-file ./configs/Market1501/bagtricks_R50.yml MODEL.DEVICE "cuda:0"
```

The configs are made for 1-GPU training.

If you want to train model with 4 GPUs, you can run:

该设置是为单卡训练准备的。

如果你想用4块卡训练模型，您可以执行：

```bash
python3 tools/train_net.py --config-file ./configs/Market1501/bagtricks_R50.yml --num-gpus 4
```

If you want to train model with multiple machines, you can run:

如果您想多台机器一起训练，您可以执行：

```
# machine 1
export GLOO_SOCKET_IFNAME=eth0
export NCCL_SOCKET_IFNAME=eth0

python3 tools/train_net.py --config-file configs/Market1501/bagtricks_R50.yml \
--num-gpus 4 --num-machines 2 --machine-rank 0 --dist-url tcp://ip:port 

# machine 2
export GLOO_SOCKET_IFNAME=eth0
export NCCL_SOCKET_IFNAME=eth0

python3 tools/train_net.py --config-file configs/Market1501/bagtricks_R50.yml \
--num-gpus 4 --num-machines 2 --machine-rank 1 --dist-url tcp://ip:port 
```

Make sure the dataset path and code are the same in different machines, and machines can communicate with each other. 

To evaluate a model's performance, use

请确保机器间能与彼此交流且每台机器上的数据集路径与代码都一致。

要评估一个模型的表现，使用：

```bash
python3 tools/train_net.py --config-file ./configs/Market1501/bagtricks_R50.yml --eval-only \
MODEL.WEIGHTS /path/to/checkpoint_file MODEL.DEVICE "cuda:0"
```

For more options, see `python3 tools/train_net.py -h`.

更多选项在`python3 tools/train_net.py -h`中。