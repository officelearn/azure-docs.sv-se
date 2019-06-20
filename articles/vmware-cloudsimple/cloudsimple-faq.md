---
title: Vanliga frågor – VMware-lösning genom CloudSimple
description: Vanliga frågor om Azure VMware-lösning genom CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8cc6cf834c54ca25c12a6d66675e4290fd66136
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165821"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Vanliga frågor och svar om VMware-lösning genom CloudSimple

Vanliga frågor och svar om Azure VMware-lösning genom CloudSimple som hjälper dig att förstå tjänsten och hur du använder den. Frågor och svar är ordnade i följande kategorier:

* CloudSimple service
* Anslutning
* Nätverk
* Säkerhet
* Compute
* Storage
* VMware
* Integrering med Azure
 
## <a name="cloudsimple-service"></a>CloudSimple service

**Vad är Azure VMware-lösning genom CloudSimple?**

Azure VMware-lösning av CloudSimple transformerar och utökar VMware-arbetsbelastningar till privat, dedikerad moln i Azure på bara några minuter. Lösningen etableras, hanterar infrastrukturen och samordnar arbetsbelastningar mellan lokala och Azure. Eftersom dina appar körs exakt de samma lokalt och i Azure, dra nytta av elasticiteten och tjänster med molnet utan komplexitet genom att utforma dina appar. CloudSimple sänker den totala ägandekostnaden med en förbrukningsmodell i molnet som tillhandahåller på begäran etablering, betala som du-växer och optimering av kapacitet. Funktioner, fördelar och scenarier finns i [vad är Azure VMware-lösning genom CloudSimple?](cloudsimple-vmware-solutions-overview.md).

**Vad är ett privat moln CloudSimple?**

Du etablerar ett privat, dedikerad moln som består av en beräknings-, lagrings- och nätverksmiljö som distribueras på Microsoft Azure-infrastrukturen (maskin- och datacenter-utrymme) i Azure-platser. Det privata molnet är en inbyggd VMware-plattform som en tjänst. I VMware villkor innehåller varje privata moln exakt en instans av vCenter-servern. VCenter-servern hanterar flera ESXi-noder i ett eller flera vSphere-kluster, tillsammans med motsvarande virtuellt SAN-lagring. En CloudSimple-tjänst kan innehålla flera privata moln i Azure-prenumerationen. Läs mer om privata moln, [optimeras översikt](cloudsimple-private-cloud.md).

**Var finns CloudSimple tjänsten?**

CloudSimple är tillgängligt i regionerna USA, östra och USA, västra.

**Hur aktiverar jag min prenumeration på CloudSimple?**

Kontakta din Microsoft-kontorepresentant på [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) att aktivera prenumerationen för tjänsten CloudSimple. Ange ditt prenumerations-ID för e-postmeddelandet som du vill köra tjänsten CloudSimple. 

**Hur kommer jag åt CloudSimple portal?**

Du har åtkomst till CloudSimple-portalen från Azure-portalen. Information om hur du kommer åt portalen CloudSimple finns [åtkomst till VMware-lösningen av CloudSimple portalen från Azure portal](https://docs.azure.cloudsimple.com/access-cloudsimple-portal).

**Hur ökar jag kapaciteten för ett privat moln?**

Du etablera noder från Azure-portalen och utöka ditt privata moln från CloudSimple-portalen. Du kan expandera ditt privata moln genom att lägga till noder i ett befintligt vSphere-kluster eller skapa ett nytt vSphere-kluster. Information om proceduren finns i [expandera ett privat moln CloudSimple](https://docs.azure.cloudsimple.com/expand-private-cloud).

**Vad händer med mitt privata moln under Underhåll?**

CloudSimple ger periodiska aviseringar dagar innan schemalagt underhåll. Underhåll sker i en avbrottsfri sätt att kontrollera tillgängligheten för ditt privata moln. Underhåll kan vara av följande typer:

- **CloudSimple infrastruktur**: CloudSimple-infrastruktur är utformad för att ha hög tillgänglighet. Under Underhåll säkerställs anslutning och tillgängligheten för ditt privata moln genom att uppdatera redundanta komponenter en i taget utan påverkas. Du har åtkomst till ditt privata moln vCenter, alla virtuella datorer, internet-anslutning från ditt privata moln och anslutningar till lokala eller Azure.
- **CloudSimple portal**: Under underhåll, vissa funktioner i CloudSimple portal kan inte nås eller kan vara inaktiverade. Meddelande om Underhåll innehåller information om vad du kan göra på portalen.

## <a name="connectivity"></a>Anslutning

**Vilka anslutningsalternativ har jag till nätverket CloudSimple region?**

CloudSimple tillhandahåller tre olika anslutningsalternativ för att ansluta till nätverket CloudSimple region. Alla tre alternativ kan användas tillsammans:

- Azure ExpressRoute-anslutning från ditt lokala datacenter till CloudSimple region nätverk: En hög hastighet med låg latens säker privat anslutning som överbryggar lokala ExpressRoute-kretsen med CloudSimple ExpressRoute-kretsen med Global räckvidd. Om du vill konfigurera anslutningen, se [Anslut från en lokal plats till CloudSimple genom att använda ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection).
- ExpressRoute-anslutning från Azure-nätverk till nätverket CloudSimple region: En hög hastighet och låg latens säker privat anslutning som förenar det virtuella nätverket på Azure med CloudSimple ExpressRoute-kretsen med hjälp av virtuella nätverksgatewayer. Om du vill konfigurera anslutningen, se [ansluta miljön CloudSimple privat moln till Azure-nätverk med hjälp av ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection).
- Plats-till-plats VPN-anslutning från ditt lokala datacenter till nätverket CloudSimple region: En virtuella privata nätverk från din lokala VPN-enhet till din region för CloudSimple privat moln. Om du vill konfigurera anslutningen, se [konfigurera en VPN-anslutning mellan ditt lokala nätverk och optimeras för CloudSimple](https://docs.azure.cloudsimple.com/set-up-vpn).

**Hur ansluter jag till ett privat moln?**

Du kan visa information om ditt privata moln i CloudSimple-portalen. Kontrollera att nätverket finns en anslutning via plats-till-plats, punkt-till-plats eller ExpressRoute för att ansluta till vCenter som motsvarar ditt privata moln. Starta sedan CloudSimple portalen från Azure-portalen. Välj **starta vSphere-klienten** på startsidan eller sidan privat moln.

**Vad är fördelen med en ExpressRoute-krets?**

En Azure ExpressRoute-krets ger en snabb och låg latens säker anslutning. CloudSimple ger en dedikerad ExpressRoute-krets per region per kund. Med den här kretsen kan upprätta du säker anslutning från lokala platser och din Azure-prenumeration.

**Vad är nätverkskostnader att ansluta till och från CloudSimple? Finns det några kostnader för utgående trafik till och från CloudSimple till Azure? Finns det några kostnader för utgående trafik mellan regioner?**

Det finns ingen kostnad för utgående nätverkstrafik. Azure standardtaxa gäller för all utgående trafik från ditt virtuella nätverk eller från en lokal ExpressRoute-krets.

## <a name="networking"></a>Nätverk

**Vilka funktioner är tillgängliga för Mina privata molnet?**

Du kan etablera virtuella lokala nätverk och deras undernät och brandväggen tabeller. Du kan tilldela offentliga IP-adresser och mappa till en virtuell dator som körs i ditt privata moln. Mer information finns i [VLAN och undernät översikt](cloudsimple-vlans-subnets.md), [översikt över brandväggen för tabeller](cloudsimple-firewall-tables.md), och [översikt över offentliga IP-adress](cloudsimple-public-ip-address.md).

**Hur ställer jag in olika undernät för mitt program i min privat moln?**

Du kan skapa virtuella lokala nätverk i ditt privata moln från CloudSimple-portalen. När du har skapat VLAN du skapar en distribuerad portgrupp på privat moln-vCenter med hjälp av VLAN och skapa virtuella datorer som är anslutna till gruppen distribuerade port. Du kan aktivera en tabell med brandväggen för virtuellt lokalt nätverk eller undernät och definiera brandväggsregler för att säkra nätverkstrafik.

**Vilka brandväggsinställningar är tillgängliga för Mina privata moln?**

Du kan konfigurera regler för Nord-sydlig och öst-väst-trafik. Reglerna har definierats i en tabell i brandväggen. Tabellen brandväggen kan kopplas till VLAN-nätverk i ditt privata moln. Under installationen, se [ställa in brandväggen tabeller och regler för privata moln](https://docs.azure.cloudsimple.com/firewall).

**Kan jag tilldela offentliga IP-adresser för virtuella datorer i mitt privata molnmiljö?**

Du kan enkelt allokera en ny offentlig IP-adress och koppla den till en privat IP-adressen för den virtuella datorn eller en installation i CloudSimple-portalen. Du kan också skapa nya brandväggsregler eller Använd befintliga brandväggsregler för att tillåta trafik från specifika portar och specifika uppsättningar med IP-adresser i portalen. Under installationen, se [tilldela offentliga IP-adresser för en privat molnmiljö](https://docs.azure.cloudsimple.com/public-ips).

## <a name="security"></a>Säkerhet

**Vilka är Mina säkerhetsalternativ på CloudSimple?**

Ett privat moln CloudSimple innehåller följande säkerhetsfunktioner för att skydda din miljö för privat moln:

- **Data kryptering av vilande data:** Du kan kryptera data i vila som finns på virtuellt SAN-lagring i ditt privata moln. virtuellt SAN-nätverk har stöd för en extern nyckelhantering-server som kan distribueras i Azure virtuella nätverk eller den lokala miljön. Mer information finns i [konfigurera virtuellt SAN-kryptering för ditt privata moln CloudSimple](https://docs.azure.cloudsimple.com/vsan-encryption).
- **Nätverkssäkerhet:** Styra flödet i nätverkstrafiken från och till ditt privata moln från internet, lokalt och i undernäten i ditt privata moln med hjälp av brandväggsregler.
- **Säker och privat anslutning:** Säker och privat anslutning mellan ditt lokala nätverk och din Azure-prenumeration.

## <a name="compute"></a>Compute

**Vilken typ av värdar är tillgängliga?**

CloudSimple erbjuder två värdtyper:

* **CS28 noden**: CPU:2 x 2,2 GHz, totalt antal 28 kärnor 48 HT. RAM: 256 GB. Lagring: 1 600 GB NVMe Cache, 5760 GB Data (All-Flash). Nätverk: 2x25Gbe nätverkskort.
* **CS36 noden**: CPU 2 x 2.3 GHz, totalt antal 36 kärnor 72 HT. RAM: 512 GB. Lagring: 3200 GB NVMe Cache 11,520 GB Data (All-Flash). Nätverk: 2x25Gbe nätverkskort.

**Hur hanteras maskinvarufel?**

Alla CloudSimple infrastruktur övervakas kontinuerligt av CloudSimple-plattformen och dess åtgärder serviceteam. Om ett maskinvarufel upptäcks, läggs en ny nod till ditt privata moln. Misslyckade noden tas bort om du vill garantera hög tillgänglighet för ditt privata moln.

## <a name="storage"></a>Storage

**Vilken typ av lagring stöds på ett privat moln?**

CloudSimple erbjuder **All-flash VMware virtuellt SAN-lagring** med varje privata moln. Varje vSphere skapas med sin egen virtuellt SAN-datalager. Mer information finns i [komponenter för privata moln VMware - virtuellt SAN-lagring](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage).

**Är kryptering av data som stöds?**
Ja. Du kan konfigurera virtuellt SAN-lagring i ditt privata moln du använder en key management server (KMS), som är distribueras lokalt eller på Azure för kryptering av data som lagras på virtuellt SAN-nätverk.

**Hur hanteras felaktiga diskar?**

CloudSimple övervakning övervakar kontinuerligt alla maskinvarukomponenter i det privata molnet. Om diskfel upptäcks eller en disk har identifierats som misslyckas utifrån heuristik, läggs automatiskt en ny nod i det privata molnet. Noden med en disk som inte fungerar eller tas bort från det privata molnet.

## <a name="vmware"></a>VMware

**Hur utför jag storskaliga överföring och migrering av program och data från en lokal plats?**

CloudSimple är en inbyggd VMware vSphere-lösning. Alla verktyg som används för migrering av bulk data kan användas med ett privat moln CloudSimple. Några av de tillgängliga alternativen är:

- VMware HCX för bulk-migrering av data.
- Kalla migrering av data med hjälp av Storage vMotion från en lokal plats till CloudSimple.

**Kan jag installera någon VMware-verktyg?**

CloudSimple är en inbyggd VMware vSphere-lösning. Alla verktyg som används för att hantera en vSphere-miljö på plats kan användas på CloudSimple. CloudSimple har stöd för en modell för bring-your-own-license (BYOL) för att installera VMware-verktyg.

**Hur hanteras uppdateringar och uppgraderingar?**

CloudSimple hanterar och uppdaterar alla infrastrukturkomponenter för ditt privata moln i en sömlös oavbruten sätt. Alla uppdateringen eller säkerhetskorrigeringen som publicerats av VMware eller infrastruktur leverantörer har schemalagts för uppdatering så snart den är kvalificerad av CloudSimple.

CloudSimple utföra inte uppgraderingar och uppdateringar av program installerade på det privata molnet.

## <a name="azure-integration"></a>Integrering med Azure

**Vilka Azure-tjänster stöds?**

CloudSimple ger Azure ExpressRoute-anslutning till din prenumeration på Azure. Alla tjänster som körs i din prenumeration har nätverksanslutning till ditt privata moln och kan ansluta till ditt privata moln. Exempel:

- **Azure Active Directory**: Använd Azure Active Directory som en som identitetskälla för din CloudSimple vCenter.
- **Azure Storage**: Använd lagring för att lagra säkerhetskopior, bilder och andra data från ditt privata moln.
- **Hybridprogram**: Du kan skapa programarkitektur som sträcker sig över offentliga och privata moln. Exempel: du kan skapa webbservrar i Azure som åtkomst till program och databasservrar på ett privat moln CloudSimple.
- **Azure Monitor** och **Azure Security Center**: Arbetsbelastning som körs på VMware kan du använda Övervakare och Security Center för loggning, prestandamått och säkerhetshantering.

**Hur mappar jag mina klienter för VMware till Azure?**

CloudSimple ger unika möjlighet att hantera dina virtuella VMware-datorer i ett privat moln från Azure-portalen. En vCenter-resurspool konfigurerad med resursbegränsningar som du vill kan mappas till din prenumeration av den globala administratören. 

**Vilka licensiering fördelar får jag med Azure?**

Med CloudSimple, kan du dra nytta av Azure Hybrid-förmånen och spara upp till 90 procent på licenser för att bevara investeringen i Microsoft-licenser och sänka den totala ägandekostnaden jämfört med andra moln. Du får även utökat säkerhetsuppdateringar för Windows Server 2008 och Microsoft SQL Server 2008. Håll nere kostnaderna med BYOL till molnet för vanliga appar som Veeam, Zerto och andra. 
