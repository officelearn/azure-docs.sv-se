---
title: Metodtips för Developer - Pod säkerhet i Azure Kubernetes Services (AKS)
description: Läs metodtipsen för utvecklare om hur du skyddar poddar i Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: eaeb81d7f93124f1f3dedf9676314b1b786d8571
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252992"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Metodtips för pod säkerhet i Azure Kubernetes Service (AKS)

När du utvecklar och kör program i Azure Kubernetes Service (AKS) är en nyckelfaktor säkerheten för dina poddar. Dina program bör vara utformade för principen om minst antal privilegier som krävs. Skydda privata data är viktigast för kunder. Du vill inte ha autentiseringsuppgifter, t. ex. databas anslutnings strängar, nycklar eller hemligheter och certifikat som exponeras för den utanför världen, där en angripare kan utnyttja dessa hemligheter i skadliga syfte. Inte lägga till dem i din kod eller bädda in dem i dina behållaravbildningar. Den här metoden skulle skapa en risk att utsättas och begränsa möjligheten att rotera autentiseringsuppgifterna som behållaravbildningarna måste återskapas.

I den här artikeln fokuserar vi på hur du skyddar poddar i AKS. Lär dig att:

> [!div class="checklist"]
> * Använd pod säkerhetskontext för att begränsa åtkomsten till processer och tjänster eller eskalering
> * Autentisera med andra Azure-resurser med hjälp av pod hanterade identiteter
> * Begära och hämta autentiseringsuppgifter från ett digitala valv, till exempel Azure Key Vault

Du kan också läsa metod tips för [kluster säkerhet][best-practices-cluster-security] och för [hantering av behållar avbildningar][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Säker pod åtkomst till resurser

**Rekommendationer om bästa praxis** – att köra som en annan användare eller grupp och begränsa åtkomsten till de underliggande nodernas processer och tjänster, definiera Pod säkerhets kontext inställningar. Tilldela minsta möjliga antal behörigheter som krävs.

För att dina program ska kunna köras korrekt ska poddar köras som en definierad användare eller grupp och inte som *rot*. Med `securityContext` för en POD eller container kan du definiera inställningar som *runAsUser* eller *fsGroup* för att anta lämpliga behörigheter. Endast tilldela nödvändiga användare eller gruppbehörigheter och Använd inte säkerhetskontexten som ett sätt att skaffa sig fler behörigheter. *RunAsUser*, behörighets eskalering och andra inställningar för Linux-funktioner är bara tillgängliga på Linux-noder och poddar.

När du kör som en rotanvändare behållare kan inte bindas till Privilegierade portar under 1024. I det här scenariot, kan Kubernetes-tjänster användas för att dölja det faktum att en app körs på en viss port.

En pod-säkerhetskontext kan också definiera ytterligare funktioner eller behörigheter för att komma åt processer och tjänster. Följande vanliga security kontext definitioner kan ställas in:

* **allowPrivilegeEscalation** definierar om Pod kan anta *rot* privilegier. Utforma dina program så att den här inställningen alltid är inställd på *falskt*.
* **Linux-funktioner** låter Pod komma åt underliggande noder. Var noga med att tilldela dessa funktioner. Tilldela minsta möjliga antal behörigheten som krävs. Mer information finns i [Linux-funktioner][linux-capabilities].
* **SELinux-etiketter** är en Linux-modul för kernel-säkerhet som gör att du kan definiera åtkomst principer för tjänster, processer och fil åtkomst program. Igen, tilldela minsta möjliga antal behörigheten som krävs. Mer information finns i [SELinux-alternativ i Kubernetes][selinux-labels]

Följande exempel pod YAML manifestet anger säkerhet för att definiera:

* Pod körs som användar-ID *1000* och del av grupp-ID *2000*
* Det går inte att eskalera privilegier att använda `root`
* Tillåter Linux-funktionerna för nätverksgränssnitt och värdens i realtid (maskinvara) klockan

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  containers:
    - name: security-context-demo
      image: nginx:1.15.5
    securityContext:
      runAsUser: 1000
      fsGroup: 2000
      allowPrivilegeEscalation: false
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```

Arbeta med din kluster-operator för att avgöra vilka säkerhetsinställningar för kontexten som du behöver. Försök att programmen utformas för att minimera ytterligare behörigheter och åtkomst som kräver att din pod. Det finns ytterligare säkerhetsfunktioner för att begränsa åtkomst med hjälp av AppArmor och seccomp (säker databehandling) och som kan implementeras med klusteroperatörer. Mer information finns i [skydda container åtkomst till resurser][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Begränsa exponeringen av autentiseringsuppgifter

**Vägledning för bästa praxis** – definiera inte autentiseringsuppgifter i program koden. Använda hanterade identiteter för Azure-resurser så att din pod begära åtkomst till andra resurser. En digital valvet, till exempel Azure Key Vault, bör också användas för att lagra och hämta digitala nycklar och autentiseringsuppgifter. Pod-hanterade identiteter är endast avsedd för användning med Linux-poddar och behållar avbildningar.

Undvik att fasta eller delade autentiseringsuppgifter används för att begränsa risken för autentiseringsuppgifter som exponeras i programkoden. Autentiseringsuppgifter eller nycklar bör inte inkluderas direkt i din kod. Om autentiseringsuppgifterna exponeras måste programmet uppdateras och omdistribueras. En bättre metod är att ge poddar sina egna identitets- och sätt att autentisera sig eller automatiskt hämta autentiseringsuppgifter från ett digitala valv.

Med följande [associerade AKS-projekt med öppen källkod][aks-associated-projects] kan du automatiskt autentisera poddar eller begära autentiseringsuppgifter och nycklar från ett digitalt valv:

* Hanterade identiteter för Azure-resurser, och
* Azure Key Vault FlexVol-drivrutin

Associerade AKS-projekt med öppen källkod stöds inte av teknisk support för Azure. De erbjuds att samla in feedback och buggar från vår community. Dessa projekt rekommenderas inte för produktions användning.

### <a name="use-pod-managed-identities"></a>Använd pod hanterade identiteter

En hanterad identitet för Azure-resurser gör att en POD autentiseras mot Azure-tjänster som stöder den, till exempel Storage eller SQL. Poden tilldelas en identitet i Azure där du kan autentisera till Azure Active Directory och ta emot en digital token. Den här digitala token kan visas för andra Azure-tjänster som kontrollerar om en pod har behörighet att komma åt tjänsten och utföra nödvändiga åtgärder. Den här metoden innebär att inga hemligheter krävs för databasanslutningssträngar, till exempel. Förenklad arbetsflödet för pod hanterad identitet visas i följande diagram:

![Förenklad arbetsflöde för pod-hanterad identitet i Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

Med en hanterad identitet behöver inte din programkod inkludera autentiseringsuppgifter för att få åtkomst till en tjänst, till exempel Azure Storage. Eftersom varje pod autentiserar med sin egen identitet, så kan du granska och granska åtkomst. Om ditt program ansluter med andra Azure-tjänster måste använda hanterade identiteter till gränsen credential återanvändning och risken för exponering av.

Mer information om Pod-identiteter finns i [Konfigurera ett AKS-kluster för att använda Pod hanterade identiteter och med dina program][aad-pod-identity]

### <a name="use-azure-key-vault-with-flexvol"></a>Använda Azure Key Vault med FlexVol

Hanterade pod identiteter fungerar bra för att autentisera mot Azure-tjänster som stöds. För dina egna tjänster eller program utan hanterade identiteter för Azure-resurser autentisera du fortfarande med autentiseringsuppgifter eller nycklar. Ett digitala valv kan användas för att lagra dessa autentiseringsuppgifter.

När program behöver en autentiseringsuppgift kan de kommunicera med digitala valvet hämta de senaste autentiseringsuppgifterna och ansluter sedan till tjänsten som krävs. Azure Key Vault kan vara digitala valvet. Förenklad arbetsflödet för att hämta autentiseringsuppgifter från Azure Key Vault med hjälp av pod hanterade identiteter visas i följande diagram:

![Förenklad arbetsflöde för att hämta autentiseringsuppgifter från Key Vault med en pod-hanterad identitet](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Med Key Vault kan du lagra och regelbundet rotera hemligheter, till exempel autentiseringsuppgifter, lagringskontonycklar eller certifikat. Du kan integrera Azure Key Vault med ett AKS-kluster med hjälp av en FlexVolume. Drivrutinen FlexVolume kan AKS-klustret internt hämta autentiseringsuppgifter från Key Vault och ge dem på ett säkert sätt till den begärande pod. Arbeta med din kluster-operatorn som ska distribuera Key Vault FlexVol drivrutinen till AKS-noder. Du kan använda en pod hanterad identitet för att begära åtkomst till Key Vault och hämta de autentiseringsuppgifter som du behöver via FlexVolume-drivrutinen.

Azure Key Vault med FlexVol är avsedd för användning med program och tjänster som körs på Linux-poddar och-noder.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på hur du skyddar dina poddar. Om du vill implementera vissa av dessa områden, finns i följande artiklar:

* [Använda hanterade identiteter för Azure-resurser med AKS][aad-pod-identity]
* [Integrera Azure Key Vault med AKS][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo-pod
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
