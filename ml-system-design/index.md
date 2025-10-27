# Machine Learning System Design Framework

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
    * Check for Data Leakages
    * How will you prepare training data, test data and validation data?
    * Sampling, Labeling, Imbalance Issues - how to handle?
    * 
* Select and Develop the Model
    * Define the Model
* Evaluate the Model
    * Mention the evaluation metrics, Guardrail metrics
    * Check if Model Caliberation is required
    * Offline Evaluation
    * A/B Testing / Interleaving test

* Deploy the Model
    *  Deployment Mode (Canary, Shadow)
    *  Prediction (Batch vs Online)
    * Model Compression (Model Distillation, Pruning, Low Rank Factorization, Quantization)
    * ML On Cloud or Edge

* Monitor the Deployment
    * Monitor the Data- drift
        * Covariate Shift
        * Lalel Shift
        * Concept Shift
    * Monitor ML Metrics
    * Continual Learning (Stateless Training vs Stateful Training) - Training frequency
    * Model Performance and need for re-training
    * Infrastructure
        * Docker, Kubernetes
    * Responsible AI (Ethics)

---
## AB Testing Framework

Think of Customer Funnel

* Success Metric
* State Hypothesis Statement & Alternative Hypothesis
    * Set Significance Level
    * Set Power of Test 
    * Minimum detectable lift
* Design the Experiment
    * Randomisation unit (think in terms of Customer Funnel)
        * Session level
        * User Level
        * Search level
    * Determine the sample size
    * N = 16sigma/d^2
    * Duration of experiment
* Run the Experiments
* Validity Check
    * Checks for Instrumentation factors
    * External Factors
    * Selection Bias (Run AA test)
    * Sample Ratio Mismatch
    * Novelty Effect 
* Interpret Results
* Launch Decision
    * Guard Rail Metric
    * Cost of Launching
