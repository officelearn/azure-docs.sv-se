---
title: Anpassad ci/cd-behållare från GitHub-åtgärder
description: Lär dig hur du använder GitHub-åtgärder för att distribuera din anpassade Linux-behållare till App Service från en CI/CD-pipeline.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: d5f175d887cec1d5b5e567d3f716e6492f4516dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246976"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Distribuera en anpassad behållare till App Service med GitHub-åtgärder

[GitHub-åtgärder](https://help.github.com/en/articles/about-github-actions) ger dig flexibiliteten att skapa ett automatiserat livscykelarbetsflöde för programvaruutveckling. Med [Azure App Service Action for Containers](https://github.com/Azure/webapps-container-deploy)kan du automatisera arbetsflödet för att distribuera appar som anpassade behållare till App [Service](https://azure.microsoft.com/services/app-service/containers/) med GitHub-åtgärder.

> [!IMPORTANT]
> GitHub-åtgärder är för närvarande i beta. Du måste först [registrera dig för att gå med i förhandsgranskningen](https://github.com/features/actions) med ditt GitHub-konto.
> 

Ett arbetsflöde definieras av en YAML-fil (.yml) i `/.github/workflows/` sökvägen i databasen. Den här definitionen innehåller de olika steg och parametrar som utgör arbetsflödet.

För ett Azure App Service-behållararbetsflöde har filen tre avsnitt:

|Section  |Aktiviteter  |
|---------|---------|
|**Autentisering** | 1. Definiera ett huvudnamn för tjänsten. <br /> 2. Skapa en GitHub-hemlighet. |
|**Utveckla** | 1. Ställ in miljön. <br /> 2. Bygg behållaravbildningen. |
|**Distribuera** | 1. Distribuera behållaravbildningen. |

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan skapa ett [tjänsthuvudnamn](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) med kommandot [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) i [Azure CLI](https://docs.microsoft.com/cli/azure/). Du kan köra det här kommandot med [Azure Cloud Shell](https://shell.azure.com/) i Azure-portalen eller genom att välja knappen **Prova.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
# Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

Utdata är ett JSON-objekt med de autentiseringsuppgifter för rolltilldelning som ger åtkomst till apptjänstappen som liknar nedan. Kopiera det här JSON-objektet för att autentisera från GitHub.

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
> Det är alltid en god praxis att ge minimal tillgång. Du kan begränsa omfattningen i kommandot ovan Az CLI till den specifika App Service-appen och Azure Container Registry där behållaravbildningarna skjuts till.

## <a name="configure-the-github-secret"></a>Konfigurera GitHub-hemligheten

I exemplet nedan används autentiseringsuppgifter på användarnivå, dvs. Följ stegen för att konfigurera hemligheten:

1. I [GitHub](https://github.com/)väljer du **Inställningar > hemligheter > Lägg till en ny hemlighet**

2. Klistra in innehållet `az cli` i kommandot nedan som värdet för den hemliga variabeln. Till exempel `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Nu i arbetsflödesfilen i `.github/workflows/workflow.yml` din gren: ersätt hemligheten i Azure-inloggningsåtgärden med din hemlighet.

4. På samma sätt definierar du följande ytterligare hemligheter för behållarregisterautentiseringsuppgifterna och anger dem i Docker-inloggningsåtgärden. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Du ser hemligheterna som visas nedan när de har definierats.

    ![behållare hemligheter](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>Skapa avbildningen för behållaren

I följande exempel visas en del av arbetsflödet som bygger dockeravbildningen.

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

## <a name="deploy-to-an-app-service-container"></a>Distribuera till en apptjänstbehållare

Om du vill distribuera avbildningen till `azure/webapps-container-deploy@v1` en anpassad behållare i App Service använder du åtgärden. Den här åtgärden har fem parametrar:

| **Parametern**  | **Förklaring**  |
|---------|---------|
| **app-namn** | (Obligatoriskt) Namn på apptjänstappen | 
| **kortplatsnamn** | (Valfritt) Ange en annan befintlig platsplats än produktionsplatsen |
| **Bilder** | (Obligatoriskt) Ange den fullständiga behållarens avbildningar. Till exempel "myregistry.azurecr.io/nginx:latest" eller "python:3.7.2-alpine/". För en app med flera behållare kan flera behållaravbildningsnamn anges (flerradsavgränsad) |
| **konfiguration-fil** | (Valfritt) Sökväg till Docker-Compose-filen. Ska vara en helt kvalificerad sökväg eller i förhållande till standardarbetskatalogen. Krävs för appar med flera behållare. |
| **kommandot behållare** | (Valfritt) Ange startkommandot. För ex. dotnet kör eller dotnet filename.dll |

Nedan finns exempelarbetsflödet för att skapa och distribuera en Node.js-app till en anpassad behållare i App Service.

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

Du hittar vår uppsättning åtgärder grupperade i olika databaser på GitHub, var och en som innehåller dokumentation och exempel som hjälper dig att använda GitHub för CI/CD och distribuera dina appar till Azure.

- [Azure-inloggning](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp för behållare](https://github.com/Azure/webapps-container-deploy)

- [Docker inloggning / utloggning](https://github.com/Azure/docker-login)

- [Händelser som utlöser arbetsflöden](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Distribution av K8:er](https://github.com/Azure/k8s-deploy)

- [Start-CI-arbetsflöden](https://github.com/actions/starter-workflows)

- [Startarbetsflöden som ska distribueras till Azure](https://github.com/Azure/actions-workflow-samples)
