---
title: Använda Azure AD i Azure Kubernetes-tjänsten
description: Lär dig hur du använder Azure AD i Azure Kubernetes service (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: b121830192a2b88185bbbbc9a92934e51b32a61c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114653"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Integrera Azure AD i Azure Kubernetes service (för hands version)

> [!Note]
> Befintliga AKS v1-kluster med AD-integration påverkas inte av den nya AKS v2-upplevelsen.

Azure AD-integrering med AKS v2 är utformad för att förenkla Azure AD-integrering med AKS v1-upplevelse, där användare var tvungna att skapa en klient app, en server app och krävde att Azure AD-klienten ska bevilja Läs behörighet för katalogen. I den nya versionen hanterar AKS Resource Provider klient-och Server apparna åt dig.

## <a name="limitations"></a>Begränsningar

* Du kan för närvarande inte uppgradera ett befintligt Azure AD-aktiverat AKS v1-kluster till v2-upplevelsen.

> [!IMPORTANT]
> AKS för hands versions funktioner är tillgängliga på en självbetjänings-och deltagande nivå. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och omfattas inte av service nivå avtal och begränsad garanti. AKS för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion. Mer information finns i följande support artiklar:
>
> - [Support principer för AKS](support-policies.md)
> - [Vanliga frågor och svar om support för Azure](faq.md)

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha följande resurser installerade:

- Azure CLI, version 2.2.0 eller senare
- Tillägget AKS-Preview 0.4.38
- Kubectl med en lägsta version av [1,18 beta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

Använd följande Azure CLI-kommandon om du vill installera/uppdatera AKS-förhands gransknings tillägget eller senare:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Om du vill installera kubectl använder du följande:

```azurecli
sudo az aks install-cli
kubectl version --client
```

Använd [de här anvisningarna](https://kubernetes.io/docs/tasks/tools/install-kubectl/) för andra operativ system.

> [!CAUTION]
> När du har registrerat en funktion på en prenumeration kan du för närvarande inte avregistrera den funktionen. När du aktiverar vissa för hands versions funktioner kan du använda standardvärden för alla AKS-kluster som skapas efteråt i prenumerationen. Aktivera inte för hands versions funktioner för produktions prenumerationer. Använd i stället en separat prenumeration för att testa för hands versions funktionerna och samla in feedback.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Det kan ta flera minuter innan statusen visas som **registrerad**. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

När statusen visas som registrerad uppdaterar du registreringen av `Microsoft.ContainerService` resurs leverantören med hjälp av [AZ Provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Skapa ett AKS-kluster med Azure AD aktiverat

Nu kan du skapa ett AKS-kluster med hjälp av följande CLI-kommandon.

Börja med att skapa en Azure-resurs grupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Skapa sedan ett AKS-kluster:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
Kommandot ovan skapar ett AKS-kluster med tre noder, men användaren, som som skapade klustret, är som standard inte medlem i en grupp som har åtkomst till det här klustret. Användaren måste skapa en Azure AD-grupp, lägga till sig själva som medlem i gruppen och sedan uppdatera klustret så som visas nedan. Följ anvisningarna [här](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

När du har skapat en grupp och lagt till dig själv (och andra) som medlem kan du uppdatera klustret med Azure AD-gruppen med hjälp av följande kommando

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
Alternativt, om du först skapar en grupp och lägger till medlemmar, kan du aktivera Azure AD-gruppen vid skapande tid med hjälp av följande kommando.

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

En lyckad skapande av ett Azure AD v2-kluster har följande avsnitt i svars texten
```
"Azure ADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Klustret skapas inom några minuter.

## <a name="access-an-azure-ad-enabled-cluster"></a>Åtkomst till ett Azure AD-aktiverat kluster
Så här hämtar du administratörsautentiseringsuppgifter för att få åtkomst till klustret:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Använd nu kommandot kubectl get Nodes för att Visa noder i klustret:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

Så här hämtar du autentiseringsuppgifter för att få åtkomst till klustret:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Följ anvisningarna för att logga in.

Du får: **du måste vara inloggad på servern (ej behörig)**

Användaren ovan får ett fel meddelande eftersom användaren inte är en del av en grupp som har åtkomst till klustret.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azure AD-rollbaserade Access Control][azure-ad-rbac].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
