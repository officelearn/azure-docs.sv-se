---
title: FOS – VMware-lösning efter CloudSimple
description: Vanliga frågor och svar om Azure VMware-lösningen av CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 80380a1c33927029e000e59a5834f297340f5be3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816238"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Vanliga frågor om VMware-lösning av CloudSimple

Vanliga frågor och svar om Azure VMware-lösning av CloudSimple som hjälper dig att förstå tjänsten och hur du använder den. Frågorna och svaren är ordnade i följande kategorier:

* CloudSimple-tjänst
* Anslutningar
* Nätverk
* Säkerhet
* Compute
* Storage
* VMware
* Azure-integrering
 
## <a name="cloudsimple-service"></a>CloudSimple-tjänst

**Vad är Azure VMware-lösning av CloudSimple?**

Azure VMware-lösningen genom CloudSimple transformerar och utökar VMware-arbetsbelastningar till privata, dedikerade moln på Azure på några minuter. Lösnings reglerna, hanterar infrastrukturen och dirigerar arbets belastningar mellan lokalt och Azure. Eftersom dina appar körs exakt på samma plats och i Azure drar du nytta av elastiskheten och tjänsterna i molnet utan att du behöver bygga om dina appar. CloudSimple sänker den totala ägande kostnaden med en moln förbruknings modell som tillhandahåller etablering på begäran, betala per användning och kapacitets optimering. För funktioner, förmåner och scenarier, se [Vad är Azure VMware-lösning av CloudSimple?](cloudsimple-vmware-solutions-overview.md).

**Vad är ett privat CloudSimple-moln?**

Du etablerar ett privat, dedikerat moln som består av en beräknings-, lagrings-och nätverks miljö med höga prestanda som distribueras på Microsoft Azure infrastruktur (maskin vara och data Center utrymme) på Azure-platser. Det privata molnet tillhandahåller en inbyggd VMware-plattform som en tjänst. I VMware-termer innehåller varje privat moln exakt en instans av vCenter Server. VCenter Server hanterar flera ESXi-noder som finns i ett eller flera vSphere-kluster, tillsammans med motsvarande virtuellt SAN-lagring. En CloudSimple-tjänst kan innehålla flera privata moln i din Azure-prenumeration. Mer information om privata moln finns i [Översikt över privata moln](cloudsimple-private-cloud.md).

**Var finns tjänsten CloudSimple?**

CloudSimple finns i regionerna USA, östra och USA, västra.

**Hur gör jag för att aktivera min prenumeration för CloudSimple?**

Kontakta din Microsoft-konto-representant [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) för att aktivera din prenumeration för CloudSimple-tjänsten. Ange ditt prenumerations-ID i e-postmeddelandet där du vill att CloudSimple-tjänsten är aktive rad 

**Hur gör jag för att du till gång till CloudSimple-portalen?**

Du kommer åt CloudSimple-portalen från Azure Portal. Information om hur du kommer åt CloudSimple-portalen finns i [komma åt VMware-lösningen från CloudSimple-portalen från Azure Portal](https://docs.azure.cloudsimple.com/access-cloudsimple-portal).

**Hur gör jag för att ökar du kapaciteten för ett privat moln?**

Du etablerar noder från Azure Portal och expanderar ditt privata moln från CloudSimple-portalen. Du kan expandera ditt privata moln genom att lägga till noder i ett befintligt vSphere-kluster eller genom att skapa ett nytt vSphere-kluster. Information om proceduren finns i [expandera ett privat CloudSimple-moln](https://docs.azure.cloudsimple.com/expand-private-cloud).

**Vad händer med mitt privata moln under underhållet?**

CloudSimple tillhandahåller periodiska meddelande dagar innan det schemalagda underhållet. Underhåll sker på ett avbrottat sätt för att säkerställa att ditt privata moln är tillgängligt. Underhåll kan vara av följande typer:

- **CloudSimple-infrastruktur**: CloudSimple-infrastrukturen är utformad för att vara hög tillgänglig. Under underhållet säkerställs anslutningen och tillgängligheten för ditt privata moln genom att uppdatera redundanta komponenter en i taget utan någon påverkan. Du har åtkomst till ditt privata moln vCenter, alla virtuella datorer, Internet anslutning från ditt privata moln och anslutningar till lokalt eller Azure.
- **CloudSimple-Portal**: Under underhållet kanske vissa funktioner på CloudSimple-portalen inte är tillgängliga eller kan vara inaktiverade. Underhålls meddelandet innehåller information om vad som kan göras på portalen.

## <a name="connectivity"></a>Anslutningar

**Vilka är mina anslutnings alternativ till mitt CloudSimple regions nätverk?**

CloudSimple tillhandahåller tre olika anslutnings alternativ för att ansluta till ditt CloudSimple-regions nätverk. Alla tre alternativ kan användas tillsammans:

- Azure ExpressRoute-anslutning från ditt lokala data Center till CloudSimple-region nätverk: En höghastighets säker privat anslutning med låg latens som förenar din lokala ExpressRoute-krets med din CloudSimple ExpressRoute-krets med hjälp av Global Reach. Om du vill konfigurera anslutningen går du till [Anslut från lokalt till CloudSimple med hjälp av ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection).
- ExpressRoute anslutning från ditt virtuella Azure-nätverk till ditt CloudSimple-region nätverk: En snabb och låg latens säker privat anslutning som förenar ditt virtuella nätverk på Azure med din CloudSimple ExpressRoute-krets med hjälp av virtuella nätverksgateway. Information om hur du konfigurerar anslutningen finns i [ansluta din CloudSimple privata moln miljö till det virtuella Azure-nätverket med hjälp av ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection).
- Plats-till-plats-VPN-anslutning från ditt lokala data Center till ditt CloudSimple-region nätverk: Ett säkert virtuellt privat nätverk från din lokala VPN-enhet till din CloudSimple privata moln region. Information om hur du konfigurerar anslutningen finns i [Konfigurera en VPN-anslutning mellan ditt lokala nätverk och CloudSimple privata moln](https://docs.azure.cloudsimple.com/set-up-vpn).

**Hur gör jag för att ansluta till ett privat moln?**

Du kan visa information om ditt privata moln i CloudSimple-portalen. För att ansluta till den vCenter som motsvarar ditt privata moln, se till att en nätverks anslutning upprättas med plats-till-plats-, punkt-till-plats-eller ExpressRoute. Starta sedan CloudSimple-portalen från Azure Portal. Välj **Starta vSphere-klient** på Start sidan eller på informations sidan för det privata molnet.

**Vad är fördelen med en ExpressRoute-krets?**

En Azure ExpressRoute-krets har en snabb och låg latens anslutning med låg latens. CloudSimple tillhandahåller en dedikerad ExpressRoute-krets per region per kund. Med den här kretsen kan du upprätta en säker anslutning från lokal plats och din Azure-prenumeration.

**Vad är nätverks kostnaderna för att ansluta till och från CloudSimple? Finns det några utgående avgifter till och från CloudSimple till Azure? Finns det några utgående avgifter mellan regioner?**

Det kostar inget att utgående från nätverket. Azure standard rates gäller för all utgående trafik från det virtuella nätverket eller från en lokal ExpressRoute-krets.

## <a name="networking"></a>Nätverk

**Vilka nätverksfunktioner är tillgängliga för mitt privata moln?**

Du kan etablera VLAN och deras undernät och brand Väggs tabeller. Du kan tilldela offentliga IP-adresser och mappa till en virtuell dator som körs i ditt privata moln. Mer information finns i [Översikt över VLAN och undernät](cloudsimple-vlans-subnets.md), översikt [över brand Väggs tabeller](cloudsimple-firewall-tables.md)och [Översikt över offentliga IP-adresser](cloudsimple-public-ip-address.md).

**Hur gör jag för att du konfigurera olika undernät för mina program i mitt privata moln?**

Du kan skapa VLAN i ditt privata moln från CloudSimple-portalen. När du har skapat VLAN-nätverket kan du skapa en distribuerad port grupp i ditt privata moln vCenter genom att använda VLAN och skapa virtuella datorer som är anslutna till den distribuerade port gruppen. Du kan aktivera en brand Väggs tabell för VLAN eller undernät och definiera brand Väggs regler för säker nätverks trafik.

**Vilka brand Väggs inställningar är tillgängliga för mina privata moln?**

Du kan konfigurera regler för Nord-Syd-och öst-väst-trafik. Reglerna definieras i en brand Väggs tabell. Brand Väggs tabellen kan kopplas till VLAN i ditt privata moln. Installations proceduren finns i [Konfigurera brand Väggs tabeller och regler för privata moln](https://docs.azure.cloudsimple.com/firewall).

**Kan jag tilldela offentliga IP-adresser för virtuella datorer i min privata moln miljö?**

I CloudSimple-portalen kan du enkelt allokera en ny offentlig IP-adress och associera den med en privat IP-adress för den virtuella datorn eller en installation. Du kan också skapa nya brand Väggs regler eller använda befintliga brand Väggs regler för att tillåta trafik från vissa portar och vissa uppsättningar IP-adresser i portalen. Installations proceduren finns i [tilldela offentliga IP-adresser för en privat moln miljö](https://docs.azure.cloudsimple.com/public-ips).

## <a name="security"></a>Säkerhet

**Vilka är mina säkerhets alternativ på CloudSimple?**

Ett privat CloudSimple-moln tillhandahåller följande säkerhetsfunktioner för att skydda din privata moln miljö:

- **Data vid rest-kryptering:** Du kan kryptera data i vila som finns på virtuellt SAN-lagring i ditt privata moln. Virtuellt San har stöd för en hanterings Server för externa nycklar, som kan distribueras i ditt virtuella Azure-nätverk eller i en lokal miljö. Mer information finns i [Konfigurera virtuellt San-kryptering för ditt CloudSimple privata moln](https://docs.azure.cloudsimple.com/vsan-encryption).
- **Nätverks säkerhet:** Kontrol lera nätverks trafikflödet från och till ditt privata moln från Internet, lokalt och inom undernät i ditt privata moln med hjälp av brand Väggs regler.
- **Säker, privat anslutning:** Säker, privat anslutning mellan ditt lokala nätverk och din Azure-prenumeration.

## <a name="compute"></a>Compute

**Vilken typ av värdar är tillgängliga?**

CloudSimple erbjuder två typer av värdar:

* **CS28-nod**: PROCESSOR: 2x 2,2 GHz, totalt 28 kärnor, 48 HT. RAM: 256 GB. Lagring: 1600-GB NVMe-cache, 5760 GB data (alla-blinkar). Nätverks 2x25Gbe NIC.
* **CS36-nod**: CPU 2x 2,3 GHz, totalt 36 kärnor, 72 HT. RAM: 512 GB. Lagring: 3200-GB NVMe cache 11 520 – GB data (alla-blinkar). Nätverks 2x25Gbe NIC.

**Hur hanteras maskin varu fel?**

All CloudSimple-infrastruktur övervakas kontinuerligt av CloudSimple-plattformen och dess tjänst drifts team. Om ett maskin varu haveri identifieras läggs en ny nod till i ditt privata moln. Den felaktiga noden tas bort för att säkerställa hög tillgänglighet för ditt privata moln.

## <a name="storage"></a>Storage

**Vilken typ av lagrings utrymme stöds i ett privat moln?**

CloudSimple erbjuder **all-Flash VMware virtuellt SAN-lagring** med varje privat moln. Varje vSphere skapas med ett eget virtuellt San-datalager. Mer information finns i [privat moln VMware-komponenter – virtuellt San Storage](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage).

**Stöds kryptering av data?**
Ja. Du kan konfigurera virtuellt San-lagringen i ditt privata moln för att använda en nyckel hanterings Server (KMS), som distribueras lokalt eller på Azure för kryptering av data som lagras på virtuellt San.

**Hur hanteras de felande diskarna?**

CloudSimple övervakning övervakar kontinuerligt alla maskin varu komponenter i det privata molnet. Om diskfel identifieras eller om en disk identifieras som misslyckad baserat på heuristik, läggs en ny nod automatiskt till i det privata molnet. Noden med en misslyckad eller felaktig disk tas bort från det privata molnet.

## <a name="vmware"></a>VMware

**Hur gör jag för att utföra stor skalnings överföring och migrering av program och data från lokala enheter?**

CloudSimple tillhandahåller en inbyggd VMware vSphere-lösning. Alla verktyg som används för migrering av Mass data kan användas med ett CloudSimple privat moln. Några av de tillgängliga alternativen är:

- VMware-HCX för Mass migrering av data.
- Kall migrering av data genom att använda lagrings vMotion från lokal plats till CloudSimple.

**Kan jag installera alla VMware-verktyg?**

CloudSimple tillhandahåller en inbyggd VMware vSphere-lösning. Alla verktyg som används för att hantera en vSphere-miljö lokalt kan användas på CloudSimple. CloudSimple har stöd för en BYOL-modell (a-Your-License) för att installera VMware-verktyg.

**Hur hanteras uppdateringar och uppgraderingar?**

CloudSimple hanterar och uppdaterar alla infrastruktur komponenter i ditt privata moln i ett sömlöst avbrotts sätt. Eventuella uppdateringar eller säkerhets korrigeringar som har publicerats av VMware-eller Infrastructure-leverantörer är schemalagda för uppdatering så snart som de är kvalificerade för CloudSimple.

CloudSimple utför inte uppgraderingar eller uppdateringar av program som är installerade i det privata molnet.

## <a name="azure-integration"></a>Azure-integrering

**Vilka Azure-tjänster stöds?**

CloudSimple tillhandahåller Azure ExpressRoute-anslutning till din prenumeration på Azure. Alla tjänster som körs i din prenumeration har nätverks anslutning till ditt privata moln och kan ansluta till ditt privata moln. Exempel:

- **Azure Active Directory**: Använd Azure Active Directory som identitets källa för CloudSimple vCenter.
- **Azure Storage**: Använd Storage för att lagra säkerhets kopior, avbildningar och andra data från ditt privata moln.
- **Hybrid program**: Du kan skapa en program arkitektur som omfattar offentliga och privata moln. Du kan till exempel skapa webb servrar i Azure som ansluter till program-och databas servrar i ett privat CloudSimple-moln.
- **Azure Monitor** och **Azure Security Center**: Arbets belastningar som körs på VMware kan använda övervaknings-och Security Center för loggning, prestanda mått och säkerhets hantering.

**Hur gör jag för att mappar jag mina VMware-klienter till Azure?**

CloudSimple tillhandahåller den unika möjligheten att hantera virtuella VMware-datorer i ett privat moln från Azure Portal. En vCenter-resurspool som kon figurer ATS med de resurs begränsningar som du vill använda kan mappas till din prenumeration av den globala administratören. 

**Vilka licensierings fördelar får jag med Azure?**

Med CloudSimple kan du dra nytta av Azure Hybrid-förmån och Spara upp till 90 procent på licenser för att bevara din investering i Microsoft-licenser och sänka den totala ägande kostnaden jämfört med andra moln. Du får också utökade säkerhets uppdateringar för Windows Server 2008 och Microsoft SQL Server 2008. Håll dina kostnader låga med BYOL i molnet för vanliga appar som Veeam, Zerto och andra. 
