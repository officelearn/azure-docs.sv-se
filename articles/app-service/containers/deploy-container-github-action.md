---
title: Distribuera din behållare från en CI/CD-pipeline med GitHub-åtgärder – Azure App Service | Microsoft Docs
description: Lär dig hur du använder GitHub-åtgärder för att distribuera din behållare till App Service
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
ms.date: 10/09/2019
ms.author: jafreebe
ms.openlocfilehash: 2341eba2c24c06d654c9d2eeda96788d168fe27c
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809817"
---
# <a name="github-actions-for-deploying-to-web-app-for-containers"></a>GitHub-åtgärder för att distribuera till Web App for Containers

[GitHub-åtgärder](https://help.github.com/en/articles/about-github-actions) ger dig flexibiliteten att bygga ett arbets flöde för automatiserad livs cykel för program utveckling. Med Azure App Service åtgärder för GitHub kan du automatisera arbets flödet för att distribuera [Azure Web Apps för behållare](https://azure.microsoft.com/services/app-service/containers/) med hjälp av GitHub-åtgärder.

> [!IMPORTANT]
> GitHub-åtgärder är för närvarande Beta versioner. Du måste först [Registrera dig för att kunna ansluta till förhands granskningen](https://github.com/features/actions) med ditt GitHub-konto.
> 

Ett arbets flöde definieras av en YAML-fil (. yml) i `/.github/workflows/` sökvägen i lagrings platsen. Den här definitionen innehåller de olika stegen och parametrarna som utgör arbets flödet.

För ett arbets flöde för en Azure Web App-behållare har filen tre delar:

|Section  |Uppgifter  |
|---------|---------|
|**Autentisering** | 1. definiera ett huvud namn för tjänsten <br /> 2. skapa en GitHub-hemlighet |
|**Konstruktion** | 1. Konfigurera miljön <br /> 2. Bygg behållar avbildningen |
|**Distribuera** | 1. distribuera behållar avbildningen |

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

I exemplet nedan används autentiseringsuppgifter på användar nivå, t. ex. Azure-tjänstens huvud namn för distribution. Följ stegen för att konfigurera hemligheten:

1. I [GitHub](https://github.com/), bläddra i din lagrings plats, välj **inställningar > hemligheter > Lägg till en ny hemlighet**

2. Klistra in innehållet i nedanstående `az cli`-kommando som värde för den hemliga variabeln. Till exempel `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Nu i arbets flödes filen i din gren: `.github/workflows/workflow.yml` ersätta hemligheten i Azures inloggnings åtgärd med din hemlighet.

4. På samma sätt definierar du följande ytterligare hemligheter för autentiseringsuppgifterna för behållar registret och anger dem i Docker login login-åtgärd. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Du ser de hemligheter som visas nedan när den har definierats.

    ![behållar hemligheter](../media/app-service-github-actions/app-service-secrets-container.png)

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
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/actions/login@v1
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

## <a name="deploy-to-web-app-container"></a>Distribuera till Web App-behållare

Om du vill distribuera avbildningen till en Web App-behållare måste du använda `Azure/appservice-actions/webapp@master` åtgärden. Den här åtgärden har 5 parametrar:

| **ProfileServiceApplicationProxy**  | **Förklaring**  |
|---------|---------|
| **App-Name** | Kunna Namn på Azure-webbappen | 
| **plats namn** | Valfritt Ange en befintlig plats förutom produktions platsen |
| **avbildningar** | Kunna Ange det fullständigt kvalificerade behållar avbildnings namnet. Till exempel "myregistry.azurecr.io/nginx:latest" eller "python: 3.7.2-alpina/". För scenario med flera behållare kan du ange namn på flera behållar avbildningar (flera rader separerade) |
| **konfiguration – fil** | Valfritt Sökväg till Docker-Skriv filen. Måste vara en fullständigt kvalificerad sökväg eller i förhållande till standard arbets katalogen. Krävs för scenario med flera behållare |
| **container-Command** | Valfritt Ange start kommandot. För t. ex. dotNet-körning eller dotNet filename. dll |

Nedan visas exempel arbets flödet för att bygga och distribuera en Node. js-webbapp till Azure Web App-behållaren.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/actions/login@v1
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

- [Azure-inloggning](https://github.com/Azure/actions)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp för behållare](https://github.com/Azure/webapps-container-deploy)

- [Docker-inloggning/utloggning](https://github.com/Azure/docker-login)

- [Händelser som utlöser arbets flöden](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s-distribution](https://github.com/Azure/k8s-deploy)

- [Start arbets flöden](https://github.com/actions/starter-workflows)
