# cli-example

Azure CLI guide to provide an example for how to deploy an Azure Container Instances (ACI) Streamlit application authenticated with Azure Active Directory (AAD).

![](images/azure-example-streamlit-architecture.png)

## Prerequisites for guides

- Azure account
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
- [Build/Push Docker image to ACR](../example-app/README.md#buildingpushing-docker-image-to-azure)

## Setup

Before starting, be sure that you've followed the [instructions](../example-app/README.md#buildingpushing-docker-image-to-azure) for building the example Docker image and pushing to ACR.

TODO: is this statement necessary?
**It is strongly recommend that you save the output of all the commands you run as there are values that will be ouput that will be needed for later commands.**

1. Deploy ACI (inspired by this guide https://docs.microsoft.com/en-us/azure/container-instances/container-instances-using-azure-container-registry)
    1. Create Azure Key Vault (AKV)
    1. Create service principal with access to ACR and store in the AKV created above
    1. Create ACI
1. Login to newly created application

### Deploy ACI

In order to deploy our ACI, we'll first have to create a service principal and store in AKV.

#### Create Azure Key Vault (AKV)

First we'll create the key vault.

```
az keyvault create \
  --resource-group streamlit-example \
  --name streamlitkeyvault
```

#### Create service principal with access to ACR and store in the AKV created above

This will nest the command to create a service principal in the command to store the password in the AKV.

```
az keyvault secret set \
  --vault-name streamlitkeyvault \
  --name streamlit-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://streamlit-pull \
                --scopes $(az acr show --name streamlit --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

This command will store the username in the AKV:

```
az keyvault secret set \
    --vault-name streamlitkeyvault \
    --name streamlit-pull-usr \
    --value $(az ad sp show --id http://streamlit-pull --query appId --output tsv)
```

#### Create ACI

Now the username/password for the service principal that has access to pull from ACR will be used so that ACI can pull the image when deployed:

```
az container create \
  --name streamlit-example \
  --resource-group streamlit-example \
  --image streamlit.azurecr.io/streamlit-example:1 \
  --cpu 1 \
  --memory 1 \
  --port 8501 \
  --registry-login-server streamlit.azurecr.io \
  --registry-username $(az keyvault secret show --vault-name streamlitkeyvault -n streamlit-pull-usr --query value -o tsv) \
  --registry-password $(az keyvault secret show --vault-name streamlitkeyvault -n streamlit-pull-pwd --query value -o tsv) \
  --dns-name-label streamlit-example-$RANDOM \
  --query ipAddress.fqdn
```

This will return output like this:

```
"streamlit-example-291.centralus.azurecontainer.io"
```

### Login to newly created application

Check your newly deployed application (https://azure-example.streamlit.io for this guide).

You will be prompted to login with the username that we created earlier. You should have gotten an email with your temporary password.
