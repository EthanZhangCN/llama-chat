# Chat with Meta's LLaMA models at home made easy

This repository is a chat example with [LLaMA](https://ai.facebook.com/blog/large-language-model-llama-meta-ai/) ([arXiv](https://arxiv.org/abs/2302.13971v1)) models running on a typical home PC. You will just need a NVIDIA videocard and some RAM to chat with model.

This repo is heavily based on Meta's original repo: https://github.com/facebookresearch/llama

And on Steve Manuatu's repo: https://github.com/venuatu/llama

And on Shawn Presser's repo: https://github.com/shawwn/llama

### Examples of chats here

https://github.com/facebookresearch/llama/issues/162

Share your best prompts, chats or generations here in this issue: https://github.com/randaller/llama-chat/issues/7

### System requirements
- Modern enough CPU
- NVIDIA graphics card
- 64 or better 128 Gb of RAM (192 or 256 would be perfect)

One may run with 32 Gb of RAM, but inference will be slow (with the speed of your swap file reading)

I am running this on 12700k/128 Gb RAM/NVIDIA 3070ti 8Gb/fast huge nvme and getting one token from 30B model in a few seconds.

For example, **30B model uses around 70 Gb of RAM**. 7B model fits into 18 Gb. 13B model uses 48 Gb.

If you do not have powerful videocard, you may use another repo for cpu-only inference: https://github.com/randaller/llama-cpu

### Conda Environment Setup Example for Windows 10+
Download and install Anaconda Python https://www.anaconda.com and run Anaconda Prompt
```
conda create -n llama python=3.10
conda activate llama
conda install pytorch torchvision torchaudio pytorch-cuda=11.7 -c pytorch -c nvidia
```

### Setup
In a conda env with pytorch / cuda available, run
```
pip install -r requirements.txt
```
Then in this repository
```
pip install -e .
```

### Download tokenizer and models
magnet:?xt=urn:btih:ZXXDAUWYLRUXXBHUYEMS6Q5CE5WA3LVA&dn=LLaMA

or

magnet:xt=urn:btih:b8287ebfa04f879b048d4d4404108cf3e8014352&dn=LLaMA&tr=udp%3a%2f%2ftracker.opentrackr.org%3a1337%2fannounce

### Prepare model

First, you need to unshard model checkpoints to a single file. Let's do this for 30B model.

```
python merge-weights.py --input_dir D:\Downloads\LLaMA --model_size 30B
```

In this example, D:\Downloads\LLaMA is a root folder of downloaded torrent with weights.

This will create merged.pth file in the root folder of this repo.

Place this file and corresponding (torrentroot)/30B/params.json of model into [/model] folder.

So you should end up with two files in [/model] folder: merged.pth and params.json.

Place (torrentroot)/tokenizer.model file to the [/tokenizer] folder of this repo. Now you are ready to go.

### Run the chat

```
python example-chat.py ./model ./tokenizer/tokenizer.model
```

### Generation parameters

![image](https://user-images.githubusercontent.com/22396871/224481306-0079dc71-a659-46f2-96a3-38d8a0b8bafc.png)

**Temperature** is one of the key parameters of generation. You may wish to play with temperature, setting it around 0.7 .. 0.99. The more temperature is, the more model should follow your prompt, the less temperature instruct model to use more imagination.

**Repetition penalty** is a feature implemented by Shawn Presser. With this, the model will be fined, when it would like to enter to repetion loop state. Set this parameter to 1.0, if you wish to disable this feature.

**Samplers**

By default, Meta provided us with top_p sampler only. Again, Shawn added an alternate top_k sampler, which (in my tests) performs pretty well. If you wish to switch to top_k sampler, use the following parameters:

```
top_p: float = 0.0,
top_k: int = 40,
sampler: str = 'top_k',
```

For sure, you may play with top_k value to get slightly different outputs.

### Enable multi-line answers

If you wish to stop generation not by "\n" sign, but by another signature, like "User:" (which is also good idea), or any other, make the following modification in the llama/generation.py:

![image](https://user-images.githubusercontent.com/22396871/224122767-227deda4-a718-4774-a7f9-786c07d379cf.png)

-5 means to remove last 5 chars from resulting context, which is length of your stop signature, "User:" in this example.

### Share the best with community

Share your best prompts and generations with others here: https://github.com/randaller/llama-chat/issues/7

### Typical generation with prompt (not a chat)

Simply comment those three lines in llama/generation.py to turn it to a generator back.

![image](https://user-images.githubusercontent.com/22396871/224283389-e29de04e-28d1-4ccd-bf6b-81b29828d3eb.png)

```
python example.py ./model ./tokenizer/tokenizer.model
```

Confirming that 30B model is able to generate code and fix errors in code: https://github.com/randaller/llama-chat/issues/7

Confirming that 30B model is able to generate prompts for Stable Diffusion: https://github.com/randaller/llama-chat/issues/7#issuecomment-1463691554

Confirming that 7B and 30B model support Arduino IDE: https://github.com/randaller/llama-chat/issues/7#issuecomment-1464179944
