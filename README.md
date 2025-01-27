# AutoMathIC: Automatic Mathematic In-Context Example Generation for LLM Using Multi-Modal Consistency

This repository contains implementation source code and experimental results for automatic in-context example generation for advancing math-solving capability of LLM as described in the following paper:

> Paper: [Automatic Mathematic In-Context Example Generation for LLM Using Multi-Modal Consistency](https://aclanthology.org/2025.coling-main.597/)

<!-- AutoMathIC operates by initially generating a collection of mutated math problems and their corresponding LLM responses across various prompt modalities. This procedure ensures that the mutated examples maintain the same reasoning algorithm utilized for solving the target math problem, resulting in potentially the most relevant in-context examples. Accordingly, this method addresses the first challenge. Subsequently, AutoMathIC iteratively selects a subset of mutated examples that improves consistency of responses across modalities for the target math problem. This approach tackles the second challenge by evaluating LLM responses of mutations by the consistency. By doing so, it elevates the confidence level of the LLM, thereby leading to a correct answer. -->

AutoMathIC is a framework that automatically generates high-quality In-
Context examples to enhance LLMs’ mathematical reasoning. In this implementation, AutoMathIC mutates an arithmatic questions and selects a subset of mutated questions for using it as In-Context examples using consistencies over multi-modalities over 4 math problem datasets([ASDiv](https://aclanthology.org/2020.acl-main.92/), [SVAMP](https://arxiv.org/abs/2103.07191), [GSM8k](https://arxiv.org/abs/2110.14168) and [MultiArith](https://arxiv.org/abs/1608.01413)). In this work, we use modality of Chain-Of-Thought, Code and Mathematical Equation.
Results of the AutoMathIC is [here](_results/README.md). Supplemental artifacts for the results can be downloaded from [here](_downloads/README.md)


Table of Contents
=================

   * [Table of Contents](#table-of-contents)
   * [Prerequisites](#prerequisites)
   * [Organization](#organization)
   * [Usage](#usage)
      * [1. Mutation of target question for In-Context Examples](#1-mutation-of-target-question-for-in-context-examples)
      * [2. Generation of Multi-Modal LLM responses](#2-generation-of-multi-modal-llm-responses)
      * [3. Optimization of LLM Responses Using Mutated In-Context Examples](#3-optimization-of-llm-responses-using-mutated-in-context-examples)
   * [Artifact](#artifact)

Prerequisites
=================
This application is written for ```Python=3.9.17```. All requirements are listed in ```requirements.txt```, and they are installed by pip with the following command.
```bash
pip install -r requirements.txt
```

Organization
=================
This artifact repository consists of the following files and folders:

`./src/python/*`: Directory for source code in python

`./_results/*`: Directory for results running the source code

`./_downloads/*`: Directory for datasets used for running the source code

Usage
=================
## 1. Mutation of target question for In-Context Examples
This step is to generate mutated math problems. 
The math problems are generated with the following command:

```bash
cd AuthoMathIC
# llm_model is between gpt3.5 for GPT-3.5 for and gpt4omini for GPT-4o-mini
# SVAMP
python -m src.python.main \
      --run mutate_nl \
      --llm_model_name "${llm_model}" \
      --dataset_name 'svamp'
# ASDiv
python -m src.python.main \
      --run mutate_nl \
      --llm_model_name "${llm_model}" \
      --dataset_name 'asdiv'

# MultiArith
python -m src.python.main \
      --run mutate_nl \
      --llm_model_name "${llm_model}" \
      --dataset_name 'multiarith'

# GSM8k
python -m src.python.main \
      --run mutate_nl \
      --llm_model_name "${llm_model}" \
      --dataset_name 'gsm8k'
```

Output after running the command are in the result directories of `{PROJ_DIR}/_results/nl2nl/{DATASET}/mutation/` where `DATASET` is the name of math problem dataset among [ASDiv](https://aclanthology.org/2020.acl-main.92/), [SVAMP](https://arxiv.org/abs/2103.07191), [GSM8k](https://arxiv.org/abs/2110.14168) and [MultiArith](https://arxiv.org/abs/1608.01413). 
For the task and its result directory, the following files are generated:
```bash
_results/
|- nl2nl/
|  |- {DATASET}}/
|  |  |- mutation/
|  |  |  |- mut-nl-{CKSUM}.json
``` 
Where `{CKSUM}` represents the checksum value of each unique math problem.
The `mut-nl-{CKSUM}.json` contains original math problem and its mutated math problems.

## 2. Generation of Multi-Modal LLM responses
This step is to obtain the LLM responses over multiple modalities. You can run it by executing the following command:
```bash
cd AuthoMathIC
# llm_model is between gpt3.5 for GPT-3.5 for and gpt4omini for GPT-4o-mini
# SVAMP
python -m src.python.main \
      --run evaluate_mm_llm \
      --llm_model_name "${llm_model}" \
      --dataset_name 'svamp'
# ASDiv
python -m src.python.main \
      --run evaluate_mm_llm \
      --llm_model_name "${llm_model}" \
      --dataset_name 'asdiv'

# MultiArith
python -m src.python.main \
      --run evaluate_mm_llm \
      --llm_model_name "${llm_model}" \
      --dataset_name 'multiarith'

# GSM8k
python -m src.python.main \
      --run evaluate_mm_llm \
      --llm_model_name "${llm_model}" \
      --dataset_name 'gsm8k'
```

Output after running the command are in the result directories of `{PROJ_DIR}/_results/nl2nl/{DATASET}/evaluate_consistency/{LLM_MODEL}` where `DATASET` is the name of math problem dataset among [ASDiv](https://aclanthology.org/2020.acl-main.92/), [SVAMP](https://arxiv.org/abs/2103.07191), [GSM8k](https://arxiv.org/abs/2110.14168) and [MultiArith](https://arxiv.org/abs/1608.01413) and LLM_MODEL represents the name of LLMs. 
For the task and its result directory, the following files are generated:
```bash
_results/
|- nl2nl/
|  |- {DATASET}}/
|  |  |- evaluate_consistency/
|  |  |  |- {LLM_MODEL}/
|  |  |  |  |- fg-eval-{CKSUM}.json
|  |  |  |  |- eval-{CKSUM}.json
``` 
Where `{CKSUM}` represents the checksum value of each unique math problem.
The `eval-{CKSUM}.json` and `fg-eval-{CKSUM}.json` contains LLM responses for original math problem and its mutated math problems over different modalities.

## 3. Optimization of LLM Responses Using Mutated In-Context Examples
This step is to select In-Context Examples among the mutated questions and generate the LLM responses using them. You can run it by executing the following command:
```bash
cd AuthoMathIC
# llm_model is between gpt3.5 for GPT-3.5 for and gpt4omini for GPT-4o-mini
# SVAMP
python -m src.python.main \
      --run genetic_fg_alg \
      --llm_model_name "${llm_model}" \
      --dataset_name 'svamp'
# ASDiv
python -m src.python.main \
      --run genetic_fg_alg \
      --llm_model_name "${llm_model}" \
      --dataset_name 'asdiv'

# MultiArith
python -m src.python.main \
      --run genetic_fg_alg \
      --llm_model_name "${llm_model}" \
      --dataset_name 'multiarith'

# GSM8k
python -m src.python.main \
      --run genetic_fg_alg \
      --llm_model_name "${llm_model}" \
      --dataset_name 'gsm8k'
```

Output after running the command are in the result directories of 
`{PROJ_DIR}/_results/genetic_fg/{DATASET}/evaluate_consistency/{LLM_MODEL}` where `DATASET` is the name of math problem dataset among [ASDiv](https://aclanthology.org/2020.acl-main.92/), [SVAMP](https://arxiv.org/abs/2103.07191), [GSM8k](https://arxiv.org/abs/2110.14168) and [MultiArith](https://arxiv.org/abs/1608.01413) and LLM_MODEL represents the name of LLMs. 
For the task and its result directory, the following files are generated:
```bash
_results/
|- genetic_fg/
|  |- {DATASET}}/
|  |  |- evaluate_consistency/
|  |  |  |- {LLM_MODEL}/
|  |  |  |  |- final_answers.json
``` 
Where `{CKSUM}` represents the checksum value of each unique math problem.
The `final_answers.json` contains final LLM responses for original math problems using selected mutations as In-Context examples for the original math problems.

Artifact
=================
Supplemental artifacts for the results can be downloaded from [here](https://utdallas.box.com/s/b21jlkww89v7i0tizaxbxqw5rwov6xzg)
