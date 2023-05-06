This repo is a codebase snapshot of [lxucs/coref-hoi](https://github.com/lxucs/coref-hoi); active issues or updates are maintained in [lxucs/coref-hoi](https://github.com/lxucs/coref-hoi) repository.

# End-to-End Coreference Resolution with Different Higher-Order Inference Methods

This repository contains the implementation of the paper: [Revealing the Myth of Higher-Order Inference in Coreference Resolution](https://www.aclweb.org/anthology/2020.emnlp-main.686.pdf).

## Architecture

The basic end-to-end coreference model is a PyTorch re-implementation based on the TensorFlow model following similar preprocessing (see this [repository](https://github.com/mandarjoshi90/coref)).

There are four higher-order inference (HOI) methods experimented: **Attended Antecedent**, **Entity Equalization**, **Span Clustering**, and **Cluster Merging**. All are included here except for Entity Equalization which is experimented in the equivalent TensorFlow environment (see this separate [repository](https://github.com/lxucs/coref-ee)).

**Files**:
* [run.py](run.py): training and evaluation
* [model.py](model.py): the coreference model
* [higher_order.py](higher_order.py): higher-order inference modules
* [analyze.py](analyze.py): result analysis
* [preprocess.py](preprocess.py): converting CoNLL files to examples
* [tensorize.py](tensorize.py): tensorizing example
* [conll.py](conll.py), [metrics.py](metrics.py): same CoNLL-related files from the [repository](https://github.com/mandarjoshi90/coref)
* [experiments.conf](experiments.conf): different model configurations

## Basic Setup
Set up environment and data for training and evaluation:
* Install Python3 dependencies: `pip install -r requirements.txt`
* Create a directory for data that will contain all data files, models and log files; set `data_dir = /path/to/data/dir` in [experiments.conf](experiments.conf)
* Prepare dataset (requiring [OntoNotes 5.0](https://catalog.ldc.upenn.edu/LDC2013T19) corpus): `./setup_data.sh /path/to/ontonotes /path/to/data/dir`

For SpanBERT, download the pretrained weights from this [repository](https://github.com/facebookresearch/SpanBERT), and rename it `/path/to/data/dir/spanbert_base` or `/path/to/data/dir/spanbert_large` accordingly.

## Evaluation
Provided trained models:
* SpanBERT + no HOI: [FILE](https://drive.google.com/file/d/1fjHrRT98XzvNSrzhJnydQBgKW0QodvGK)
* SpanBERT + Attended Antecedent: [FILE](https://drive.google.com/file/d/1qTrTYM2aEocvrO-cq2kcL64NFZmRUn6Z)
* SpanBERT + Span Clustering: [FILE](https://drive.google.com/file/d/1NAXbCbJBPtPYBj3lttPnlioBoLdxjKoc)
* SpanBERT + Cluster Merging: [FILE](https://drive.google.com/file/d/1ZdT9QjIwJxCGZjj7utFQdtjvFlwwdnP2)
* SpanBERT + Entity Equalization: see [repository](https://github.com/lxucs/coref-ee)

The name of each directory corresponds with a **configuration** in [experiments.conf](experiments.conf). Each directory has two trained models inside.

If you want to use the official evaluator, download and unzip [conll 2012 scorer](https://drive.google.com/file/d/1UeDIAFFNpJXfSH-PvOvacA60mC-XRDk5) under this directory.

Evaluate a model on the dev/test set:
* Download the corresponding model directory and unzip it under `data_dir`
* `python evaluate.py [config] [model_id] [gpu_id]`
    * e.g. Attended Antecedent:`python evaluate.py train_spanbert_large_ml0_d2 May08_12-38-29_58000 0`

## Training
`python run.py [config] [gpu_id]`

* [config] can be any **configuration** in [experiments.conf](experiments.conf)
* Log file will be saved at `your_data_dir/[config]/log_XXX.txt`
* Models will be saved at `your_data_dir/[config]/model_XXX.bin`
* Tensorboard is available at `your_data_dir/tensorboard`


## Configurations
Some important configurations in [experiments.conf](experiments.conf):
* `data_dir`: the full path to the directory containing dataset, models, log files
* `coref_depth` and `higher_order`: controlling the higher-order inference module
* `bert_pretrained_name_or_path`: the name/path of the pretrained BERT model ([HuggingFace BERT models](https://huggingface.co/transformers/main_classes/model.html#transformers.PreTrainedModel.from_pretrained))
* `max_training_sentences`: the maximum segments to use when document is too long; for BERT-Large and SpanBERT-Large, set to `3` for 32GB GPU or `2` for 24GB GPU

## Citation
```
@inproceedings{xu-choi-2020-revealing,
    title = "Revealing the Myth of Higher-Order Inference in Coreference Resolution",
    author = "Xu, Liyan  and  Choi, Jinho D.",
    booktitle = "Proceedings of the 2020 Conference on Empirical Methods in Natural Language Processing (EMNLP)",
    month = nov,
    year = "2020",
    publisher = "Association for Computational Linguistics",
    url = "https://www.aclweb.org/anthology/2020.emnlp-main.686",
    pages = "8527--8533"
}
```
