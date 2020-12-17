## Abstract

Question-answering through highlighting has limited direct applications in the real world, while question-entailment has direct applications such as producing a medical FAQ with a good database of question/answer pairs and a retrieval algorithm. For this project, question-entailment is defined as whether a hypothesis sentence is true given a premise sentence. We want to optimize cross-lingual performance, paying particular attention to low-resource languages. We also want to see if cosine-similarity is a good proxy for entailment due to it's low cost, and if locality sensitive hashing (LSH) can be used to decrease runtime. 

Ultimately, we found that cosine-similarity was ineffective as a proxy for entailment despite its speed. Our accuracy with cosine-similarity was better than random guessing, but it was significantly below the baseline for entailment established by traditional approaches. However, we did find success with LSH.

## Problem Statement

Our goals were to determine the efficacy of cosine-similarity as a proxy for question entailment and whether LSH significantly speeds up premise retrieval from memory with minimal accuracy loss.

## Related Work

https://arxiv.org/pdf/1901.08079.pdf - a question entailment approach to question answering  
https://arxiv.org/pdf/1907.04286.pdf - medical NLI  
https://arxiv.org/pdf/1911.02116.pdf - XLM-R pretraining for cross-lingual performance  

Abacha et al. explored other approaches to question answering, acknowledging its increasing relevance due to the increasing numbers of similar questions posted online and its potential for building QA systems. Several question entailment approaches are detailed here, including but not limited to a logistic regression approach containing cosine similarity as a feature.

Kearns et al. approached the Natural Language Inference task by predicting inference labels with features extracted from language models, hypothesizing that inference models can effectively capture the semantic information in the extracted embeddings.

Conneau et al. revolutionized cross lingual approaches by taking the pretraining approach one step further. By pretraining at a very large scale, they achieved significant performance gains on XNLI, particularly with low resource languages.

As for data, we used the Stanford Natural Language Inference (SNLI) Corpus and Cross-Lingual NLI (XNLI) Corpus. Each dataset had premise-hypothesis pairs classified as either entailment, contradiction, or neutral. SNLI was an english dataset with manually labeled premise-hypothesis pairs classified as either entailment, contradiction, or neutral. XNLI added different languages to the mix, with each premise-hypothesis pair appearing 15 times in 15 different languages.

## Methodology

We first pruned each dataset down to just entailment pairs. Then, we fed the premise in each premise-hypothesis pairs into the XLM-RoBERTa model, extracted the last layer features, and hashed each feature vector into buckets with LSH. Following that, for each hypothesis sentence, we generated a feature vector and compared it with other premise feature vectors in the same bucket via cosine similarity. From that comparison, we generated top 1, top 5, and top 10 rankings for premises that could entail the hypothesis. Accuracy was determined based on if the premises in those rankings were actually the premises that entailed the hypothesis. Additionally, the experiment runtime was used as a measure of effectiveness of LSH.

## Evaluation

We evaluated our cosine-similarity results based on the accuracy of the top 1, top 5, and top 10 premise rankings. Top 1 rankings were based on if the top ranked premise for a hypothesis was in fact the associated premise for that hypothesis. Top 5 and 10 rankings were similar, but had more leeway in that accuracy was determined based on if the correct premise for a hypothesis was in the top 5 or top 10 premises selected for that hypothesis, respectively. Ultimately, our determination of cosine-similarity success was based on the comparison of those rankings to the baseline accuracy of the XLMR model.

We evaluated the LSH results based on the runtime of experiments with and without LSH.

## Results

### SNLI results

(LSH_X_Y indicates locality sensitive hashing was used with X tables and Y hyperplanes.)

| Experimental Setups  | Top 1 Accuracy (%) | Top 5 Accuracy (%) | Top 10 Accuracy (%) | Table Generation Time (s) | Experiment Runtime (s) | Dataset Size (pairs) |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| No hash  | 1.34  | 1.49  | 1.63  | 120.66  | 230.49  | 673  |
| LSH_1_16  | 1.34  | 1.49  | 1.49  | 130.68  | 206.04  | 673  |
| LSH_5_16  | 1.19  | 1.19  | 1.49  | 130.39  | 230.32  | 673  |
| LSH_10_16  | 0.89  | 1.19  | 1.34  | 128.03  | 231.81  | 673  |
| LSH_1_32  | 1.34  | 1.34  | 1.49  | 242.72  | 199.95  | 673  |
| LSH_5_32  | 1.19  | 1.34  | 1.34  | 249.15  | 249.09  | 673  |
| LSH_10_32  | 0.89  | 1.49  | 1.49  | 258.71  | 267.21  | 673  |
| No hash  | 0.98  | 1.28  | 1.48  | 1193.64  | 2227.06  | 3368  |
| LSH_1_64  | 0.27  | 0.30  | 0.30  | 1319.49  | 1032.78  | 3368  |
| LSH_5_64  | 0.39  | 0.59  | 0.59  | 1332.03  | 1083.58  | 3368  |
| LSH_10_64  | 0.48  | 0.59  | 0.62  | 1332.00  | 1075.54  | 3368  |

### XNLI results

(All language columns are top 10 accuracy (%) for the given language)  
Legend:  
* AR: Arabic
* BG: Bulgarian
* DE: German
* EL: Greek
* ES: Spanish
* FR: French
* HI: Hindi
* RU: Russian
* SW: Swahili
* TH: Thai
* TR: Turkish
* UR: Urdu
* VI: Vietnamese
* ZH: Chinese

| Experimental Setups  | AR | BG | DE | EL | ES | FR | HI | RU | SW | TH | TR | UR | VI | ZH | Table Generation Time (s) | Experiment Runtime (s) | Dataset Size (pairs) |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| No hash  | 3.60  | 5.09  | 3.00  | 3.89  | 3.89  | 3.59  | 1.50  | 5.39  | 4.49  | 3.89  | 2.40  | 2.69  | 4.49  | 2.10  | 126.59  | 1457.08  | 334  |
| LSH_1_16  | 1.50  | 1.50  | 0.90  | 1.20  | 1.20  | 1.80  | 0.30  | 1.80  | 1.50  | 0.90  | 0.60  | 0.90  | 1.50  | 0.00  | 139.61  | 1465.46  | 334  |
| LSH_5_16  | 2.40  | 3.00  | 2.10  | 2.70  | 2.40  | 3.00  | 1.20  | 2.99  | 3.59  | 1.80  | 1.80  | 1.80  | 3.90  | 0.90  | 139.59  | 1490.43  | 334  |
| LSH_10_16  | 2.40  | 3.59  | 3.00  | 2.69  | 2.40  | 3.29 | 1.20  | 3.59  | 4.19  | 2.40  | 1.20  | 1.50  | 3.59  | 1.20  | 139.27  | 1500.97  | 334  |
| LSH_1_32  | 0.30  | 0.00  | 0.90  | 0.60  | 0.30  | 0.30  | 0.30  | 0.30  | 0.00  | 0.60  | 0.30  | 0.30  | 0.00  | 0.30  | 139.27  | 1500.97  | 334  |
| LSH_5_32  | 0.90  | 1.50  | 1.50  | 0.60  | 0.30  | 1.50  | 0.90  | 1.80  | 0.90  | 0.30  | 1.20  | 0.60  | 0.30  | 0.90  | 136.81  | 1477.36  | 334  |
| LSH_10_32  | 0.90  | 0.90  | 2.10  | 1.50  | 0.60  | 0.60  | 0.60  | 1.20  | 0.60  | 1.50  | 0.30  | 0.90  | 1.80  | 1.50  | 137.09  | 1492.94  | 334  |


## Examples

## Video

## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/nanjamu/DeepLearningFinalProject/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/nanjamu/DeepLearningFinalProject/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
