# Document Question Answering
### _Eval Recipe for model migration_

This Eval Recipe demonstrates how to compare performance of a Document Question Answering prompt with Gemini 1.0 and Gemini 2.0 using a labeled dataset and open source evaluation tool [Promptfoo](https://www.promptfoo.dev/).

![](diagram.png "Promptfoo")

- Use case: answer questions based on information from the given document.

- The Evaluation Dataset is based on [SQuAD2.0](https://rajpurkar.github.io/SQuAD-explorer/). It includes 6 documents stored as plain text files, and a JSONL file that provides ground truth labels: [`dataset.jsonl`](./dataset.jsonl). Each record in this file includes 3 attributes wrapped in the `vars` object. This structure allows Promptfoo to specify the variables needed to populate prompt templates (document and question), as well as the ground truth label required to score the accuracy of model responses:
    - `document`: relative path to the plain text document file
    - `question`: the question that we want to ask about this particular document
    - `answer`: expected correct answer or special code `ANSWER_NOT_FOUND` used to verify that the model does not hallucinate answers when the document does not provide enough information to answer the given question.

- Prompt Template is a zero-shot prompt located in [`prompt_template.txt`](./prompt_template.txt) with two prompt variables (`document` and `question`) that are automatically populated from our dataset.

- [`promptfooconfig.yaml`](./promptfooconfig.yaml) contains all Promptfoo configuration:
    - `providers`: list of models that will be evaluated
    - `prompts`: location of the prompt template file
    - `tests`: location of the labeled dataset file
    - `defaultTest`: defines the scoring logic:
        1. `type: factuality` uses an Autorater (aka LLM Judge) to compare the model answer with our ground truth label and rate its correctness
        1. `value: "{{answer}}"` instructs Promptfoo to use the dataset attribute "answer" as the ground truth label

## How to run this Eval Recipe

- Google Cloud Shell is the easiest option as it automatically clones our Github repo:

    <a href="https://console.cloud.google.com/cloudshell/open?git_repo=https://github.com/GoogleCloudPlatform/applied-ai-engineering-samples&cloudshell_git_branch=main&cloudshell_workspace=genai-on-vertex-ai/gemini/model_upgrades">
        <img alt="Open in Cloud Shell" src="http://gstatic.com/cloudssh/images/open-btn.png">
    </a>

- Alternatively, you can use the following command to clone this repo to any Linux environment with configured [Google Cloud Environment](https://cloud.google.com/vertex-ai/docs/start/cloud-environment):

    ``` bash
    git clone --filter=blob:none --sparse https://github.com/GoogleCloudPlatform/applied-ai-engineering-samples.git && \
    cd applied-ai-engineering-samples && \
    git sparse-checkout init && \
    git sparse-checkout set genai-on-vertex-ai/gemini/model_upgrades && \
    git pull origin main
    cd genai-on-vertex-ai/gemini/model_upgrades
    ```

1. Install Promptfoo using [these instructions](https://www.promptfoo.dev/docs/installation/).
1. Navigate to the Eval Recipe directory in terminal and run the command `promptfoo eval`.

    ``` bash
    cd document_qna/promptfoo
    promptfoo eval
    ```

1. Run `promptfoo view` to analyze the eval results. You can switch the Display option to `Show failures only` in order to investigate any underperforming prompts.

## How to customize this Eval Recipe:
1. Copy the configuration file `promptfooconfig.yaml` to a new folder.
1. Add your labeled dataset file with JSONL schema similar to `dataset.jsonl`. 
1. Save your prompt template to `prompt_template.txt` and make sure that the template variables map to the variables defined in your dataset.
1. That's it! You are ready to run `promptfoo eval`. If needed, add alternative prompt templates or additional metrics to promptfooconfig.yaml as explained [here](https://www.promptfoo.dev/docs/configuration/parameters/).