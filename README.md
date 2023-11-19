# Play.Catalog

Play Economy Catalog microservice

## Create and publish package

MacOS

```shell
version='1.0.4'
owner='iga1dotnetmicroservices'
gh_pat='[PAT HERE]'

dotnet pack src/Play.Catalog.Contracts --configuration Release -p:PackageVersion=$version -p:RepositoryUrl=https://github.com/$owner/play.catalog.git -o ../packages

dotnet nuget push ../packages/Play.Catalog.Contracts.$version.nupkg --api-key $gh_pat --source "github"
```

Windows Powershell

```powershell
$version='1.0.4'
$owner='iga1dotnetmicroservices'
$gh_pat='[PAT HERE]'

dotnet pack src/Play.Catalog.Contracts --configuration Release -p:PackageVersion=$version -p:RepositoryUrl=https://github.com/$owner/play.catalog.git -o ../packages

dotnet nuget push ../packages/Play.Catalog.Contracts.$version.nupkg --api-key $gh_pat --source "github"
```

## Build the docker image

MacOS

```shell
appname='iga1playeconomy'

export GH_OWNER='iga1dotnetmicroservices'
export GH_PAT='[PAT HERE]'
docker build --secret id=GH_OWNER --secret id=GH_PAT -t "$appname.azurecr.io/play.catalog:$version" .
```

Windows Shell

```powershell
$appname='iga1playeconomy'

$env:GH_OWNER='iga1dotnetmicroservices'
$env:GH_PAT='[PAT HERE]'
docker build --secret id=GH_OWNER --secret id=GH_PAT -t "$appname.azurecr.io/play.catalog:$version" .
```

## Run the docker image

MacOS

```shell 
authority='[AUTHORITY]'
cosmosDbConnString='[CONN STRING HERE]'
serviceBusConnString='[CONN STRING HERE]'

docker run -it --rm -p 5000:5000 --name catalog -e MongoDbSettings__ConnectionString=$cosmosDbConnString -e ServiceBusSettings__ConnectionString=$serviceBusConnString -e ServiceSettings__Authority=$authority -e ServiceSettings__MessageBroker="SERVICEBUS" play.catalog:$version
```

Windows

```powershell
$authority='[AUTHORITY]'
$cosmosDbConnString='[CONN STRING HERE]'
$serviceBusConnString='[CONN STRING HERE]'

docker run -it --rm -p 5000:5000 --name catalog -e MongoDbSettings__ConnectionString=$cosmosDbConnString -e ServiceBusSettings__ConnectionString=$serviceBusConnString -e ServiceSettings__Authority=$authority -e ServiceSettings__MessageBroker="SERVICEBUS" play.catalog:$version
```

## Publishing the docker image

MacOS

```powershell
appname='iga1playeconomy'
az acr login --name $appname
docker push "$appname.azurecr.io/play.catalog:$version"
```

Windows

```powershell
$appname='iga1playeconomy'
az acr login --name $appname
docker push "$appname.azurecr.io/play.catalog:$version"
```

## Create the Kubernetes namespace

MacOS

```shell
namespace='catalog'
kubectl create namespace $namespace
```

Windows

```powershell
$namespace='catalog'
kubectl create namespace $namespace
```

## Creating the pod managed identity

Mac OS

```shell
az identity create --resource-group $appname --name $namespace

IDENTITY_RESOURCE_ID=$(az identity show -g $appname -n $namespace --query id -otsv)

az aks pod-identity add --resource-group $appname --cluster-name $appname --namespace $namespace --identity-resource-id $IDENTITY_RESOURCE_ID
```

Windows

```powershell
az identity create --resource-group $appname --name $namespace

$IDENTITY_RESOURCE_ID=az identity show -g $appname -n $namespace --query id -otsv

az aks pod-identity add --resource-group $appname --cluster-name $appname --namespace $namespace --identity-resource-id $IDENTITY_RESOURCE_ID
```

## Granting access to Key Vault secrets

MacOS

```shell
IDENTITY_CLIENT_ID=$(az identity show -g $appname -n $namespace --query clientId -otsv)
az keyvault set-policy -n $appname --secret-permissions list get --spn $IDENTITY_CLIENT_ID
```

MacOS

```powershell
$IDENTITY_CLIENT_ID=az identity show -g $appname -n $namespace --query clientId -otsv
az keyvault set-policy -n $appname --secret-permissions list get --spn $IDENTITY_CLIENT_ID
```

## Creating the Kubernetes resources

```powershell
kubectl apply -f ./kubernetes/catalog.yaml -n $namespace
```
