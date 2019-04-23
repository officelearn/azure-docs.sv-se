---
title: Begrepp - åtkomst och identitet i Azure Kubernetes Services (AKS)
description: Läs mer om åtkomst och identitet i Azure Kubernetes Service (AKS), inklusive Azure Active Directory-integrering, Kubernetes rollbaserad åtkomstkontroll (RBAC), och roller och -bindningar.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: 3432ba671431c25b7cd9ee58decc638861e884c3
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60000682"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Alternativ för åtkomst och identitet för Azure Kubernetes Service (AKS)

Det finns olika sätt att autentisera med och skydda Kubernetes-kluster. Med rollbaserad åtkomstkontroll (RBAC) kan ge du användare och grupper åtkomst till de resurser de behöver. Med Azure Kubernetes Service (AKS) kan du förbättra säkerheten och behörigheterna strukturen ytterligare med hjälp av Azure Active Directory. Dessa metoder för att skydda dina arbetsbelastningar för program och kunddata.

Den här artikeln beskrivs de grundläggande begrepp som hjälper dig att autentisera och tilldela behörigheter i AKS:

- [Kubernetes service-konton](#kubernetes-service-accounts)
- [Azure Active Directory-integrering](#azure-active-directory-integration)
- [Rollbaserad åtkomstkontroll (RBAC)](#role-based-access-controls-rbac)
- [Roller och ClusterRoles](#roles-and-clusterroles)
- [RoleBindings och ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Kubernetes service-konton

En primär användare-typer i Kubernetes är en *tjänstkontot*. Ett tjänstkonto finns i, och hanteras av Kubernetes-API. Autentiseringsuppgifterna för tjänstkonton lagras som hemligheter för Kubernetes, där de kan användas av auktoriserade poddar för att kommunicera med API-servern. De flesta API-begäranden tillhandahåller ett Autentiseringstoken för ett tjänstkonto eller ett vanligt användarkonto.

Vanliga användarkonton kan mer traditionellt åtkomst för mänskliga administratörer eller utvecklare, inte bara tjänster och processer. Kubernetes själva tillhandahåller inte en lösning för Identitetshantering där vanliga användarkonton och lösenord lagras. I stället kan externa identitetslösningar integreras i Kubernetes. Den här integrerade identitetslösning är Azure Active Directory för AKS-kluster.

Mer information om alternativ för identiteten i Kubernetes finns i [Kubernetes autentisering][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integrering

Säkerheten för AKS-kluster kan förbättras med integreringen av Azure Active Directory (AD). Bygger på lång erfarenhet av enterprise-Identitetshantering och är Azure AD en flera innehavare, molnbaserad katalog och identity management-tjänsten som kombinerar viktiga katalogtjänster, åtkomsthantering för program och identitetsskydd. Du kan integrera lokala identiteter med Azure AD i AKS-kluster för att tillhandahålla en enda källa för hantering och säkerhet.

![Azure Active Directory-integrering med AKS-kluster](media/concepts-identity/aad-integration.png)

Med Azure AD-integrerade AKS-kluster kan ge du användare eller grupper åtkomst till Kubernetes-resurser i ett namnområde eller i klustret. Att hämta en `kubectl` Konfigurationskontexten, som en användare kan köra den [aaz aks get-credentials] [ az-aks-get-credentials] kommando. När en användare sedan interagerar med AKS-kluster med `kubectl`, uppmanas de att logga in med sina autentiseringsuppgifter för Azure AD. Den här metoden tillhandahåller en enda källa för hantering av användarkonton och lösenord. Användaren kan bara komma åt resurserna som definieras av Klusteradministratören.

Azure AD-autentisering i AKS-kluster använder OpenID Connect, en Identitetslagret som bygger på OAuth 2.0-protokollet. OAuth 2.0 definierar metoder för att skaffa och använda åtkomsttoken kommer åt skyddade resurser, och OpenID Connect implementerar autentisering som en utökning av OAuth 2.0-auktoriseringsprocessen. Mer information om OpenID Connect finns i den [öppna ID Connect dokumentation][openid-connect]. Använd autentisering med Kubernetes Webhook-Token för att kontrollera autentiseringstoken som hämtats från Azure AD via OpenID Connect AKS-kluster. Mer information finns i den [Webhook Tokenautentisering dokumentation][webhook-token-docs].

## <a name="role-based-access-controls-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)

För att ge detaljerad filtrering av de åtgärder som användarna kan utföra, använder Kubernetes rollbaserad åtkomstkontroll (RBAC). Den här mekanismen för multifaktoråtkomstkontroll kan du tilldela användare eller grupper av användare, tillstånd att till exempel skapa eller ändra resurser eller visa loggar från att köra arbetsbelastningar för program. Dessa behörigheter kan tillhöra en enda namnrymd eller beviljas över hela AKS-klustret. Med Kubernetes RBAC, skapar du *roller* att definiera behörigheter och sedan tilldela roller till användare med *rollen bindningar*.

Mer information finns i [med RBAC-auktorisering][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Azure rollbaserad åtkomstkontroll (RBAC)
En ytterligare mekanism för att styra åtkomst till resurser är Azure rollbaserad åtkomstkontroll (RBAC). Kubernetes RBAC är utformad för att arbeta med resurser i ditt AKS-kluster och Azure RBAC är utformad för att arbeta med resurser i din Azure-prenumeration. Med Azure RBAC, skapar du en *rolldefinition* som beskriver de behörigheter som ska användas. En användare eller grupp tilldelas sedan den här rolldefinitionen för en viss *omfång*, vilket kan orsaka en enskild resurs, en resurs gruppen, eller i prenumerationen.

Mer information finns i [vad är Azure RBAC?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Roller och ClusterRoles

Innan du tilldelar behörigheter till användare med Kubernetes RBAC måste du först definiera dessa behörigheter som en *rollen*. Kubernetes roller *bevilja* behörigheter. Det finns ingen av en *neka* behörighet.

Roller används för att bevilja behörigheter i ett namnområde. Om du vill bevilja behörigheter över hela klustret eller till klusterresurser utanför ett visst namnområde kan du istället använda *ClusterRoles*.

En ClusterRole fungerar på samma sätt att ge behörighet till resurser, men kan tillämpas på resurser över hela klustret, inte en specifik namnrymd.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings och ClusterRoleBindings

När roller har definierats för att tilldela behörigheter till resurser kan du tilldela dessa Kubernetes RBAC-behörigheter med en *RoleBinding*. Om AKS-klustret kan integreras med Azure Active Directory, är bindningar hur de Azure AD-användarna beviljas behörighet att utföra åtgärder i klustret.

Rollen bindningar används för att tilldela roller för ett visst namnområde. Den här metoden kan du särskilja logiskt ett enda AKS-kluster med användare som endast kan komma åt resursen i deras tilldelade namnområdet. Om du vill binda roller i hela klustret eller till klusterresurser utanför ett visst namnområde kan du istället använda *ClusterRoleBindings*.

En ClusterRoleBinding fungerar på samma sätt att binda roller till användare, men kan tillämpas på resurser över hela klustret, inte en specifik namnrymd. Den här metoden kan du ge administratörer eller stöd för tekniker åtkomst till alla resurser i AKS-klustret.

## <a name="next-steps"></a>Nästa steg

Du kommer igång med Azure AD och Kubernetes RBAC, se [integrera Azure Active Directory med AKS][aks-aad].

Associerade metodtips finns [bästa praxis för autentisering och auktorisering i AKS][operator-best-practices-identity].

Mer information om core Kubernetes och AKS-begrepp finns i följande artiklar:

- [Kubernetes / AKS-kluster och arbetsbelastningar][aks-concepts-clusters-workloads]
- [Kubernetes / AKS-säkerhet][aks-concepts-security]
- [Kubernetes / AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes / AKS-lagring][aks-concepts-storage]
- [Kubernetes / AKS skala][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v1-protocols-openid-connect-code.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
