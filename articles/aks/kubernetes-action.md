---
title: Skapa, testa och distribuera behållare till Azure Kubernetes-tjänsten med GitHub-åtgärder
description: Lär dig hur du använder GitHub-åtgärder för att distribuera din behållare till Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 5ee8ee4d2c9e225d82e58daffeef9e5f09e43e6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595373"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub-åtgärder för distribution till Kubernetes-tjänsten

[GitHub-åtgärder](https://help.github.com/en/articles/about-github-actions) ger dig flexibiliteten att skapa ett automatiserat livscykelarbetsflöde för programvaruutveckling. Kubernetes-åtgärden [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) underlättar distributioner till Azure Kubernetes-tjänstkluster. Åtgärden anger mål AKS-klusterkontexten, som kan användas av andra åtgärder som [azure/k8s-deploy,](https://github.com/Azure/k8s-deploy/tree/master) [azure/k8s-create-secret](https://github.com/Azure/k8s-create-secret/tree/master) etc. eller köra några kubectl-kommandon.

Ett arbetsflöde definieras av en YAML-fil (.yml) i `/.github/workflows/` sökvägen i databasen. Den här definitionen innehåller de olika steg och parametrar som utgör arbetsflödet.

För ett arbetsflöde som riktar sig till AKS har filen tre avsnitt:

|Section  |Aktiviteter  |
|---------|---------|
|**Autentisering** | Logga in i ett privat behållarregister (ACR) |
|**Utveckla** | Skapa & pusha behållaravbildningen  |
|**Distribuera** | 1. Ställ in målet AKS-kluster |
| |2. Skapa en allmän/docker-registerhemlighet i Kubernetes-klustret  |
||3. Distribuera till Kubernetes-klustret|

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan skapa ett [tjänsthuvudnamn](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) med kommandot [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) i [Azure CLI](https://docs.microsoft.com/cli/azure/). Du kan köra det här kommandot med [Azure Cloud Shell](https://shell.azure.com/) i Azure-portalen eller genom att välja knappen **Prova.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

I kommandot ovan ersätter du platshållarna med ditt prenumerations-ID och resursgrupp. Utdata är de autentiseringsuppgifter för rolltilldelning som ger åtkomst till din resurs. Kommandot ska mata ut ett JSON-objekt som liknar detta.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Kopiera det här JSON-objektet, som du kan använda för att autentisera från GitHub.

## <a name="configure-the-github-secrets"></a>Konfigurera GitHub-hemligheterna

Följ stegen för att konfigurera hemligheterna:

1. I GitHub väljer du **Inställningar > hemligheter > Lägg till en ny hemlighet**i [GitHub.](https://github.com/)

    ![secrets](media/kubernetes-action/secrets.png)

2. Klistra in innehållet `az cli` i kommandot ovan som värdet för den hemliga variabeln. Till exempel `AZURE_CREDENTIALS`.

3. På samma sätt definierar du följande ytterligare hemligheter för behållarregisterautentiseringsuppgifterna och anger dem i Docker-inloggningsåtgärden. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Du kommer att se hemligheterna som visas nedan när de har definierats.

    ![kubernetes-hemligheter](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Skapa en behållaravbildning och distribuera till Azure Kubernetes Service-kluster

Uppbyggnaden och push av behållaravbildningar görs med hjälp av `Azure/docker-login@v1` åtgärder. Om du vill distribuera en behållaravbildning `Azure/k8s-deploy@v1` till AKS måste du använda åtgärden. Den här åtgärden har fem parametrar:

| **Parametern**  | **Förklaring**  |
|---------|---------|
| **Namespace** | (Valfritt) Välj namnområdet målkubernetes. Om namnområdet inte anges körs kommandona i standardnamnområdet | 
| **Manifesterar** |  (Obligatoriskt) Sökväg till manifestfilerna, som ska användas för distribution |
| **Bilder** | (Valfritt) Fullständigt kvalificerad resurs-URL för den eller de bilder som ska användas för ersättningar på manifestfilerna |
| **imagepullsecrets** | (Valfritt) Namn på en dockerregisterhemlighet som redan har konfigurerats i klustret. Vart och ett av dessa hemliga namn läggs till under fältet imagePullSecrets för arbetsbelastningarna som finns i indatamanifestfilerna |
| **kubectl-version** | (Valfritt) Installerar en specifik version av kubectl binary |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Distribuera till Azure Kubernetes Service-kluster

på slutarbetsflödet för att skapa behållaravbildningar och distribuera till ett Azure Kubernetes-tjänstkluster.

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>Nästa steg

Du hittar vår uppsättning åtgärder i olika databaser på GitHub, var och en som innehåller dokumentation och exempel som hjälper dig att använda GitHub för CI/CD och distribuera dina appar till Azure.

- [setup-kubectl](https://github.com/Azure/setup-kubectl)

- [k8s-set-sammanhang](https://github.com/Azure/k8s-set-context)

- [aks-set-sammanhang](https://github.com/Azure/aks-set-context)

- [k8s-skapa-hemlighet](https://github.com/Azure/k8s-create-secret)

- [k8s-distribuera](https://github.com/Azure/k8s-deploy)

- [webapps-container-distribuera](https://github.com/Azure/webapps-container-deploy)

- [åtgärder-arbetsflöde-exempel](https://github.com/Azure/actions-workflow-samples)
