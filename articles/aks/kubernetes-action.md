---
title: Bygg, testa och distribuera behållare till Azure Kubernetes-tjänsten med hjälp av GitHub-åtgärder
description: Lär dig hur du använder GitHub-åtgärder för att distribuera din behållare till Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 5ee8ee4d2c9e225d82e58daffeef9e5f09e43e6b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77595373"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub-åtgärder för att distribuera till Kubernetes-tjänsten

[GitHub-åtgärder](https://help.github.com/en/articles/about-github-actions) ger dig flexibiliteten att bygga ett arbets flöde för automatiserad livs cykel för program utveckling. Kubernetes-åtgärden [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) underlättar distributioner till Azure Kubernetes service-kluster. Åtgärden ställer in mål AKS kluster kontext, som kan användas av andra åtgärder, t. ex. [Azure/K8s-Deploy](https://github.com/Azure/k8s-deploy/tree/master), [Azure/K8s-Create-Secret](https://github.com/Azure/k8s-create-secret/tree/master) osv. eller kör eventuella kubectl-kommandon.

Ett arbets flöde definieras av en YAML-fil (. yml) i `/.github/workflows/` sökvägen i lagrings platsen. Den här definitionen innehåller de olika stegen och parametrarna som utgör arbets flödet.

För ett arbets flöde som riktar sig till AKS har filen tre delar:

|Avsnitt  |Aktiviteter  |
|---------|---------|
|**Autentisering** | Logga in på ett privat container Registry (ACR) |
|**Utveckla** | Bygg & push-överför behållar avbildningen  |
|**Distribuera** | 1. Ange mål AKS-klustret |
| |2. skapa en allmän/Docker-register hemlighet i Kubernetes-kluster  |
||3. distribuera till Kubernetes-klustret|

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan skapa ett [huvud namn för tjänsten](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) med hjälp av kommandot [AZ AD SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) i [Azure CLI](https://docs.microsoft.com/cli/azure/). Du kan köra det här kommandot med [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja knappen **prova** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

I kommandot ovan ersätter du plats hållarna med ditt prenumerations-ID och resurs grupp. Utdata är de autentiseringsuppgifter för roll tilldelning som ger åtkomst till din resurs. Kommandot ska generera ett JSON-objekt liknande detta.

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

## <a name="configure-the-github-secrets"></a>Konfigurera GitHub hemligheter

Följ stegen för att konfigurera hemligheterna:

1. I [GitHub](https://github.com/), bläddra till din lagrings plats, välj **inställningar > hemligheter > Lägg till en ny hemlighet**.

    ![secrets](media/kubernetes-action/secrets.png)

2. Klistra in innehållet i ovanstående `az cli` kommando som värde för den hemliga variabeln. Till exempel `AZURE_CREDENTIALS`.

3. På samma sätt definierar du följande ytterligare hemligheter för autentiseringsuppgifterna för behållar registret och anger dem i Docker login login-åtgärd. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Du ser de hemligheter som visas nedan när den har definierats.

    ![Kubernetes – hemligheter](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Bygg en behållar avbildning och distribuera till Azure Kubernetes service-kluster

Att bygga och skicka behållar avbildningar görs med hjälp av `Azure/docker-login@v1` åtgärden. Om du vill distribuera en behållar avbildning till AKS måste du använda `Azure/k8s-deploy@v1` åtgärden. Den här åtgärden har fem parametrar:

| **Parameter**  | **Förklaring**  |
|---------|---------|
| **namn område** | Valfritt Välj mål namn området Kubernetes. Om namn området inte anges körs kommandona i standard namn området | 
| **manifest** |  Kunna Sökväg till manifest-filerna som ska användas för distribution |
| **avbildningar** | Valfritt Fullständigt resurs-URL för den eller de avbildningar som ska användas för ersättningar på manifest filerna |
| **imagepullsecrets** | Valfritt Namnet på en Docker-register hemlighet som redan har kon figurer ATS i klustret. Vart och ett av dessa hemliga namn läggs till i fältet imagePullSecrets för de arbets belastningar som finns i manifest filen för indatafiler |
| **kubectl-version** | Valfritt Installerar en speciell version av kubectl Binary |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Distribuera till Azure Kubernetes service-kluster

Slut punkt till slut punkt för att skapa behållar avbildningar och distribuera till ett Azure Kubernetes service-kluster.

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

Du hittar vår uppsättning åtgärder i olika databaser på GitHub, där var och en innehåller dokumentation och exempel som hjälper dig att använda GitHub för CI/CD och distribuera dina appar till Azure.

- [Konfigurera kubectl](https://github.com/Azure/setup-kubectl)

- [K8s – uppsättning – kontext](https://github.com/Azure/k8s-set-context)

- [AKS – uppsättning – kontext](https://github.com/Azure/aks-set-context)

- [K8s-Create-Secret](https://github.com/Azure/k8s-create-secret)

- [K8s – distribuera](https://github.com/Azure/k8s-deploy)

- [Webbappar-container-Deploy](https://github.com/Azure/webapps-container-deploy)

- [åtgärder – arbets flöde – exempel](https://github.com/Azure/actions-workflow-samples)
