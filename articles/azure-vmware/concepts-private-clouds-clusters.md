---
title: Koncept – privata moln och kluster
description: Lär dig mer om de viktigaste funktionerna i Azure VMware-programdefinierade Data Center och vSphere-kluster i VMware-lösningar på Azure av VMware.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 09e1fd45b1dd873509f942ef8b524783acfed4ce
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84906997"
---
# <a name="azure-vmware-solution-avs-preview-private-cloud-and-cluster-concepts"></a>Azure VMware-lösning (AVS) för hands version av privata moln och kluster koncept

Azure VMware-lösningen (AVS) levererar VMware-baserade privata moln i Azure. Privata moln skapas från kluster av dedikerade Bare Metal-värdar och distribueras och hanteras via Azure Portal. Kluster i privata moln är etablerade med VMware vSphere-, vCenter-, virtuellt San-och NSX-programvara. Molnets privata moln maskin-och program varu distributioner är helt integrerade och automatiserade i Azure.

Det finns en logisk relation mellan Azure-prenumerationer, molnets privata moln, virtuellt San-kluster och-värdar. I diagrammet visas två privata moln i en enda Azure-prenumeration. Privata moln är en utveckling och en produktions miljö, var och en med ett eget privat moln. I vart och ett av dessa privata moln finns det två kluster. För att visa de lägre potentiella behoven i en utvecklings miljö används mindre kluster med lägre kapacitets värdar. Alla dessa begrepp beskrivs i avsnitten nedan.

![Bild av två privata moln i en kund prenumeration](./media/hosts-clusters-private-clouds-final.png)

## <a name="private-clouds"></a>Privata moln

Privata moln innehåller virtuellt San-kluster som är byggda med dedikerade, Bare-Metal Azure-värdar. Varje privat moln kan ha flera kluster, som hanteras av samma vCenter-Server och NSX-T-hanterare. Du kan distribuera och hantera privata moln i portalen, från CLI eller med PowerShell. Precis som med andra resurser installeras och hanteras privata moln i en Azure-prenumeration.

Antalet privata moln i en prenumeration är skalbart. Inlednings vis finns det en gräns för ett privat moln per prenumeration.

## <a name="clusters"></a>Kluster

Du ska skapa minst ett virtuellt San-kluster i varje privat moln. När du skapar ett privat moln finns det ett kluster som standard. Du kan lägga till ytterligare kluster i ett privat moln med hjälp av Azure Portal eller via API: et. Alla kluster har en standard storlek på tre värdar och kan skalas från 3 till 16 värdar. Den typ av värdar som används i ett kluster måste vara av samma typ. Värd typerna beskrivs i nästa avsnitt.

Utvärderings kluster är tillgängliga för utvärdering och de är begränsade till tre värdar och ett enda utvärderings kluster per privat moln. Du kan skala ett utvärderings kluster med en enda värd under utvärderings perioden.

Du skapar, tar bort och skalar kluster via portalen eller API: et. Du använder fortfarande vSphere-och NSX-T-hanteraren för att hantera de flesta andra aspekter av kluster konfigurationen eller åtgärden. All lokal lagring för varje värd i ett kluster styrs av virtuellt San.

## <a name="hosts"></a>Värdar

Hyper-konvergerade, Bare-Metal Infrastructure-noder används i molnets privata moln kluster. Värdens RAM-, processor-och disk kapacitet anges i tabellen nedan. 

| Värdtyp              |             Processor             |   RAM (GB)   |  Virtuellt San NVMe cache-nivå (TB, RAW)  |  kapacitets nivå för virtuellt San SSD (TB, RAW)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| Avancerad (HE)          |  dubbla Intel 18 kärnor 2,3 GHz  |     576      |                3.2               |                15,20               |

Värdar som används för att bygga eller skala kluster förvärvas från en isolerad pool av värdar. De här värdarna har passerat maskin varu test och har haft alla data säkert borttagna från Flash-diskarna. När du tar bort en värd från ett kluster rensas de interna diskarna på ett säkert sätt och värdarna placeras i den isolerade poolen med värdar. När du lägger till en värd i ett kluster används en sanerad värd från den isolerade poolen.

## <a name="vmware-software-versions"></a>Versioner av VMware-programvara

De aktuella program varu versionerna av VMware-programvaran som används i AVS-kluster för privata moln är:

| Programvara              |    Version   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U2    | 
| ESXi                  |    6,7 U2    | 
| Virtuellt San                  |    6,7 U2    |
| NSX-T                 |      2.5     |

För alla nya kluster i ett privat moln kommer program versionen att matcha vad som för närvarande körs i det privata molnet. Den senaste versionen av program varu stacken installeras för alla nya privata moln i en kund prenumeration.

De allmänna uppgraderings principerna och processerna för AVS-plattformens program vara beskrivs i avsnittet Uppgradera koncept.

## <a name="host-maintenance-and-lifecycle-management"></a>Underhåll av värd och livs cykel hantering

Värd underhåll och livs cykel hantering görs utan påverkan på kapaciteten eller prestandan för privata moln kluster. Exempel på automatiserat värd underhåll är uppgraderingar av inbyggd program vara och reparation eller ersättning av maskin vara.

Microsoft ansvarar för livs cykel hantering av NSX-T-apparater som NSX-T Manager och NSX-T-Edge. Microsoft ansvarar också för start av nätverks konfiguration, till exempel att skapa nivå 0-gateway och aktivera Nord-syd-routning. Som administratör för ditt moln privata moln är du ansvarig för NSX-T SDN-konfiguration som nätverks segment, distribuerade brand Väggs regler, nivå 1-gatewayer och belastningsutjämnare.

> [!IMPORTANT]
> En AVS-administratör får inte ändra konfigurationen för NSX-T Edge eller nivå 0-Gateway. Detta kan leda till förlust av tjänst.

## <a name="backup-and-restoration"></a>Säkerhets kopiering och återställning

Konfigurationer för privat moln vCenter och NSX-T-konfigurationer säkerhets kopie ras varje timme. Säkerhets kopior sparas i tre dagar. Återställning från en säkerhets kopia begärs genom en tjänstbegäran i Azure Portal.

## <a name="next-steps"></a>Nästa steg

Nästa steg är att lära dig om [nätverks-och samanslutnings koncept](concepts-networking.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

