# Session - 3

## Assignment

<br>

# DVC Installation and Set-up

```
!pip install dvc
```

Setting up DVC with your ML Project
```
git init .
dvc init
```
Add data folder for dvc
```
git rm -r --cached 'data'
dvc add data
```
We first have to remove data folder from being tracked by git and then let dvc take care of it
```
git add .
dvc config core.autostage true
```
**autostage**: if enabled, DVC will automatically stage (git add) DVC files created or modified by DVC commands.

Add a remote
```
dvc remote add gdrive gdrive://<google-drive-folder-id>
dvc push -r gdrive
```
This will now push all the files inside data folder to google drive

<br>

# DVC pipeline

create a `dvc.yaml`

```
stages:
  train-mnist:
    cmd: python3 src/train.py experiment=mnist
    deps:
      - data/MNIST
```

To run this pipeline
```
dvc repro train-mnist
```

<br>

# Hyper Parameters Tuning

## Optuna Optimizer

Without Optuna, Simple Hyper Param Grid Search
```
python3 src/train.py -m experiment=example datamodule.batch_size=16,32,64,128 tags=["batch_size_exp"]
```
 
With Optuna
```
python3 src/train.py -m hparams_search=mnist_optuna
``` 

Let’s take a look at `mnist_optuna.yaml`
```
# @package _global_

# example hyperparameter optimization of some experiment with Optuna:
# python train.py -m hparams_search=mnist_optuna experiment=example

defaults:
  - override /hydra/sweeper: optuna

# choose metric which will be optimized by Optuna
# make sure this is the correct name of some metric logged in lightning module!
optimized_metric: "val/acc_best"

# here we define Optuna hyperparameter search
# it optimizes for value returned from function with @hydra.main decorator
# docs: <https://hydra.cc/docs/next/plugins/optuna_sweeper>
hydra:
  mode: "MULTIRUN" # set hydra to multirun by default if this config is attached

  sweeper:
    _target_: hydra_plugins.hydra_optuna_sweeper.optuna_sweeper.OptunaSweeper

    # storage URL to persist optimization results
    # for example, you can use SQLite if you set 'sqlite:///example.db'
    storage: null

    # name of the study to persist optimization results
    study_name: null

    # number of parallel workers
    n_jobs: 1

    # 'minimize' or 'maximize' the objective
    direction: maximize

    # total number of runs that will be executed
    n_trials: 20

    # choose Optuna hyperparameter sampler
    # you can choose bayesian sampler (tpe), random search (without optimization), grid sampler, and others
    # docs: <https://optuna.readthedocs.io/en/stable/reference/samplers.html>
    sampler:
      _target_: optuna.samplers.TPESampler
      seed: 1234
      n_startup_trials: 10 # number of random sampling runs before optimization starts

    # define hyperparameter search space
    params:
      model.optimizer.lr: interval(0.0001, 0.1)
      datamodule.batch_size: choice(32, 64, 128, 256)
      model.net.lin1_size: choice(64, 128, 256)
      model.net.lin2_size: choice(64, 128, 256)
      model.net.lin3_size: choice(32, 64, 128, 256)
```

<br>

# Logging in PyTorch Lightning

- CometLogger - Track your parameters, metrics, source code and more using Comet.

- CSVLogger - Log to local file system in yaml and CSV format.

- MLFlowLogger - Log using MLflow.

- NeptuneLogger - Log using Neptune.

- TensorBoardLogger - Log to local file system in TensorBoard format.

- WandbLogger - Log using Weights and Biases.

- Remote Logging with PyTorch Lightning: https://pytorch-lightning.readthedocs.io/en/stable/common/remote_fs.html

<br>

Add this in your experiment yaml file
```
- override /logger: tensorboard.yaml
```
 

To Log to multiple logger we can do

```
- override /logger: many_loggers.yaml
```

Now we can see the logs with
```
tensorboard --bind_all --logdir logs/
```

And MLFlow logs with
```
mlflow ui
```