# Play.Catalog

Play Economy Catalog microservice

## Create and publish package

MacOS

```shell
version='1.0.2'
owner='iga1dotnetmicroservices'
gh_pat='[PAT HERE]'

dotnet pack src/Play.Catalog.Contracts --configuration Release -p:PackageVersion=$version -p:RepositoryUrl=https://github.com/$owner/play.catalog.git -o ../packages

dotnet nuget push ../packages/Play.Catalog.Contracts.$version.nupkg --api-key $gh_pat --source "github"
```

Windows Powershell

```powershell
$version='1.0.2'
$owner='iga1dotnetmicroservices'
$gh_pat='[PAT HERE]'

dotnet pack src/Play.Catalog.Contracts --configuration Release -p:PackageVersion=$version -p:RepositoryUrl=https://github.com/$owner/play.catalog.git -o ../packages

dotnet nuget push ../packages/Play.Catalog.Contracts.$version.nupkg --api-key $gh_pat --source "github"
```