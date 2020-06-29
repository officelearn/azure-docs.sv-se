---
title: Använda hanterade identiteter i Azure Kubernetes-tjänsten
description: Lär dig hur du använder hanterade identiteter i Azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/04/2020
ms.author: mlearned
ms.openlocfilehash: 5854f512eb5a85430fbf95499274187a6d66016c
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445278"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Använda hanterade identiteter i Azure Kubernetes-tjänsten

För närvarande kräver ett Azure Kubernetes service (AKS)-kluster (specifikt Kubernetes Cloud Provider) en identitet för att skapa ytterligare resurser som belastningsutjämnare och hanterade diskar i Azure. Den här identiteten kan vara antingen en *hanterad identitet* eller ett *huvud namn för tjänsten*. Om du använder ett [huvud namn för tjänsten](kubernetes-service-principal.md)måste du antingen ange ett eller AKS skapar ett för din räkning. Om du använder hanterad identitet skapas detta automatiskt åt dig av AKS. Kluster som använder tjänstens huvud namn når till sist ett tillstånd där tjänstens huvud namn måste förnyas för att hålla klustret igång. Hantering av tjänstens huvud namn ökar komplexiteten, vilket innebär att det är lättare att använda hanterade identiteter i stället. Samma behörighets krav gäller för både tjänstens huvud namn och hanterade identiteter.

*Hanterade identiteter* är i grunden en omslutning av tjänstens huvud namn och gör hanteringen enklare. Rotation av autentiseringsuppgifter för MI sker automatiskt var 46: e dag enligt Azure Active Directory standard. AKS använder både systemtilldelade och användarspecifika hanterade identitets typer. Dessa identiteter är för närvarande oföränderliga. Läs mer om [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha följande resurs installerad:

- Azure CLI, version 2.2.0 eller senare

## <a name="limitations"></a>Begränsningar

* Det finns för närvarande inte stöd för att ta med dina egna hanterade identiteter.
* AKS-kluster med hanterade identiteter kan bara aktive ras när klustret skapas.
* Det går inte att uppdatera eller uppgradera befintliga AKS-kluster för att aktivera hanterade identiteter.
* Under kluster **uppgraderings** åtgärder är den hanterade identiteten tillfälligt otillgänglig.

## <a name="summary-of-managed-identities"></a>Sammanfattning av hanterade identiteter

AKS använder flera hanterade identiteter för inbyggda tjänster och tillägg.

| Identitet                       | Name    | Användningsfall | Standard behörigheter | Ta med din egen identitet
|----------------------------|-----------|----------|
| Kontroll plan | inte synlig | Används av AKS för att hantera nätverks resurser, t. ex. skapa en belastningsutjämnare för ingress, offentlig IP osv.| Deltagar roll för nod resurs grupp | Stöds för närvarande inte
| Kubelet | AKS-kluster namn – agentpoolegenskap | Autentisering med Azure Container Registry (ACR) | Läsar roll för nod resurs grupp | Stöds för närvarande inte
| Tillägg | AzureNPM | Ingen identitet krävs | NA | No
| Tillägg | AzureCNI nätverks övervakning | Ingen identitet krävs | NA | No
| Tillägg | azurepolicy (Gatekeeper) | Ingen identitet krävs | NA | No
| Tillägg | azurepolicy | Ingen identitet krävs | NA | No
| Tillägg | Calico | Ingen identitet krävs | NA | No
| Tillägg | Instrumentpanel | Ingen identitet krävs | NA | No
| Tillägg | HTTPApplicationRouting | Hanterar nödvändiga nätverks resurser | Läsar roll för nod resurs grupp, deltagar roll för DNS-zon | No
| Tillägg | Ingress Application Gateway | Hanterar nödvändiga nätverks resurser| Deltagar roll för nod resurs grupp | No
| Tillägg | omsagent | Används för att skicka AKS-mått till Azure Monitor | Övervaknings mått utgivar rollen | No
| Tillägg | Virtuell-nod (ACIConnector) | Hanterar nödvändiga nätverks resurser för Azure Container Instances (ACI) | Deltagar roll för nod resurs grupp | No


## <a name="create-an-aks-cluster-with-managed-identities"></a>Skapa ett AKS-kluster med hanterade identiteter

Nu kan du skapa ett AKS-kluster med hanterade identiteter med hjälp av följande CLI-kommandon.

Börja med att skapa en Azure-resurs grupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Skapa sedan ett AKS-kluster:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

Ett lyckat kluster skapas med hanterade identiteter som innehåller den här tjänstens huvud princips profil information:

```json
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Använd följande kommando för att fråga ObjectID för din hanterade identitet för kontroll planet:

```azurecli-interactive
az aks show -g myResourceGroup -n MyManagedCluster --query "identity"
```

Resultatet bör se ut så här:

```json
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

> [!NOTE]
> För att skapa och använda ditt eget VNet, en statisk IP-adress eller en ansluten Azure-disk där resurserna ligger utanför resurs gruppen för arbetsnoder, använder du PrincipalID för den tilldelade hanterade identiteten i kluster systemet för att utföra en roll tilldelning. Mer information om roll tilldelning finns i [Delegera åtkomst till andra Azure-resurser](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Behörighets bidrag till kluster hanterad identitet som används av Azure Cloud Provider kan ta upp till 60 minuter att fylla i.

Slutligen kan du hämta autentiseringsuppgifter för att få åtkomst till klustret:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Klustret kommer att skapas på några minuter. Du kan sedan distribuera dina program arbets belastningar till det nya klustret och interagera med det precis som du har gjort med service-huvudbaserade AKS-kluster.
