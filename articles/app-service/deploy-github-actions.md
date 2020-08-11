---
title: Konfigurera CI/CD med GitHub-åtgärder
description: Lär dig hur du distribuerar din kod till Azure App Service från en CI/CD-pipeline med GitHub-åtgärder. Anpassa Bygg aktiviteterna och kör komplexa distributioner.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python
ms.openlocfilehash: 713f4228bc2ba968fc96668d4d5c568f33b7e786
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080291"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Distribuera till App Service med GitHub-åtgärder

[GitHub-åtgärder](https://help.github.com/en/articles/about-github-actions) ger dig flexibiliteten att bygga ett arbets flöde för automatiserad livs cykel för program utveckling. Med Azure App Service åtgärder för GitHub kan du automatisera arbets flödet för att distribuera till [Azure App Service](overview.md) med GitHub-åtgärder.

> [!IMPORTANT]
> GitHub-åtgärder är för närvarande Beta versioner. Du måste först [Registrera dig för att kunna ansluta till förhands granskningen](https://github.com/features/actions) med ditt GitHub-konto.
> 

Ett arbets flöde definieras av en YAML-fil (. yml) i `/.github/workflows/` sökvägen i lagrings platsen. Den här definitionen innehåller de olika stegen och parametrarna som utgör arbets flödet.

För ett Azure App Service-arbetsflöde har filen tre delar:

|Avsnitt  |Aktiviteter  |
|---------|---------|
|**Autentisering** | 1. definiera ett huvud namn för tjänsten. <br /> 2. skapa en GitHub-hemlighet. |
|**Skapa** | 1. Konfigurera miljön. <br /> 2. Bygg webb programmet. |
|**Distribuera** | 1. distribuera webbappen. |

## <a name="generate-deployment-credentials"></a>Generera autentiseringsuppgifter för distribution

# <a name="user-level-credentials"></a>[Autentiseringsuppgifter för användar nivå](#tab/userlevel)

Du kan skapa ett [huvud namn för tjänsten](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) med hjälp av kommandot [AZ AD SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) i [Azure CLI](https://docs.microsoft.com/cli/azure/). Du kan köra det här kommandot med [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja knappen **prova** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

I exemplet ovan ersätter du plats hållarna med ditt prenumerations-ID, resurs grupp namn och app-namn. Utdata är ett JSON-objekt med roll tilldelningens autentiseringsuppgifter som ger åtkomst till din App Service-app på liknande sätt som nedan. Kopiera det här JSON-objektet för senare.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Det är alltid en bra idé att bevilja minimal åtkomst. Omfånget i föregående exempel är begränsat till den särskilda App Service-appen och inte hela resurs gruppen.

# <a name="app-level-credentials"></a>[Autentiseringsuppgifter för app-nivå](#tab/applevel)

Du kan använda autentiseringsuppgifter på program nivå med hjälp av publicerings profilen för appen. Gå till appens hanterings sida i portalen. På sidan **Översikt** klickar du på alternativet **Hämta publicerings profil** .

Du kommer att behöva filens innehåll senare.

---

## <a name="configure-the-github-secret"></a>Konfigurera GitHub-hemligheten

# <a name="user-level-credentials"></a>[Autentiseringsuppgifter för användar nivå](#tab/userlevel)

I [GitHub](https://github.com/), bläddra i din lagrings plats, välj **inställningar > hemligheter > Lägg till en ny hemlighet**.

Om du vill använda [autentiseringsuppgifter för användar nivå](#generate-deployment-credentials)klistrar du in hela JSON-utdata från Azure CLI-kommandot till fältet hemligt värde. Ge hemligheten namnet som `AZURE_CREDENTIALS` .

När du konfigurerar arbets flödes filen senare använder du hemligheten för indata `creds` från åtgärden för Azure-inloggning. Till exempel:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

# <a name="app-level-credentials"></a>[Autentiseringsuppgifter för app-nivå](#tab/applevel)

I [GitHub](https://github.com/), bläddra i din lagrings plats, välj **inställningar > hemligheter > Lägg till en ny hemlighet**.

Om du vill använda [autentiseringsuppgifter för program nivå](#generate-deployment-credentials)klistrar du in innehållet i den hämtade publicerings profil filen i fältet hemligt värde. Ge hemligheten namnet som `azureWebAppPublishProfile` .

När du konfigurerar arbets flödes filen senare använder du hemligheten för indata `publish-profile` för åtgärden Distribuera Azure Web App. Till exempel:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

## <a name="set-up-the-environment"></a>Konfigurera miljön

Konfigurationen av miljön kan göras med hjälp av en av installations åtgärderna.

|**Språk**  |**Installations åtgärd**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

I följande exempel visas en del av arbets flödet som konfigurerar miljön för de olika språk som stöds:

**JavaScript**

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

## <a name="build-the-web-app"></a>Bygg webb programmet

Detta beror på språket och för språk som stöds av Azure App Service, bör det här avsnittet vara standard stegen för version av varje språk.

I följande exempel visas den del av arbets flödet som bygger webbappen på de olika språk som stöds.

**JavaScript**

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

Om du vill distribuera din kod till en App Service-app använder du `azure/webapps-deploy@v2` åtgärden. Den här åtgärden har fyra parametrar:

| **Parameter**  | **Förklaring**  |
|---------|---------|
| **App-Name** | Kunna Namnet på App Service-appen | 
| **publicera – profil** | Valfritt Publicera profil filens innehåll med webb distributions hemligheter |
| **paketfilerna** | Valfritt Sökväg till paket eller mapp. *. zip, *. War, *. jar eller en mapp för distribution |
| **plats namn** | Valfritt Ange en befintlig plats förutom produktions platsen |

# <a name="user-level-credentials"></a>[Autentiseringsuppgifter för användar nivå](#tab/userlevel)

Nedan visas exempel arbets flödet för att bygga och distribuera en Node.js-app till Azure med hjälp av ett Azure-tjänstens huvud namn. Observera att `creds` indatamängden refererar till `AZURE_CREDENTIALS` hemligheten som du skapade tidigare.

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
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

# <a name="app-level-credentials"></a>[Autentiseringsuppgifter för app-nivå](#tab/applevel)

Nedan visas exempel arbets flödet för att bygga och distribuera en Node.js-app till Azure med appens publicerings profil. Observera att `publish-profile` indatamängden refererar till `azureWebAppPublishProfile` hemligheten som du skapade tidigare.

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
          uses: azure/webapps-deploy@v2
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

## <a name="next-steps"></a>Nästa steg

Du hittar vår uppsättning åtgärder grupperade i olika databaser på GitHub, var och en innehåller dokumentation och exempel som hjälper dig att använda GitHub för CI/CD och distribuera dina appar till Azure.

- [Åtgärder arbets flöden att distribuera till Azure](https://github.com/Azure/actions-workflow-samples)

- [Azure-inloggning](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp för behållare](https://github.com/Azure/webapps-container-deploy)

- [Docker-inloggning/utloggning](https://github.com/Azure/docker-login)

- [Händelser som utlöser arbetsflöden](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s-distribution](https://github.com/Azure/k8s-deploy)

- [Start arbets flöden](https://github.com/actions/starter-workflows)
