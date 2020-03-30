---
title: Konfigurera CI/CD med GitHub-åtgärder
description: Lär dig hur du distribuerar din kod till Azure App Service från en CI/CD-pipeline med GitHub-åtgärder. Anpassa bygguppgifterna och kör komplexa distributioner.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 4a8b3cf47235e061e5dbcc08a409fce84d421771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562215"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Distribuera till App Service med GitHub-åtgärder

[GitHub-åtgärder](https://help.github.com/en/articles/about-github-actions) ger dig flexibiliteten att skapa ett automatiserat livscykelarbetsflöde för programvaruutveckling. Med Azure App Service Actions for GitHub kan du automatisera arbetsflödet för att distribuera till [Azure App Service](overview.md) med GitHub-åtgärder.

> [!IMPORTANT]
> GitHub-åtgärder är för närvarande i beta. Du måste först [registrera dig för att gå med i förhandsgranskningen](https://github.com/features/actions) med ditt GitHub-konto.
> 

Ett arbetsflöde definieras av en YAML-fil (.yml) i `/.github/workflows/` sökvägen i databasen. Den här definitionen innehåller de olika steg och parametrar som utgör arbetsflödet.

För ett Azure App Service-arbetsflöde har filen tre avsnitt:

|Section  |Aktiviteter  |
|---------|---------|
|**Autentisering** | 1. Definiera ett huvudnamn för tjänsten <br /> 2. Skapa en GitHub-hemlighet |
|**Utveckla** | 1. Ställ in miljön <br /> 2. Bygg webbappen |
|**Distribuera** | 1. Distribuera webbappen |

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan skapa ett [tjänsthuvudnamn](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) med kommandot [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) i [Azure CLI](https://docs.microsoft.com/cli/azure/). Du kan köra det här kommandot med [Azure Cloud Shell](https://shell.azure.com/) i Azure-portalen eller genom att välja knappen **Prova.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

I det här exemplet ersätter du platshållarna i resursen med ditt prenumerations-ID, resursgruppsnamn och appnamn. Utdata är de autentiseringsuppgifter för rolltilldelning som ger åtkomst till apptjänstappen. Kopiera det här JSON-objektet, som du kan använda för att autentisera från GitHub.

> [!NOTE]
> Du behöver inte skapa ett tjänsthuvudnamn om du bestämmer dig för att använda publiceringsprofilen för autentisering.

> [!IMPORTANT]
> Det är alltid en god praxis att ge minimal tillgång. Det är därför omfånget i föregående exempel är begränsat till den specifika App Service-appen och inte hela resursgruppen.

## <a name="configure-the-github-secret"></a>Konfigurera GitHub-hemligheten

Du kan också använda autentiseringsuppgifter på appnivå, dvs. Följ stegen för att konfigurera hemligheten:

1. Ladda ned publiceringsprofilen för App Service-appen från portalen med alternativet **Hämta publiceringsprofil.**

2. I [GitHub](https://github.com/)väljer du **Inställningar > hemligheter > Lägg till en ny hemlighet**

    ![secrets](media/app-service-github-actions/secrets.png)

3. Klistra in innehållet för den nedladdade publiceringsprofilfilen i hemlighetens värdefält.

4. Nu i arbetsflödesfilen i `.github/workflows/workflow.yml` din gren: `publish-profile` ersätt hemligheten för indata från den distribuerade Azure Web App-åtgärden.
    
    ```yaml
        - uses: azure/webapps-deploy@v1
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. Du ser hemligheten som visas nedan en gång definierat.

    ![secrets](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>Konfigurera miljön

Ställa in miljön kan göras med hjälp av en av inställningsåtgärderna.

|**Språk**  |**Inställningsåtgärd**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**Javascript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

I följande exempel visas den del av arbetsflödet som ställer in miljön för de olika språk som stöds:

**Javascript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>Skapa webbappen

Detta beror på språket och för språk som stöds av Azure App Service, bör det här avsnittet vara standard byggstegen för varje språk.

I följande exempel visas den del av arbetsflödet som bygger webbappen på de olika språk som stöds.

**Javascript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-to-app-service"></a>Distribuera till App Service

Om du vill distribuera koden till `azure/webapps-deploy@v1 ` en App Service-app använder du åtgärden. Den här åtgärden har fyra parametrar:

| **Parametern**  | **Förklaring**  |
|---------|---------|
| **app-namn** | (Obligatoriskt) Namn på apptjänstappen | 
| **publicera-profil** | (Valfritt) Publicera profilfilinnehåll med webbdetränna hemligheter |
| **Paket** | (Valfritt) Sökväg till paket eller mapp. *.zip, *.war, *.jar eller en mapp för att distribuera |
| **kortplatsnamn** | (Valfritt) Ange en annan befintlig platsplats än produktionsplatsen |

### <a name="deploy-using-publish-profile"></a>Distribuera med publiceringsprofil

Nedan finns exempelarbetsflödet för att skapa och distribuera en Node.js-app till Azure med hjälp av publiceringsprofilen.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v1
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Distribuera med Azure-tjänstens huvudnamn

Nedan finns exempelarbetsflödet för att skapa och distribuera en Node.js-app till Azure med hjälp av ett Huvudnamn för Azure-tjänsten.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v1
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>Nästa steg

Du hittar vår uppsättning åtgärder grupperade i olika databaser på GitHub, var och en som innehåller dokumentation och exempel som hjälper dig att använda GitHub för CI/CD och distribuera dina appar till Azure.

- [Arbetsflöde för åtgärder som ska distribueras till Azure](https://github.com/Azure/actions-workflow-samples)

- [Azure-inloggning](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp för behållare](https://github.com/Azure/webapps-container-deploy)

- [Docker inloggning / utloggning](https://github.com/Azure/docker-login)

- [Händelser som utlöser arbetsflöden](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Distribution av K8:er](https://github.com/Azure/k8s-deploy)

- [Startarbetsflöden](https://github.com/actions/starter-workflows)
