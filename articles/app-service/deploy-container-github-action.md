---
title: Anpassad CI/CD för behållare från GitHub-åtgärder
description: Lär dig hur du använder GitHub-åtgärder för att distribuera en anpassad Linux-behållare till App Service från en CI/CD-pipeline.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 21019917f37ad95dc15056daa51b3d9e53ec06fa
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88083229"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Distribuera en anpassad behållare för att App Service med GitHub-åtgärder

[GitHub-åtgärder](https://help.github.com/en/articles/about-github-actions) ger dig flexibiliteten att bygga ett arbets flöde för automatiserad livs cykel för program utveckling. Med [åtgärden Azure App Service för behållare](https://github.com/Azure/webapps-container-deploy)kan du automatisera arbets flödet för att distribuera anpassade behållare [App Service](overview.md) med GitHub-åtgärder.

> [!IMPORTANT]
> GitHub-åtgärder är för närvarande Beta versioner. Du måste först [Registrera dig för att kunna ansluta till förhands granskningen](https://github.com/features/actions) med ditt GitHub-konto.
> 

Ett arbets flöde definieras av en YAML-fil (. yml) i `/.github/workflows/` sökvägen i lagrings platsen. Den här definitionen innehåller de olika stegen och parametrarna som utgör arbets flödet.

För ett arbets flöde för Azure App Service container har filen tre delar:

|Avsnitt  |Aktiviteter  |
|---------|---------|
|**Autentisering** | 1. definiera ett huvud namn för tjänsten. <br /> 2. skapa en GitHub-hemlighet. |
|**Skapa** | 1. Konfigurera miljön. <br /> 2. Bygg behållar avbildningen. |
|**Distribuera** | 1. distribuera behållar avbildningen. |

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan skapa ett [huvud namn för tjänsten](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) med hjälp av kommandot [AZ AD SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) i [Azure CLI](https://docs.microsoft.com/cli/azure/). Du kan köra det här kommandot med [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja knappen **prova** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

I exemplet ovan ersätter du plats hållarna med ditt prenumerations-ID och resurs gruppens namn. Utdata är ett JSON-objekt med roll tilldelningens autentiseringsuppgifter som ger åtkomst till din App Service-app på liknande sätt som nedan. Kopiera det här JSON-objektet för senare.

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
> Det är alltid en bra idé att bevilja minimal åtkomst. Du kan begränsa omfattningen i ovanstående AZ CLI-kommando till den speciella App Service-appen och Azure Container Registry där behållar avbildningarna flyttas till.

## <a name="configure-the-github-secret"></a>Konfigurera GitHub-hemligheten

I [GitHub](https://github.com/), bläddra i din lagrings plats, välj **inställningar > hemligheter > Lägg till en ny hemlighet**.

Klistra in innehållet i JSON-utdata från [skapa ett huvud namn för tjänsten](#create-a-service-principal) som värde för den hemliga variabeln. Ge hemligheten namnet som `AZURE_CREDENTIALS` .

När du konfigurerar arbets flödes filen senare använder du hemligheten för indata `creds` från åtgärden för Azure-inloggning. Till exempel:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

På samma sätt definierar du följande ytterligare hemligheter för autentiseringsuppgifterna för behållar registret och anger dem i Docker login login-åtgärd.

- REGISTRY_USERNAME
- REGISTRY_PASSWORD

## <a name="build-the-container-image"></a>Bygg behållar avbildningen

I följande exempel visas en del av arbets flödet som bygger Docker-avbildningen.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>Distribuera till en App Service-behållare

Om du vill distribuera avbildningen till en anpassad behållare i App Service använder du `azure/webapps-container-deploy@v1` åtgärden. Den här åtgärden har fem parametrar:

| **Parameter**  | **Förklaring**  |
|---------|---------|
| **App-Name** | Kunna Namnet på App Service-appen | 
| **plats namn** | Valfritt Ange en befintlig plats förutom produktions platsen |
| **avbildningar** | Kunna Ange det fullständigt kvalificerade behållar avbildnings namnet. Till exempel "myregistry.azurecr.io/nginx:latest" eller "python: 3.7.2-alpina/". För en app med flera behållare kan du ange flera behållar avbildnings namn (flera rader separerade) |
| **konfiguration – fil** | Valfritt Sökväg till Docker-Skriv filen. Måste vara en fullständigt kvalificerad sökväg eller i förhållande till standard arbets katalogen. Krävs för appar med flera behållare. |
| **container-Command** | Valfritt Ange start kommandot. För t. ex. dotNet-körning eller dotNet-filename.dll |

Nedan visas exempel arbets flödet för att bygga och distribuera en Node.js-app till en anpassad behållare i App Service. Observera att `creds` indatamängden refererar till `AZURE_CREDENTIALS` hemligheten som du skapade tidigare.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

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
