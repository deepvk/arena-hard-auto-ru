# Arena-Hard-Auto

This is a public fork of the [lmsys arena-hard auto benchmark](https://github.com/lmarena/arena-hard-auto), designed to evaluate the capabilities of large language models (LLMs) in the Russian language. For setup, evaluation, the original leaderboard, and other details, please refer to the original repository.

## Key Differences from the Original Implementation

We have made the following changes in this fork:

* **Task Prompts:** Evaluation is conducted using two sets of task prompts: [ru-arena-general](https://huggingface.co/datasets/Vikhrmodels/ru-arena-general) and [ru-arena-hard-v0.1](https://huggingface.co/datasets/Vikhrmodels/arena_hard_ru).

* **Judge Models:** The judge models for both setups are either `gpt-4o-2024-11-20` or `claude-3-5-sonnet-20241022`. This choice helps mitigate self-preference bias, as many models are distilled from the outputs of OpenAI's foundational models.

* **Baseline Model:** The baseline model used is `gpt-4o-mini-2024-07-18`.

* **Additional API Calls:** We have added API calls for YandexGPT and GigaChat.

All model outputs and judges can be found here: [TBA](#).

## Leaderboard

```console
TBA
```


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
