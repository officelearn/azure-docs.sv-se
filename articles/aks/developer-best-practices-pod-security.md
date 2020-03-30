---
title: Metodtips för utvecklare – pod-säkerhet i Azure Kubernetes Services (AKS)
description: Lär dig metodtips för utvecklare för hur du skyddar poddar i Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: 2ee41770f9993da381215f03ebf67aea3cc7dd9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475586"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Metodtips för pod-säkerhet i Azure Kubernetes Service (AKS)

När du utvecklar och kör program i Azure Kubernetes Service (AKS) är säkerheten för dina poddar en viktig faktor. Dina program bör utformas för principen om minsta antal privilegier som krävs. Att hålla privata data säkra är top of mind för kunderna. Du vill inte ha autentiseringsuppgifter som databasanslutningssträngar, nycklar eller hemligheter och certifikat som exponeras för omvärlden där en angripare kan dra nytta av dessa hemligheter för skadliga ändamål. Lägg inte till dem i koden eller bädda in dem i behållaravbildningar. Den här metoden skulle skapa en risk för exponering och begränsa möjligheten att rotera dessa autentiseringsuppgifter eftersom behållaravbildningarna måste återskapas.

Den här artikeln om metodtips fokuserar på hur du skyddar poddar i AKS. Lär dig att:

> [!div class="checklist"]
> * Använd pod säkerhetskontext för att begränsa åtkomsten till processer och tjänster eller eskalering av privilegier
> * Autentisera med andra Azure-resurser med hjälp av pod-hanterade identiteter
> * Begära och hämta autentiseringsuppgifter från ett digitalt valv som Azure Key Vault

Du kan också läsa metodtipsen för [klustersäkerhet][best-practices-cluster-security] och hantering av [behållaravbildning][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Säker pod-åtkomst till resurser

**Vägledning om bästa praxis** – Om du vill köra som en annan användare eller grupp och begränsa åtkomsten till de underliggande nodprocesserna och nodtjänsterna definierar du säkerhetskontextinställningar för pod. Tilldela det minsta antalet privilegier som krävs.

För att dina program ska fungera korrekt ska poddar köras som en definierad användare eller grupp och inte som *root*. För `securityContext` en pod eller behållare kan du definiera inställningar som *runAsUser* eller *fsGroup* att anta lämpliga behörigheter. Tilldela endast de användar- eller gruppbehörigheter som krävs och använd inte säkerhetskontexten som ett sätt att anta ytterligare behörigheter. *RunAsUser,* eskalering av privilegier och andra Linux-funktioner är endast tillgängliga på Linux-noder och poddar.

När du kör som en icke-rotanvändare kan behållare inte binda till de privilegierade portarna under 1024. I det här fallet kan Kubernetes Services användas för att dölja det faktum att en app körs på en viss port.

En säkerhetskontext för pod kan också definiera ytterligare funktioner eller behörigheter för åtkomst till processer och tjänster. Följande gemensamma säkerhetskontextdefinitioner kan ställas in:

* **allowPrivilegeEscalation** definierar om podden kan anta rotprivilegier. *root* Utforma dina program så att den här inställningen alltid är inställd på *false*.
* **Linux-funktioner** gör att podn kan komma åt underliggande nodprocesser. Var försiktig med att tilldela dessa funktioner. Tilldela det minsta antalet privilegier som behövs. Mer information finns i [Linux-funktioner][linux-capabilities].
* **SELinux-etiketter** är en Linux-kärnsäkerhetsmodul som låter dig definiera åtkomstprinciper för tjänster, processer och filsystemåtkomst. Tilldela återigen det minsta antalet privilegier som behövs. Mer information finns [i SELinux-alternativ i Kubernetes][selinux-labels]

I följande exempel pod YAML-manifest anger säkerhetskontextinställningar för att definiera:

* Pod körs som användar-ID *1000* och en del av grupp-ID *2000*
* Det går inte att eskalera privilegier att använda`root`
* Tillåter Linux-funktioner att komma åt nätverksgränssnitt och värdens realtidsklocka (maskinvara)

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

Arbeta med klusteroperatorn för att avgöra vilka säkerhetskontextinställningar du behöver. Försök att utforma dina program för att minimera ytterligare behörigheter och komma åt pod kräver. Det finns ytterligare säkerhetsfunktioner för att begränsa åtkomsten med AppArmor och seccomp (secure computing) som kan implementeras av klusteroperatörer. Mer information finns i [Säker behållaråtkomst till resurser][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Begränsa exponeringen för autentiseringsuppgifter

**Vägledning för bästa praxis** – Definiera inte autentiseringsuppgifter i programkoden. Använd hanterade identiteter för Azure-resurser för att låta din pod-begäran åtkomst till andra resurser. Ett digitalt valv, till exempel Azure Key Vault, bör också användas för att lagra och hämta digitala nycklar och autentiseringsuppgifter. Pod-hanterade identiteter är endast avsedda att användas med Linux-poddar och behållaravbildningar.

Undvik användningen av fasta eller delade autentiseringsuppgifter om du vill begränsa risken för att autentiseringsuppgifter exponeras i programkoden. Autentiseringsuppgifter eller nycklar ska inte inkluderas direkt i koden. Om dessa autentiseringsuppgifter visas måste programmet uppdateras och distribueras om. Ett bättre tillvägagångssätt är att ge poddar sin egen identitet och sätt att autentisera sig själva, eller automatiskt hämta autentiseringsuppgifter från ett digitalt valv.

Med följande [associerade AKS-projekt med öppen källkod][aks-associated-projects] kan du automatiskt autentisera poddar eller begära autentiseringsuppgifter och nycklar från ett digitalt valv:

* Hanterade identiteter för Azure-resurser och
* Azure Key Vault FlexVol-drivrutin

Associerade AKS-projekt med öppen källkod stöds inte av teknisk Azure-support. De tillhandahålls för att samla in feedback och buggar från vårt samhälle. Dessa projekt rekommenderas inte för produktionsanvändning.

### <a name="use-pod-managed-identities"></a>Använda pod-hanterade identiteter

En hanterad identitet för Azure-resurser gör att en pod autentiserar sig mot Azure-tjänster som stöder den, till exempel Lagring eller SQL. Podden tilldelas en Azure Identity som låter dem autentisera till Azure Active Directory och ta emot en digital token. Den här digitala token kan visas för andra Azure-tjänster som kontrollerar om podden har behörighet att komma åt tjänsten och utföra de nödvändiga åtgärderna. Den här metoden innebär att inga hemligheter krävs för databasanslutningssträngar, till exempel. Det förenklade arbetsflödet för pod-hanterad identitet visas i följande diagram:

![Förenklat arbetsflöde för pod-hanterad identitet i Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

Med en hanterad identitet behöver din programkod inte innehålla autentiseringsuppgifter för att komma åt en tjänst, till exempel Azure Storage. När varje pod autentiserar med sin egen identitet, så att du kan granska och granska åtkomsten. Om ditt program ansluter till andra Azure-tjänster använder du hanterade identiteter för att begränsa återanvändning av autentiseringsuppgifter och risk för exponering.

Mer information om pod-identiteter finns i [Konfigurera ett AKS-kluster för att använda pod-hanterade identiteter och med dina program][aad-pod-identity]

### <a name="use-azure-key-vault-with-flexvol"></a>Använda Azure Key Vault med FlexVol

Hanterade pod-identiteter fungerar bra för att autentisera mot stöd för Azure-tjänster. För dina egna tjänster eller program utan hanterade identiteter för Azure-resurser autentiserar du fortfarande med autentiseringsuppgifter eller nycklar. Ett digitalt valv kan användas för att lagra dessa autentiseringsuppgifter.

När program behöver en autentiseringsuppgifter kommunicerar de med det digitala valvet, hämtar de senaste autentiseringsuppgifterna och ansluter sedan till den tjänst som krävs. Azure Key Vault kan vara det här digitala valvet. Det förenklade arbetsflödet för att hämta en autentiseringsuppgifter från Azure Key Vault med pod-hanterade identiteter visas i följande diagram:

![Förenklat arbetsflöde för att hämta en autentiseringsuppgifter från Key Vault med hjälp av en hanterad pod-identitet](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Med Key Vault lagrar och roterar du regelbundet hemligheter som autentiseringsuppgifter, lagringskontonycklar eller certifikat. Du kan integrera Azure Key Vault med ett AKS-kluster med hjälp av ett FlexVolume. FlexVolume-drivrutinen gör att AKS-klustret internt kan hämta autentiseringsuppgifter från Key Vault och på ett säkert sätt bara tillhandahålla dem till den begärande podden. Arbeta med klusteroperatorn för att distribuera Key Vault FlexVol-drivrutinen till AKS-noderna. Du kan använda en pod-hanterad identitet för att begära åtkomst till Key Vault och hämta de autentiseringsuppgifter du behöver via FlexVolume-drivrutinen.

Azure Key Vault med FlexVol är avsett för användning med program och tjänster som körs på Linux-poddar och noder.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserade på hur du säkrar dina poddar. Information om hur du implementerar några av dessa områden finns i följande artiklar:

* [Använda hanterade identiteter för Azure-resurser med AKS][aad-pod-identity]
* [Integrera Azure Key Vault med AKS][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
