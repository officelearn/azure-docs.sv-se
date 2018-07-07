---
title: Autentisera med Azure Container Registry från Azure Kubernetes Service
description: Lär dig hur du ger åtkomst till avbildningar i ditt privata behållarregister i Azure Kubernetes Service med hjälp av en Azure Active Directory-tjänstobjekt.
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: marsma
ms.openlocfilehash: 36b66fdcd157e4c44fcd451c8cc07ba68242b583
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888770"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Autentisera med Azure Container Registry från Azure Kubernetes Service

När du använder Azure Container Registry (ACR) med Azure Kubernetes Service (AKS), måste en autentiseringsmetod upprättas. Det här dokumentet beskriver de rekommenderade konfigurationerna för autentisering mellan dessa två Azure-tjänster.

## <a name="grant-aks-access-to-acr"></a>Grant AKS åtkomst till ACR.

När ett AKS-kluster skapas, skapas även ett huvudnamn för tjänsten för att hantera klustret funktionalitet med Azure-resurser. Den här tjänstens huvudnamn kan också användas för autentisering med en ACR-registret. Om du vill göra det måste en rolltilldelning skapas för att ge tjänstens huvudnamn läsåtkomst till resursen i ACR.

I följande exempel kan användas för att slutföra åtgärden.

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
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Åtkomst med Kubernetes-hemlighet

I vissa fall kan inte tjänstens huvudnamn som används av AKS begränsas till ACR-registret. För dessa fall kan du skapa ett unikt tjänstens huvudnamn och begränsa den till endast ACR-registret.

Följande skript kan användas för att skapa tjänsten huvudnamn.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a contributor role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principle client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Autentiseringsuppgifter för tjänstens huvudnamn kan nu lagras i en Kubernetes [bild pull hemlighet] [ image-pull-secret] och användas som referens när behållare som körs i ett AKS-kluster.

Följande kommando skapar Kubernetes hemliga. Ersätt namnet på med ACR-inloggningsserver, användarnamnet med id för tjänstens huvudnamn och lösenordet med lösenordet för tjänstens huvudnamn.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Kubernetes-hemlighet som kan användas i en pod-distribution med den `ImagePullSecrets` parametern.

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
