# Question anwsering

## Install 
1. `git clone git@gitlab:ai/question-answering.git`
2. `cd question-answering`
3. `git submodule init`
4. `git submodule update`
5. `cd transformers`
6. `pip3 install -e .`
7. `pip3 install pandas`
8. `pip3 install -U scikit-learn`

Actually, for solving a **question-answering** problem like the email answering, we must use **text-generation** solutions, the type of task we must use **text2text-generation**. Like:

- [Text Generation](https://huggingface.co/tasks/text-generation)
- [Task Summary: Text Generation](https://huggingface.co/docs/transformers/task_summary#text-generation)
- [Hugging Face: install from source](https://huggingface.co/docs/transformers/installation#installing-from-source)
- [transformers/examples/pytorch/text-generation/](https://github.com/huggingface/transformers/tree/master/examples/pytorch/text-generation)
- [Hugging Face - Text2Text Generation models](https://huggingface.co/models?language=es&library=pytorch&pipeline_tag=text2text-generation&sort=downloads)
- [Huggingface: fine-tuning with custom datasets](https://huggingface.co/transformers/v3.2.0/custom_datasets.html)
- [transformers/examples/pytorch/language-modeling/](https://github.com/huggingface/transformers/tree/master/examples/pytorch/language-modeling)
- [train transformer with pytorch](https://github.com/huggingface/transformers/tree/master/examples/pytorch/text-generation)
- [Finetune in native pytorch](https://huggingface.co/docs/transformers/master/en/training#finetune-in-native-pytorc)]
- [Preprocessing data](https://huggingface.co/docs/transformers/preprocessing)
- [GPT2Config param doc](https://huggingface.co/docs/transformers/model_doc/gpt2#transformers.GPT2Config)
- [transformers.ConversationalPipeline](https://huggingface.co/docs/transformers/v4.17.0/en/main_classes/pipelines#transformers.ConversationalPipeline)
- [DialoGPT](https://github.com/microsoft/DialoGPT)

## Guide 1 to retrain a GPT-2 model with PyTorch
- [Fine-tuning GPT2 for Text Generation Using Pytorch](https://towardsdatascience.com/fine-tuning-gpt2-for-text-generation-using-pytorch-2ee61a4f1ba7)
- [This previous guide uses the old huggingface transformer script](https://github.com/huggingface/transformers/blob/master/examples/legacy/run_language_modeling.py)

```python
special_tokens_dict = {'bos_token': '<BOS>', 'eos_token': '<EOS>', 'pad_token': '<PAD>'}
num_added_toks = tokenizer.add_special_tokens(special_tokens_dict)
model.resize_token_embeddings(len(tokenizer))
```

## Grocery and Gourmet Food
- [Source of data](http://jmcauley.ucsd.edu/data/amazon/qa/)

### Download and prepare data

1. Download: `$ wget http://jmcauley.ucsd.edu/data/amazon/qa/qa_Grocery_and_Gourmet_Food.json.gz`
2. Prepare train and verify data `$ python3 prepare_GG_data.py`
2. Prepare train and verify data for seq2seq `$ python3 prepare_seq_data.py`

#### qa_Grocery_and_Gourmet_Food Data format
Each line has a json object with the following properties:
- questionType
- asin
- answerTime
- unixTime
- question 
- answer

##### Sample code to load files
```python
import pandas as pd
import gzip

def parse(path):
  g = gzip.open(path, 'rb')
  for l in g:
    yield eval(l)

def getDF(path):
  i = 0
  df = {}
  for d in parse(path):
    df[i] = d
    i += 1
  return pd.DataFrame.from_dict(df, orient='index')

df = getDF('qa_Video_Games.json.gz')
```

## Models

### Text2Text-generations

- [hiiamsid/est5-base](https://huggingface.co/hiiamsid/est5-base?text=Tengo+un+problema+con+el+sistema+de+archivos%2C+parace+que+he+perdido+los+persmisos+para+acceder+a+los+ficheros+A3+y+A4.+¿Puedo+hacer+una+copia+de+seguridad+de+los+ficheros+A3+y+A4%3F)
- [mrm8488/spanish-t5-small-sqac-for-qa](https://huggingface.co/mrm8488/spanish-t5-small-sqac-for-qa?text=question%3A+¿Cuál+es+el+nombre+que+se+le+da+a+la+unidad+morfológica+y+funcional+de+los+seres+vivos%3F+context%3A+La+célula+%28del+lat%C3%ADn+cellula%2C+diminutivo+de+cella%2C+‘celda’%29+es+la+unidad+morfológica+y+funcional+de+todo+ser+vivo.+De+hecho%2C+la+célula+es+el+elemento+de+menor+tamaño+que+puede+considerarse+vivo.%E2%80%8B+De+este+modo%2C+puede+clasificarse+a+los+organismos+vivos+según+el+número+de+células+que+posean%3A+si+solo+tienen+una%2C+se+les+denomina+unicelulares+%28como+pueden+ser+los+protozoos+o+las+bacterias%2C+organismos+microscópicos%29%3B+si+poseen+más%2C+se+les+llama+pluricelulares.+En+estos+últimos+el+número+de+células+es+variable%3A+de+unos+pocos+cientos%2C+como+en+algunos+nematodos%2C+a+cientos+de+billones+%281014%29%2C+como+en+el+caso+del+ser+humano.+Las+células+suelen+poseer+un+tamaño+de+10+µm+y+una+masa+de+1+ng%2C+si+bien+existen+células+mucho+mayores.)

## Investigation ways
I must investigate four ways to solve this problem:
1. Train a lenguage model from scratch, to generate text.
2. Use a pre-trained language model retrained, to generate text. Like GPT-2.
3. Fine-tune a pre-trained language model, to generate text, by doing a "conversational" tasks.
4. Fine-tune a seq2seq model, to generate text.
5. Train from scratch a tiny model to get a 100% accuracy

### 1. Train a lenguage model from scratch, to generate text.
First install requirements:

    $ pip3 install -r ./transformers/examples/pytorch/language-modeling/requirements.txt

Also, do the step **Download and prepare data**.

Then run training:

    $ ./train_gpt2_from_scratch/train_gpt2_model.sh

After training is done, do inference:

    $ python3 ./train_gpt2_from_scratch/inference_gpt2_model.py

#### Results

Almost returns the same anwsers, no matter the questions: almos always returns: "I don't know"

### 2. Use a pre-trained language model retrained, to generate text. Like GPT-2.
First install requirements:

    $ pip3 install -r ./transformers/examples/pytorch/language-modeling/requirements.txt

Also, do the step **Download and prepare data**.

Then run training:

    $ ./train_gpt2_from_pretrained/train_gpt2_model.sh

After training is done, do inference:

    $ python3 ./train_gpt2_from_pretrained/inference_gpt2_model.py

#### Results

Training was fast, but i could not solve inference, because the script launch an exception that I could no solve.

### 3. Fine-tune a pre-trained language model, to generate text, by doing a "conversational" tasks
NoTHINMG

### 4. Fine-tune a seq2seq model, to generate text
NOTHOING

### 5. Train from scratch a tiny model to get a 100% accuracy
First install requirements:

    $ pip3 install -r ./transformers/examples/pytorch/language-modeling/requirements.txt

Then run training:

    $ ./train_tiny_gpt2_from_scratch/train_gpt2_model.sh

After training is done, do inference:

    $ python3 ./train_tiny_gpt2_from_scratch/inference_gpt2_model.py

### 6. Train a pytorch model based on translation
Use a translation model by adapting it to solve this questions-answering.

https://pytorch.org/tutorials/beginner/translation_transformer.html

ToDo:
- [X] Buscar códigos donde ya haya implementado Datasets.
- [X] Buscar códigos donde ya haya usado los transformers de pytorch.
- [X] Implementar el código tal y como está en el doc translation_transformer.
- [ ] Adaptar el código para hacer una question-answering.

tokenizer = get_tokenizer('basic_english')

- https://gitlab/ai/pytorch-word2vec/-/blob/main/word2vec/dataset.py
- https://gitlab/ai/libtorch-lm/-/blob/master/language_translation.py
- https://gitlab/ai/libtorch-lm/-/wikis/home
- https://andrewpeng.dev/transformer-pytorch/

Doc:
tokens
- UNK_IDX -> default index. This index is returned when the token is not found.
- PAD_IDX -> value used to fill short sequences.
- BOS_IDX -> begining of string
- EOS_IDX -> end of string

Run sample file:    

    # Create source and target language tokenizer. Make sure to install the dependencies.
    # pip3 install -U spacy
    # python3 -m spacy download en_core_web_sm
    # python3 -m spacy download de_core_news_sm
    $ python3 ./train_pytorch_for_translation/language_translation.py


## ToDo
- [ ] Encontrar cómo guardar y restaurar modelos reentrenados.
- [X] Por lo que veo en el documento de custom dataset, lo que tengo que hacer es crear mi propio código que cargue los datos.
- [X] Investigar [Question Answering with SQuAD 2.0](https://huggingface.co/transformers/v3.2.0/custom_datasets.html#qa-squad)
- [X] Necesito encontrar la manera de reentrenar sistemas de **text2text-generation**, -> USAR LM https://github.com/huggingface/transformers/tree/master/examples/pytorch/language-modeling#gpt-2gpt-and-causal-language-modeling
- [X] Crear un sistema sencillo de `inference` de pipelines para **text2text-generation**.
- [X] Una opción que debo tener en cuenta es que quizás no necesito ralizar un fine-tuning, podría simplemente entrenar todo el modelo con mis correos. SEPARANDO LA PREGUNTA DE LA RESPUESTA CON UNA PALABRA TOKEN CLAVE COMO '>>>QA>>>'
- [X] Igual puedo hacer un reentreno de un gpt2 en español. ASÍ ES.

## Remember
- Text generation is currently possible with GPT-2, OpenAi-GPT, CTRL, XLNet, Transfo-XL and Reformer in PyTorch
- Una posible solución sería el **Text Generation**
- También se puede usar los transformers para el **Names Entity Recognition** (NER)
- Esto es lo que quiero hacer: fine-tuning un GPT-2 ---->>>> https://github.com/huggingface/transformers/tree/master/examples/pytorch/language-modeling#gpt-2gpt-and-causal-language-modeling

# Patches
## token patches
line run_clm.py:346

tokenizer.add_special_tokens({
        "eos_token": "</S>",
        "bos_token": "<S>",
        "unk_token": "<SEP>"
    })
    
# Marketing

- https://mailytica.com/en/pricing/
- https://emailtree.ai