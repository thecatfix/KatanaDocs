# Forecasting Models - How many exist?

## Prediction Model

[model.py](https://gitlab.com/katanalabs/katana/-/blob/a4f73f37a8c9a1c0baf2e614125739d41ac0d908/src/generate/components/domain/model.py)

This documentation provides an overview of the `model.py` module, including descriptions of the `ModelResult`, `Model`, and `ModelLoader` classes, their attributes, and methods. It's formatted in a way that's suitable for a GitLab wiki and can be easily integrated into your project documentation.

This module defines abstract base classes for machine learning models and model loaders used in the project.

## Classes

### ModelResult

Represents the result of a model prediction.

#### Attributes

- `alert` (bool): Indicates whether an alert should be triggered.
- `alternative` (bool): Indicates whether this is an alternative result.
- `similarity_score` (Optional[float]): The similarity score of the prediction.
- `pearson_score` (Optional[float]): The Pearson correlation score.
- `adf_score` (Optional[float]): The Augmented Dickey-Fuller test score.

### Model

An abstract base class for machine learning models.

#### Attributes

- `id` (str): A unique identifier for the model.

#### Methods

##### _preprocess

```python
@abstractmethod
def _preprocess(self, input: PI) -> PR:
```

Model-specific transforms of the input data.

**Arguments:**

- `input` (PI): The input data to preprocess.

**Returns:**

- `PR`: The preprocessed data.

##### predict

```python
@abstractmethod
def predict(self, candidate: IdeaCandidate) -> ModelResult:
```

Makes a prediction for a given candidate.

**Arguments:**

- `candidate` (IdeaCandidate): The candidate to make a prediction for.

**Returns:**

- `ModelResult`: The result of the prediction.

##### save

```python
def save(self):
```

Save the model (typically weights, e.g., for a neural network) to a path in GCloud.

### ModelLoader

An abstract base class for model loaders.

#### Methods

##### load

```python
@abstractmethod
def load(self, model_path: str, model_options: Optional[Dict[str, Any]]) -> Model:
```

Loads a model from a specified path.

**Arguments:**

- `model_options` (Optional[Dict[str, Any]]): Additional options for loading the model.

**Returns:**

- `Model`: The loaded model.

### Type Variables

- `PI`: Type variable for model input.
- `PR`: Type variable for preprocessed input.

## Usage

This module provides abstract base classes that should be inherited and implemented by concrete model and model loader classes. The `Model` class defines the interface for machine learning models used in the project, while the `ModelLoader` class defines how these models should be loaded.

**Example usage (implementation in a derived class):**

```python
class ConcreteModel(Model[InputType, ProcessedType]):
        def _preprocess(self, input: InputType) -> ProcessedType:
                # Implement preprocessing logic
                pass

        def predict(self, candidate: IdeaCandidate) -> ModelResult:
                # Implement prediction logic
                pass

class ConcreteModelLoader(ModelLoader):
        def load(self, model_path: str, model_options: Optional[Dict[str, Any]]) -> Model:
                # Implement model loading logic
                pass
```

**Note:** The actual implementation of these classes will depend on the specific requirements of your project and the machine learning models you're using.

## What is the model id?

Based on the `parse_arguments` function in the code, the model ID is specified as follows:

```python
parser.add_argument('--model_id',
                                        type=str,
                                        default='stationarity-model_a875ef6f-e259-4c42-8d49-aa8484bd4713')
```

The default model ID is `'stationarity-model_a875ef6f-e259-4c42-8d49-aa8484bd4713'`. This appears to be a unique identifier for the model, likely stored in Google Cloud Storage (GCS). The ID consists of two parts:
1. The model type: `"stationarity-model"`
2. A UUID: `"a875ef6f-e259-4c42-8d49-aa8484bd4713"`

This model ID is used to locate and load the specific model for generating trade ideas. It's worth noting that this is the default value, and it could be overridden by providing a different model ID as a command-line argument when running the pipeline.

## Finding other models (possibly in experiments)

To find other model IDs, you should look in the following places:

- **Google Cloud Storage (GCS):** Check the GCS bucket specified by the `--models_bucket` argument (default is `'models'`). Look for objects or folders with names following a similar pattern to the default model ID.
- **Configuration Files:** Search for YAML, JSON, or Python configuration files in your project that might list available models.
- **Database:** If your project uses a database to track models, query it for a list of model IDs.
- **Model Registry:** If you're using a model registry (like MLflow or Vertex AI Model Registry), check there for registered models.
- **CI/CD Pipeline Configurations:** Look in your CI/CD configurations (e.g., `.gitlab-ci.yml`, Jenkins files) for model ID references.
- **Codebase:** Search your codebase for files or functions that might define or use model IDs.
- **Logs:** Examine pipeline run logs to see which model IDs have been used in past executions.
- **Team Communication:** Ask your data science or machine learning team if they maintain a list of current model IDs.
- **Model Training Scripts:** Look for scripts that train models; they might generate and output model IDs.

Since there's an abstract `Model` class defined, this class is designed to be generic and can be extended to create different types of models.

The `Model` class in this file is abstract and defines a general structure for models, including methods for preprocessing, prediction, and saving.

To find out if there are other models in your group or project that could replace the stationary model, you might want to:

- Check other Python files in your project for classes that inherit from this `Model` class.
- Look for implementations of the `ModelLoader` class, which might give clues about what models are available.
- Check the version control history to see if there have been recent additions or changes to model implementations.
```
