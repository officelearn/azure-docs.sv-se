---
title: Distribuera din kod från en CI/CD-pipeline med GitHub-åtgärder – Azure App Service | Microsoft Docs
description: Lär dig hur du använder GitHub-åtgärder för att distribuera din kod till App Service
services: app-service
documentationcenter: ''
author: jasonfreeberg
writer: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: jafreebe
ms.openlocfilehash: b7ec1ae1d04fb1dbe16fd9f4a2640b2b3d9584c2
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809778"
---
# <a name="github-actions-for-deploying-to-app-service"></a>GitHub-åtgärder för att distribuera till App Service

[GitHub-åtgärder](https://help.github.com/en/articles/about-github-actions) ger dig flexibiliteten att bygga ett arbets flöde för automatiserad livs cykel för program utveckling. Med Azure App Service åtgärder för GitHub kan du automatisera arbets flödet för att distribuera [Azure-Web Apps](https://azure.microsoft.com/services/app-service/web/) med GitHub-åtgärder.

> [!IMPORTANT]
> GitHub-åtgärder är för närvarande Beta versioner. Du måste först [Registrera dig för att kunna ansluta till förhands granskningen](https://github.com/features/actions) med ditt GitHub-konto.
> 

Ett arbets flöde definieras av en YAML-fil (. yml) i `/.github/workflows/` sökvägen i lagrings platsen. Den här definitionen innehåller de olika stegen och parametrarna som utgör arbets flödet.

För ett Azure Web App-arbetsflöde har filen tre delar:

|Section  |Uppgifter  |
|---------|---------|
|**Autentisering** | 1. definiera ett huvud namn för tjänsten <br /> 2. skapa en GitHub-hemlighet |
|**Konstruktion** | 1. Konfigurera miljön <br /> 2. Bygg webb programmet |
|**Distribuera** | 1. distribuera webbappen |

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan skapa ett [huvud namn för tjänsten](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) med hjälp av kommandot [AZ AD SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) i [Azure CLI](https://docs.microsoft.com/cli/azure/). Du kan köra det här kommandot med [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja knappen **prova** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

I det här exemplet ersätter du plats hållarna i resursen med ditt prenumerations-ID, resurs grupp och webb program namn. Utdata är de autentiseringsuppgifter för roll tilldelning som ger åtkomst till din webbapp. Kopiera det här JSON-objektet, som du kan använda för att autentisera från GitHub.

> [!NOTE]
> Du behöver inte skapa ett huvud namn för tjänsten om du bestämmer dig för att använda publicerings profilen för autentisering.

> [!IMPORTANT]
> Det är alltid en bra idé att bevilja minimal åtkomst. Detta är anledningen till att omfånget i föregående exempel är begränsat till den särskilda webbappen och inte hela resurs gruppen.

## <a name="configure-the-github-secret"></a>Konfigurera GitHub-hemligheten

Du kan också använda autentiseringsuppgifter på program nivå, t. ex. publicera profil för distribution. Följ stegen för att konfigurera hemligheten:

1. Ladda ned publicerings profilen för webbappen från portalen med alternativet **Hämta publicerings profil** .

2. I [GitHub](https://github.com/), bläddra i din lagrings plats, välj **inställningar > hemligheter > Lägg till en ny hemlighet**

    ![hemligheter](media/app-service-github-actions/secrets.png)

3. Klistra in innehållet för den hämtade publicerings profil filen i fältet hemligt värde.

4. Nu i arbets flödes filen i din gren: `.github/workflows/workflow.yml` ersätta hemligheten för indata-`publish-profile` för åtgärden Distribuera Azure Web App.
    
    ```yaml
        - uses: azure/webapps-deploy@v1
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. Du ser hemligheten som visas nedan när den har definierats.

    ![hemligheter](media/app-service-github-actions/app-service-secrets.png)

## <a name="setup-the-environment"></a>Konfigurera miljön

Konfigurationen av miljön kan göras med hjälp av en av installations åtgärderna.

|**Språk**  |**Installations åtgärd**  |
|---------|---------|
|**NET**     | `actions/setup-dotnet` |
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

**NET**

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

Detta beror på språket och för språk som stöds av Azure Web Apps, bör det här avsnittet vara standard stegen för att bygga på varje språk.

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

**NET**

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
## <a name="deploy-the-web-app"></a>Distribuera webbappen

Om du vill distribuera din kod till en webbapp måste du använda `Azure/appservice-actions/webapp@master` åtgärden. Den här åtgärden har 4 parametrar:

| **ProfileServiceApplicationProxy**  | **Förklaring**  |
|---------|---------|
| **App-Name** | Kunna Namn på Azure-webbappen | 
| **publicera – profil** | Valfritt Publicera profil filens innehåll med webb distributions hemligheter |
| **paketfilerna** | Valfritt Sökväg till paket eller mapp. *. zip, *. War, *. jar eller en mapp för distribution |
| **plats namn** | Valfritt Ange en befintlig plats förutom produktions platsen |

### <a name="deploy-using-publish-profile"></a>Distribuera med publicerings profil

Nedan visas exempel arbets flödet för att bygga och distribuera en Node. js-webbapp till Azure med hjälp av publicerings profil.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
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

### <a name="deploy-using-azure-service-principal"></a>Distribuera med hjälp av Azure-tjänstens huvud namn

Nedan visas exempel arbets flödet för att bygga och distribuera en Node. js-webbapp till Azure med hjälp av Azure-tjänstens huvud namn.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
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

Du hittar vår uppsättning åtgärder grupperade i olika databaser på GitHub, var och en innehåller dokumentation och exempel som hjälper dig att använda GitHub för CI/CD och distribuera dina appar till Azure.

- [Azure-inloggning](https://github.com/Azure/actions)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp för behållare](https://github.com/Azure/webapps-container-deploy)

- [Docker-inloggning/utloggning](https://github.com/Azure/docker-login)

- [Händelser som utlöser arbets flöden](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s-distribution](https://github.com/Azure/k8s-deploy)

- [Start arbets flöden](https://github.com/actions/starter-workflows)
