---
title: Metodtips för isolering av kluster
titleSuffix: Azure Kubernetes Service
description: Lär dig metodtips för klusteroperatören för isolering i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 00643dc1699d1cbd47efd271738015ea05e895e2
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668346"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Metodtips för klusterisolering i Azure Kubernetes Service (AKS)

När du hanterar kluster i Azure Kubernetes Service (AKS) måste du ofta isolera team och arbetsbelastningar. AKS ger flexibilitet i hur du kan köra kluster med flera innehavare och isolera resurser. För att maximera din investering i Kubernetes bör dessa multi-arrendator och isoleringsfunktioner förstås och implementeras.

Den här artikeln om metodtips fokuserar på isolering för klusteroperatorer. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Planera för kluster med flera innehavare och separation av resurser
> * Använda logisk eller fysisk isolering i AKS-kluster

## <a name="design-clusters-for-multi-tenancy"></a>Designkluster för flerargsinnehavare

Kubernetes innehåller funktioner som gör det möjligt att logiskt isolera team och arbetsbelastningar i samma kluster. Målet bör vara att ange minsta möjliga antal privilegier, som är begränsade till de resurser som varje team behöver. Ett [namnområde][k8s-namespaces] i Kubernetes skapar en logisk isoleringsgräns. Ytterligare Kubernetes-funktioner och överväganden för isolering och flerargsinnehavare inkluderar följande områden:

* **Schemaläggning** omfattar användning av grundläggande funktioner som resurskvoter och pod avbrott budgetar. Mer information om dessa funktioner finns i [Metodtips för grundläggande schemaläggarfunktioner i AKS][aks-best-practices-scheduler].
  * Mer avancerade schemaläggarfunktioner inkluderar taints och tolerations, nodväljare och nod- och pod-tillhörighet eller anti-tillhörighet. Mer information om dessa funktioner finns i [Metodtips för avancerade schemaläggarfunktioner i AKS][aks-best-practices-advanced-scheduler].
* **Nätverk omfattar** användning av nätverksprinciper för att styra trafikflödet in och ut ur poddar.
* **Autentisering och auktorisering** omfattar användaren av RBAC-integrering (Role-Based Access Control) och Azure Active Directory (AD), pod-identiteter och hemligheter i Azure Key Vault. Mer information om dessa funktioner finns [i Metodtips för autentisering och auktorisering i AKS][aks-best-practices-identity].
* **Behållare** inkluderar pod säkerhetsprinciper, pod säkerhetskontexter, skanna bilder och körningar för sårbarheter. Innebär också att använda App Armor eller Seccomp (Secure Computing) för att begränsa behållaråtkomsten till den underliggande noden.

## <a name="logically-isolate-clusters"></a>Isolera kluster logiskt

**Vägledning för bästa praxis** - Använd logisk isolering för att separera team och projekt. Försök att minimera antalet fysiska AKS-kluster som du distribuerar för att isolera team eller program.

Med logisk isolering kan ett enda AKS-kluster användas för flera arbetsbelastningar, team eller miljöer. Kubernetes namnområden utgör den logiska [isoleringsgränsen][k8s-namespaces] för arbetsbelastningar och resurser.

![Logisk isolering av ett Kubernetes-kluster i AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Logisk separation av kluster ger vanligtvis en högre pod densitet än fysiskt isolerade kluster. Det finns mindre överskjutande beräkningskapacitet som sitter inaktiv i klustret. I kombination med Kubernetes-kluster automatisk skalning kan du skala antalet noder uppåt eller nedåt för att uppfylla kraven. Med den här metod för bästa praxis för automatisk skalning kan du bara köra antalet noder som krävs och minimera kostnaderna.

Kubernetes miljöer, i AKS eller någon annanstans, är inte helt säkra för fientlig användning av flera innehavare. I en miljö med flera innehavare arbetar flera klienter med en gemensam, delad infrastruktur. Därför om alla klienter inte kan lita på, måste du göra ytterligare planering för att undvika att en klient påverkar säkerheten och tjänsten för en annan. Ytterligare säkerhetsfunktioner som *Pod Security Policy* och mer detaljerade rollbaserade åtkomstkontroller (RBAC) för noder gör det svårare att utnyttja. Men för verklig säkerhet när du kör fientliga arbetsbelastningar med flera innehavare är en hypervisor den enda säkerhetsnivå som du bör lita på. Säkerhetsdomänen för Kubernetes blir hela klustret, inte en enskild nod. För dessa typer av fientliga arbetsbelastningar med flera innehavare bör du använda fysiskt isolerade kluster.

## <a name="physically-isolate-clusters"></a>Isolera kluster fysiskt

**Vägledning för bästa praxis** – Minimera användningen av fysisk isolering för varje separat team eller programdistribution. Använd i stället *logisk* isolering, som beskrivs i föregående avsnitt.

En vanlig metod för klusterisolering är att använda fysiskt separata AKS-kluster. I den här isoleringsmodellen tilldelas team eller arbetsbelastningar sitt eget AKS-kluster. Den här metoden ser ofta ut som det enklaste sättet att isolera arbetsbelastningar eller team, men lägger till ytterligare hantering och ekonomiska omkostnader. Du måste nu underhålla dessa flera kluster och måste individuellt ge åtkomst och tilldela behörigheter. Du faktureras också för alla enskilda noder.

![Fysisk isolering av enskilda Kubernetes-kluster i AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fysiskt separata kluster har vanligtvis en låg pod densitet. Eftersom varje team eller arbetsbelastning har sitt eget AKS-kluster överetableras klustret ofta med beräkningsresurser. Ofta är ett litet antal poddar schemalagda på dessa noder. Oanvänd kapacitet på noderna kan inte användas för program eller tjänster under utveckling av andra team. Dessa överskjutande resurser bidrar till merkostnaderna i fysiskt separata kluster.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserade på klusterisolering. Mer information om klusteråtgärder i AKS finns i följande metodtips:

* [Grundläggande kubernetes-schemaläggare][aks-best-practices-scheduler]
* [Avancerade Kubernetes-schemaläggare][aks-best-practices-advanced-scheduler]
* [Autentisering och auktorisering][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
