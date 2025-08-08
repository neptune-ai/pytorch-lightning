# Neptune Scale Logger for PyTorch Lightning

[![PyTorch Lightning](https://img.shields.io/badge/PyTorch%20Lightning-2.5.1rc2-blue)](https://lightning.ai/)
[![Neptune Scale](https://img.shields.io/badge/Neptune%20Scale-%3E%3D0.18.0-orange)](https://neptune.ai/)

This integration allows you to log your PyTorch Lightning training runs to Neptune Scale, providing seamless experiment tracking.

## Changelog

- **2025-08-11** - Updated to support `ddp-spawn` strategy
- **2025-07-25** - Updated to better support nested mappings and dataclasses in `log_hyperparameters()`
- **2025-06-27** - Initial release



## Installation

> [!WARNING] Prerequisites
> This integration works only with `neptune-scale>=0.18.0`

There are two ways this integration can be used before it is included in the official PyTorch Lightning release:

### Option 1: Install from source (recommended)

Install the custom version of `pytorch-lightning` from the source:

```bash
pip install git+https://github.com/neptune-ai/pytorch-lightning.git
```

### Option 2: Manual file replacement

In your `pytorch-lightning/src/lightning/pytorch/loggers` folder, replace the `__init__.py` and `neptune.py` files with the corresponding files available [here](https://github.com/SiddhantSadangi/pytorch-lightning/tree/master/src/lightning/pytorch/loggers)

> [!NOTE]
> The integration is built on top of `pytorch-lightning==2.5.1rc2`

## Quickstart

```python
from lightning.pytorch.loggers.neptune import NeptuneScaleLogger
from lightning.pytorch import Trainer

# Initialize the logger
neptune_logger = NeptuneScaleLogger()

# Use with your trainer
trainer = Trainer(
    ...,
    logger=neptune_logger,
)

# Your training will automatically log to Neptune Scale
trainer.fit(model, datamodule, ...)
```

## Usage

### 1. Import the logger

```python
from lightning.pytorch.loggers.neptune import NeptuneScaleLogger
```

### 2. Initialize NeptuneScaleLogger

```python
neptune_scale_logger = NeptuneScaleLogger()
```

> [!TIP]
> - Check the class signature and docstring for initialization args.
> - Check the `Run()` init params for additional `kwargs` that can be passed to `NeptuneScaleLogger()`: [Run | neptune.ai docs](https://docs.neptune.ai/run/#parameters)

### 3. Pass to Trainer

```python
trainer = Trainer(
    logger=neptune_scale_logger,
    # ... other trainer arguments
)
```

### 4. Use Lightning's logging methods

Use `self.log()` in your Lightning module as usual - everything will be automatically logged to Neptune Scale.

> [!WARNING] Checkpoint uploads
> - `NeptuneScaleLogger` logs the path to model checkpoints instead of uploading the checkpoints themselves to Neptune.
> - Paths to all checkpoints are logged irrespective of `save_last` and `save_top_k` `ModelCheckpoint()` parameters.

> [!NOTE] Accessing Neptune run
> To access the underlying Neptune run, you can use either `neptune_scale_logger.experiment` or `neptune_scale_logger.run` attributes. You can then use Neptune specific methods like `log_metrics()` and `log_configs()` directly on the underlying run, outside of the training loop.


> [!TIP] Examples   
> Refer to our example notebook to see how this works in practice: [Neptune + PyTorch Lightning Example](https://github.com/neptune-ai/scale-examples/blob/37e83854b31377b77e3d6dfae4ffeb39e7057510/integrations-and-supported-tools/pytorch-lightning/notebooks/Neptune_PyTorch_Lightning.ipynb)

## Updating the integration

Updates to this integration are merged to the `master` branch of the fork rather than being released.

### 1. Enable notifications

Turn on notifications for all activity to be notified when changes have been merged:

![GitHub Notifications](https://neptune.ai/wp-content/uploads/2025/08/GH_notifications.png)

### 2. Update installation

Update your installation using `pip`:

```bash
pip install git+https://github.com/neptune-ai/pytorch-lightning.git
```

## Support and feedback

> [!NOTE]
> This is an early version, and we would appreciate your feedback. Please don't hesitate to reach out if you have any questions, concerns, or suggestions for new features. 🤗

### Getting help

- 📖 [Support Center](https://support.neptune.ai/)
- 🐛 [Report Issues](https://github.com/neptune-ai/pytorch-lightning/issues)
