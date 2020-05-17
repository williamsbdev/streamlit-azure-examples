# streamlit-example application

A very basic streamlit example application to show how to build/deploy the application to Azure.

## Building example application

Build the example application as a Docker image:

```
docker build -t streamlit-example -f Dockerfile .
```

Run the application:

```
docker run -p 8501:8501 streamlit-example
```

## Building/pushing Docker image to Azure

Push to your Azure Container Registry (ACR) to be used in the Azure architecture examples.

### Prerequisites

- Azure account
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)

### Commands

1. Create resource group
1. Create the ACR (providing a globally unique name)
1. Authenticate with ACR
1. Build Docker image
1. Push Docker image to ACR

```
az group create --location centralus --name streamlit-example
az acr create --resource-group streamlit-example --name streamlit --sku Basic
az acr login --name streamlit
docker build -t streamlit.azurecr.io/streamlit-example:1 .
docker push streamlit.azurecr.io/streamlit-example:1
```
