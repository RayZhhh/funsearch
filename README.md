# FunSearch Implementation

This repository implements the following publication:

> Romera-Paredes, B. et al. [Mathematical discoveries from program search with large language models](https://www.nature.com/articles/s41586-023-06924-6). *Nature* (2023)

## Installation and Requirements

Please note that **the Python version** **must larger or equal to Python 3.9**, or the '*ast*' package used in the implementations will fail to work. 

You can run FunSearch for online bin packing locally if enough GPU devices are available. Or you can try to use LLM interfaces to request responses online. 

Please install the packages listed in `requirements.txt`.

## Project Structure

There are some independent directories in this project:

- `bin_packing` contains example jupyter notebook for bin packing task. 
- `implementation` contains an implementation of the evolutionary algorithm, code manipulation routines, and a single-threaded implementation of the FunSearch pipeline. 
- `llm-server` contains the implementations of an LLM server that gets the prompt by monitoring requests from FunSearch and response to the inference results to the FunSearch algorithm. 

## Files in *funsearch/implementation*

There are some files in `funsearch/implementation`. They are as follows:

- `code_manipulatoin.py` provides functions to modify the code in the specification.
- `config.py` includes configs of funsearch.
- `evaluator.py` trims the sample results from LLM, and evaluates the sampled functions.
- `evaluator_accelerate.py` accelerates the evaluation using the 'numba' library.
- `funsearch.py` implements funsearch pipeline. 
- `profile.py` records the score of the sampled functions.
- `programs_database.py` evolves the sampled functions.
- `sampler.py` sends prompts to LLM and gets results.

## Run FunSearch

### Run FunSearch bin packing demo on Colab

The jupyter notebook in `bin_packing/bin_packing_funsearch.ipynb` can be opened via [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/RayZhhh/funsearch/blob/main/bin_packing/bin_packing_funsearch.ipynb). Please note that do not run the juypter notebook locally, as the jupyter notebook backend does not support mutiprocessor.

### Parameters and Settings

If you want to adjust the following parameters, you should modify the code in `funsearch/implementation` manually. 

- `timeout_seconds` This parameter defines the maximum evaluation time for a single function. If the evaluation time exceeds this, the evaluation process will be killed. This strategy can prevent *while True* loop and reduce total evaluation costs but may discard potential outstanding functions. You can modify this in `implementation/evaluator.py/class Evaluator`.
- `_reduce_score` This function does reduction to the score of a sampled function in some instances. The reduction is implemented as *mean* by default. You can modify it in `implementation/program_database.py`, where you can find a '_reduce_score' function.

### Use Local LLM

First, start the local LLM server.

```shell
# Suppose we are in funsearch directory (root dir of this project).
cd llm-server
# Start LLM server: python llm_server.py --port 8088 --path [model path] --d [GPU IDs]
python llm_server.py --port 8088 --path /LLms/CodeLlama-34b --d 0 1 2 3 4 5
```

Then, start FunSearch.

```shell
# Run FunSearch
python funsearch_bin_packing_local_llm.py
```

You can see logs via *Tensorboard*. Please check the *log_dir* variable defined in `bin_packing_funsearch_my_template.py`, and start the Tensorboard using the following instructions:

```shell
# Suppose we are in funsearch directory (root directory of this project)
cd logs
tensorboard --logdir funsearch_local_llm
```

### Use LLM Interfaces

Start FunSearch.

```shell
# Run FunSearch
python funsearch_bin_packing_llm_api.py
```

You can see logs via *Tensorboard*. Please check the *log_dir* variable defined in `bin_packing_funsearch_my_template.py`, and start the Tensorboard using the following instructions:

```shell
# Suppose we are in funsearch directory (root directory of this project).
cd logs
tensorboard --logdir funsearch_llm_api
```

