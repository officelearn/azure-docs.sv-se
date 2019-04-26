---
title: Operatorn bästa praxis - identitet i Azure Kubernetes Services (AKS)
description: Läs kluster operatorn Metodtips för att hantera autentisering och auktorisering för kluster i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 42f6fefa930a36fbfcca7b3f792cc749723f7b99
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60464499"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Metodtips för autentisering och auktorisering i Azure Kubernetes Service (AKS)

När du distribuerar och underhåller kluster i Azure Kubernetes Service (AKS), måste du implementera metoder för att hantera åtkomst till resurser och tjänster. Konton kan ha åtkomst till resurser och tjänster som de behöver utan att dessa kontroller. Det kan också vara svårt att spåra vilken uppsättning autentiseringsuppgifter som användes för att göra ändringar.

Den här bästa praxis-artikeln fokuserar på hur en kluster-operator kan hantera åtkomst och identitet för AKS-kluster. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Autentisera användare för AKS-kluster med Azure Active Directory
> * Kontrollera åtkomsten till resurser med rollbaserad åtkomstkontroll (RBAC)
> * Använda en hanterad identitet för att autentisera sig med andra tjänster

## <a name="use-azure-active-directory"></a>Use Azure Active Directory

**Bästa praxis riktlinjer** – distribuera AKS-kluster med Azure AD-integrering. Använda Azure AD centraliserar komponenten identity management. Ändringar i användarens konto eller grupp status uppdateras automatiskt i tillgång till AKS-klustret. Använd roller eller ClusterRoles och bindningar, enligt beskrivningen i nästa avsnitt, för att samla användare eller grupper till minsta möjliga behörigheter som krävs.

Utvecklare och programägare till ett Kubernetes-kluster behöver du åtkomst till olika resurser. Kubernetes tillhandahåller inte en lösning för Identitetshantering att styra vilka användare kan interagera med vilka resurser. I stället integrera du vanligtvis ditt kluster med en befintlig identitetslösning. Azure Active Directory (AD) innehåller en lösning för Identitetshantering för företag och kan integrera med AKS-kluster.

Med Azure AD-integrerade kluster i AKS kan du skapa *roller* eller *ClusterRoles* som definierar åtkomstbehörigheter till resurser. Du sedan *binda* roller till användare eller grupper från Azure AD. I nästa avsnitt beskrivs dessa Kubernetes rollbaserad åtkomstkontroll (RBAC). Integrering av Azure AD och hur du styr åtkomsten till resurser kan ses i följande diagram:

![Klusternivå autentisering för Azure Active Directory-integrering med AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Utvecklare autentiserar med Azure AD.
1. Azure AD-slutpunkten för utfärdande utfärdar en åtkomsttoken.
1. Utvecklaren utför en åtgärd med Azure AD-token som `kubectl create pod`
1. Kubernetes verifierar token med Azure Active Directory och hämtar utvecklarens gruppmedlemskap.
1. Kubernetes rollbaserad åtkomst till kontroll (RBAC) och kluster-principer tillämpas.
1. Utvecklarens förfrågan är lyckades eller inte baserat på föregående validering av medlemskap i Azure AD och Kubernetes RBAC och principer.

Om du vill skapa ett AKS-kluster som använder Azure AD, [integrera Azure Active Directory med AKS][aks-aad].

## <a name="use-role-based-access-controls-rbac"></a>Använda rollbaserad åtkomstkontroll (RBAC)

**Bästa praxis riktlinjer** – Använd Kubernetes RBAC för att definiera de behörigheter som användare eller grupper har till resurser i klustret. Skapa roller och -bindningar som tilldelar den minsta uppsättningen behörigheter som krävs. Integrera med Azure AD så uppdateras automatiskt ändringar i användarens status eller gruppmedlemskap och åtkomst till klusterresurserna är aktuell.

I Kubernetes, kan du ge detaljerad kontroll av åtkomst till resurser i klustret. Behörigheter kan definieras på klusternivå eller till specifika namnområden. Du kan definiera vilka resurser kan hanteras, och med vilken behörighet. Dessa roller är den kopplade till användare eller grupper med en bindning. Mer information om *roller*, *ClusterRoles*, och *bindningar*, se [alternativ för åtkomst och identitet för Azure Kubernetes Service (AKS)] [aks-concepts-identity].

Exempelvis kan du skapa en roll som ger fullständig åtkomst till resurser i namnområdet med namnet *ekonomi-app*, enligt följande exempel YAML manifestet:

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

En RoleBinding skapas som binder Azure AD-användare *developer1\@contoso.com* till RoleBinding, enligt följande YAML-manifest:

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

När *developer1\@contoso.com* autentiseras mot AKS-kluster som de har fullständiga behörigheter till resurser i den *ekonomi-app* namnområde. I det här sättet, du logiskt separat och styra åtkomsten till resurser. Kubernetes RBAC bör användas tillsammans med Azure AD-integrering, enligt beskrivningen i föregående avsnitt.

Om du vill se hur du använder Azure AD-grupper för att styra åtkomsten till Kubernetes-resurser med RBAC kan se [styra åtkomsten till klusterresurser med rollbaserade åtkomstkontroller och Azure Active Directory-identiteter i AKS] [ azure-ad-rbac].

## <a name="use-pod-identities"></a>Använda pod-identiteter

**Bästa praxis riktlinjer** – Använd inte fasta autentiseringsuppgifter i poddar eller behållaravbildningar, eftersom de är på risken för exponering eller missbruk. Använd i stället pod identiteter automatiskt begära åtkomst med hjälp av en central identitetslösning för Azure AD.

När poddar behöver åtkomst till andra Azure-tjänster, till exempel Cosmos DB, Key Vault eller Blob-lagring, måste din pod autentiseringsuppgifter. Dessa autentiseringsuppgifter kan definieras med behållaravbildningen eller matas in som en Kubernetes-hemlighet, men måste skapas och tilldelas manuellt. Ofta autentiseringsuppgifterna som återanvänds i poddar och regelbundet roteras med inte.

Hanterade identiteter för Azure-resurser (för närvarande implementeras som ett tillhörande AKS-öppenkällkodsprojekt) kan du automatiskt begära åtkomst till tjänster via Azure AD. Du inte manuellt definiera autentiseringsuppgifter för poddar, i stället de begär en åtkomsttoken i realtid och använda den för att få åtkomst till endast sina tilldelade tjänster. I AKS distribueras två komponenter av kluster-operatorn för att tillåta poddar hanterade identiteter:

* **Noden Management identitet (NMI)-server** är en pod som körs som en DaemonSet på varje nod i AKS-klustret. NMI servern lyssnar efter pod-begäranden till Azure-tjänster.
* **Hanterad identitet Controller (MIC)** är en central pod med behörighet att fråga Kubernetes API-servern och söker efter en Azure identitet mappning som motsvarar en pod.

När poddar begär åtkomst till en Azure-tjänst kan omdirigera regler för trafiken till noden Management identitet (NMI)-server. NMI-servern identifierar poddar som begär åtkomst till Azure-tjänster baserat på deras Fjärradress och frågar den hanterade identitet Controller (MIC). MIC söker efter Azure identity-mappningar i AKS-klustret och NMI-server och begär en åtkomsttoken från Azure Active Directory (AD) baserat på din pod identitetsmappning. Azure AD tillhandahåller åtkomst till servern NMI som returneras till din pod. Den här åtkomst-token kan användas av poden för att begära åtkomst till tjänster i Azure.

I följande exempel skapar en utvecklare en pod som använder en hanterad identitet för att begära åtkomst till en Azure SQL Server-instans:

![Pod identiteter tillåter en pod att automatiskt begära åtkomst till andra tjänster](media/operator-best-practices-identity/pod-identities.png)

1. Kluster-operatorn skapar först ett tjänstkonto som kan användas för att mappa identiteter när poddar begär åtkomst till tjänster.
1. NMI servern och MIC distribueras för att vidarebefordra alla pod-begäranden för åtkomst-token till Azure AD.
1. En utvecklare distribuerar en pod med en hanterad identitet som begär en åtkomsttoken via NMI-servern.
1. Token tillbaka till din pod och används för åtkomst till en Azure SQL Server-instans.

Hanterade pod identiteter är ett öppenkällkodsprojekt i AKS och stöds inte av teknisk support för Azure. Den tillhandahålls för att samla in feedback och buggar från vår community. Projektet rekommenderas inte för användning i produktion.

Om du vill använda pod identiteter, se [Azure Active Directory-identiteter för Kubernetes-program][aad-pod-identity].

## <a name="next-steps"></a>Nästa steg

Den här bästa praxis-artikeln fokuserar på autentisering och auktorisering för ditt kluster och resurser. Om du vill implementera vissa av dessa metodtips, finns i följande artiklar:

* [Integrera Azure Active Directory med AKS][aks-aad]
* [Använda hanterade identiteter för Azure-resurser med AKS][aad-pod-identity]

Mer information om kluster i AKS finns i följande metoder:

* [Isolering för flera innehavare och kluster][aks-best-practices-scheduler]
* [Grundläggande funktioner som Kubernetes scheduler][aks-best-practices-scheduler]
* [Avancerade funktioner för Kubernetes scheduler][aks-best-practices-advanced-scheduler]

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
