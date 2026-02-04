Pipeline Architecture
The pipeline is structured into two distinct optimization paths: Scikit-learn with Hyperdrive and Automated ML (AutoML).

1. Data Ingestion & Preprocessing
Data Source: The dataset is fetched from a remote URL using TabularDatasetFactory.

Cleaning: The clean_data function performs several transformations:

One-hot encoding for categorical variables (job, contact, education).

Binary encoding for "yes/no" features (marital, default, housing, loan).

Mapping month and day strings to integer values.

Splitting: Data is split into 80% training and 20% testing sets to validate model performance.

2. Hyperparameter Tuning (Hyperdrive)
Algorithm: We utilized Logistic Regression from the Scikit-learn library. It is a robust, interpretable algorithm specifically suited for binary classification tasks like this.

Optimization: The Hyperdrive engine automates the search for the best configuration of:

--C: The inverse of regularization strength.

--max_iter: The maximum number of iterations for the solver to converge.

3. Automated ML (AutoML)
In parallel, an AutoML experiment was configured to automatically iterate through various algorithms (e.g., Random Forest, LightGBM, XGBoost) and scaling methods to find the optimal model without manual intervention.

Parameter Sampling
For this project, we used RandomParameterSampling.

Benefits:

Efficiency: Unlike Grid Sampling, which exhausts every possible combination, Random Sampling selects a subset of values. This significantly reduces computational costs and time while still exploring the search space effectively.

Discovery: It supports both discrete and continuous hyperparameters, allowing the model to discover "hidden" high-performing regions of the hyperparameter space that might be skipped by a rigid grid.

Early Termination Policy
We implemented the Bandit Policy as our early stopping mechanism.

Benefits:

Resource Management: It automatically terminates poorly performing runs. If a run’s primary metric (Accuracy) falls outside a specified slack factor relative to the best-performing run, it is canceled immediately.

Zero Waste: This ensures that compute power is not wasted on "hopeless" trials, allowing the pipeline to allocate those resources to more promising hyperparameter configurations.

Flexibility: The slack_factor and evaluation_interval allow us to be as aggressive or lenient as needed depending on the project budget and time constraints.

Summary of Best Model
The final step involves comparing the best run from Hyperdrive against the best run from AutoML. Usually, AutoML yields a higher accuracy by exploring complex ensemble models (like VotingEnsembles), whereas the Hyperdrive Logistic Regression provides a faster, highly interpretable baseline.