---
title: Begränsningar för Windows Server Node-pooler i Azure Kubernetes service (AKS)
description: Läs om kända begränsningar när du kör Windows Server-nodkonfigurationer och program arbets belastningar i Azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: c2c9e3d29ced5f75873656e253ecdbab5efe7df8
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114412"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Aktuella begränsningar för Windows Server-nodkonfigurationer och program arbets belastningar i Azure Kubernetes service (AKS)

I Azure Kubernetes service (AKS) kan du skapa en Node-pool som kör Windows Server som gäst operativ system på noderna. Dessa noder kan köra interna Windows-programbehållare, till exempel de som skapats på .NET Framework. Eftersom det finns stora skillnader i hur Linux och Windows OS tillhandahåller stöd för behållare, är vissa vanliga Kubernetes-och Pod-relaterade funktioner inte tillgängliga för Windows-nodkonfigurationer.

Den här artikeln beskriver några begränsningar och OS-koncept för Windows Server-noder i AKS. Node-pooler för Windows Server är för närvarande en för hands version.

> [!IMPORTANT]
> AKS för hands versions funktioner är självbetjänings deltagande. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och undantas från service nivå avtalen och den begränsade garantin. AKS för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion. Om du vill ha ytterligare information kan du läsa följande artiklar om support:
>
> * [Support principer för AKS][aks-support-policies]
> * [Vanliga frågor och svar om support för Azure][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Begränsningar för Windows Server i Kubernetes

Windows Server-behållare måste köras på en Windows-baserad behållar värd. Om du vill köra Windows Server-behållare i AKS kan du [skapa en noduppsättning som kör Windows Server][windows-node-cli] som gäst operativ system. Window Server Node pool support innehåller vissa begränsningar som ingår i den överordnade Windows Server i Kubernetes-projektet. Dessa begränsningar är inte begränsade till AKS. Mer information om det här överordnade stödet för Windows Server i Kubernetes finns i [Windows Server-behållare i Kubernetes-begränsningar](https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations).

Följande överordnade begränsningar för Windows Server-behållare i Kubernetes är relevanta för AKS:

- Windows Server-behållare kan bara använda Windows Server 2019, som matchar den underliggande Windows Server-nodens operativ system.
    - Behållar avbildningar som skapats med Windows Server 2016 eftersom bas operativ systemet inte stöds.
- Det går inte att använda privilegierade behållare.
- Linux-/regionsspecifika funktioner som RunAsUser, SELinux, AppArmor eller POSIX är inte tillgängliga i Windows Server-behållare.
    - Fil system begränsningar som är Linux-specifika, till exempel UUI/GUID, per användar behörigheter är inte tillgängliga i Windows Server-behållare.
- Azure-diskar och Azure Files är de volym typer som stöds, som används som NTFS-volymer i Windows Server-behållaren.
    - NFS-baserad lagring/volymer stöds inte.

## <a name="aks-limitations-for-windows-server-node-pools"></a>AKS begränsningar för Windows Server-noder i pooler

Följande ytterligare begränsningar gäller för stöd för Windows Server Node-pool i AKS:

- Ett AKS-kluster innehåller alltid en Linux Node-pool som den första noden. Den första Linux-baserade Node-poolen kan inte tas bort om inte själva AKS-klustret tas bort.
- AKS-kluster måste använda nätverks modellen Azure CNI (avancerat).
    - Kubernetes-nätverk (Basic) stöds inte. Du kan inte skapa ett AKS-kluster som använder Kubernetes. Mer information om skillnaderna i nätverks modeller finns i [nätverks koncept för program i AKS][azure-network-models].
    - Nätverks modellen i Azure CNI kräver ytterligare planering och överväganden för hantering av IP-adresser. Mer information om hur du planerar och implementerar Azure-CNI finns i [Konfigurera Azure cni Networking i AKS][configure-azure-cni].
- Windows Server-noder i AKS måste *uppgraderas* till den senaste versionen av windows Server 2019 för att upprätthålla de senaste korrigerings korrigeringarna och uppdateringarna. Windows-uppdateringar är inte aktiverade i den grundläggande noden i AKS. I ett regelbundet schema kring Windows Updates lanserings cykel och din egen verifierings process bör du utföra en uppgradering på Windows Server-pool (er) i ditt AKS-kluster. Mer information om hur du uppgraderar en pool för Windows Server-noder finns [i uppgradera en Node-pool i AKS][nodepool-upgrade].
    - Dessa Windows Server-noder använder tillfälligt ytterligare IP-adresser i det virtuella nätverkets undernät när en ny nod distribueras, innan den gamla noden tas bort.
    - vCPU kvoter förbrukas också tillfälligt i prenumerationen när en ny nod distribueras, och den gamla noden tas sedan bort.
    - Du kan inte automatiskt uppdatera och hantera omstarter `kured` med hjälp av Linux-noder i AKS.
- AKS-klustret kan ha högst åtta noder i pooler.
    - Du kan ha högst 400 noder i de åtta noderna i poolen.
- Namnet på Windows Server-adresspoolen innehåller högst 6 tecken.
- För hands versions funktioner i AKS, till exempel nätverks princip och kluster autoskalning, har inte godkänts för Windows Server-noder.
- Ingångs styrenheter ska bara schemaläggas på Linux-noder med en avmarkerare.
- Azure dev Spaces är för närvarande bara tillgängligt för Linux-baserade nodkonfigurationer.
- Stöd för grupphanterade tjänst konton (gMSA) när Windows Server-noder inte är anslutna till en Active Directory domän är för närvarande inte tillgänglig i AKS.
    - [AKS-motorn][aks-engine] med öppen källkod ger för närvarande gMSA-stöd om du behöver använda den här funktionen.

## <a name="os-concepts-that-are-different"></a>OS-koncept som skiljer sig

Kubernetes är historiskt Linux-fokuserad. Många exempel som används i den överordnade [Kubernetes.io][kubernetes] -webbplatsen är avsedda att användas på Linux-noder. När du skapar distributioner som använder Windows Server-behållare gäller följande vid OS-nivå:

- **Identitet** – Linux använder UserID (UID) och ID (GID) som visas som heltals typer. Användar-och grupp namn är inte kanoniska – de är bara ett alias i */etc/Groups* eller */etc/passwd* tillbaka till UID + GID.
    - Windows Server använder en större säkerhets identifierare för binärfiler (SID) som lagras i Windows Security Access Manager-databasen (SAM). Den här databasen delas inte mellan värden och behållare, eller mellan behållare.
- **Fil behörigheter** – Windows Server använder en åtkomst kontrol lista baserat på sid, i stället för en bitmask med behörigheter och UID + GID
- **Fil Sök vägar** – konvention på Windows Server är att använda \ i stället för/.
    - I pod-specifikationer som monterar volymer anger du sökvägen korrekt för Windows Server-behållare. I stället för en monterings punkt för */mnt/Volume* i en Linux-behållare anger du till exempel en enhets beteckning och en plats, till exempel */K/Volume* , som ska monteras som *K:* enhet.

## <a name="next-steps"></a>Nästa steg

Kom igång med Windows Server-behållare i AKS genom att [skapa en noduppsättning som kör Windows Server i AKS][windows-node-cli].

<!-- LINKS - external -->
[upstream-limitations]: https://kubernetes.io/docs/setup/windows/#limitations
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
