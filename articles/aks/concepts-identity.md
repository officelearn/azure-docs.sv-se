---
title: Koncept – åtkomst och identitet i Azure Kubernetes Services (AKS)
description: Lär dig mer om åtkomst och identitet i Azure Kubernetes service (AKS), inklusive Azure Active Directory-integrering, Kubernetes rollbaserad åtkomst kontroll (RBAC) och roller och bindningar.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: e4945535417f7d8d33308121267ba97e1f835e13
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374658"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Åtkomst-och identitets alternativ för Azure Kubernetes service (AKS)

Det finns olika sätt att autentisera med och säkra Kubernetes-kluster. Med hjälp av rollbaserad åtkomst kontroll (RBAC) kan du ge användare eller grupper åtkomst till de resurser som de behöver. Med Azure Kubernetes service (AKS) kan du ytterligare förbättra strukturen för säkerhet och behörigheter med hjälp av Azure Active Directory. Dessa metoder hjälper dig att skydda dina program arbets belastningar och kund information.

Den här artikeln beskriver de viktigaste begreppen som hjälper dig att autentisera och tilldela behörigheter i AKS:

- [Kubernetes tjänst konton](#kubernetes-service-accounts)
- [Azure Active Directory-integrering](#azure-active-directory-integration)
- [Rollbaserad åtkomst kontroll (RBAC)](#role-based-access-controls-rbac)
- [Roller och ClusterRoles](#roles-and-clusterroles)
- [RoleBindings och ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Kubernetes tjänst konton

En av de primära användar typerna i Kubernetes är ett *tjänst konto*. Ett tjänst konto finns i och hanteras av Kubernetes-API: et. Autentiseringsuppgifterna för tjänst konton lagras som Kubernetes hemligheter, vilket gör att de kan användas av auktoriserade poddar för att kommunicera med API-servern. De flesta API-begäranden tillhandahåller en autentiseringstoken för ett tjänst konto eller ett vanligt användar konto.

Vanliga användar konton ger mer traditionell åtkomst till personal eller utvecklare, inte bara tjänster och processer. Kubernetes tillhandahåller inte en identitets hanterings lösning där vanliga användar konton och lösen ord lagras. I stället kan externa identitets lösningar integreras i Kubernetes. Denna integrerade identitets lösning är Azure Active Directory för AKS-kluster.

Mer information om identitets alternativen i Kubernetes finns i Kubernetes- [autentisering][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integrering

Säkerheten för AKS-kluster kan förbättras med integrering av Azure Active Directory (AD). Azure AD bygger på årtionden av företags identitets hantering och är en moln-och identitets hanterings tjänst för flera innehavare som kombinerar kärn katalog tjänster, program åtkomst hantering och identitets skydd. Med Azure AD kan du integrera lokala identiteter i AKS-kluster för att tillhandahålla en enda källa för konto hantering och säkerhet.

![Azure Active Directory integrering med AKS-kluster](media/concepts-identity/aad-integration.png)

Med Azure AD-integrerade AKS-kluster kan du ge användare eller grupper åtkomst till Kubernetes-resurser inom ett namn område eller över klustret. För att hämta en `kubectl` konfigurations kontext kan en användare köra kommandot [AZ AKS get-credentials][az-aks-get-credentials] . När en användare interagerar med AKS-klustret med `kubectl`uppmanas de att logga in med sina autentiseringsuppgifter för Azure AD. Den här metoden ger en enda källa för användar konto hantering och autentiseringsuppgifter för lösen ord. Användaren kan bara komma åt resurserna som definieras av kluster administratören.

Azure AD-autentisering i AKS-kluster använder OpenID Connect, ett identitets lager som byggts ovanpå OAuth 2,0-protokollet. OAuth 2,0 definierar mekanismer för att hämta och använda åtkomsttoken för att komma åt skyddade resurser och OpenID Connect implementerar autentisering som ett tillägg till auktoriseringen av OAuth 2,0. Mer information om OpenID Connect finns i [Open ID Connect-dokumentationen][openid-connect]. För att verifiera de autentiseringstoken som erhålls från Azure AD via OpenID Connect, använder AKS-kluster Kubernetes webhook-token-autentisering. Mer information finns i dokumentationen för [webhook token-autentisering][webhook-token-docs].

## <a name="role-based-access-controls-rbac"></a>Rollbaserad åtkomst kontroll (RBAC)

För att ge detaljerad filtrering av de åtgärder som användarna kan utföra, använder Kubernetes rollbaserad åtkomst kontroll (RBAC). Med den här kontrollen kan du tilldela användare eller grupper av användare behörighet att göra saker som att skapa eller ändra resurser, eller Visa loggar från att köra program arbets belastningar. Dessa behörigheter kan begränsas till ett enda namn område eller beviljas i hela AKS-klustret. Med Kubernetes RBAC skapar du *roller* för att definiera behörigheter och tilldelar sedan rollerna till användare med *roll bindningar*.

Mer information finns i [använda RBAC-auktorisering][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Rollbaserad åtkomst kontroll i Azure (RBAC)
En ytterligare mekanism för att kontrol lera åtkomst till resurser är Azure-rollbaserad åtkomst kontroll (RBAC). Kubernetes RBAC är utformat för att fungera med resurser i ditt AKS-kluster och Azure RBAC är utformat för att fungera med resurser i din Azure-prenumeration. Med Azure RBAC skapar du en *roll definition* som beskriver de behörigheter som ska tillämpas. En användare eller grupp tilldelas sedan den här roll definitionen för ett visst *omfång*, som kan vara en enskild resurs, en resurs grupp eller över prenumerationen.

Mer information finns i [Vad är Azure RBAC?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Roller och ClusterRoles

Innan du tilldelar behörigheter till användare med Kubernetes RBAC definierar du först dessa behörigheter som en *roll*. Kubernetes-roller *beviljar* behörigheter. Det finns inget begrepp för *neka* -behörighet.

Roller används för att bevilja behörigheter inom ett namn område. Om du behöver bevilja behörigheter över hela klustret, eller till kluster resurser utanför ett angivet namn område, kan du i stället använda *ClusterRoles*.

En ClusterRole fungerar på samma sätt för att bevilja behörigheter till resurser, men kan tillämpas på resurser i hela klustret, inte för en speciell namnrymd.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings och ClusterRoleBindings

När rollerna har definierats för att ge behörigheter till resurser tilldelar du dessa Kubernetes RBAC-behörigheter till en *RoleBinding*. Om ditt AKS-kluster integreras med Azure Active Directory, är bindningar hur Azure AD-användare beviljar behörigheter att utföra åtgärder i klustret.

Roll bindningar används för att tilldela roller för en specifik namnrymd. Med den här metoden kan du logiskt särskilja ett enda AKS-kluster, där användare bara kan komma åt program resurserna i sitt tilldelade namn område. Om du behöver binda roller över hela klustret, eller till kluster resurser utanför ett angivet namn område, kan du i stället använda *ClusterRoleBindings*.

En ClusterRoleBinding fungerar på samma sätt för att binda roller till användare, men kan tillämpas på resurser i hela klustret, inte för en speciell namnrymd. Med den här metoden kan du ge administratörer eller support tekniker åtkomst till alla resurser i AKS-klustret.

## <a name="next-steps"></a>Nästa steg

För att komma igång med Azure AD och Kubernetes RBAC, se [integrera Azure Active Directory med AKS][aks-aad].

För associerade metod tips, se [metod tips för autentisering och auktorisering i AKS][operator-best-practices-identity].

Mer information om kärn Kubernetes-och AKS-koncept finns i följande artiklar:

- [Kubernetes/AKS-kluster och arbets belastningar][aks-concepts-clusters-workloads]
- [Kubernetes/AKS-säkerhet][aks-concepts-security]
- [Kubernetes/AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes/AKS-lagring][aks-concepts-storage]
- [Kubernetes/AKS-skala][aks-concepts-scale]

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
