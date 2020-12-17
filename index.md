## Abstract

Question-answering through highlighting has limited direct applications in the real world, while question-entailment has direct applications such as producing a medical FAQ with a good database of question/answer pairs and a retrieval algorithm. For this project, question-entailment is defined as whether a hypothesis sentence is true given a premise sentence. We want to optimize cross-lingual performance, paying particular attention to low-resource languages. We also want to see if cosine-similarity is a good proxy for entailment due to it's low cost. 

Ultimately, we found that cosine-similarity was ineffective as a proxy for entailment despite it's speed. Our accuracy with cosine-similarity was better than random guessing, but it was significantly below the baseline for entailment established by traditional approaches.

## Problem Statement

## Related Work

## Methodology
We first fed each premise in a dataset of premise-hypothesis sentence pairs into the XLM-RoBERTa model, extracted the last layer features, and hashed each feature vector into buckets with locality sensitive hashing. Following that, for each hypothesis sentence, we generated a feature vector and compared it with other premise feature vectors in the same bucket via cosine similarity. From that comparison, we generated top 1, top 5, and top 10 rankings for premises that could entail the hypothesis. Accuracy was determined based on if the premises in those rankings were actually the premises that entailed the hypothesis.

## Evaluation

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
