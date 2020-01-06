# New Entity Labelling

 * Typically the pretrained natural language processing (NLP) models are trained to recognise limited [entities](https://spacy.io/api/annotation#named-entities). 

 * This notebook provides a short and crisp way to recognise user set of entities to cater to a specific task, where an entity can **sequence** of tokens (word, character etc.). 

 * The process of annotating data follow the [BILUO](https://spacy.io/api/annotation#biluo) convention, where the scheme is defined as follows:

	| TAG        | DESCRIPTION                              |
	|------------|------------------------------------------|
	| **B** EGIN | The first token of a multi-token entity. |
	| **I** N    | An inner token of a multi-token entity.  |
	| **L** AST  | The final token of a multi-token entity. |
	| **U** NIT  | A single-token entity.                   |
	| **O** UT   | A non-entity token.                      |

	<!-- Source: https://www.tablesgenerator.com/markdown_tables# -->

 * One can read this paper by [Akbik et al.](https://alanakbik.github.io/papers/coling2018.pdf), this should help in understanding the algorithm behind the sequence labelling (i.e. multiple-word entities). 


 * [FAQ](#faq)



## Installation Process with Error Resolution
 
 * Using [**spaCy**](https://spacy.io/): Industrial-Strength Natural Language Processing

 * Installation

   - `pip install spacy`

 * Download a pretrained model
  
   * `python -m spacy download en` - This downloads only simples and light weight [model](https://spacy.io/models/en#en_core_web_sm) 
  
   * To download other pretrained model `python -m spacy download en_core_web_md` ([source](https://spacy.io/models/en)) this is also small and only has tagger, parser, ner. There are other and more options are available at the source along with for different languages. 

   * To load the model run following:

		```
		import spacy
		nlp = spacy.load('en') # en_core_web_md
		# If this line outputs a error (lint or something) please see below to resolve them
		```

   

### Not required for this notebook:

 * Pretrained Transformer model

   * The prerequisite is to install spacy transformer `pip install spacy-transformers`

   * To install one of the [transformer](https://github.com/explosion/spacy-transformers) model:
        `python -m spacy download  --always-link  en_trf_bertbaseuncased_lg --timeout=10000`. Timeout arg is for to avoid error because of timeout. Might need to install timeout package

   * To load the model run following:

        ```
        import spacy
        nlp = spacy.load('en_trf_bertbaseuncased_lg')
        ```

    * The above line might cause an error something along `[E050] Can't find model 'en_trf_bertbaseuncased_lg'. It doesn't seem to be a shortcut link, a Python package or a valid path to a data directory.` The [solution](https://github.com/explosion/spaCy/issues/3435) is as follows:

        ```
        from spacy.cli import link
        from spacy.util import get_package_path

        model_name = "en_trf_bertbaseuncased_lg" # model_name = 'en_core_web_md'
        model_path = get_package_path(model_name)
        link(model_name, model_name, force=True, model_path=model_path)
        ```
    **Note**: 
     - Follow the above steps if there is error loading this, `en_core_web_md`, or any other model from [here](https://spacy.io/models/en). 

     - If there is error something like `[E048] Can't import language trf from spacy.lang: No module named 'spacy.lang.trf'` then make sure you have already installed `spacy-transformers` using `pip install spacy-transformers`. Look if it is installed using `pip list`. If not then install it else restart the project of `ipython` console again should work. 





## <a id='faq'>FAQ</a>

**Q**: Will the training for new types of entities can harm the previously learned entities?
**ANS** Yes, most likely if the older type is not included in current training data. 
```
# Note: If you're using an existing model, make sure to mix in examples of
# other entity types that spaCy correctly recognized before. Otherwise, your
# model might learn the new type, but "forget" what it previously knew.
# https://explosion.ai/blog/pseudo-rehearsal-catastrophic-forgetting 
```



**Q**: How to reproduce results in spcCy?

**Ans**: Fix the random seed for both `spacy` and `numpy` if only running on CPU: 
```
import spacy
import numpy as np

s = 999
np.random.seed(s)
spacy.util.fix_random_seed(s)
```

if running on GPU then also need to fix the random seed for `cupy`
```
cupy.random.seed(s)
```
Read more [here](https://github.com/explosion/spaCy/issues/3182).



**Q**: Why [BILUO](https://spacy.io/api/annotation#biluo) is better than [IOB](https://spacy.io/api/annotation#iob) scheme?

**Ans**: [There are several coding schemes for encoding entity annotations as token tags.](https://spacy.io/api/annotation#biluo) These coding schemes are equally expressive, but not necessarily equally learnable. [Ratinov and Roth](https://www.aclweb.org/anthology/W09-1119/) showed that the minimal Begin, In, Out scheme was more difficult to learn than the BILUO scheme that we use, which explicitly marks boundary tokens.


**Q**: How to input data to the Model?

**Ans**: Use [GoldParse](https://spacy.io/api/goldparse#docs_to_json) to parse the token-annotated to convert in a required format directly or use offset-indices process.


