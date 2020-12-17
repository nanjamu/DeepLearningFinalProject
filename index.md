## Abstract

Question-answering through highlighting has limited direct applications in the real world, while question-entailment has direct applications such as producing a medical FAQ with a good database of question/answer pairs and a retrieval algorithm. For this project, question-entailment is defined as whether a hypothesis sentence is true given a premise sentence. We want to optimize cross-lingual performance, paying particular attention to low-resource languages. We also want to see if cosine-similarity is a good proxy for entailment due to it's low cost. 

Ultimately, we found that cosine-similarity was ineffective as a proxy for entailment despite its speed. Our accuracy with cosine-similarity was better than random guessing, but it was significantly below the baseline for entailment established by traditional approaches.

## Problem Statement

Our goal was to determine the efficacy of cosine-similarity as a proxy for question entailment.

## Related Work

https://arxiv.org/pdf/1901.08079.pdf - a question entailment approach to question answering  
https://arxiv.org/pdf/1907.04286.pdf - medical NLI  
https://arxiv.org/pdf/1911.02116.pdf - XLM-R pretraining for cross-lingual performance  

Abacha et al. explored other approaches to question answering, acknowledging its increasing relevance due to the increasing numbers of similar questions posted online and its potential for building QA systems. Several question entailment approaches are detailed here, including but not limited to a logistic regression approach containing cosine similarity as a feature.

Kearns et al. approached the Natural Language Inference task by predicting inference labels with features extracted from language models, hypothesizing that inference models can effectively capture the semantic information in the extracted embeddings.

Conneau et al. revolutionized cross lingual approaches by taking the pretraining approach one step further. By pretraining at a very large scale, they achieved significant performance gains on XNLI, particularly with low resource languages.

As for data, we used the Stanford Natural Language Inference (SNLI) Corpus, Multi-Genre NLI (MNLI) Corpus, and Cross-Lingual NLI (XNLI) Corpus. Each dataset had premise-hypothesis pairs classified as either entailment, contradiction, or neutral. SNLI and MNLI were similar in that they were both english datasets, but MNLI was larger with more genres and modes of communication than SNLI. XNLI added different languages to the mix, with each premise-hypothesis pair appearing 15 times in 15 different languages.

## Methodology

We first pruned each dataset down to just entailment pairs. Then, we fed the premise in each premise-hypothesis pairs into the XLM-RoBERTa model, extracted the last layer features, and hashed each feature vector into buckets with locality sensitive hashing. Following that, for each hypothesis sentence, we generated a feature vector and compared it with other premise feature vectors in the same bucket via cosine similarity. From that comparison, we generated top 1, top 5, and top 10 rankings for premises that could entail the hypothesis. Accuracy was determined based on if the premises in those rankings were actually the premises that entailed the hypothesis.

## Evaluation

We evaluated our results based on the accuracy of the top 1, top 5, and top 10 premise rankings. Top 1 rankings were based on if the top ranked premise for a hypothesis was in fact the associated premise for that hypothesis. Top 5 and 10 rankings were similar, but had more leeway in that accuracy was determined based on if the correct premise for a hypothesis was in the top 5 or top 10 premises selected for that hypothesis, respectively. Ultimately, our determination of success was based on the comparison of those rankings to the baseline accuracy of the XLMR model.

## Results

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
