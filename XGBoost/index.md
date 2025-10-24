# Learning nuts and bolt of XGboost
## Introduction
Gradient Tree boosting has gained a wide popularity in recent years, Its has been used in many Kaggle wining competetions. Here we going deep dive into understanding the Mathematics behind the working of Xgboost and whats makes it scalable.
Take the challenges hosted by the machine learning competition site Kaggle for example. Among
the 29 challenge winning solutions 3 published at Kaggle’s
blog during 2015, 17 solutions used XGBoost.

Here is link of the paper for the reference: [XGBoost: A Scalable Tree Boosting System](https://arxiv.org/pdf/1603.02754)

We
will first review tree boosting and introduce a regularized
objective in Sec. 2. We then describe the split finding meth-
ods in Sec. 3 as well as the system design in Sec. 4, including
experimental results when relevant to provide quantitative
support for each optimization we describe. Related work
is discussed in Sec. 5. Detailed end-to-end evaluations are
included in Sec. 6. Finally we conclude the paper in Sec. 7



