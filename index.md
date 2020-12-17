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

From these results, we can see that cosine-similarity was very ineffective, as the accuracy percentages were extremely low. Typical accuracy for the SNLI and XNLI datasets would be at least 70%, and cosine-similarity did not come anywhere near that. On the other hand, LSH seemed to significantly decrease runtime, particularly when the dataset size used was larger. At a low number of pairs like 334 or 673, it was not as effective because it was largely unneeded, but when the number of pairs used was increased to 3368, LSH halved the experiment runtime.

## Example

The following is an example of a top 10 ranking by cosine-similarity given an SNLI premise and hypothesis.  
Hypothesis: Man eating on a bench near a child.  
Correct premise: A Man is eating food next to a child on a bench.
Cosine-similarity rankings:

| Ranking  | Premise | Score |
| ------------- | ------------- | ------------- |
| 1  | A man is eating food next to a child on a bench.  | 0.9911  |
| 2  | An Asian man makes faces for the camera.  | 0.9895  |
| 3  | A man walks by a building at night.  | 0.9882  |
| 4  | An medical worker examines a young girl.  | 0.9868  |
| 5  | Tourists waiting outside of a home to visit.  | 0.9850  |
| 6  | A woman lowering ballast on a boat.  | 0.9848  |
| 7  | Uniformed group walks down a street.  | 0.9846  |
| 8  | A woman poses for a picture with a child.  | 0.9841  |
| 9  | A man and a Woman cutting dough.  | 0.9841  |
| 10  | A man is throwing something into the road.  | 0.9835  |

As can be seen in the rankings, premises are assigned a score based on how likely they are to entail the hypothesis, and their ranks are determined by that score.
  
  
The following is an example of a top 10 ranking by cosine-similarity given an XNLI premise and hypothesis.  
Hypothesis: أردنا إنقاذ شيء واحد أكثر من الباقي.
Language: AR
Corresponding english hypothesis: That was the primary thing we wanted to save since there wasn't any way to dump a 20-megaton H-bomb off a 30, a C124.
Correct premise: We wanted to save one thing more than the rest.
Cosine-similarity rankings:

| Ranking  | Premise | Score |
| ------------- | ------------- | ------------- |
| 1  | Sure, then, I'll be telling ye.  | 0.9635  |
| 2  | Russia is facing increasing challenges in the Chechen war.  | 0.9627  |
| 3  | If you find any Peculiar (Missouri) names or ones that comes as a Surprise (Nebraska), just Jot 'Em Down (Texas) Safely (Tennessee)--unless, of course, they are Errata (Mississippi).  | 0.1551  |
| 4  | Walcott trained to be a painter--like his schoolteacher father, who died when Walcott was a baby--and The Bounty is his most painterly book, in method and theme.  | 0.0973  |
| 5  | The Move to San Diego By February 4, Hazmi and Mihdhar had come to San Diego from Los Angeles, possibly driven by Mohdar Abdullah.  | 0.0926  |
| 6  | The data presented in this appendix are based on the demographic data for the 5-Digit ZIP Code for each route in a quartile.  | 0.0913  |
| 7  | Although Rock 'n' Roll was racing down the fast lane like a candy apple VETTE, FOREVER PLAID believed in their music.  | 0.0872  |
| 8  | In a sense, it seems illogical that we have retained the historic spellings for Spenser's works yet use modern spellings for the titles of plays by his contemporary, William Shakespeare.  | 0.0854  |
| 9  | We wanted to save one thing more than the rest.  | 0.0841  |
| 10  | But I can't forget that when I was no better than a slave in your uncle's household in Barbados, ye used me with a certain kindness.  | 0.0582  |

In the results from the XNLI example, we can see that the correct premise was in the top 10 premises for the hypothesis, but was ranked 9th. Additionally, it was given a very low score relative to the top 2 premises, showing the limitations of just using cosine-similarity.

## Video
