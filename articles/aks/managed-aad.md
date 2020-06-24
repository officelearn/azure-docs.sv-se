---
title: Använda Azure AD i Azure Kubernetes-tjänsten
description: Lär dig hur du använder Azure AD i Azure Kubernetes service (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 06/04/2020
ms.openlocfilehash: 8d446d82550a6bc790d162ee944b0753979b6546
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84782678"
---
# <a name="integrate-aks-managed-azure-ad-preview"></a>Integrera AKS-hanterad Azure AD (för hands version)

> [!Note]
> Befintliga AKS-kluster (Azure Kubernetes service) med Azure Active Directory (Azure AD)-integration påverkas inte av den nya AKS-hanterade Azure AD-upplevelsen.

Azure AD-integrering med AKS-hanterad Azure AD är utformad för att förenkla Azure AD-integration, där användare tidigare behövde skapa en klient app, en server app och krävde att Azure AD-klienten ska bevilja Läs behörighet för katalogen. I den nya versionen hanterar AKS Resource Provider klient-och Server apparna åt dig.

## <a name="limitations"></a>Begränsningar

* Du kan för närvarande inte uppgradera ett befintligt AKS Azure AD-integrerat kluster till den nya AKS-hanterade Azure AD-upplevelsen.

> [!IMPORTANT]
> AKS för hands versions funktioner är tillgängliga på en självbetjänings-och deltagande nivå. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och omfattas inte av service nivå avtal och begränsad garanti. AKS för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion. Mer information finns i följande support artiklar:
>
> - [Support principer för AKS](support-policies.md)
> - [Vanliga frågor och svar om support för Azure](faq.md)

## <a name="before-you-begin"></a>Innan du börjar

* Leta upp ditt Azure-kontos klient-ID genom att gå till Azure Portal och välja Azure Active Directory > egenskaper > katalog-ID

> [!Important]
> Du måste använda Kubectl med en lägsta version av 1,18

Du måste ha följande resurser installerade:

- Azure CLI, version 2.5.1 eller senare
- Tillägget AKS-Preview 0.4.38
- Kubectl med en lägsta version av [1,18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180)

Använd följande Azure CLI-kommandon om du vill installera/uppdatera AKS-förhands gransknings tillägget eller senare:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Om du vill installera kubectl använder du följande kommandon:

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
## <a name="azure-ad-authentication-overview"></a>Översikt över Azure AD-autentisering

Kluster administratörer kan konfigurera Kubernetes-rollbaserad åtkomst kontroll (RBAC) baserat på användarens identitet eller katalog grupp medlemskap. Azure AD-autentisering tillhandahålls för AKS-kluster med OpenID Connect. OpenID Connect är ett identitets lager som byggts ovanpå OAuth 2,0-protokollet. Mer information om OpenID Connect finns i [Open ID Connect-dokumentationen][open-id-connect].

Från inifrån Kubernetes-klustret används webhook-token-autentisering för att verifiera autentiseringstoken. Webhook-token-autentisering konfigureras och hanteras som en del av AKS-klustret.

## <a name="webhook-and-api-server"></a>Webhook och API-Server

:::image type="content" source="media/aad-integration/auth-flow.png" alt-text="Webhook-och API-serverautentisering":::

Som du ser i bilden ovan anropar API-servern AKS-webhook-servern och utför följande steg:

1. Azure AD-klientprogrammet används av kubectl för att logga in användare med [OAuth 2,0-enhetens Authorization-flöde](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).
2. Azure AD tillhandahåller en access_token, id_token och en refresh_token.
3. Användaren gör en begäran till kubectl med en access_token från kubeconfig.
4. Kubectl skickar access_token till APIServer.
5. API-servern konfigureras med auth-webhook-servern för att utföra verifiering.
6. Autentiserings-webhook-servern bekräftar att JSON Web Token signaturen är giltig genom att kontrol lera Azure AD offentlig signerings nyckel.
7. Serverprogrammet använder användarspecifika autentiseringsuppgifter för att fråga grupp medlemskap för den inloggade användaren från MS-Graph API.
8. Ett svar skickas till APIServer med användar information som User Principal Name-anspråk (UPN) för åtkomsttoken och grupp medlemskapet för användaren baserat på objekt-ID: t.
9. API: et utför ett auktoriserings beslut baserat på Kubernetes-rollen/RoleBinding.
10. När den är auktoriserad returnerar API-servern ett svar till kubectl.
11. Kubectl ger användaren feedback.


## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Skapa ett AKS-kluster med Azure AD aktiverat

Skapa ett AKS-kluster med hjälp av följande CLI-kommandon.

Skapa en Azure-resurs grupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Du kan använda en befintlig Azure AD-grupp eller skapa en ny. Du behöver objekt-ID för din Azure AD-grupp.

```azurecli-interactive
# List existing groups in the directory
az ad group list
```

Om du vill kapa en ny Azure AD-grupp för kluster administratörer använder du följande kommando:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name MyDisplay --mail-nickname MyDisplay
```

Skapa ett AKS-kluster och aktivera administrations åtkomst för din Azure AD-grupp

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

En lyckad skapande av ett AKS Azure AD-kluster har följande avsnitt i svars texten
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

Du behöver den inbyggda rollen [Azure Kubernetes service Cluster-användare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-user-role) för att utföra följande steg.

Hämta användarautentiseringsuppgifter för att få åtkomst till klustret:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Följ anvisningarna för att logga in.

Använd kommandot kubectl get Nodes för att Visa noder i klustret:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Konfigurera [rollbaserad Access Control (RBAC)](https://review.docs.microsoft.com/azure/aks/azure-ad-rbac?branch=pr-en-us-117564) för att konfigurera ytterligare säkerhets grupper för dina kluster.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Fel sökning av åtkomst problem med Azure AD

> [!Important]
> Stegen som beskrivs nedan kringgår normal Azure AD-gruppautentisering. Använd dem endast i nödfall.

Om du har blockerat permanent genom att inte ha åtkomst till en giltig Azure AD-grupp med åtkomst till klustret kan du fortfarande få administratörs behörighet för att få åtkomst till klustret direkt.

För att utföra de här stegen måste du ha till gång till den inbyggda rollen [Azure Kubernetes service Cluster admin](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-admin-role) .

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```

## <a name="non-interactive-login-with-kubelogin"></a>Icke-interaktiv inloggning med kubelogin

Det finns vissa icke-interaktiva scenarier, till exempel kontinuerliga integrerings pipeliner, som för närvarande inte är tillgängliga med kubectl. Du kan använda [kubelogin](https://github.com/Azure/kubelogin) för att komma åt klustret i icke-interaktiva scenarier.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [rollbaserad Access Control i Azure AD][azure-ad-rbac].
* Använd [kubelogin](https://github.com/Azure/kubelogin) för att få åtkomst till funktioner för Azure-autentisering som inte är tillgängliga i kubectl.
* Använd [Azure Resource Manager arm-mallar][aks-arm-template] för att skapa AKS-hanterade Azure AD-kluster.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md

