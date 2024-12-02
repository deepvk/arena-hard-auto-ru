# Arena-Hard-Auto

> 🚨 New feature: **Style Control** is now added to Arena Hard Auto! Check this [section](#style-control) to start using style control!

Arena-Hard-Auto-v0.1 ([See Paper](https://arxiv.org/abs/2406.11939)) is an automatic evaluation tool for instruction-tuned LLMs. It contains 500 challenging user queries sourced from Chatbot Arena. We prompt GPT-4-Turbo as judge to compare the models' responses against a baseline model (default: GPT-4-0314). Notably, Arena-Hard-Auto has the highest correlation and separability to Chatbot Arena among popular open-ended LLM benchmarks ([See Paper](https://arxiv.org/abs/2406.11939)). If you are curious to see how well your model might perform on Chatbot Arena, we recommend trying Arena-Hard-Auto.

Although both Arena-Hard-Auto and Chatbot Arena Category Hard ([See Blog](https://lmsys.org/blog/2024-05-17-category-hard/)) employ similar pipeline to select hard prompts, Arena-Hard-Auto employs automatic judge as a cheaper and faster approximator to human preference. Checkout [BenchBuilder](BenchBuilder) folder for code and resources on how we curate Arena-Hard-Auto.

## Content
- [Style Control Leaderboard](#style-control-leaderboard)
- [Leaderboard](#leaderboard)
- [Install](#install-dependencies)
- [Evaluation](#evaluate)
- [Style Control Guide](#style-control)

## Style Control Leaderboard
Following the newly introduced Style Control on Chatbot Arena, we release Style Control on Arena Hard Auto! We employ the same Style Control methods as proposed in the [blogpost](https://lmsys.org/blog/2024-08-28-style-control/). Please refer to the blogpost for methodology and technical background. 

```console
TBA
```

# Leaderboard
The following leaderboard has no style control.

(Updated: 11/14)
```console
TBA
```

## Install Dependencies
```
git clone https://github.com/lm-sys/arena-hard.git
cd arena-hard
pip install -r requirements.txt
pip install -r requirements-optional.txt  # Optional dependencies (e.g., anthropic sdk)
```

## Download dataset
We have pre-generated many popular models answers and judgments. You can browse them with an online [demo](https://huggingface.co/spaces/lmsys/arena-hard-browser) or download them (with [`git-lfs`](https://git-lfs.com) installed) by
```console
> git clone https://huggingface.co/spaces/lmsys/arena-hard-browser
// copy answers/judgments to the data directory
> cp -r arena-hard-browser/data . 
```
Then run
```console
> python show_result.py
gpt-4-0125-preview             | score: 78.0  | 95% CI: (-1.8, 2.2)  | average #tokens: 619
claude-3-opus-20240229         | score: 60.4  | 95% CI: (-2.6, 2.1)  | average #tokens: 541
gpt-4-0314                     | score: 50.0  | 95% CI:  (0.0, 0.0)  | average #tokens: 423
claude-3-sonnet-20240229       | score: 46.8  | 95% CI: (-2.7, 2.3)  | average #tokens: 552
claude-3-haiku-20240307        | score: 41.5  | 95% CI: (-2.4, 2.5)  | average #tokens: 505
gpt-4-0613                     | score: 37.9  | 95% CI: (-2.1, 2.2)  | average #tokens: 354
mistral-large-2402             | score: 37.7  | 95% CI: (-2.9, 2.8)  | average #tokens: 400
Qwen1.5-72B-Chat               | score: 36.1  | 95% CI: (-2.1, 2.4)  | average #tokens: 474
command-r-plus                 | score: 33.1  | 95% CI: (-2.0, 1.9)  | average #tokens: 541
```
Running `show_result.py` will save generated battles into `data/arena_hard_battles.jsonl` and bootstrapping statistics into `data/bootstrapping_results.jsonl`. If you don't want to regenerate battles or bootstrapping statistics, simply toggle argument `--load-battles` or `--load-bootstrap`, respectively.

## Evaluate

### Step 1. Set up the endpoint config to your model

Fill in your API endpoint in `config/api_config.yaml`. We support OpenAI compatible API server. You can specify `parallel` to indicate the number of concurrent API requests (default: 1).
```yaml
# example
gpt-3.5-turbo-0125:
    model_name: gpt-3.5-turbo-0125
    endpoints: null
    api_type: openai
    parallel: 8

[YOUR-MODEL-NAME]:
    model_name: [YOUR-MODEL-NAME]
    endpoints:
        - api_base: [YOUR-ENDPOINT-URL]
          api_key: [YOUR-API-KEY]
    api_type: openai
    parallel: 8
```
You may use inference engine such as [Latest TGI version](https://huggingface.co/docs/text-generation-inference/en/messages_api) or [vLLM](https://docs.vllm.ai/en/latest/serving/openai_compatible_server.html) or [SGLang](https://github.com/sgl-project/sglang?tab=readme-ov-file#using-local-models) to host your model with an OpenAI compatible API server.

TGI Quick start
```
hf_pat=
model=
volume=/path/to/cache
port=1996

huggingface-cli download $model
sudo docker run --gpus 8 -e HUGGING_FACE_HUB_TOKEN=$hf_pat --shm-size 2000g -p $port:80 -v $volume:/data ghcr.io/huggingface/text-generation-inference:2.0.4 --model-id $model --max-input-length 8192 --max-batch-total-tokens 8193 --max-batch-prefill-tokens 8193 --max-total-tokens 8193
```

### Step 2. Generate Model Answers

In `config/gen_answer_config.yaml`, add your model name in `model_list`.
```yaml
bench_name: arena-hard-v0.1
temperature: 0.0
max_tokens: 4096
num_choices: 1


model_list:
  - [YOUR-MODEL-NAME]
```
Run the command to generate answers:
```console
python gen_answer.py
```
Caching feature is implemented. The code will skip generating an answer when there is already an existing answer/judgment to the same prompt. 

### Step 3. Generate Judgments

In `config/judge_config.yaml`, add your model name in `model_list`.
```yaml
...
# Add your model below for evaluation
model_list:
  - gpt-3.5-turbo-0125
  - [YOUR-MODEL-NAME]
```

Run the command to generate judgments:
```console
python gen_judgment.py
```
Judgment caching is also implemented. It will skip generating judgments that has already been generated or lacks one of the model answers.  

### Step 4. Show result
Output model win rates.  Optionally, use `--full-stats` for detailed results. To save a csv file of the model rankings, use `--output`
```console
> python show_result.py
```

### Step 5. Arena Hard UI
You can review individual judgment results using our UI code.
```console
> python qa_browser.py --share
```

## Style Control
Following the newly introduced Style Control on Chatbot Arena, we release Style Control on Arena Hard Auto! We employ the same Style Control methods as proposed in the [blogpost](https://lmsys.org/blog/2024-08-28-style-control/). Please refer to the blogpost for methodology and technical background.

Before applying style control, make sure your model answers has proper style attribute generated. Either pull the latest data from [huggingface repo](https://huggingface.co/spaces/lmsys/arena-hard-browser), or run the following script!

To add style attribute to your model answers, use `add_markdown_info.py`. The following command takes model answers from `--dir`, append style attributes (token length, number of headers, etc), and save the new answers in `--output-dir`.

```console
> python add_markdown_info.py --dir data/arena-hard-v0.1/model_answer --output-dir data/arena-hard-v0.1/model_answer
```

To control for style (token length and markdown elements), use `--style-control` when running `show_result.py`.

```console
> python show_result.py --style-control
```

To control for length and markdown separately, use `--length-control-only` and `--markdown-control-only`.


## Community Contribution
Coming soon...

## Citation
The code in this repository is mostly developed for or derived from the papers below. Please cite it if you find the repository helpful.
```
@article{li2024crowdsourced,
  title={From Crowdsourced Data to High-Quality Benchmarks: Arena-Hard and BenchBuilder Pipeline},
  author={Li, Tianle and Chiang, Wei-Lin and Frick, Evan and Dunlap, Lisa and Wu, Tianhao and Zhu, Banghua and Gonzalez, Joseph E and Stoica, Ion},
  journal={arXiv preprint arXiv:2406.11939},
  year={2024}
}
@misc{chiang2024chatbot,
    title={Chatbot Arena: An Open Platform for Evaluating LLMs by Human Preference},
    author={Wei-Lin Chiang and Lianmin Zheng and Ying Sheng and Anastasios Nikolas Angelopoulos and Tianle Li and Dacheng Li and Hao Zhang and Banghua Zhu and Michael Jordan and Joseph E. Gonzalez and Ion Stoica},
    year={2024},
    eprint={2403.04132},
    archivePrefix={arXiv},
    primaryClass={cs.AI}
}
@misc{arenahard2024,
    title = {From Live Data to High-Quality Benchmarks: The Arena-Hard Pipeline},
    url = {https://lmsys.org/blog/2024-04-19-arena-hard/},
    author = {Tianle Li*, Wei-Lin Chiang*, Evan Frick, Lisa Dunlap, Banghua Zhu, Joseph E. Gonzalez, Ion Stoica},
    month = {April},
    year = {2024}
}
```
