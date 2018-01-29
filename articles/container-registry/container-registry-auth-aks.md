---
title: "Autentisera med Azure-behållaren registret från Azure Container Service"
description: "Lär dig att ge åtkomst till avbildningar i registret privata behållare från Azure Container Service med hjälp av en Azure Active Directory-tjänstens huvudnamn."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/12/2018
ms.author: nepeters
ms.openlocfilehash: 86a160d8f2dbfb0e385d9dbed7cf6d789f5a8df6
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2018
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-service"></a>Autentisera med Azure-behållaren registret från Azure Container Service

När du använder Azure Container registret (ACR) med Azure Container Service (AKS), måste en autentiseringsmetod upprättas. Det här dokumentet beskriver rekommenderade konfigurationer för autentisering mellan dessa två Azure-tjänster.

## <a name="grant-aks-access-to-acr"></a>Grant AKS åtkomst till ACR

När en AKS klustret skapas, skapas även ett huvudnamn för tjänsten för att hantera klustret funktionalitet med Azure-resurser. Den här tjänstens huvudnamn kan också användas för autentisering med en ACR-registret. Om du vill göra det måste en rolltilldelning skapas för att ge service principal läsbehörighet till resursen ACR. 

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

## <a name="access-with-kubernetes-secret"></a>Åtkomst med Kubernetes hemlighet

I vissa fall kan tjänsten som används av AKS inte omfattas i ACR-registret. Du kan skapa en unik tjänstens huvudnamn och omfång ACR registret i dessa fall.

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

Huvudnamn autentiseringsuppgifterna för tjänsten kan nu lagras i en Kubernetes [avbildningen pull hemlighet] [ image-pull-secret] och refereras när behållare som körs i ett AKS-kluster. 

Följande kommando skapar Kubernetes hemliga. Ersätt namnet på servern med ACR inloggningen servern, användarnamnet med tjänsten ägar-id och lösenord med lösenordet för tjänstens huvudnamn.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Hemligheten som Kubernetes kan användas i en baljor distribution med den `ImagePullSecrets` parameter. 

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
