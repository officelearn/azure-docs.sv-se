---
title: Begränsningar för Windows Server-nodpooler i Azure Kubernetes Service (AKS)
description: Mer information om kända begränsningar när du kör nodpooler för Windows Server och arbetsbelastningar för program i Azure Kubernetes Service (AKS)
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: twhitney
ms.openlocfilehash: 12fb9dc67e8afae3dcb9ade97dd61ab438e0fac5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475399"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Aktuella begränsningar för nodpooler för Windows Server och arbetsbelastningar för program i Azure Kubernetes Service (AKS)

Du kan skapa en pool för noden som kör Windows Server som gästoperativsystemet på noderna i Azure Kubernetes Service (AKS). Dessa noder kan köra interna Windows-behållarprogram, till exempel de som bygger på .NET Framework. Eftersom det finns viktiga skillnader i hur Linux och Windows OS tillhandahåller stöd för behållare, är det inte för närvarande tillgängligt för Windows-nodpooler med vissa vanliga Kubernetes och pod-relaterade funktioner.

Den här artikeln beskriver några begränsningar och OS-koncept för Windows Server-noderna i AKS. Nodpooler för Windows Server finns för närvarande i förhandsversion.

> [!IMPORTANT]
> AKS-förhandsversionsfunktioner är självbetjäning, delta i. De tillhandahålls för att samla in feedback och buggar från vår community. I förhandsversionen kan är inte dessa funktioner avsedda för användning i produktion. Funktioner i offentliga förhandsversioner omfattas ”bästa prestanda” support. Hjälp från teamen för AKS-teknisk support är tillgänglig under kontorstid Pacific tidszon (Stillahavstid) endast. Mer information finns i följande supportartiklar:
>
> * [AKS supportprinciper][aks-support-policies]
> * [Vanliga frågor om Azure-Support][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Begränsningar för Windows Server i Kubernetes

Windows Server-behållare måste köras på en Windows-baserade behållarvärd. Om du vill köra Windows Server-behållare i AKS kan du [skapar en pool för noden som kör Windows Server] [ windows-node-cli] som gästoperativsystemet. Fönstret serverstöd noden poolen innehåller vissa begränsningar som ingår i den överordnade Windows-servern i Kubernetes-projektet. Dessa begränsningar är inte specifik för AKS. Läs mer om den här överordnade stöd för Windows Server i Kubernetes [Windows Server-behållare i Kubernetes begränsningar][upstream-limitations].

Följande överordnade begränsningar för Windows Server-behållare i Kubernetes är relevanta för AKS:

- Windows Server-behållare kan bara använda Windows Server 2019 som matchar den underliggande Windows Server-noden OS.
    - Behållaravbildningar som skapats med hjälp av Windows Server 2016 som grundläggande Operativsystemet inte stöds.
- Privilegierade behållare kan inte användas.
- Linux-specifika funktioner, till exempel användare, SELinux, AppArmor eller POSIX-funktioner är inte tillgängliga i Windows Server-behållare.
    - Filen systembegränsningar som är specifika för Linux, till exempel UUI/GUID, per användarbehörigheter inte finns tillgängliga i Windows Server-behållare.
- Azure-diskar och Azure Files är de volymtyper som stöds, åt som NTFS-volymer i Windows Server-behållare.
    - NFS-baserad lagring / volymer stöds inte.

## <a name="aks-limitations-for-windows-server-node-pools"></a>AKS begränsningar för Windows Server-nodpooler

Följande extra begränsningar gäller för Windows Server-noden pool stöd i AKS:

- Ett AKS-kluster innehåller alltid en Linux-pool för noden som den första nod-adresspoolen. Den här första Linux-baserade nodpoolen kan inte tas bort om inte själva AKS-klustret tas bort.
- AKS stöder för närvarande endast med basic load balancer, vilket gör att endast en serverdelspool, standardpoolen för Linux-noden. Därför kan utgående trafik från Windows poddar kommer alltid att [översätts till en Azure-hanterade offentliga IP-adress][azure-outbound-traffic]. Eftersom den här IP-adressen inte är konfigurerbara, går det inte för närvarande att vitlista trafik som kommer från Windows poddar. 
- AKS-kluster måste använda Azure CNI (Avancerat) Nätverksmodellen.
    - Kubenet (grundläggande) nätverk stöds inte. Du kan inte skapa ett AKS-kluster som använder kubenet. Mer information om skillnaderna i nätverket modeller finns i [Network begrepp för program i AKS][azure-network-models].
    - Azure CNI nätverk modellen kräver ytterligare planering och överväganden för hantering av IP-adress. Läs mer om hur du planerar och implementerar Azure CNI [CNI konfigurera Azure-nätverk i AKS][configure-azure-cni].
- Windows Server-noder i AKS måste vara *uppgraderas* till en senaste 2019 för Windows Server-version för att underhålla den senaste korrigeringen korrigeringar och uppdateringar. Windows-uppdateringar har inte aktiverats i basnod bild i AKS. Med jämna mellanrum kring Windows-versionen uppdateringscykeln och egna verifieringsprocessen, bör du utföra en uppgradering på Windows Server-nod lagringspoolerna AKS-klustret. Läs mer om hur du uppgraderar en Windows Server-nodpool [uppgradera en nodpool i AKS][nodepool-upgrade].
    - Dessa uppgraderingar för Windows Server-nod använder tillfälligt ytterligare IP-adresser i virtuella nätverkets undernät som en ny nod har distribuerats, innan den gamla noden tas bort.
    - vCPU-kvoter används också tillfälligt i prenumerationen som en ny nod har distribuerats, då den gamla noden tas bort.
    - Du kan inte automatiskt uppdatera och hantera omstarter med `kured` precis som med Linux-noder i AKS.
- AKS-klustret kan ha högst åtta nodpooler.
    - Du kan ha högst 400 noder i poolerna åtta noden.
- Poolnamn för Windows Server-noden har en gräns på 6 tecken.
- Förhandsversion av funktioner i AKS som nätverksprincip- och klustret autoskalningen inte är godkända för Windows Server-noder.
- Ingående domänkontrollanter ska endast schemaläggas på Linux-noder med en NodeSelector.
- Azure Dev blanksteg finns för närvarande endast för Linux-baserade nodpooler.
- Gruppen hanterade tjänstkonton (gMSA) support när Windows-filservernoder som inte är anslutna till en Active Directory-domän inte är tillgänglig i AKS.
    - Öppen källkod, uppströms [aks-engine] [ aks-engine] projekt för närvarande erbjuder stöd för gMSA om du vill använda den här funktionen.

## <a name="os-concepts-that-are-different"></a>OS-begrepp som skiljer sig

Kubernetes är historiskt Linux fokus. Många exemplen som används i den överordnade [Kubernetes.io] [ kubernetes] webbplats är avsedda att användas på Linux-noder. När du skapar distributioner som använder Windows Server-behållare, följande överväganden vid tillämpningen OS-nivå:

- **Identitet** -Linux använder användar-ID (UID) och grupp-ID (GID), som heltal typer. Namn för användare och grupper är inte kanoniska – de är ett alias i */etc/grupper* eller */etc/passwd* tillbaka till UID + GID.
    - Windows Server använder en större binära säkerhetsidentifierare (SID) som lagras i databasen Windows Security Access Manager (SAM). Den här databasen delas inte mellan värden och behållare, eller mellan behållare.
- **Filbehörigheter** -Windows Server använder en åtkomstkontrollista som baseras på SID i stället för en bitmask av behörigheter och UID + GID
- **Filsökvägar** -konventionen på Windows Server är att använda \ i stället för /.
    - I pod-specifikationer som monteringsvolymer, anger du sökvägen korrekt för Windows Server-behållare. Till exempel i stället för en monteringspunkt som pekar på */mnt/volym* i en Linux-behållare, anger en enhetsbeteckning och plats som */K/volym* att montera som den *K:* enhet.

## <a name="next-steps"></a>Nästa steg

Du kommer igång med Windows Server-behållare i AKS, [skapar en pool för noden som kör Windows Server i AKS][windows-node-cli].

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
