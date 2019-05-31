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
ms.openlocfilehash: e727398e1b7bfa406166574ab40320c68dac5709
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358535"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Vanliga frågor och svar om VMware-lösning genom CloudSimple

Vanliga frågor och svar om VMware-lösning genom CloudSimple som hjälper dig att förstå tjänsten och hur du använder den.  Frågor och svar är ordnade i följande kategorier.

* CloudSimple Service
* Anslutning
* Nätverk
* Säkerhet
* Compute
* Storage
* VMware
* Azure Integration
  
## <a name="cloudsimple-service"></a>CloudSimple Service

**Vad är VMware-lösning genom CloudSimple?**

**VMware-lösning genom CloudSimple** transformerar och utökar VMware-arbetsbelastningar till privat, dedikerad moln i Azure på bara några minuter. Vi hand tar om etablering, hantering av infrastrukturen och samordna arbetsbelastningar mellan lokala och Azure. Eftersom dina appar körs exakt de samma lokalt och i Azure, dra nytta av elasticiteten och tjänster med molnet utan komplexitet genom att utforma dina appar. CloudSimple sänker den totala ägandekostnaden med en förbrukningsmodell i molnet som tillhandahåller på begäran etablering, betala som du-växer och optimering av kapacitet.  Se [vad är VMware-lösning på Azure via CloudSimple](cloudsimple-vmware-solutions-overview.md) för funktioner, fördelar och scenarier.

**Vad är ett CloudSimple privat moln?**

Du etablerar ett privat, dedikerad moln som består av databehandling med höga prestanda, lagring och nätverk miljö som har distribuerats på Microsoft Azure-infrastrukturen (maskin- och datacenter-utrymme) i Azure-platser.  Privat moln är en inbyggd VMware-plattform 'as-a-service ”. I VMware villkor innehåller varje privata moln exakt en instans av vCenter-servern. VCenter-servern hanterar flera ESXi-noder i ett eller flera vSphere-kluster, tillsammans med motsvarande virtuella SAN (vSAN)-lagring. En CloudSimple Service kan innehålla flera privata moln i Azure-prenumerationen.  Läs mer om privata moln, [privat moln översikt](cloudsimple-private-cloud.md).

**Var finns CloudSimple service?**

CloudSimple är tillgängligt i regionerna USA, östra och USA, västra.

**Hur aktiverar jag min prenumeration på CloudSimple?**

Du kan kontakta din Microsoft-kontorepresentant på [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) att aktivera prenumerationen för CloudSimple-tjänsten. Ange ditt prenumerations-ID för e-postmeddelandet som du vill köra tjänsten CloudSimple.  

**Hur kommer jag åt CloudSimple portal?**

Du kan komma åt CloudSimple portalen från Azure-portalen.  Se [åtkomst till VMware-lösning genom CloudSimple portalen från Azure-portalen](https://docs.azure.cloudsimple.com/access-cloudsimple-portal) nedan för information om hur du använder CloudSimple-portalen. 

**Hur ökar jag kapaciteten för ett privat moln?**

Du köper noder från Azure-portalen och utöka ditt privata moln från CloudSimple-portalen.  Du kan expandera ditt privata moln genom att lägga till ytterligare noder i befintligt vSphere-kluster eller genom att skapa nya vSphere-klustret.  Se [expandera ett CloudSimple privat moln](https://docs.azure.cloudsimple.com/expand-private-cloud) artikel för proceduren.

**Vad händer med mitt privata moln under Underhåll?**

CloudSimple innehåller periodiska aviseringar dagar före schemalagt underhåll.  Underhåll sker i ett avbrottsfria sätt att kontrollera tillgängligheten för ditt privata moln.  Underhåll kan vara av följande typer:

1. **CloudSimple infrastruktur:**  CloudSimple infrastruktur är utformad för att ha hög tillgänglighet.  Under Underhåll säkerställs anslutning och tillgängligheten för ditt privata moln genom att uppdatera redundanta komponenter en i taget utan påverkas.  Du har åtkomst till ditt vCenter-privat moln, alla virtuella datorer, internet-anslutning från ditt privata moln och anslutningar till lokala eller Azure.
2. **CloudSimple Portal:** Under Underhåll kanske inte vissa funktioner i CloudSimple portal tillgänglig eller är inaktiverad.  Meddelande om Underhåll innehåller information om vad du kan göra på portalen.

## <a name="connectivity"></a>Anslutning

**Vilka anslutningsalternativ har jag till CloudSimple region nätverk?**

CloudSimple tillhandahåller tre olika anslutningsalternativ för att ansluta till nätverket CloudSimple region.  Alla tre kan användas tillsammans.

1. ExpressRoute-anslutning från ditt lokala datacenter till CloudSimple region nätverk – privat anslutning med hög hastighet med låg latens säker bryggning lokala ExpressRoute-kretsen med din CloudSimple ExpressRoute-krets med Global räckvidd. Se [Anslut från en lokal plats till CloudSimple med hjälp av ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection) artikeln för att konfigurera anslutningen.
2. ExpressRoute-anslutning från Azure-nätverk till nätverket CloudSimple region - privat anslutning med hög hastighet med låg latens säker bryggning ditt virtuella nätverk på Azure med CloudSimple ExpressRoute-kretsen med hjälp av virtuella nätverksgatewayer.  Se [ansluta miljön CloudSimple privat moln till Azure-nätverket med hjälp av ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection) artikeln för att konfigurera anslutningen.
3. Plats-till-plats VPN-anslutning från ditt lokala datacenter till nätverket CloudSimple region - virtuella privata nätverk från din lokala VPN-enhet till din CloudSimple privat moln-region.  Se [konfigurera VPN-anslutning mellan ditt lokala nätverk och CloudSimple privat moln] artikeln för att konfigurera VPN-anslutning.

**Hur ansluter jag till ett privat moln?**

Du kan visa information om ditt privata moln i CloudSimple-portalen. För att ansluta till vCenter som motsvarar ditt privata moln, se till att nätverksanslutningen är etablerad med plats-till-plats, punkt-till-plats eller ExpressRoute. Starta sedan CloudSimple portalen från Azure-portalen och klicka på *starta vSphere-klienten* på startsidan eller sidan privat moln.

**Vad är fördelen med ExpressRoute-krets?**

Azure ExpressRoute-krets tillhandahåller snabba svarstider säker anslutning.  CloudSimple ger en dedikerad ExpressRoute-krets per region per kund.  Med den här kretsen kan upprätta du säker anslutning från lokala och/eller din Azure-prenumeration.

**Vad är network-kostnaderna för att ansluta till och från CloudSimple. Alla kostnader för utgående trafik till och från CloudSimple till Azure? I olika regioner?**

Det är kostnadsfritt för alla utgående nätverkstrafik.  Azure standardtaxa gäller för all utgående trafik från ditt virtuella nätverk eller från den lokala ExpressRoute-krets.

## <a name="networking"></a>Nätverk

**Vilka funktioner är tillgängliga för Mina privat moln?**

Du kan etablera virtuella lokala nätverk (och deras undernät), tabeller, i brandväggen och tilldela offentliga IP-adresser och mappa till en virtuell dator som körs i ditt privata moln.  Mer information finns i [VLAN/undernät översikt](cloudsimple-vlans-subnets.md), [översikt över brandväggen tabeller](cloudsimple-firewall-tables.md), och [översikt över offentliga IP-adress](cloudsimple-public-ip-address.md) artiklar.

**Hur ställer jag in olika undernät för mitt program i min privat moln?**

Du kan skapa virtuella lokala nätverk i ditt privata moln från CloudSimple-portalen.  När du skapar VLAN kan du skapa en distribuerad portgrupp på ditt privata moln-vCenter med VLAN och skapa virtuella datorer som är anslutna till gruppen distribuerade port.  Du kan aktivera brandväggen tabell för VLAN/undernät och definiera brandväggsregler för att säkra nätverkstrafik.

**Vilka brandväggsinställningar är tillgängliga för Mina privat moln?**

Du kan konfigurera regler för Nord-sydlig och öst-väst-trafik.  Reglerna har definierats i en tabell i brandväggen.  Tabellen brandväggen kan kopplas till VLAN(s) i ditt privata moln.  Se [konfigurera brandväggen tabeller och regler för privata moln](https://docs.azure.cloudsimple.com/firewall) artikeln för att ställa in proceduren.

**Kan jag tilldela offentliga IP-adresser för virtuella datorer i min privat moln-miljö?**

Du kan enkelt allokera en ny offentlig IP-adress och koppla den till en privat IP-adressen för den virtuella datorn eller en installation i CloudSimple-portalen.  Du kan också skapa nya brandväggsregler eller Använd befintliga brandväggsregler för att tillåta trafik från specifika portar och/eller specifik uppsättning IP-adresser i portalen. Se [tilldela offentliga IP-adresser för privat molnmiljö](https://docs.azure.cloudsimple.com/public-ips) för att ställa in proceduren.

## <a name="security"></a>Säkerhet

**Vilka är Mina säkerhetsalternativ på CloudSimple?**

CloudSimple privat moln innehåller följande säkerhetsfunktioner för att skydda din miljö för privat moln:

1. **Data för kryptering av vilande data**: Du kan kryptera data i vila som finns på virtuellt SAN-lagring i ditt privata moln. virtuellt SAN-nätverk stöder externa nyckelhantering server, som kan distribueras i din Azure vNet eller en lokal miljö.  Se [konfigurera virtuellt SAN-kryptering för din CloudSimple privat moln](https://docs.azure.cloudsimple.com/vsan-encryption) för mer information.
2. **Nätverkssäkerhet**: Styra flödet i nätverkstrafiken från/till ditt privata moln från Internet, lokalt och i undernäten i ditt privata moln med hjälp av brandväggsregler.
3. **Säkra privata anslutningar**: Säkra privata anslutningar mellan ditt lokala nätverk och din Azure-prenumeration.

## <a name="compute"></a>Compute

**Vilken typ av värdar är tillgängliga?**

CloudSimple erbjuder två värdtyper:

* **CS28 nod:** CPU:2 x 2,2 GHz, totalt antal 28 kärnor 48 HT.  RAM: 256 GB.  Lagring: 1 600 GB NVMe Cache, 5760 GB Data (All-Flash). Nätverk: 2x25Gbe NIC
* **CS36 nod:** CPU 2 x 2.3 GHz, totalt antal 36 kärnor, 72 HT.  RAM: 512 GB.  Lagring: 3200 GB NVMe Cache 11,520 GB Data (All-Flash).  Nätverk: 2x25Gbe NIC

**Hur hanteras eventuella maskinvarufel?**

Alla CloudSimple infrastruktur övervakas kontinuerligt av CloudSimple-plattformen och vår tjänst-driftteam.  Om ett maskinvarufel identifieras får en ny nod läggs till i ditt privata moln och misslyckade noden tas bort för att säkerställa hög tillgänglighet för privat moln.

## <a name="storage"></a>Storage

**Vilken typ av lagring stöds på ett privat moln?**

CloudSimple erbjuder **All-flash VMware virtuellt SAN-lagring** med varje privat moln.  Varje vSphere skapas med sin egen virtuellt SAN-datalager.  Se [komponenter för privata moln VMware - virtuellt SAN-lagring](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage) nedan för mer information.

**Är kryptering av data som stöds?**
Ja.  Du kan konfigurera virtuellt SAN-lagring i ditt privata moln du använder en key management server (KMS) som är distribueras lokalt eller på Azure för kryptering av data som lagras på virtuellt SAN-nätverk

**Hur hanteras felaktiga diskar?**

CloudSimple övervakning övervakar kontinuerligt alla maskinvarukomponenter för privat moln.  Om eventuella diskfel upptäcks eller en disk har identifierats som misslyckas (baserat på heuristik), läggs automatiskt en ny nod i privat moln.  Noden med inte fungerar eller disken tas bort från det privata molnet.

## <a name="vmware"></a>VMware

**Hur utför jag storskaliga uppladdning/migrera program och data från en lokal plats?**

CloudSimple får inbyggda VMware vSphere-lösning.  Alla verktyg som används för migrering av bulk data kan användas med CloudSimple privat moln.  Några av de tillgängliga alternativen är:

1. VMware HCX för bulk-migrering av data.
2. Kalla migrering av data med hjälp av Storage vMotion från en lokal plats till CloudSimple.

**Kan jag installera någon VMware-verktyg?**

CloudSimple får inbyggda VMware vSphere-lösning.  Alla verktyg som används för att hantera vSphere-miljön på plats kan användas på CloudSimple.  CloudSimple har stöd för Bring-Your-Own-License (BYOL)-modell för att installera VMware-verktyg.

**Hur hanteras uppdateringar och uppgraderingar?**

CloudSimple hanterar och uppdaterar alla infrastrukturkomponenter för privat moln i en sömlös avbrottsfria sätt.  Alla uppdateringen eller säkerhetskorrigeringen som publicerats av VMware eller infrastruktur leverantörer schemaläggs för uppdatering så snart den är kvalificerad av CloudSimple.

CloudSimple utför inte uppgraderingar och uppdateringar av program installerade på privat moln.

## <a name="azure-integration"></a>Azure Integration

**Vilka Azure-tjänster stöds?**

CloudSimple ger Azure ExpressRoute-anslutning till din prenumeration på Azure.  Alla tjänster som körs i din prenumeration har en nätverksanslutning till ditt privata moln och kan ansluta till ditt privata moln.  Exempel:

1. **Azure Active Directory** som en som identitetskälla för CloudSimple vCenter
2. **Azure storage** för att lagra säkerhetskopior, bilder och andra data från ditt privata moln
3. **Hybridprogram** – du kan skapa programarkitektur som sträcker sig över offentliga och privata moln.  Du kan till exempel skapa webservers i Azure som har åtkomst till program och databasservrar på CloudSimple privat moln.
4. **Med Azure monitor** och **Azure security center** -arbetsbelastningar som körs på VMware kan använda dem för loggning, prestandamått och säkerhetshantering.

**Hur mappar jag mina klienter för VMware till Azure?**

CloudSimple ger unika möjlighet att hantera dina virtuella VMware-datorer i privata moln från Azure-portalen.  En vCenter-resurspool (konfigureras med önskad resursbegränsningar) kan mappas till din prenumeration av den globala administratören.  

**Vilka licensiering fördelar får jag med Azure?**

Du kan använda CloudSimple, för att utnyttja Azure Hybrid-förmånen för användning och spara upp till 90% för licenser bevara investeringen i Microsoft Licenses sänka den totala Ägandekostnaden jämfört med andra moln. Dessutom får du utökat säkerhetsuppdateringar för Windows Server 2008 och Microsoft SQL Server 2008.  Håll nere kostnaderna med Bring Your Own licenser (BYOL) till molnet för vanliga appar som Veeam, Zerto och andra.  