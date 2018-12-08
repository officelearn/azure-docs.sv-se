---
title: Metodtips för Developer - Pod säkerhet i Azure Kubernetes Services (AKS)
description: Läs metodtipsen för utvecklare om hur du skyddar poddar i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: 8acd69480d6cd441c33ccc696794977bbfbfd975
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53110210"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Metodtips för pod säkerhet i Azure Kubernetes Service (AKS)

När du utvecklar och kör program i Azure Kubernetes Service (AKS) är en nyckelfaktor säkerheten för dina poddar. Ditt program bör utformas för huvudnamn för minsta antal behörigheter som krävs. Skydda privata data är viktigast för kunder. Du vill inte att autentiseringsuppgifterna som databasanslutningssträngar, nycklar eller hemligheter och certifikat som är exponerade för omvärlden där en angripna kunna dra nytta av dessa hemligheter för skadliga syften. Inte lägga till dem i din kod eller bädda in dem i dina behållaravbildningar. Den här metoden skulle skapa en risk att utsättas och begränsa möjligheten att rotera autentiseringsuppgifterna som behållaravbildningarna måste återskapas.

Den här bästa praxis-artikeln handlar om hur säker poddar i AKS. Lär dig att:

> [!div class="checklist"]
> * Använd pod säkerhetskontext för att begränsa åtkomsten till processer och tjänster eller eskalering
> * Autentisera med andra Azure-resurser med hjälp av pod hanterade identiteter
> * Begära och hämta autentiseringsuppgifter från ett digitala valv, till exempel Azure Key Vault

Du kan också läsa Metodtips för [kluster security] [ best-practices-cluster-security] och för [bild behållarhantering][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Säker pod åtkomst till resurser

**Bästa praxis riktlinjer** – om du vill köra som en annan användare eller grupp och begränsa åtkomst till den underliggande noden processer och tjänster, definiera pod säkerhetsinställningar för kontexten. Tilldela minsta möjliga antal behörigheter som krävs.

För dina program kan köras korrekt, poddar ska köras som en definierad användare eller grupp och inte som *rot*. Den `securityContext` för en pod eller behållare kan du definiera inställningar som *användare* eller *fsGroup* kan de behörigheter som krävs. Endast tilldela nödvändiga användare eller gruppbehörigheter och Använd inte säkerhetskontexten som ett sätt att skaffa sig fler behörigheter. När du kör som en rotanvändare behållare kan inte bindas till Privilegierade portar under 1024. I det här scenariot, kan Kubernetes-tjänster användas för att dölja det faktum att en app körs på en viss port.

En pod-säkerhetskontext kan också definiera ytterligare funktioner eller behörigheter för att komma åt processer och tjänster. Följande vanliga security kontext definitioner kan ställas in:

* **allowPrivilegeEscalation** definierar om poden kan anta *rot* privilegier. Utforma dina program så att den här inställningen har alltid värdet *FALSKT*.
* **Linux-funktioner** låta pod åtkomst till underliggande noden processer. Var noga med att tilldela dessa funktioner. Tilldela minsta möjliga antal behörigheten som krävs. Mer information finns i [Linux funktioner][linux-capabilities].
* **SELinux etiketter** är en Linux-kernel security-modul som kan du definiera åtkomstprinciper för åtkomst till tjänster, processer och filsystem. Igen, tilldela minsta möjliga antal behörigheten som krävs. Mer information finns i [SELinux alternativ i Kubernetes][selinux-labels]

Följande exempel pod YAML manifestet anger säkerhet för att definiera:

* Pod körs som användar-ID *1000* och en del av grupp-ID *2000*
* Det går inte att skaffa sig fler behörigheter för att använda `root`
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

Arbeta med din kluster-operator för att avgöra vilka säkerhetsinställningar för kontexten som du behöver. Försök att programmen utformas för att minimera ytterligare behörigheter och åtkomst som kräver att din pod. Det finns ytterligare säkerhetsfunktioner för att begränsa åtkomst med hjälp av AppArmor och seccomp (säker databehandling) och som kan implementeras med klusteroperatörer. Mer information finns i [säker behållare åtkomst till resurser][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Begränsa exponeringen av autentiseringsuppgifter

**Bästa praxis riktlinjer** -inte definiera autentiseringsuppgifter i programkoden. Använda hanterade identiteter för Azure-resurser så att din pod begära åtkomst till andra resurser. En digital valvet, till exempel Azure Key Vault, bör också användas för att lagra och hämta digitala nycklar och autentiseringsuppgifter.

Undvik att fasta eller delade autentiseringsuppgifter används för att begränsa risken för autentiseringsuppgifter som exponeras i programkoden. Autentiseringsuppgifter eller nycklar bör inte inkluderas direkt i din kod. Om autentiseringsuppgifterna exponeras måste programmet uppdateras och omdistribueras. En bättre metod är att ge poddar sina egna identitets- och sätt att autentisera sig eller automatiskt hämta autentiseringsuppgifter från ett digitala valv.

AKS innehåller två sätt att automatiskt autentisera poddar eller begäran om autentiseringsuppgifter och nycklar från en digital vault:

* Hanterade identiteter för Azure-resurser, och
* Azure Key Vault FlexVol-drivrutin

### <a name="use-pod-managed-identities"></a>Använd pod hanterade identiteter

En hanterad identitet för Azure-resurser kan en pod autentisera sig mot en tjänst i Azure som stöder det som lagring, SQL. Poden tilldelas en identitet i Azure där du kan autentisera till Azure Active Directory och ta emot en digital token. Den här digitala token kan visas för andra Azure-tjänster som kontrollerar om en pod har behörighet att komma åt tjänsten och utföra nödvändiga åtgärder. Den här metoden innebär att inga hemligheter krävs för databasanslutningssträngar, till exempel. Förenklad arbetsflödet för pod hanterad identitet visas i följande diagram:

![Förenklad arbetsflöde för pod-hanterad identitet i Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

Med en hanterad identitet behöver inte din programkod inkludera autentiseringsuppgifter för att få åtkomst till en tjänst, till exempel Azure Storage. Eftersom varje pod autentiserar med sin egen identitet, så kan du granska och granska åtkomst. Om ditt program ansluter med andra Azure-tjänster måste använda hanterade identiteter till gränsen credential återanvändning och risken för exponering av.

Läs mer om pod identiteter, [konfigurera ett AKS-kluster om du vill använda pod hanterade identiteter] [ aad-pod-identity] och [tilldela och använda pod hanterade identiteter i din kod] [ aad-pod-identity].

### <a name="use-azure-key-vault-with-flexvol"></a>Använda Azure Key Vault med FlexVol

Hanterade pod identiteter fungerar bra för att autentisera mot Azure-tjänster som stöds. För dina egna tjänster eller program utan hanterade identiteter för Azure-resurser autentisera du fortfarande med autentiseringsuppgifter eller nycklar. Ett digitala valv kan användas för att lagra dessa autentiseringsuppgifter.

När program behöver en autentiseringsuppgift kan de kommunicera med digitala valvet hämta de senaste autentiseringsuppgifterna och ansluter sedan till tjänsten som krävs. Azure Key Vault kan vara digitala valvet. Förenklad arbetsflödet för att hämta autentiseringsuppgifter från Azure Key Vault med hjälp av pod hanterade identiteter visas i följande diagram:

![Förenklad arbetsflöde för att hämta autentiseringsuppgifter från Key Vault med en pod-hanterad identitet](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Med Key Vault kan du lagra och regelbundet rotera hemligheter, till exempel autentiseringsuppgifter, lagringskontonycklar eller certifikat. Du kan integrera Azure Key Vault med ett AKS-kluster med hjälp av en FlexVolume. Drivrutinen FlexVolume kan AKS-klustret internt hämta autentiseringsuppgifter från Key Vault och ge dem på ett säkert sätt till den begärande pod. Arbeta med din kluster-operatorn som ska distribuera Key Vault FlexVol drivrutinen till AKS-noder. Du kan använda en pod hanterad identitet för att begära åtkomst till Key Vault och hämta de autentiseringsuppgifter som du behöver via FlexVolume-drivrutinen.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på hur du skyddar dina poddar. Om du vill implementera vissa av dessa områden, finns i följande artiklar:

* [Använda hanterade identiteter för Azure-resurser med AKS][aad-pod-identity]
* [Integrera Azure Key Vault med AKS][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo-pod
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
