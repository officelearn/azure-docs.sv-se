---
title: Autentisera med Azure Container Registry från Azure Kubernetes Service
description: Lär dig hur du ger åtkomst till avbildningar i ditt privata behållarregister i Azure Kubernetes Service med hjälp av en Azure Active Directory-tjänstobjekt.
services: container-service
author: dlepow
ms.service: container-service
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.openlocfilehash: 850919f8ca8bb68af544ae528a779e16068424b1
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752545"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Autentisera med Azure Container Registry från Azure Kubernetes Service

När du använder Azure Container Registry (ACR) med Azure Kubernetes Service (AKS), måste en autentiseringsmetod upprättas. Den här artikeln beskriver de rekommenderade konfigurationerna för autentisering mellan dessa två Azure-tjänster.

## <a name="grant-aks-access-to-acr"></a>Grant AKS åtkomst till ACR.

När du skapar ett AKS-kluster, skapar Azure även en tjänst huvudnamn för klustret funktionalitet med andra Azure-resurser. Du kan använda den här automatiskt genererade tjänstens huvudnamn för autentisering med en ACR-registret. Om du vill göra det måste du skapa en Azure AD [rolltilldelning](../role-based-access-control/overview.md#role-assignments) som beviljar klustrets tjänstens huvudnamn åtkomst till behållarregistret.

Använd följande skript för att ge AKS-genererade tjänstens huvudnamn pull åtkomst till ett Azure container registry. Ändra den `AKS_*` och `ACR_*` variabler för din miljö innan du kör skriptet.

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role acrpull --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Åtkomst med Kubernetes-hemlighet

I vissa fall kanske du inte att kunna tilldela rollen krävs att automatiskt genererade AKS tjänstens huvudnamn åtkomst till ACR beviljas. På grund av säkerhetsmodellen för din organisation, kanske du exempelvis inte har tillräcklig behörighet i din Azure AD-katalog för att tilldela en roll till AKS-genererade tjänstens huvudnamn. I sådana fall kan du skapa ett nytt huvudnamn för tjänsten och sedan ge det åtkomst till behållarregistret med en Kubernetes-hemlighet som bild pull.

Använd följande skript för att skapa ett nytt huvudnamn för tjänsten (du ska använda dess autentiseringsuppgifter för Kubernetes bild pull-hemlighet). Ändra den `ACR_NAME` variabeln för din miljö innan du kör skriptet.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create acrpull role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role acrpull --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principal client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Nu kan du lagra autentiseringsuppgifter för tjänstens huvudnamn i ett Kubernetes [bild pull hemlighet][image-pull-secret], som sedan hänvisar till AKS-klustret när behållare som körs.

Använd följande **kubectl** kommando för att skapa Kubernetes-hemlighet. Ersätt `<acr-login-server>` med fullständigt kvalificerade namnet på Azure container registry (det är i formatet ”acrname.azurecr.io”). Ersätt `<service-principal-ID>` och `<service-principal-password>` med de värden du erhöll genom att köra föregående skript. Ersätt `<email-address>` med valfri giltig e-postadress.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Du kan nu använda Kubernetes-hemlighet i pod-distributioner genom att ange dess namn (i det här fallet ”acr-auth”) i den `imagePullSecrets` parameter:

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
