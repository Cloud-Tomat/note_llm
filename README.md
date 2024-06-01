# note_llm

## Example training LLM with LLama-factory

https://github.com/hiyouga/LLaMA-Factory


```
CUDA_VISIBLE_DEVICES=0 llamafactory-cli train examples/lora_single_gpu/llama3_lora_sft.yaml
CUDA_VISIBLE_DEVICES=0 llamafactory-cli chat examples/inference/llama3_lora_sft.yaml
CUDA_VISIBLE_DEVICES=0 llamafactory-cli export examples/merge_lora/llama3_lora_sft.yaml
```

## Convert model to GGUF format to use it with ollama

Tuto here
https://github.com/ggerganov/llama.cpp/discussions/2948


Install llama.cpp

```
python convert-hf-to-gguf.py ../LLaMA-Factory/models/llama3_lora_sft/ --outfile test_make.gpuf
```

## install the GGUF file in ollama

get model file from the base model
```
ollama show --modelfile llama3
```

Adapat the model file to use GGUF generated at previous steps
```
FROM ./test_make.gpuf
TEMPLATE "{{ if .System }}<|start_header_id|>system<|end_header_id|>

{{ .System }}<|eot_id|>{{ end }}{{ if .Prompt }}<|start_header_id|>user<|end_header_id|>

{{ .Prompt }}<|eot_id|>{{ end }}<|start_header_id|>assistant<|end_header_id|>

{{ .Response }}<|eot_id|>"
PARAMETER num_keep 24
PARAMETER stop <|start_header_id|>
PARAMETER stop <|end_header_id|>
PARAMETER stop <|eot_id|>
```

Create the model with ollama

```
ollama create test3 -f modelFile.txt
```
