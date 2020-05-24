# streamlit-azure-examples

Repo containing Azure examples for building/deploying a Streamlit application.

## Prerequisites for guides

- Azure account
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)

## Running locally

Streamlit is as easy `pip install streamlit` (either globally or in a virtualenv) and then running `streamlit hello`.

## Building example application

Build the example application as a Docker image:

```
docker build -t streamlit-example -f app/Dockerfile app
```

Run the application:

```
docker run -p 8501:8501 streamlit-example
```
