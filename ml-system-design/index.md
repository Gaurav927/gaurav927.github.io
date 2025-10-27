# Machine Learning System Design Framwork

Many of the Machine Learning Scientist or ML Engineer goes through ML System Design Interview, In this blog we are going to discuss a set of checks that a interviewee should include while solution formulation. I have gone through the Machine Learning System Design book by Chip Huyen couple of times, most of items are taken from the books.


* Clarify the requirements
* Frame the business problems as an ML Problem
    * What sort of problem are you going to solve?
* Prepare the Data
    * Mention Data Sources
    * Mention the label (how are you going to capture?)
    * Feature Engineering 
        * Handling Missing Values
        * Trade of between One-hot Encoding vs Other Encoders 
        * Scaling
        * Embedding
    * Data Leakages
    * How will you prepare training data, test data and validation data?
    * Sampling, Labeling, Imbalance Issues?
    * 
* Select and Develop the Model
    * Define the Model
    * Two - Tower Architecture
* Evaluate the Model
    * Offline Evaluation
    * A/B Testing / Interleaving test
* Deploy the Model
    *  Deployment Mode (Canary, Shadow)
    *  Prediction (Batch vs Online)
    * Model Compression (Model Distillation, Pruning, Low Rank Factorization, Quantization)
    * ML On Cloud or Edge
    * 
* Monitor the Deployment
    * Monitor the Data- drift
        * Covariate Shift
        * Lalel Shift
        * Concept Shift
    * Monitor ML Metrics
    * Continual Learning (Stateless Training vs Stateful Training)
    * Model Performance and need for re-training
    * Infrastructure
        * Docker, Kubernetes
    * Responsible AI (Ethics)


