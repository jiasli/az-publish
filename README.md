# az-publish

A Python command-line tool that can

1. Publish Python build artifacts (sdist and wheel files) to Azure Storage Account
2. Create [PEP 503](https://www.python.org/dev/peps/pep-0503/) index

## Install dependencies

```sh
pip install --requirement requirements.txt
```

## Publish

```sh
# Replace these arguments with your own values
dist='pypi'
storage='<storage_name>'
container='<container_name>'
expiry=$(date --utc --date "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Generate container SAS token with create/write/list permissions
sas=$(az storage container generate-sas --account-name $storage --name $container --expiry $expiry --permissions cwl --output tsv)

# Publish to Storage Account
python az-publish.py --dist $dist --account $storage --container $container --sas "$sas"
```

## Install the published package

```sh
# Bash
env='<env_name>'
# PowerShell
$env = '<env_name>'

# Create a virtual environment
python -m venv $env

# Activate the virtual environment
# Bash
. $env/bin/activate
# PowerShell
. .\$env\Scripts\Activate.ps1

# Install the published packaged, like 'azure-cli'
pip install --no-cache-dir --pre --extra-index-url https://$storage.blob.core.windows.net/$container/simple/ azure-cli
```

## Delete the installed package

```sh
# Deactivate the virtual environment
deactivate

# Delete the environment
# Bash
rm -rf $env
# PowerShell
Remove-Item -Force -Recurse $env
```
