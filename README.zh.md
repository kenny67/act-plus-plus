# Imitation Learning algorithms and Co-training for Mobile ALOHA

# Mobile ALOHA 的模仿学习算法和协同训练

#### Project Website: https://mobile-aloha.github.io/

This repo contains the implementation of ACT, Diffusion Policy and VINN, together with 2 simulated environments:
Transfer Cube and Bimanual Insertion. You can train and evaluate them in sim or real.
For real, you would also need to install [Mobile ALOHA](https://github.com/MarkFzp/mobile-aloha). This repo is forked from the [ACT repo](https://github.com/tonyzhaozh/act).

该仓库包含 ACT、Diffusion Policy 和 VINN 的实现，

以及 2 个模拟环境：

Transfer Cube (移动物体)和 Bimanual Insertion（双手操控穿插交互）。您可以模拟或真实地训练和评估它们。实际上，您还需要安装[Mobile ALOHA](https://github.com/MarkFzp/mobile-aloha)。该存储库是从[ACT 存储库](https://github.com/tonyzhaozh/act)Fork分叉出来的。

### Updates:

You can find all scripted/human demo for simulated environments [here](https://drive.google.com/drive/folders/1gPR03v05S1xiInoVJn7G7VJ9pDCnxq9O?usp=share_link).

[您可以在此处](https://drive.google.com/drive/folders/1gPR03v05S1xiInoVJn7G7VJ9pDCnxq9O?usp=share_link)找到模拟环境的所有脚本/人工演示。运行环境之类的。

### Repo Structure

- ``imitate_episodes.py`` Train and Evaluate ACT
  
   脚本功能： 训练和评估 ACT

- ``policy.py`` An adaptor for ACT policy
  
  功能： 策略的适配器

- ``detr`` Model definitions of ACT, modified from DETR
  
  功能： ACT 的模型定义，修改自 DETR

- ``sim_env.py`` Mujoco + DM_Control environments with joint space control
  功能： 具有联合空间控制的 Mujoco + DM_Control 环境

- ``ee_sim_env.py`` Mujoco + DM_Control environments with EE space control
  功能： 具有 EE 空间控制的 Mujoco + DM_Control 环境

- ``scripted_policy.py`` Scripted policies for sim environments
  功能： 模拟环境的脚本化策略

- ``constants.py`` Constants shared across files
  功能： 跨文件共享的常量

- ``utils.py`` Utils such as data loading and helper functions
  功能： 数据加载和辅助函数等实用程序

- ``visualize_episodes.py`` Save videos from a .hdf5 dataset
  功能： 保存 .hdf5 为扩展名的视频文件， 里面是 HDF5数据集

### Installation 安装的PYTHON模块

    conda create -n aloha python=3.8.10
    conda activate aloha
    pip install torchvision
    pip install torch
    pip install pyquaternion
    pip install pyyaml
    pip install rospkg
    pip install pexpect
    pip install mujoco==2.3.7
    pip install dm_control==1.0.14
    pip install opencv-python
    pip install matplotlib
    pip install einops
    pip install packaging
    pip install h5py
    pip install ipython
    cd act/detr && pip install -e .

- also need to install https://github.com/ARISE-Initiative/robomimic/tree/r2d2 (note the r2d2 branch) for Diffusion Policy by `pip install -e .`
  
  还需要安装[GitHub - ARISE-Initiative/robomimic at r2d2](https://github.com/ARISE-Initiative/robomimic/tree/r2d2)（BRANCH分支，注意是 r2d2 分支）以进行扩散策略`pip install -e .`
  
  扩散策略的封装

### Example Usages

To set up a new terminal, run:

要设置新终端，请运行：

    conda activate aloha
    cd <path to act repo>

### Simulated experiments (LEGACY table-top ALOHA environments)

### 拟实验（LEGACY 桌面 ALOHA 环境）

We use ``sim_transfer_cube_scripted`` task in the examples below. 

我们在下面的示例中使用该脚本sim_transfer_cube_scripted执行。

Another option is ``sim_insertion_scripted``.

另一种选择是执行 sim_insertion_scripted脚本。

To generated 50 episodes of scripted data, run:

要生成 50 集脚本数据，请运行：

    python3 record_sim_episodes.py --task_name sim_transfer_cube_scripted --dataset_dir <data save dir> --num_episodes 50

To can add the flag ``--onscreen_render`` to see real-time rendering.
To visualize the simulated episodes after it is collected, run

可以添加选项flag`--onscreen_render`来查看实时渲染。要在收集后可视化模拟事件，请运行

    python3 visualize_episodes.py --dataset_dir <data save dir> --episode_idx 0

Note: to visualize data from the mobile-aloha hardware, use the visualize_episodes.py from https://github.com/MarkFzp/mobile-aloha

[注意：把数据进行可视化，通过项目中的硬件配置， 通过调用脚本  visualize_episodes.py

To train ACT: 

训练 ACT

```训练
# Transfer Cube task
python3 imitate_episodes.py --task_name sim_transfer_cube_scripted --ckpt_dir <ckpt dir> --policy_class ACT --kl_weight 10 --chunk_size 100 --hidden_dim 512 --batch_size 8 --dim_feedforward 3200 --num_epochs 2000  --lr 1e-5 --seed 0
```

To evaluate the policy, run the same command but add ``--eval``. This loads the best validation checkpoint.

要评估策略，请运行相同的命令，但添加`--eval`. 这会加载最佳验证检查点。

The success rate should be around 90% for transfer cube, and around 50% for insertion.

转移物体的成功率应在 90% 左右，插入立方体的成功率应在 50% 左右。

To enable temporal ensembling, add flag ``--temporal_agg``.

要启用时间集成，请添加 flag `--temporal_agg`。

Videos will be saved to ``<ckpt_dir>`` for each rollout.
`<ckpt_dir>`每次推出时都会保存视频。

You can also add ``--onscreen_render`` to see real-time rendering during evaluation.

您还可以添加`--onscreen_render`以在评估期间查看实时渲染。

For real-world data where things can be harder to model, train for at least 5000 epochs or 3-4 times the length after the loss has plateaued.

对于难以建模的现实数据，训练至少 5000 个 epoch，或者在损失一定的稳定性，训练 3-4 倍的长度。

Please refer to [tuning tips](https://docs.google.com/document/d/1FVIZfoALXg_ZkYKaYVh-qOlaXveq5CtvJHXkY25eYhs/edit?usp=sharing) for more info.

请参阅[调整提示](https://docs.google.com/document/d/1FVIZfoALXg_ZkYKaYVh-qOlaXveq5CtvJHXkY25eYhs/edit?usp=sharing)以获取更多信息。

### [ACT tuning tips](https://docs.google.com/document/d/1FVIZfoALXg_ZkYKaYVh-qOlaXveq5CtvJHXkY25eYhs/edit?usp=sharing)

TL;DR: if your ACT policy is jerky or pauses in the middle of an episode, just train for longer! Success rate and smoothness can improve way after loss plateaus.

如果您的 ACT 策略不稳定或在训练场景中间暂停，请训练更长时间！成功率和平滑度，可以在损失一定的稳定性后得到改善。
