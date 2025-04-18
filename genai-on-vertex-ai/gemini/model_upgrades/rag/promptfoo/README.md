# RAG
### _Eval Recipe for model migration_

This Eval Recipe demonstrates how to compare performance of a Document Question Answering prompt with Gemini 1.0 and Gemini 2.0 using a labeled dataset and open source evaluation tool [Promptfoo](https://www.promptfoo.dev/).

![](diagram.png "Promptfoo")

- Use case: answer questions based on information from the given document.

- Evaluation Dataset is based on [Single-Topic RAG Evaluation Dataset](https://www.kaggle.com/datasets/samuelmatsuoharris/single-topic-rag-evaluation-dataset/). It includes 4 randomly sampled documents stored as plain text files, and a JSONL file `dataset.jsonl` that provides ground truth answers. Each record in this file includes 3 attributes wrapped in the `vars` object. This structure allows Promptfoo to specify the variables needed to populate prompt templates (document and question), as well as the ground truth label required to score the accuracy of model responses:
    - `document`: relative path to the plain text document file
    - `question`: the question that we want to ask about this particular document
    - `reference`:  expected correct answer from the document.

- Prompt Template is a zero-shot prompt located in [`prompt_template.txt`](./prompt_template.txt) with two prompt variables (`document` and `question`) that are automatically populated from our dataset.

- [`promptfooconfig.yaml`](./promptfooconfig.yaml) contains all Promptfoo configuration:
    - `providers`: list of models that will be evaluated
    - `prompts`: location of the prompt template file
    - `tests`: location of the labeled dataset file
    - `defaultTest`: defines the scoring logic:
        1. `type: g-eval` G-Eval is a framework that uses LLMs with chain-of-thoughts (CoT) to evaluate LLM outputs based on custom criteria. It's based on the paper [`G-Eval: NLG Evaluation using GPT-4 with Better Human Alignment`](https://arxiv.org/abs/2303.16634).
        1. `value: "Check if the response is similar to {{reference}}"` instructs Promptfoo to use the dataset attribute "reference" as the ground truth answer and compare it against the LLM generated response. 

## How to run this Eval Recipe

1. Configure your [Google Cloud Environment](https://cloud.google.com/vertex-ai/docs/start/cloud-environment) and clone this Github repo to your environment. We recommend [Cloud Shell](https://shell.cloud.google.com/) or [Vertex AI Workbench](https://cloud.google.com/vertex-ai/docs/workbench/instances/introduction).

``` bash
git clone --filter=blob:none --sparse https://github.com/GoogleCloudPlatform/applied-ai-engineering-samples.git && \
cd applied-ai-engineering-samples && \
git sparse-checkout init && \
git sparse-checkout set genai-on-vertex-ai/gemini/model_upgrades && \
git pull origin main
```

1. Install Promptfoo using [these instructions](https://www.promptfoo.dev/docs/installation/).
1. Navigate to the Eval Recipe directory in terminal and run the command `promptfoo eval`.

``` bash
cd genai-on-vertex-ai/gemini/model_upgrades/rag/promptfoo
promptfoo eval
```
1. Run `promptfoo view` to analyze the eval results. You can switch the Display option to `Show failures only` in order to investigate any underperforming prompts.

## How to customize this Eval Recipe:
1. Copy the configuration file `promptfooconfig.yaml` to a new folder.
1. Add your labeled dataset file with JSONL schema similar to `dataset.jsonl`. 
1. Save your prompt template to `prompt_template.txt` and make sure that the template variables map to the variables defined in your dataset.
1. That's it! You are ready to run `promptfoo eval`. If needed, add alternative prompt templates or additional metrics to promptfooconfig.yaml as explained [here](https://www.promptfoo.dev/docs/configuration/parameters/).