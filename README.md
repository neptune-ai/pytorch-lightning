# Neptune Logger for PyTorch Lightning

[![PyTorch Lightning](https://img.shields.io/badge/PyTorch%20Lightning-2.5.1rc2-blue)](https://github.com/Lightning-AI/pytorch-lightning)
[![Neptune Scale](https://img.shields.io/badge/Neptune%20Scale-%3E%3D0.18.0-orange)](https://github.com/neptune-ai/neptune-client-scale)

This integration allows you to log your PyTorch Lightning training runs to Neptune 3.x, providing seamless experiment tracking.

## Changelog

- **2025-08-11** - Updated to support `ddp-spawn` strategy
- **2025-07-25** - Updated to better support nested mappings and dataclasses in `log_hyperparameters()`
- **2025-06-27** - Initial release



## Installation

> [!IMPORTANT]
> This integration works only with `neptune-scale>=0.18.0`, and is built on top of `pytorch-lightning==2.5.1rc2`

To install the integration, use one of the following options:

### Option 1: Install from source (recommended)

Install the custom version of `pytorch-lightning` from the source:

```bash
pip install git+https://github.com/neptune-ai/pytorch-lightning.git
```

### Option 2: Manual file replacement

In your `pytorch-lightning/src/lightning/pytorch/loggers` folder, replace the `__init__.py` and `neptune.py` files with the corresponding files available [here](https://github.com/SiddhantSadangi/pytorch-lightning/tree/master/src/lightning/pytorch/loggers)

> [!NOTE]
> Once the integration is included in the official PyTorch Lightning release, it's not necessary install it separately.

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

# Your training will automatically log to Neptune
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

### 4. Log metadata to Neptune

With the Neptune integration, you can combine the default PyTorch Lightning logging methods with the Neptune methods and capabilities.

#### Use Lightning's logging methods
Use `self.log()` in your Lightning module as usual – everything will be automatically logged to Neptune.

#### Use Neptune's logging methods
In addition to the default logging methods, you can use the Neptune [Run](https://docs.neptune.ai/run) methods to log more metadata types. You can also use the Neptune methods outside of the training loop.

To access the Neptune run directly, use the `neptune_scale_logger.run` reference:

```python
neptune_scale_logger.run.assign_files({"dataset/data_sample": "sample_data.csv"})
```

You can also use the `neptune_scale_logger.run` reference to log outside the prefix passed to `NeptuneScaleLogger`.

#### Add tags
Use `tags` to identify and organize your runs. Add tags with the `neptune_scale_logger.run` reference:

```python
neptune_scale_logger.run.add_tags(["notebook", "lightning"])
```

#### Log hyperparameters
To log hyperparameters, use the standard log_hyperparams() method from the PyTorch Lightning logger:

```python
PARAMS = {
    "batch_size": 64,
    "lr": 0.07,
    ...
}
neptune_scale_logger.log_hyperparams(PARAMS)
```

Hyperparameters are logged under the `<prefix>/hyperparams` namespace. To log outside the prefix, or to use Neptune's automatic casting, use the Neptune's [`log_configs()`](https://docs.neptune.ai/run/log_configs) method on the Neptune run:

```python
neptune_scale_logger.run.log_configs(
    data={
        "data/batch_size": 64,
        "model/optimizer/name": "adam",
        "model/optimizer/lr": 0.07,
        "model/optimizer/decay_factor": 0.97,
        "model/tokenizer/name": "bert-base-uncased",
    },
)
```

#### Log model checkpoint paths
If you have the `ModelCheckpoint` callback configured, you can log the paths to model checkpoints. Note that:

- All checkpoint paths are logged. The `save_last` and `save_top_k` parameters aren't supported.
- Neptune Scale logger doesn't upload the checkpoints to Neptune.

To disable logging of checkpoint paths, set the `log_model_checkpoints` init parameter to `False`:

```python
neptune_scale_logger = NeptuneScaleLogger(log_model_checkpoints=False)
```

#### Log model summary
To log a text file with the model summary, use:

```python
model = LitModel()
neptune_scale_logger.log_model_summary(model=model, max_depth=-1)
```

## Examples
- Refer to our example Colab notebook to see how this works in practice: [Neptune + PyTorch Lightning Notebook](https://colab.research.google.com/github/neptune-ai/scale-examples/blob/lb/pytorch-lightning/integrations-and-supported-tools/pytorch-lightning/notebooks/Neptune_PyTorch_Lightning.ipynb)
- Play with an interactive example: [Neptune + PyTorch Lightning Example](https://scale.neptune.ai/o/examples/org/pytorch-lightning/runs/details?viewId=9ea6121c-42a7-4ece-83b2-c591044837e7&detailsTab=dashboard&dashboardId=9f3b0e0b-90ba-4706-a109-c8ffd8443e50&runIdentificationKey=lightning-experiment&type=experiment&experimentsOnly=true&runsLineage=FULL&lbViewUnpacked=true&sortBy=%5B%22sys%2Fcreation_time%22%5D&sortFieldType=%5B%22datetime%22%5D&sortFieldAggregationMode=%5B%22auto%22%5D&sortDirection=%5B%22descending%22%5D&experimentOnly=true)


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
