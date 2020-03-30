---
title: Begrepp - Åtkomst och identitet i Azure Kubernetes Services (AKS)
description: Lär dig mer om åtkomst och identitet i Azure Kubernetes Service (AKS), inklusive Azure Active Directory-integrering, Kubernetes rollbaserad åtkomstkontroll (RBAC) och roller och bindningar.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: e4945535417f7d8d33308121267ba97e1f835e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259609"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Åtkomst och identitetsalternativ för Azure Kubernetes Service (AKS)

Det finns olika sätt att autentisera med och säkra Kubernetes-kluster. Med hjälp av rollbaserade åtkomstkontroller (RBAC) kan du ge användare eller grupper åtkomst till endast de resurser de behöver. Med Azure Kubernetes Service (AKS) kan du ytterligare förbättra säkerhets- och behörighetsstrukturen med hjälp av Azure Active Directory. Dessa metoder hjälper dig att skydda dina programarbetsbelastningar och kunddata.

I den här artikeln beskrivs de grundläggande begrepp som hjälper dig att autentisera och tilldela behörigheter i AKS:

- [Kubernetes tjänstkonton](#kubernetes-service-accounts)
- [Azure Active Directory-integrering](#azure-active-directory-integration)
- [Rollbaserade åtkomstkontroller (RBAC)](#role-based-access-controls-rbac)
- [Roller och ClusterRoles](#roles-and-clusterroles)
- [RoleBindings och ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Kubernetes tjänstkonton

En av de primära användartyperna i Kubernetes är ett *tjänstkonto*. Ett tjänstkonto finns i och hanteras av Kubernetes API. Autentiseringsuppgifterna för tjänstkonton lagras som Kubernetes hemligheter, vilket gör att de kan användas av auktoriserade poddar för att kommunicera med API-servern. De flesta API-begäranden tillhandahåller en autentiseringstoken för ett tjänstkonto eller ett vanligt användarkonto.

Normala användarkonton ger mer traditionell åtkomst för mänskliga administratörer eller utvecklare, inte bara tjänster och processer. Kubernetes själv tillhandahåller inte en identitetshanteringslösning där vanliga användarkonton och lösenord lagras. I stället kan externa identitetslösningar integreras i Kubernetes. För AKS-kluster är den här integrerade identitetslösningen Azure Active Directory.

Mer information om identitetsalternativen i Kubernetes finns i [Kubernetes-autentisering][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integrering

Säkerheten för AKS-kluster kan förbättras med integreringen av Azure Active Directory (AD). Azure AD bygger på årtionden av hantering av företagsidentiteter och är en molnbaserad katalog- och identitetshanteringstjänst med flera innehavare som kombinerar grundläggande katalogtjänster, hantering av programåtkomst och identitetsskydd. Med Azure AD kan du integrera lokala identiteter i AKS-kluster för att tillhandahålla en enda källa för kontohantering och säkerhet.

![Azure Active Directory-integrering med AKS-kluster](media/concepts-identity/aad-integration.png)

Med Azure AD-integrerade AKS-kluster kan du ge användare eller grupper åtkomst till Kubernetes-resurser inom ett namnområde eller över klustret. Om du `kubectl` vill hämta en konfigurationskontext kan en användare köra kommandot [az aks get-credentials.][az-aks-get-credentials] När en användare sedan interagerar med `kubectl`AKS-klustret med uppmanas de att logga in med sina Azure AD-autentiseringsuppgifter. Den här metoden ger en enda källa för hantering av användarkonton och lösenordsuppgifter. Användaren kan bara komma åt resurserna enligt klusteradministratörens definition.

Azure AD-autentisering i AKS-kluster använder OpenID Connect, ett identitetslager som bygger ovanpå OAuth 2.0-protokollet. OAuth 2.0 definierar mekanismer för att hämta och använda åtkomsttoken för att komma åt skyddade resurser, och OpenID Connect implementerar autentisering som ett tillägg till OAuth 2.0-auktoriseringsprocessen. Mer information om OpenID Connect finns i [Open ID Connect-dokumentationen][openid-connect]. För att verifiera autentiseringstoken som hämtats från Azure AD via OpenID Connect använder AKS-kluster Kubernetes Webhook Token Authentication. Mer information finns i [webhook token authentication dokumentation][webhook-token-docs].

## <a name="role-based-access-controls-rbac"></a>Rollbaserade åtkomstkontroller (RBAC)

Kubernetes använder rollbaserade åtkomstkontroller (RBAC) för att tillhandahålla detaljerad filtrering av de åtgärder som användarna kan utföra. Med den här kontrollmekanismen kan du tilldela användare, eller grupper av användare, behörighet att göra saker som att skapa eller ändra resurser eller visa loggar från att köra programarbetsbelastningar. Dessa behörigheter kan begränsas till ett enda namnområde eller beviljas i hela AKS-klustret. Med Kubernetes RBAC skapar du *roller* för att definiera behörigheter och tilldelar sedan dessa roller till användare med *rollbindningar*.

Mer information finns i [Använda RBAC-auktorisering][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Azure rollbaserade åtkomstkontroller (RBAC)
En ytterligare mekanism för att kontrollera åtkomsten till resurser är Azure-rollbaserade åtkomstkontroller (RBAC). Kubernetes RBAC har utformats för att fungera med resurser i AKS-klustret och Azure RBAC har utformats för att fungera med resurser i din Azure-prenumeration. Med Azure RBAC skapar du en *rolldefinition* som beskriver de behörigheter som ska tillämpas. En användare eller grupp tilldelas sedan den här *rolldefinitionen*för ett visst scope , som kan vara en enskild resurs, en resursgrupp eller över prenumerationen.

Mer information finns i [Vad är Azure RBAC?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Roller och ClusterRoles

Innan du tilldelar behörigheter till användare med Kubernetes RBAC definierar du först dessa behörigheter som en *roll*. Kubernetes-roller *beviljar* behörigheter. Det finns inget begrepp om *neka* tillstånd.

Roller används för att bevilja behörigheter inom ett namnområde. Om du behöver bevilja behörigheter i hela klustret eller klusterresurser utanför ett visst namnområde kan du i stället använda *ClusterRoles*.

En ClusterRole fungerar på samma sätt för att bevilja behörigheter till resurser, men kan tillämpas på resurser i hela klustret, inte ett visst namnområde.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings och ClusterRoleBindings

När roller har definierats för att bevilja behörigheter till resurser tilldelar du kubernetes RBAC-behörigheterna med ett *rollbindning*. Om AKS-klustret integreras med Azure Active Directory är bindningar hur dessa Azure AD-användare beviljas behörighet att utföra åtgärder i klustret.

Rollbindningar används för att tilldela roller för ett visst namnområde. Med den här metoden kan du logiskt segrera ett enda AKS-kluster, med användare som bara kan komma åt programresurserna i deras tilldelade namnområde. Om du behöver binda roller över hela klustret eller klusterresurser utanför ett visst namnområde kan du i stället använda *ClusterRoleBindings*.

En ClusterRoleBinding fungerar på samma sätt för att binda roller till användare, men kan tillämpas på resurser i hela klustret, inte ett specifikt namnområde. Med den här metoden kan du ge administratörer eller supporttekniker åtkomst till alla resurser i AKS-klustret.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med Azure AD och Kubernetes RBAC finns i [Integrera Azure Active Directory med AKS][aks-aad].

För tillhörande metodtips finns i [Metodtips för autentisering och auktorisering i AKS][operator-best-practices-identity].

Mer information om kubernetes och AKS-huvudbegrepp finns i följande artiklar:

- [Kubernetes / AKS kluster och arbetsbelastningar][aks-concepts-clusters-workloads]
- [Kubernetes / AKS säkerhet][aks-concepts-security]
- [Kubernetes / AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes / AKS lagring][aks-concepts-storage]
- [Kubernetes / AKS skala][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
