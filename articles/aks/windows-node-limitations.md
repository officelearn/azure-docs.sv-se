---
title: Begränsningar för nodpooler i Windows Server
titleSuffix: Azure Kubernetes Service
description: Lär dig mer om de kända begränsningarna när du kör Windows Server-nodpooler och programarbetsbelastningar i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/18/2019
ms.openlocfilehash: 934acf06a779c1c3b0b13e74b196b174dd944e66
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886678"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Aktuella begränsningar för Windows Server-nodpooler och programarbetsbelastningar i Azure Kubernetes Service (AKS)

I Azure Kubernetes Service (AKS) kan du skapa en nodpool som kör Windows Server som gästoperativsystem på noderna. Dessa noder kan köra inbyggda Windows-behållarprogram, till exempel de som bygger på .NET Framework. Eftersom det finns stora skillnader i hur Linux och Windows OS tillhandahåller behållarstöd är vissa vanliga Kubernetes och pod-relaterade funktioner för närvarande inte tillgängliga för Windows-nodpooler.

I den här artikeln beskrivs några av begränsningarna och OS-begreppen för Windows Server-noder i AKS. Nodpooler för Windows Server är för närvarande i förhandsversion.

> [!IMPORTANT]
> AKS-förhandsgranskningsfunktioner är självbetjäningsanmälan. Förhandsvisningar tillhandahålls "i nu och "som tillgängligt" och är undantagna från servicenivåavtalen och den begränsade garantin. AKS-förhandsvisningar omfattas delvis av kundsupport efter bästa ansträngning. Därför är dessa funktioner inte avsedda för produktionsanvändning. Mer information finns i följande supportartiklar:
>
> * [Aks-supportpolicyer][aks-support-policies]
> * [Vanliga frågor och svar om Azure-support][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>Vilka Windows-operativsystem stöds?

AKS använder Windows Server 2019 som värdoperosversion och stöder endast processisolering. Behållaravbildningar som skapats med andra Windows Server-versioner stöds inte. [Kompatibilitet med Windows-behållarversion][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Är Kubernetes olika på Windows och Linux?

Stöd för Windows Server-nodpoolen innehåller vissa begränsningar som ingår i projektet Windows Server i Kubernetes. Dessa begränsningar är inte specifika för AKS. Mer information om det här stöd för uppströms för Windows Server i Kubernetes finns i avsnittet [Funktioner och begränsningar som stöds][upstream-limitations] i [introt till Windows-dokumentet i Kubernetes][intro-windows] från Kubernetes-projektet.

Kubernetes är historiskt Linux-fokuserad. Många exempel som används i uppströms [Kubernetes.io][kubernetes] webbplats är avsedda att användas på Linux-noder. När du skapar distributioner som använder Windows Server-behållare gäller följande överväganden på OS-nivå:

- **Identitet** - Linux identifierar en användare med en heltalsanvändaridentifierare (UID). En användare har också ett alfanumeriskt användarnamn för att logga in, vilket Linux översätter till användarens UID. På samma sätt identifierar Linux en användargrupp med en GID -gruppidentifierare (Inteal Group Och översätter ett gruppnamn till motsvarande GID.Similarly Linux identifies a user group by an heltal group identifier (GID) and translates a group name to its corresponding GID.
    - Windows Server använder en större SID-identifierare (Binary Security Identifier) som lagras i SAM-databasen (Windows Security Access Manager). Den här databasen delas inte mellan värden och behållarna eller mellan behållare.
- **Filbehörigheter** - Windows Server använder en åtkomstkontrolllista baserad på SID-kort, i stället för en bitmask med behörigheter och UID+GID
- **Filsökvägar** - konventionen på Windows Server är att använda \ i stället för /.
    - I pod-specifikationer som monterar volymer anger du sökvägen korrekt för Windows Server-behållare. I stället för en monteringspunkt på */mnt/volym* i en Linux-behållare anger du till exempel en enhetsbeteckning och plats som */K/Volume* som ska monteras som *K:-enhet.*

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Vilken typ av diskar stöds för Windows?

Azure Disks och Azure Files är de volymtyper som stöds och används som NTFS-volymer i Windows Server-behållaren.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Kan jag bara köra Windows-kluster i AKS?

Huvudnoderna (kontrollplanet) i ett AKS-kluster är värd för AKS tjänsten, du kommer inte att exponeras för operativsystemet för noderna som är värdar för huvudkomponenterna. Alla AKS-kluster skapas med en standardpool för första nod, som är Linux-baserad. Den här nodpoolen innehåller systemtjänster som behövs för att klustret ska fungera. Vi rekommenderar att du kör minst två noder i den första nodpoolen för att säkerställa tillförlitligheten i klustret och möjligheten att utföra klusteråtgärder. Den första Linux-baserade nodpoolen kan inte tas bort om inte AKS-klustret tas bort.

## <a name="what-network-plug-ins-are-supported"></a>Vilka nätverksplug-program stöds?

AKS-kluster med Windows-nodpooler måste använda Azure CNI-nätverksmodellen (avancerat). Kubenet -nätverk (grundläggande) stöds inte. Mer information om skillnaderna i nätverksmodeller finns i [Nätverkskoncept för program i AKS][azure-network-models]. - Azure CNI-nätverksmodellen kräver ytterligare planering och överväganden för IP-adresshantering. Mer information om hur du planerar och implementerar Azure CNI finns [i Konfigurera Azure CNI-nätverk i AKS][configure-azure-cni].

## <a name="can-i-change-the-max--of-pods-per-node"></a>Kan jag ändra max. # av poddar per nod?

Ja. För de konsekvenser och alternativ som är tillgängliga, se [Maximalt antal poddar][maximum-number-of-pods].

## <a name="how-do-patch-my-windows-nodes"></a>Hur patchar mina Windows-noder?

Windows Server-noder i AKS måste *uppgraderas* för att de senaste korrigeringarna och uppdateringarna ska kunna åtgärdas. Windows-uppdateringar är inte aktiverade på noder i AKS. AKS släpper nya nodpoolavbildningar så snart korrigeringar är tillgängliga, är det kundernas ansvar att uppgradera nodpooler för att hålla sig aktuella på patchar och snabbkorrigering. Detta gäller även för kubernetes-versionen som används. AKS-viktig information anger när nya versioner är tillgängliga. Mer information om hur du uppgraderar en Windows Server-nodpool finns [i Uppgradera en nodpool i AKS][nodepool-upgrade].

> [!NOTE]
> Den uppdaterade Windows Server-avbildningen används endast om en klusteruppgradering (kontrollplansuppgradering) har utförts innan nodpoolen uppgraderas
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Hur roterar jag tjänstens huvudnamn för min Windows-nodpool?

Under förhandsversionen stöder Windows-nodpooler inte tjänstens huvudrotation som en förhandsgranskningsbegränsning. Skapa en ny Windows-nodpool för att uppdatera tjänstens huvudnamn och migrera poddar från den äldre poolen till den nya. När detta är klart tar du bort den äldre nodpoolen.

## <a name="how-many-node-pools-can-i-create"></a>Hur många nodpooler kan jag skapa?

AKS-klustret kan ha högst 10 nodpooler. Du kan ha högst 1 000 noder över nodpoolerna. [Begränsningar för nodpool .][nodepool-limitations]

## <a name="what-can-i-name-my-windows-node-pools"></a>Vad kan jag namnge mina Windows-nodpooler?

Du måste hålla namnet till högst 6 (sex) tecken. Detta är en aktuell begränsning av AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Stöds alla funktioner med Windows-noder?

Nätverksprinciper och kubenet stöds för närvarande inte med Windows-noder. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Kan jag köra inträngningskontroller på Windows-noder?

Ja, en ingressstyrenhet som stöder Windows Server-behållare kan köras på Windows-noder i AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Kan jag använda Azure Dev Spaces med Windows-noder?

Azure Dev Spaces är för närvarande endast tillgängligt för Linux-baserade nodpooler.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Kan mina Windows Server-behållare använda gMSA?

Stöd för grupphanterade tjänstkonton (gMSA) är inte tillgängligt i AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Kan jag använda Azure Monitor för behållare med Windows-noder och behållare?

Ja du kan, men Azure Monitor samlar inte loggar (stdout) från Windows-behållare. Du kan fortfarande ansluta till livestreamen med stdout-loggar från en Windows-behållare.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>Vad händer om jag behöver en funktion som inte stöds?

Vi arbetar hårt för att få alla funktioner du behöver till Windows i AKS, men om du stöter på luckor, ger open-source, upstream [aks-motor][aks-engine] projektet ett enkelt och helt anpassningsbart sätt att köra Kubernetes i Azure, inklusive Windows-stöd. Se till att kolla in vår färdplan för funktioner som kommer [AKS färdplan][aks-roadmap].

## <a name="next-steps"></a>Nästa steg

Om du vill komma igång med Windows Server-behållare i AKS [skapar du en nodpool som kör Windows Server i AKS][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
