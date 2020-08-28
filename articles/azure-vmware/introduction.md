---
title: Introduktion
description: Lär dig om funktionerna och fördelarna med Azure VMware-lösningen för att distribuera och hantera VMware-baserade arbets belastningar i Azure.
ms.topic: overview
ms.date: 05/04/2020
ms.openlocfilehash: 3d6801a6cdec7600cc6003711a08e6af8c86483e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89005062"
---
# <a name="what-is-azure-vmware-solution-preview"></a>Vad är för hands versionen av Azure VMware-lösningen?

Med Azure VMware-lösningen får du privata moln i Azure. De privata molnen innehåller vSphere-kluster som bygger på dedikerad Azure-infrastruktur. Du kan skala ut privata molnkluster från 3 till 16 värdar och ha flera kluster i samma privata moln. Alla privata moln är etablerade med vCenter Server, virtuellt San, vSphere och NSX-T. Du kan migrera arbetsbelastningar från din lokala miljö, skapa och distribuera nya virtuella datorer och använda Azure-tjänster från dina privata moln.

Azure VMware-lösningen är en VMware-verifierad lösning med pågående verifiering och testning av förbättringar och uppgraderingar. Den privata moln infrastrukturen och program varan hanteras och underhålls av Microsoft, så att du kan fokusera på att utveckla och köra arbets belastningar i dina privata moln.

Följande diagram visar sammanlänkning mellan privata moln och virtuella nätverk i Azure, Azure-tjänster och lokala miljöer. Nätverks åtkomst från privata moln till Azure-tjänster eller virtuella nätverk tillhandahåller SLA-driven integrering av Azures tjänst slut punkter. Åtkomst till privata moln från lokala miljöer använder ExpressRoute Global Reach för en privat och säker anslutning.

![Bild av Azure VMware-lösning för privat moln sammanlänkning till Azure och lokalt](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Värdar, kluster och privata moln

Privata moln och kluster i Azure VMware-lösningen bygger på en dator utan operativ system som är värd för Azure-infrastrukturen utan operativ system. De avancerade värdarna har 576 GB RAM och dubbla Intel 18 kärnor, 2,3 GHz-processorer. Han är värd för att ha två virtuellt San-diskgroups med en sammanlagd kapacitet på 15,36 TB (SSD) RAW virtuellt San och en virtuellt San på 3,2 TB (NVMe).

Nya privata moln distribueras via Azure Portal eller Azure CLI.

## <a name="networking"></a>Nätverk

När ett privat moln distribueras skapas privata nätverk för hantering, etablering och vMotion. Dessa privata nätverk används för åtkomst till vCenter-och NSX-hanteraren och för vMotion eller distribution av virtuella datorer. Alla privata nätverk är tillgängliga från ett VNet i Azure eller från lokala miljöer. ExpressRoute Global Reach används för att ansluta privata moln till lokala miljöer och den här anslutningen kräver ett VNet med en ExpressRoute-krets i din prenumeration.

Åtkomst till Internet-och Azure-tjänster tillhandahålls när ett privat moln distribueras. Åtkomsten tillhandahålls så att virtuella datorer i produktions arbets belastnings nätverk kan använda Azure eller Internetbaserade tjänster. Internet åtkomst är inaktive rad som standard för nya privata moln och kan aktive ras eller inaktive ras när som helst.

Mer information om nätverk och anslutningar finns i artikeln om [nätverks begrepp](concepts-networking.md) .

## <a name="access-and-security"></a>Åtkomst och säkerhet

För ökad säkerhet använder Azure VMware-lösningar privata moln vSphere rollbaserad åtkomst kontroll. vSphere SSO LDAP-funktioner kan integreras med Azure Active Directory. Mer information om identitet och behörigheter finns i artikeln [åtkomst och identitets begrepp](concepts-identity.md) .

Virtuellt San data-at-rest-kryptering är aktiverat som standard och används för att tillhandahålla virtuellt San data lagrings säkerhet. Den beskrivs mer detaljerat i artikeln [Storage Concepts](concepts-storage.md) .

## <a name="host-and-software-lifecycle-maintenance"></a>Underhåll av värd-och program varu livs cykel

Vanliga uppgraderingar av Azure VMware-lösningen privat moln och VMware-programvara garanterar att de senaste säkerhets-, stabilitets-och funktions uppsättningarna körs i dina privata moln. Mer information om plattforms underhåll och uppgraderingar finns i artikeln [Uppgradera begrepp](concepts-upgrades.md) .

## <a name="monitoring-your-private-cloud"></a>Övervaka ditt privata moln

Du kan använda [loggar i Azure Monitor](../azure-monitor/overview.md) för att samla in loggar på dina virtuella datorer som körs i ditt privata moln i Azure VMware-lösningen. Du kan [Hämta och installera MMA-agenten](../azure-monitor/platform/log-analytics-agent.md#installation-options) på virtuella Linux-och Windows-datorer som körs i dina privata moln i Azure VMware-lösningen med samma frågor som du kör på dina lokala virtuella datorer. Du kan köra samma frågor som du brukar köra på dina virtuella datorer på samma sätt. Mer information om hur du skapar frågor finns i [så här skriver du frågor](../azure-monitor/log-query/log-query-overview.md#how-can-i-learn-how-to-write-queries).

## <a name="next-steps"></a>Nästa steg

Nästa steg är att lära dig viktiga [koncept för privata moln och kluster](concepts-private-clouds-clusters.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
