# Extend Named Entity Recogniser (NER) for recognising new entities


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


 * [FAQ](#faq): Please do read faq for more clarification. 





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