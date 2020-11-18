---
title: Koncept – åtkomst och identitet i Azure Kubernetes Services (AKS)
description: Lär dig mer om åtkomst och identitet i Azure Kubernetes service (AKS), inklusive Azure Active Directory-integrering, Kubernetes rollbaserad åtkomst kontroll (Kubernetes RBAC) och roller och bindningar.
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: ca167a2ae313c29581d40fe921a8742b9b6b61fe
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686063"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Åtkomst och identitetsalternativ för Azure Kubernetes Service (AKS)

Det finns olika sätt att autentisera, kontrol lera åtkomst/auktorisera och säkra Kubernetes-kluster. Med Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC) kan du ge användare, grupper och tjänst konton åtkomst till enbart de resurser de behöver. Med Azure Kubernetes service (AKS) kan du ytterligare förbättra strukturen för säkerhet och behörighet genom att använda Azure Active Directory och Azure RBAC. De här metoderna hjälper dig att skydda kluster åtkomsten och ger endast de behörigheter som krävs för utvecklare och operatörer.

Den här artikeln beskriver de viktigaste begreppen som hjälper dig att autentisera och tilldela behörigheter i AKS:

- [Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC)](#kubernetes-role-based-access-control-kubernetes-rbac)
  - [Roller och ClusterRoles](#roles-and-clusterroles)
  - [RoleBindings och ClusterRoleBindings](#rolebindings-and-clusterrolebindings) 
  - [Kubernetes tjänst konton](#kubernetes-service-accounts)
- [Azure Active Directory-integrering](#azure-active-directory-integration)
- [Azure RBAC](#azure-role-based-access-control-azure-rbac)
  - [Azure RBAC för att ge åtkomst till AKS-resursen](#azure-rbac-to-authorize-access-to-the-aks-resource)
  - [Azure RBAC för Kubernetes-auktorisering (för hands version)](#azure-rbac-for-kubernetes-authorization-preview)


## <a name="kubernetes-role-based-access-control-kubernetes-rbac"></a>Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC)

För att ge detaljerad filtrering av de åtgärder som användarna kan utföra använder Kubernetes Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC). Med den här kontrollen kan du tilldela användare eller grupper av användare behörighet att göra saker som att skapa eller ändra resurser, eller Visa loggar från att köra program arbets belastningar. Dessa behörigheter kan begränsas till ett enda namn område eller beviljas i hela AKS-klustret. Med Kubernetes RBAC skapar du *roller* för att definiera behörigheter och tilldelar sedan rollerna till användare med *roll bindningar*.

Mer information finns i [använda KUBERNETES RBAC-auktorisering][kubernetes-rbac].


### <a name="roles-and-clusterroles"></a>Roller och ClusterRoles

Innan du tilldelar behörigheter till användare med Kubernetes RBAC definierar du först dessa behörigheter som en *roll*. Kubernetes-roller *beviljar* behörigheter. Det finns inget begrepp för *neka* -behörighet.

Roller används för att bevilja behörigheter inom ett namn område. Om du behöver bevilja behörigheter över hela klustret, eller till kluster resurser utanför ett angivet namn område, kan du i stället använda *ClusterRoles*.

En ClusterRole fungerar på samma sätt för att bevilja behörigheter till resurser, men kan tillämpas på resurser i hela klustret, inte för en speciell namnrymd.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings och ClusterRoleBindings

När rollerna har definierats för att ge behörigheter till resurser tilldelar du dessa Kubernetes RBAC-behörigheter till en *RoleBinding*. Om ditt AKS-kluster [integreras med Azure Active Directory](#azure-active-directory-integration), är bindningar hur de Azure AD-användare beviljar behörigheter att utföra åtgärder i klustret, se så [här kontrollerar du åtkomsten till kluster resurser med hjälp av Kubernetes-rollbaserad åtkomst kontroll och Azure Active Directory identiteter](azure-ad-rbac.md).

Roll bindningar används för att tilldela roller för en specifik namnrymd. Med den här metoden kan du logiskt särskilja ett enda AKS-kluster, där användare bara kan komma åt program resurserna i sitt tilldelade namn område. Om du behöver binda roller över hela klustret, eller till kluster resurser utanför ett angivet namn område, kan du i stället använda *ClusterRoleBindings*.

En ClusterRoleBinding fungerar på samma sätt för att binda roller till användare, men kan tillämpas på resurser i hela klustret, inte för en speciell namnrymd. Med den här metoden kan du ge administratörer eller support tekniker åtkomst till alla resurser i AKS-klustret.


> [!NOTE]
> Alla kluster åtgärder som utförs av Microsoft/AKS görs med användar medgivande under en inbyggd Kubernetes-roll `aks-service` och inbyggd roll bindning `aks-service-rolebinding` . Med den här rollen kan AKS felsöka och diagnostisera kluster problem, men det går inte att ändra behörigheter eller skapa roller eller roll bindningar eller andra åtgärder med hög behörighet. Roll åtkomst är bara aktiverat under aktiva support biljetter med JIT-åtkomst (just-in-Time). Läs mer om [stöd principer för AKS](support-policies.md).


### <a name="kubernetes-service-accounts"></a>Kubernetes tjänst konton

En av de primära användar typerna i Kubernetes är ett *tjänst konto*. Ett tjänst konto finns i och hanteras av Kubernetes-API: et. Autentiseringsuppgifterna för tjänst konton lagras som Kubernetes hemligheter, vilket gör att de kan användas av auktoriserade poddar för att kommunicera med API-servern. De flesta API-begäranden tillhandahåller en autentiseringstoken för ett tjänst konto eller ett vanligt användar konto.

Vanliga användar konton ger mer traditionell åtkomst till personal eller utvecklare, inte bara tjänster och processer. Kubernetes tillhandahåller inte en identitets hanterings lösning där vanliga användar konton och lösen ord lagras. I stället kan externa identitets lösningar integreras i Kubernetes. Denna integrerade identitets lösning är Azure Active Directory för AKS-kluster.

Mer information om identitets alternativen i Kubernetes finns i Kubernetes- [autentisering][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integrering

Säkerheten för AKS-kluster kan förbättras med integrering av Azure Active Directory (AD). Azure AD bygger på årtionden av företags identitets hantering och är en moln-och identitets hanterings tjänst för flera innehavare som kombinerar kärn katalog tjänster, program åtkomst hantering och identitets skydd. Med Azure AD kan du integrera lokala identiteter i AKS-kluster för att tillhandahålla en enda källa för konto hantering och säkerhet.

![Azure Active Directory integrering med AKS-kluster](media/concepts-identity/aad-integration.png)

Med Azure AD-integrerade AKS-kluster kan du ge användare eller grupper åtkomst till Kubernetes-resurser inom ett namn område eller över klustret. För att hämta en `kubectl` konfigurations kontext kan en användare köra kommandot [AZ AKS get-credentials][az-aks-get-credentials] . När en användare interagerar med AKS-klustret med `kubectl` , uppmanas de att logga in med sina autentiseringsuppgifter för Azure AD. Den här metoden ger en enda källa för användar konto hantering och autentiseringsuppgifter för lösen ord. Användaren kan bara komma åt resurserna som definieras av kluster administratören.

Azure AD-autentisering tillhandahålls för AKS-kluster med OpenID Connect. OpenID Connect är ett identitets lager som byggts ovanpå OAuth 2,0-protokollet. Mer information om OpenID Connect finns i [Open ID Connect-dokumentationen][openid-connect]. Från inifrån Kubernetes-klustret används [webhook-token-autentisering][webhook-token-docs] för att verifiera autentiseringstoken. Webhook-token-autentisering konfigureras och hanteras som en del av AKS-klustret.

### <a name="webhook-and-api-server"></a>Webhook och API-Server

![Webhook-och API-serverautentisering](media/concepts-identity/auth-flow.png)

Som du ser i bilden ovan anropar API-servern AKS-webhook-servern och utför följande steg:

1. Azure AD-klientprogrammet används av kubectl för att logga in användare med [OAuth 2,0-enhetens Authorization-flöde](../active-directory/develop/v2-oauth2-device-code.md).
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
 
**Lär dig hur du integrerar AKS med AAD [här](managed-aad.md).**

## <a name="azure-role-based-access-control-azure-rbac"></a>Azure RBAC (rollbaserad åtkomstkontroll)

Azure RBAC är ett auktoriserings system som bygger på [Azure Resource Manager](../azure-resource-manager/management/overview.md) som ger detaljerad åtkomst hantering av Azure-resurser.

 Azure RBAC är utformat för att fungera med resurser i din Azure-prenumeration medan Kubernetes RBAC är utformad för att fungera med Kubernetes-resurser i ditt AKS-kluster. 

Med Azure RBAC skapar du en *roll definition* som beskriver de behörigheter som ska tillämpas. En användare eller grupp tilldelas sedan den här roll definitionen via en *roll tilldelning* för ett visst *omfång*, som kan vara en enskild resurs, en resurs grupp eller över prenumerationen.

Mer information finns i [Vad är Azures rollbaserad åtkomst kontroll (Azure RBAC)?][azure-rbac]

Det finns två åtkomst nivåer som krävs för att fullständigt kunna använda ett AKS-kluster: 
1. [Få åtkomst till AKS-resursen i din Azure-prenumeration](#azure-rbac-to-authorize-access-to-the-aks-resource). Med den här processen kan du styra sakernas skalning eller uppgradera klustret med hjälp av AKS-API: er samt hämta din kubeconfig.
2. Åtkomst till Kubernetes-API: et. Den här åtkomsten styrs antingen av [KUBERNETES RBAC](#kubernetes-role-based-access-control-kubernetes-rbac) (traditionellt) eller genom att [integrera Azure RBAC med AKS för Kubernetes-auktorisering](#azure-rbac-for-kubernetes-authorization-preview)

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC för att ge åtkomst till AKS-resursen

Med Azure RBAC kan du ange dina användare (eller identiteter) med detaljerad åtkomst till AKS-resurser för en eller flera prenumerationer. Du kan till exempel ha [rollen Azure Kubernetes service Contributor](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) som gör det möjligt att utföra åtgärder som att skala och uppgradera klustret. En annan användare kan ha [rollen som administratör för Azure Kubernetes service Cluster](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) som bara ger behörighet att hämta admin-kubeconfig.

Alternativt kan du ge användaren rollen som allmän [deltagare](../role-based-access-control/built-in-roles.md#contributor) , som omfattar ovanstående behörigheter och alla åtgärder som är möjliga för AKS-resursen, med undantag för att hantera behörigheter.

Se mer information om hur du använder Azure RBAC för att skydda åtkomsten till kubeconfig-filen som ger åtkomst till Kubernetes-API: et [här](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC för Kubernetes-auktorisering (för hands version)

Med Azure RBAC-integrering använder AKS en Kubernetes-auktoriserings-webhook-server så att du kan hantera behörigheter och tilldelningar av Azure AD-integrerade K8s kluster resurser med roll definitions-och roll tilldelningar i Azure.

![Azure RBAC för Kubernetes-auktoriseringsarkiv](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Som du ser i diagrammet ovan, när du använder Azure RBAC-integrering, kommer alla begär anden till Kubernetes-API: et att följa samma autentiseringspaket som beskrivs i [avsnittet om Azure Active integration](#azure-active-directory-integration). 

Men efter det, i stället för att enbart förlita sig på Kubernetes RBAC för auktorisering, kommer begäran faktiskt att godkännas av Azure, så länge identiteten som gjorde begäran finns i AAD. Om identiteten inte finns i AAD, till exempel ett Kubernetes-tjänstkonto, kommer inte Azure RBAC att lanseras och det är den normala Kubernetes RBAC.

I det här scenariot kan du ge användarna en av de fyra inbyggda rollerna eller skapa anpassade roller på samma sätt som du gör med Kubernetes-roller, men i det här fallet med Azure RBAC-mekanismer och API: er. 

Med den här funktionen kan du till exempel inte bara ge användare behörighet till AKS-resursen över prenumerationer, men konfigurera och ge dem rollen och behörighet som de kommer att ha i vart och ett av dessa kluster som styr åtkomsten till Kubernetes-API: et. Du kan till exempel bevilja `Azure Kubernetes Service RBAC Viewer` rollen i prenumerations omfånget och dess mottagare kommer att kunna visa och hämta alla Kubernetes-objekt från alla kluster, men inte ändra dem.


#### <a name="built-in-roles"></a>Inbyggda roller

AKS tillhandahåller följande fyra inbyggda roller. De liknar de [inbyggda rollerna Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) , men med några skillnader som stöd för CRDs. En fullständig lista över åtgärder som tillåts av varje inbyggd roll finns [här](../role-based-access-control/built-in-roles.md).

| Roll                                | Beskrivning  |
|-------------------------------------|--------------|
| RBAC-visningsprogrammet i Azure Kubernetes service  | Tillåter skrivskyddad åtkomst för att se de flesta objekt i ett namn område. Den tillåter inte visning av roller eller roll bindningar. Den här rollen tillåter inte visning `Secrets` , eftersom läsning av innehållet i hemligheter ger åtkomst till `ServiceAccount` autentiseringsuppgifter i namn området, vilket skulle tillåta API-åtkomst `ServiceAccount` i namn området (en form av behörighets eskalering)  |
| RBAC-skrivare för Azure Kubernetes service | Tillåter Läs-/skriv åtkomst till de flesta objekt i ett namn område. Den här rollen tillåter inte visning eller ändring av roller eller roll bindningar. Den här rollen tillåter dock åtkomst `Secrets` och körning av poddar som alla ServiceAccount i namn området, så att den kan användas för att få åtkomst nivåer för API: er för alla ServiceAccount i namn området. |
| RBAC-administratör för Azure Kubernetes-tjänsten  | Tillåter administratörs åtkomst, som är avsedd att beviljas inom ett namn område. Tillåter Läs-/skriv åtkomst till de flesta resurser i ett namn område (eller ett kluster omfång), inklusive möjligheten att skapa roller och roll bindningar i namn området. Den här rollen tillåter inte skriv åtkomst till resurs kvot eller själva namn området. |
| Azure Kubernetes service RBAC-kluster administratör  | Ger åtkomst till superanvändare för att utföra alla åtgärder på en resurs. Den ger fullständig kontroll över alla resurser i klustret och i alla namn områden. |

**[Läs här](manage-azure-rbac.md)om du vill veta mer om hur du aktiverar Azure RBAC för Kubernetes-auktorisering.**

## <a name="next-steps"></a>Nästa steg

- För att komma igång med Azure AD och Kubernetes RBAC, se [integrera Azure Active Directory med AKS][aks-aad].
- För associerade metod tips, se [metod tips för autentisering och auktorisering i AKS][operator-best-practices-identity].
- Information om hur du kommer igång med Azure RBAC för Kubernetes-auktorisering finns i [använda Azure RBAC för att auktorisera åtkomst i Azure Kubernetes service-klustret (AKS)](manage-azure-rbac.md).
- Information om hur du kommer igång med att skydda din kubeconfig-fil finns i [begränsa åtkomsten till kluster konfigurations filen](control-kubeconfig-access.md)

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
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
