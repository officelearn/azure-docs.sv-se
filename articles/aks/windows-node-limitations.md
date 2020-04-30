---
title: Begränsningar för Windows Server-adresspooler
titleSuffix: Azure Kubernetes Service
description: Läs om kända begränsningar när du kör Windows Server-nodkonfigurationer och program arbets belastningar i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 12/18/2019
ms.openlocfilehash: 935b049ce5e1951952b4af4e7df9574df764b6e8
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82208014"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Aktuella begränsningar för Windows Server-nodkonfigurationer och program arbets belastningar i Azure Kubernetes service (AKS)

I Azure Kubernetes service (AKS) kan du skapa en Node-pool som kör Windows Server som gäst operativ system på noderna. Dessa noder kan köra interna Windows-programbehållare, till exempel de som skapats på .NET Framework. Eftersom det finns stora skillnader i hur Linux och Windows OS tillhandahåller stöd för behållare, är vissa vanliga Kubernetes-och Pod-relaterade funktioner inte tillgängliga för Windows-nodkonfigurationer.

Den här artikeln beskriver några begränsningar och OS-koncept för Windows Server-noder i AKS.

## <a name="which-windows-operating-systems-are-supported"></a>Vilka Windows-operativsystem stöds?

AKS använder Windows Server 2019 som värd-OS-version och stöder bara process isolering. Behållar avbildningar som skapats med andra Windows Server-versioner stöds inte. [Kompatibilitet för Windows container version][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Är Kubernetes annorlunda för Windows och Linux?

Window Server Node pool support innehåller vissa begränsningar som ingår i den överordnade Windows Server i Kubernetes-projektet. Dessa begränsningar är inte begränsade till AKS. Mer information om det här överordnade stödet för Windows Server i Kubernetes finns i avsnittet [funktioner som stöds och begränsningar][upstream-limitations] i [introduktionen till Windows-stöd i Kubernetes][intro-windows] -dokument, från Kubernetes-projektet.

Kubernetes är historiskt Linux-fokuserad. Många exempel som används i den överordnade [Kubernetes.io][kubernetes] -webbplatsen är avsedda att användas på Linux-noder. När du skapar distributioner som använder Windows Server-behållare gäller följande vid OS-nivå:

- **Identitet** – Linux identifierar en användare med en heltals användar IDENTIFIERARE (UID). En användare har också ett alfanumeriskt användar namn för inloggning, vilket innebär att Linux översätts till användarens UID. Linux identifierar på samma sätt en användar grupp med en heltals grupp identifierare (GID) och översätter ett grupp namn till motsvarande GID.
    - Windows Server använder en större säkerhets identifierare för binärfiler (SID) som lagras i Windows Security Access Manager-databasen (SAM). Den här databasen delas inte mellan värden och behållare, eller mellan behållare.
- **Fil behörigheter** – Windows Server använder en åtkomst kontrol lista baserat på sid, i stället för en bitmask med behörigheter och UID + GID
- **Fil Sök vägar** – konvention på Windows Server är att använda \ i stället för/.
    - I pod-specifikationer som monterar volymer anger du sökvägen korrekt för Windows Server-behållare. I stället för en monterings punkt för */mnt/Volume* i en Linux-behållare anger du till exempel en enhets beteckning och en plats, till exempel */K/Volume* , som ska monteras som *K:* enhet.

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Vilken typ av diskar stöds för Windows?

Azure-diskar och Azure Files är de volym typer som stöds, som används som NTFS-volymer i Windows Server-behållaren.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Kan jag endast köra Windows-kluster i AKS?

Huvudnoderna (kontroll planet) i ett AKS-kluster är värd för AKS-tjänsten, men du kommer inte att exponeras för operativ systemet för de noder som är värdar för huvud komponenterna. Alla AKS-kluster skapas med en standardinställd första Node-pool, som är Linux-baserad. Den här noden innehåller system tjänster som behövs för att klustret ska fungera. Vi rekommenderar att du kör minst två noder i den första noden för att säkerställa hur tillförlitlig klustret är och möjligheten att utföra kluster åtgärder. Den första Linux-baserade Node-poolen kan inte tas bort om inte själva AKS-klustret tas bort.

## <a name="what-network-plug-ins-are-supported"></a>Vilka nätverks-plugin-program stöds?

AKS-kluster med Windows-noder måste använda nätverks modellen Azure CNI (avancerat). Kubernetes-nätverk (Basic) stöds inte. Mer information om skillnaderna i nätverks modeller finns i [nätverks koncept för program i AKS][azure-network-models]. – Nätverks modellen för Azure CNI kräver ytterligare planering och överväganden för hantering av IP-adresser. Mer information om hur du planerar och implementerar Azure-CNI finns i [Konfigurera Azure cni Networking i AKS][configure-azure-cni].

## <a name="can-i-change-the-max--of-pods-per-node"></a>Kan jag ändra max. antal poddar per nod?

Ja. Information om vilka effekter och alternativ som är tillgängliga finns i [Max antalet poddar][maximum-number-of-pods].

## <a name="how-do-patch-my-windows-nodes"></a>Hur gör jag för att korrigera mina Windows-noder?

Windows Server-noder i AKS måste *uppgraderas* för att få de senaste korrigeringarna och uppdateringarna. Windows-uppdateringar är inte aktiverade på noder i AKS. AKS släpper nya avbildningar för resurspooler så snart korrigeringarna är tillgängliga, är det kunden som ansvarar för att uppgradera nodkonfigurationer för att hålla dig uppdaterad om korrigeringar och snabb korrigeringar. Detta gäller även för den Kubernetes-version som används. AKS viktig information visar när nya versioner är tillgängliga. Mer information om hur du uppgraderar en pool för Windows Server-noder finns [i uppgradera en Node-pool i AKS][nodepool-upgrade].

> [!NOTE]
> Den uppdaterade Windows Server-avbildningen kommer bara att användas om en kluster uppgradering (kontroll Plans uppgradering) har utförts innan du uppgraderar Node-poolen
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Hur gör jag för att rotera tjänstens huvud namn för min Windows Node-pool?

Windows Node-pooler stöder inte tjänstens huvuds rotation. För att kunna uppdatera tjänstens huvud namn skapar du en ny Windows Node-pool och migrerar din poddar från den äldre poolen till den nya. När detta har slutförts tar du bort den äldre Node-poolen.

## <a name="how-many-node-pools-can-i-create"></a>Hur många noder I pooler kan jag skapa?

AKS-klustret kan ha högst 10 noder i pooler. Du kan ha högst 1000 noder i noderna. [Begränsningar för Node-pool][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Vad kan jag ge till mina Windows-nodkonfigurationer?

Du måste ha ett namn på högst 6 tecken (sex). Detta är en aktuell begränsning på AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Stöds alla funktioner med Windows-noder?

Nätverks principer och Kubernetes stöds för närvarande inte med Windows-noder. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Kan jag köra ingångs styrenheter på Windows-noder?

Ja, en ingress-kontrollant som stöder Windows Server-behållare kan köras på Windows-noder i AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Kan jag använda Azure dev Spaces med Windows-noder?

Azure dev Spaces är för närvarande bara tillgängligt för Linux-baserade nodkonfigurationer.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Kan mina Windows Server-behållare använda gMSA?

GMSA-stöd (Group Managed Service accounts) är för närvarande inte tillgängligt i AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Kan jag använda Azure Monitor för behållare med Windows-noder och behållare?

Ja, men du kan Azure Monitor inte samla in loggar (STDOUT) från Windows-behållare. Du kan fortfarande ansluta till den aktiva strömmen av STDOUT-loggar från en Windows-behållare.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>Vad händer om jag behöver en funktion som inte stöds?

Vi arbetar hårt för att ta med alla funktioner som du behöver i Windows i AKS, men om du stöter på luckor ger det ett [AKS-motor][aks-engine] projekt med öppen källkod ett enkelt och helt anpassningsbart sätt att köra Kubernetes i Azure, inklusive Windows-support. Se till att kolla vår översikt över funktionerna som kommer [AKS-översikt][aks-roadmap].

## <a name="next-steps"></a>Nästa steg

Kom igång med Windows Server-behållare i AKS genom att [skapa en noduppsättning som kör Windows Server i AKS][windows-node-cli].

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
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
