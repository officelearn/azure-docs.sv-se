---
title: Metodtips för hantering av identitet
titleSuffix: Azure Kubernetes Service
description: Lär dig metodtips för klusteroperatören för hur du hanterar autentisering och auktorisering för kluster i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 0e3569be769fcf70a65cbfee62a3b80a5abdc3b5
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668319"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Metodtips för autentisering och auktorisering i Azure Kubernetes Service (AKS)

När du distribuerar och underhåller kluster i Azure Kubernetes Service (AKS) måste du implementera sätt att hantera åtkomst till resurser och tjänster. Utan dessa kontroller kan konton ha åtkomst till resurser och tjänster som de inte behöver. Det kan också vara svårt att spåra vilka uppsättning autentiseringsuppgifter som användes för att göra ändringar.

Den här artikeln med metodtips fokuserar på hur en klusteroperator kan hantera åtkomst och identitet för AKS-kluster. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Autentisera AKS-klusteranvändare med Azure Active Directory
> * Kontrollera åtkomsten till resurser med rollbaserade åtkomstkontroller (RBAC)
> * Använda en hanterad identitet för att autentisera sig med andra tjänster

## <a name="use-azure-active-directory"></a>Använda Azure Active Directory

**Vägledning för bästa praxis** – distribuera AKS-kluster med Azure AD-integrering. Om du använder Azure AD centraliseras identitetshanteringskomponenten. Alla ändringar i användarkontots eller gruppstatusen uppdateras automatiskt i åtkomsten till AKS-klustret. Använd Roller eller ClusterRoles och Bindningar, som beskrivs i nästa avsnitt, för att begränsa användare eller grupper till minst mängd behörigheter som behövs.

Utvecklarna och programägarna i kubernetes-klustret behöver åtkomst till olika resurser. Kubernetes tillhandahåller inte en identitetshanteringslösning för att styra vilka användare som kan interagera med vilka resurser. I stället integrerar du vanligtvis klustret med en befintlig identitetslösning. Azure Active Directory (AD) tillhandahåller en företagsklar identitetshanteringslösning och kan integreras med AKS-kluster.

Med Azure AD-integrerade kluster i AKS skapar du *roller* eller *ClusterRolles* som definierar åtkomstbehörigheter till resurser. Du *binder* sedan rollerna till användare eller grupper från Azure AD. Dessa Kubernetes rollbaserade åtkomstkontroller (RBAC) beskrivs i nästa avsnitt. Integreringen av Azure AD och hur du styr åtkomsten till resurser kan ses i följande diagram:

![Autentisering på klusternivå för Azure Active Directory-integrering med AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Utvecklaren autentiserar med Azure AD.
1. Slutpunkten för Azure AD-tokenutfärdning utfärdar åtkomsttoken.
1. Utvecklaren utför en åtgärd med Azure AD-token, till exempel`kubectl create pod`
1. Kubernetes validerar token med Azure Active Directory och hämtar utvecklarens gruppmedlemskap.
1. Kubernetes rollbaserad åtkomstkontroll (RBAC) och klusterprinciper tillämpas.
1. Utvecklarens begäran lyckas eller inte baseras på tidigare validering av Azure AD-gruppmedlemskap och Kubernetes RBAC och principer.

Information om hur du skapar ett AKS-kluster som använder Azure AD finns i [Integrera Azure Active Directory med AKS][aks-aad].

## <a name="use-role-based-access-controls-rbac"></a>Använda rollbaserade åtkomstkontroller (RBAC)

**Vägledning för bästa praxis** - Använd Kubernetes RBAC för att definiera de behörigheter som användare eller grupper har till resurser i klustret. Skapa roller och bindningar som tilldelar den minsta mängd behörigheter som krävs. Integrera med Azure AD så att alla ändringar i användarstatus eller gruppmedlemskap uppdateras automatiskt och åtkomst till klusterresurser är aktuell.

I Kubernetes kan du ge detaljerad kontroll över åtkomst till resurser i klustret. Behörigheter kan definieras på klusternivå eller till specifika namnområden. Du kan definiera vilka resurser som kan hanteras och med vilka behörigheter. Dessa roller tillämpas sedan på användare eller grupper med en bindning. Mer information om *Roller,* *ClusterRoles*och *Bindningar*finns i [Åtkomst- och identitetsalternativ för Azure Kubernetes Service (AKS)][aks-concepts-identity].

Som ett exempel kan du skapa en roll som ger fullständig åtkomst till resurser i namnområdet med namnet *finance-app*, vilket visas i följande exempel YAML manifest:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

En RoleBinding skapas sedan som binder Azure *AD-användarutvecklare1\@contoso.com* till RoleBinding, vilket visas i följande YAML-manifest:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

När *\@developer1 contoso.com* autentiseras mot AKS-klustret har de fullständig behörighet till resurser i namnområdet *ekonomi-app.* På så sätt separerar och styr du logiskt åtkomst till resurser. Kubernetes RBAC ska användas tillsammans med Azure AD-integrering, som beskrivs i föregående avsnitt.

Information om hur du använder Azure AD-grupper för att styra åtkomsten till Kubernetes-resurser med RBAC finns i [Kontrollera åtkomst till klusterresurser med hjälp av rollbaserade åtkomstkontroller och Azure Active Directory-identiteter i AKS][azure-ad-rbac].

## <a name="use-pod-identities"></a>Använda podidentiteter

**Vägledning för bästa praxis** – Använd inte fasta autentiseringsuppgifter i poddar eller behållarbilder, eftersom de riskerar att utsättas eller missbrukas. Använd i stället pod-identiteter för att automatiskt begära åtkomst med hjälp av en central Azure AD-identitetslösning. Pod-identiteter är endast avsedda att användas med Linux-poddar och behållaravbildningar.

När poddar behöver åtkomst till andra Azure-tjänster, till exempel Cosmos DB, Key Vault eller Blob Storage, behöver podden åtkomstautentiseringsuppgifter. Dessa åtkomstautentiseringsuppgifter kan definieras med behållaravbildningen eller injiceras som en Kubernetes-hemlighet, men måste skapas och tilldelas manuellt. Ofta återanvänds autentiseringsuppgifterna över poddar och roteras inte regelbundet.

Hanterade identiteter för Azure-resurser (för närvarande implementerade som ett associerat AKS open source-projekt) gör att du automatiskt kan begära åtkomst till tjänster via Azure AD. Du definierar inte manuellt autentiseringsuppgifter för poddar, i stället begär de en åtkomsttoken i realtid och kan använda den för att komma åt endast sina tilldelade tjänster. I AKS distribueras två komponenter av klusteroperatorn så att poddar kan använda hanterade identiteter:

* **NMI-servern (Node Management Identity)** är en pod som körs som en DaemonSet på varje nod i AKS-klustret. NMI-servern lyssnar efter pod-begäranden till Azure-tjänster.
* **MIC (Managed Identity Controller)** är en central pod med behörighet att fråga Kubernetes API-server och söker efter en Azure-identitetsmappning som motsvarar en pod.

När poddar begär åtkomst till en Azure-tjänst omdirigerar nätverksregler trafiken till NMI-servern (Node Management Identity). NMI-servern identifierar poddar som begär åtkomst till Azure-tjänster baserat på deras fjärradress och frågar den hanterade identitetskontrollanten (MIC). MIC söker efter Azure-identitetsmappningar i AKS-klustret och NMI-servern begär sedan en åtkomsttoken från Azure Active Directory (AD) baserat på poddens identitetsmappning. Azure AD ger åtkomst till NMI-servern, som returneras till podden. Den här åtkomsttoken kan användas av podden för att sedan begära åtkomst till tjänster i Azure.

I följande exempel skapar en utvecklare en pod som använder en hanterad identitet för att begära åtkomst till en Azure SQL Server-instans:

![Pod-identiteter gör det möjligt för en pod att automatiskt begära åtkomst till andra tjänster](media/operator-best-practices-identity/pod-identities.png)

1. Klusteroperatören skapar först ett tjänstkonto som kan användas för att mappa identiteter när poddar begär åtkomst till tjänster.
1. NMI-servern och MIC distribueras för att vidarebefordra alla pod-begäranden för åtkomsttoken till Azure AD.
1. En utvecklare distribuerar en pod med en hanterad identitet som begär en åtkomsttoken via NMI-servern.
1. Token returneras till podden och används för att komma åt en Azure SQL Server-instans.

> [!NOTE]
> Hanterade pod-identiteter är ett open source-projekt och stöds inte av Azures tekniska support.

Information om hur du använder pod-identiteter finns i [Azure Active Directory-identiteter för Kubernetes-program][aad-pod-identity].

## <a name="next-steps"></a>Nästa steg

Den här artikeln med metodtips fokuserade på autentisering och auktorisering för klustret och resurserna. Information om hur du implementerar några av de här metodtipsen finns i följande artiklar:

* [Integrera Azure Active Directory med AKS][aks-aad]
* [Använda hanterade identiteter för Azure-resurser med AKS][aad-pod-identity]

Mer information om klusteråtgärder i AKS finns i följande metodtips:

* [Flera innehavare och isolering av kluster][aks-best-practices-cluster-isolation]
* [Grundläggande kubernetes-schemaläggare][aks-best-practices-scheduler]
* [Avancerade Kubernetes-schemaläggare][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
