---
title: Skapa en Azure Arc-aktiverad onboarding service-huvudobjekt (för hands version)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Skapa en Azure Arc-aktiverad onboarding-tjänstens huvud namn '
keywords: Kubernetes, båge, Azure, behållare
ms.openlocfilehash: f9f750980d8a8b5d8190ba0b399fe068f1dd99c7
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680790"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>Skapa en Azure Arc-aktiverad onboarding service-huvudobjekt (för hands version)

## <a name="overview"></a>Översikt

När ett kluster har publicerats i Azure måste agenterna som körs i klustret autentiseras Azure Resource Manager som en del av registreringen. `connectedk8s`Azure CLI-tillägget har automatiskt skapande av tjänstens huvud namn. Det kan dock finnas några scenarier där CLI-Automation inte fungerar:

* Din organisation begränsar vanligt vis skapandet av tjänstens huvud namn
* Den användare som onboarding The Cluster har inte behörighet att skapa tjänstens huvud namn

I stället ska vi skapa tjänstens huvud namn out-of-band och sedan skicka huvud kontot till Azure CLI-tillägget.

## <a name="create-a-new-service-principal"></a>Skapa ett nytt huvud namn för tjänsten

Skapa ett nytt huvud namn för tjänsten med ett informativt namn. Observera att namnet måste vara unikt för din Azure Active Directory-klient:

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**Utdataparametrar**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>Tilldela behörigheter

När du har skapat det nya huvud namnet för tjänsten tilldelar du rollen "Azure-Arc för Kubernetes onboarding" till det nya huvudobjektet. Det här är en inbyggd Azure-roll med begränsad behörighet, som endast tillåter att huvud kontot registrerar kluster i Azure. Huvudobjektet kan inte uppdatera, ta bort eller ändra andra kluster eller resurser i prenumerationen.

Med tanke på de begränsade förmågorna kan kunder enkelt använda den här huvudobjektet för att publicera flera kluster.

Behörigheter kan begränsas ytterligare genom att skicka i lämpligt `--scope` argument när du tilldelar rollen. Detta gör det möjligt för kunderna att begränsa kluster registreringen. Följande scenarier stöds av olika `--scope` Parametrar:

| Resurs  | `scope`-argument| Verkan |
| ------------- | ------------- | ------------- |
| Prenumeration | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | Tjänstens huvud namn kan registrera alla kluster i en befintlig resurs grupp i den aktuella prenumerationen |
| Resursgrupp | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | Tjänstens huvud namn kan __bara__ registrera kluster i resurs gruppen`myGroup` |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the apropriate scope
```

**Utdataparametrar**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>Använd tjänstens huvud namn med Azure CLI

Referera till det nyligen skapade tjänstens huvud namn:

```console
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Nästa steg

* [Använd Azure Policy för att styra kluster konfigurationen](./use-azure-policy.md)
