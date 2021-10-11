# The codes for BERT-based models
This repository provides fine-tuning codes using BERT-based models, especially designed for biomedical text mining tasks such as biomedical named entity recognition and relation extraction.

### Named Entity Recognition (NER)
Download and unpack the NER datasets provided the previous directory. From now on, `$NER_DIR` indicates a folder for a single dataset which should include `train_dev.tsv`, `train.tsv`, `devel.tsv` and `test.tsv`. For example, `export NER_DIR=~/PPRcorpus/NERdata/`. Following command runs fine-tuining code on NER with default arguments.

```
mkdir /tmp/NER_output/
python run_ner.py \
    --do_train=true \
    --do_eval=true \
    --do_predict=true \
    --vocab_file=$BERT_DIR/vocab.txt \
    --bert_config_file=$BERT_DIR/bert_config.json \
    --init_checkpoint=$BERT_DIR/bert_model.ckpt \
    --num_train_epochs=5.0 \
    --data_dir=$NER_DIR/ \
    --output_dir=/tmp/NER_output/
```
Note that this result is the token-level evaluation measure while the official evaluation should use the entity-level evaluation measure. 
The results of `python run_ner.py` will be recorded as two files: `token_test.txt` and `label_test.txt` in `output_dir`. 
Use `ner_detokenize.py` in `./biocodes/` to obtain word level prediction file.
```
python biocodes/ner_detokenize.py \
--token_test_path=/tmp/NER_output/token_test.txt \
--label_test_path=/tmp/NER_output/label_test.txt \
--answer_path=$NER_DIR/test.tsv \
--output_dir=/tmp/NER_output
```
This will generate `NER_result_conll.txt` in `output_dir`.
Use `conlleval.pl` in `./biocodes/` for entity-level exact match evaluation results.
```
perl biocodes/conlleval.pl < /tmp/NER_output/NER_result_conll.txt
```

### Relation Extraction (RE)
Download and unpack the RE datasets provided the previous directory. From now on, `$RE_DIR` indicates a folder for a single dataset. 

```
mkdir /tmp/RE_output/
python run_re.py \
    --do_train=true \
    --do_eval=true \
    --do_predict=true \
    --vocab_file=$BERT_DIR/vocab.txt \
    --bert_config_file=$BERT_DIR/bert_config.json \
    --init_checkpoint=$BERT_DIR/bert_model.ckpt \
    --num_train_epochs=5.0 \
    --data_dir=$RE_DIR/ \
    --output_dir=/tmp/RE_output/ 
```

The predictions will be saved into a file called `test_results.tsv` in the `output_dir`. Once you have trained your model, you can use it in inference mode by using `--do_train=false --do_predict=true` for evaluating test.tsv. Use `./biocodes/re_eval.py` in `./biocodes/` folder for evaluation. 
```
python ./biocodes/re_eval.py --output_path={output_dir}/test_results.tsv --answer_path=$RE_DIR/test.tsv
```


## License
Please see LICENSE file for details. Downloading data indicates your acceptance of our disclaimer.

