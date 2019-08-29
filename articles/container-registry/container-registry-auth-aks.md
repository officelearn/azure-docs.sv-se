---
title: Autentisera med Azure Container Registry från Azure Kubernetes-tjänsten
description: Lär dig hur du ger åtkomst till avbildningar i ditt privata behållar register från Azure Kubernetes-tjänsten med hjälp av ett Azure Active Directory tjänstens huvud namn.
services: container-service
author: dlepow
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/27/2019
ms.author: danlep
ms.openlocfilehash: f80956ec401737766f7a85540e90be70b9d621e7
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114693"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Autentisera med Azure Container Registry från Azure Kubernetes-tjänsten

När du använder Azure Container Registry (ACR) med Azure Kubernetes service (AKS) måste du upprätta en autentiseringsmekanism. Den här artikeln beskriver de rekommenderade konfigurationerna för autentisering mellan dessa två Azure-tjänster.

Du behöver bara konfigurera en av dessa autentiseringsmetoder. Det vanligaste tillvägagångs sättet är att [bevilja åtkomst med AKS-tjänstens huvud namn](#grant-aks-access-to-acr). Om du har speciella behov kan du välja att [bevilja åtkomst med Kubernetes hemligheter](#access-with-kubernetes-secret).

Den här artikeln förutsätter att du redan har skapat ett AKS-kluster och att du kan komma åt klustret `kubectl` med kommando rads klienten. Om du i stället vill skapa ett kluster och konfigurera åtkomst till ett behållar register när klustret skapas, [se Självstudier: Distribuera ett AKS-](../aks/tutorial-kubernetes-deploy-cluster.md) kluster eller [autentisera med Azure Container Registry från Azure Kubernetes service (för hands version)](../aks/cluster-container-registry-integration.md).

## <a name="grant-aks-access-to-acr"></a>Bevilja AKS åtkomst till ACR

När du skapar ett AKS-kluster skapar Azure också ett tjänst huvud namn för att stödja kluster Operability med andra Azure-resurser. Du kan använda den här automatiskt genererade tjänstens huvud namn för autentisering med ett ACR-register. För att göra det måste du skapa en [roll tilldelning](../role-based-access-control/overview.md#role-assignments) för Azure AD som ger klustrets huvud namn för tjänsten åtkomst till behållar registret.

Använd följande skript för att ge AKS-genererade tjänstens huvud namn åtkomst till ett Azure Container Registry. Ändra- `ACR_*` och-variablerna för din miljö innan du kör skriptet. `AKS_*`

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

I vissa fall kanske du inte kan tilldela den nödvändiga rollen till den automatiskt genererade AKS-tjänstens huvud namn som beviljar åtkomst till ACR. På grund av din organisations säkerhets modell kanske du till exempel inte har tillräcklig behörighet i Azure Active Directory-klienten för att tilldela en roll till det AKS tjänstens huvud namn. Att tilldela en roll till ett huvud namn för tjänsten kräver att ditt Azure AD-konto har Skriv behörighet till din Azure AD-klient. Om du inte har behörighet kan du skapa ett nytt huvud namn för tjänsten och sedan ge det åtkomst till behållar registret med en Kubernetes avbildnings-och pull-hemlighet.

Använd följande skript för att skapa ett nytt huvud namn för tjänsten (du kommer att använda dess autentiseringsuppgifter för Kubernetes-bildens pull-hemlighet). `ACR_NAME` Ändra variabeln för din miljö innan du kör skriptet.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create acrpull role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name http://$SERVICE_PRINCIPAL_NAME --role acrpull --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principal client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Nu kan du lagra autentiseringsuppgifterna för tjänstens huvud namn i en Kubernetes [image][image-pull-secret]-filhemlighet, som ditt AKS-kluster kommer att referera till när behållare körs.

Använd följande **kubectl** -kommando för att skapa Kubernetes-hemligheten. Ersätt `<acr-login-server>` med det fullständigt kvalificerade namnet för ditt Azure Container Registry (det har formatet "acrname.azurecr.io"). Ersätt `<service-principal-ID>` och`<service-principal-password>` med de värden du erhöll genom att köra föregående skript. Ersätt `<email-address>` med en korrekt formaterad e-postadress.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Du kan nu använda Kubernetes-hemligheten i pod-distributioner genom att ange dess namn (i det här fallet "ACR-auth" `imagePullSecrets` ) i parametern:

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
