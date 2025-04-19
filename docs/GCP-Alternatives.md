# GCP Current Workflow

The model processes bond pairs and makes predictions using:

Z-spread differences
Historical pricing data
Forward-filled missing values
Normalized inputs
If you're interested in seeing how the actual model works, I'd recommend looking at:

pipelines/generate/models/compression_probability_model.py
pipelines/generate/components/model_loaders/vertex_ai.py
The system appears to use Google Cloud's Vertex AI for model serving rather than traditional time series models like ARIMA.

Here's a breakdown of vertex_ai.py and how to implement it on other cloud providers:

Current Implementation (Google Cloud):
class VertexAIModelLoader:
    # Initializes connection to Google Cloud
    def __init__(self, project_id: str, location: str)
    
    # Loads model from Vertex AI endpoint
    def load(self, model_id: str, model_options: Dict[str, Any]) -> Model

class VertexAIModelClient:
    # Makes predictions using loaded endpoint
    def predict(self, candidate: IdeaCandidate) -> ModelResult


## Core Components Required for Any Cloud Provider:
Model endpoint management
Authentication
Prediction service
Response handling

**Implementation Guide for Other Providers:**
### AWS
AWS Implementation:

class SageMakerModelLoader(ModelLoader):
    def __init__(self, region: str):
        import boto3
        self.sagemaker = boto3.client('sagemaker-runtime')
        
    def load(self, model_id: str, model_options: Dict[str, Any]) -> Model:
        return SageMakerModelClient(
            endpoint_name=model_id,
            sagemaker_client=self.sagemaker
        )

class SageMakerModelClient(Model):
    def predict(self, candidate: IdeaCandidate) -> ModelResult:
        response = self.sagemaker_client.invoke_endpoint(
            EndpointName=self.endpoint_name,
            Body=json.dumps(candidate.to_json())
        )
        prediction = json.loads(response['Body'].read())
        return ModelResult(**prediction)

### Azure Implementation:

class AzureMLModelLoader(ModelLoader):
    def __init__(self, workspace: str):
        from azureml.core import Workspace
        self.ws = Workspace.from_config(workspace)
        
    def load(self, model_id: str, model_options: Dict[str, Any]) -> Model:
        return AzureMLModelClient(
            endpoint_name=model_id,
            workspace=self.ws
        )

class AzureMLModelClient(Model):
    def predict(self, candidate: IdeaCandidate) -> ModelResult:
        service = Model.get_service(self.workspace, self.endpoint_name)
        response = service.run(json.dumps(candidate.to_json()))
        return ModelResult(**response)


Key requirements for any implementation:

Model serving endpoint
Authentication mechanism
JSON serialization/deserialization
Error handling
Response formatting to match ModelResult interface
The core logic remains the same - only the cloud-specific SDK calls need to be adapted.