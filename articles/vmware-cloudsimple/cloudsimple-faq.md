---
title: Vanliga frågor och svar om Azure VMware-lösningar (AVS)
description: Vanliga frågor och svar om Azure VMware-lösningar (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c3808491c84f6c76a51c914aac6ee5e5ee370970
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025069"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-avs"></a>Vanliga frågor om VMware-lösning enligt AVS

## <a name="avs-service"></a>AVS-tjänst

**Vad är Azure VMware-lösningar (AVS)?**

Azure VMware-lösningar (AVS) omvandlar och utökar VMware-arbetsbelastningar till privata, dedikerade moln på Azure på några minuter. AVS tar hand om etablering, hantering av infrastruktur och dirigering av arbets belastningar mellan lokalt och Azure. Eftersom dina appar körs exakt på samma plats och i Azure drar du nytta av elastiskheten och tjänsterna i molnet utan att du behöver bygga om dina appar. AVS sänker den totala ägande kostnaden med en moln förbruknings modell som tillhandahåller etablering på begäran, betala per användning och kapacitets optimering. Se [Vad är VMware-lösning på Azure via AVS](cloudsimple-vmware-solutions-overview.md) för funktioner, förmåner och scenarier.

**Vad är ett privat AVS-moln?**

Ett privat moln moln är ett privat, dedikerat moln som består av en beräknings-, lagrings-och nätverks miljö med höga prestanda som distribueras på Microsoft Azure infrastruktur (maskin vara och data Center utrymme) på Azure-platser. Ett privat AVS-moln tillhandahåller en inbyggd VMware-plattform som tjänst. I VMware-termer innehåller varje AVS-privat moln exakt en instans av vCenter Server. VCenter Server hanterar flera ESXi-noder som finns i ett eller flera vSphere-kluster, tillsammans med motsvarande Virtual SAN-lagring (virtuellt SAN). En AVS-tjänst kan innehålla flera moln privata moln i din Azure-prenumeration. Mer information finns i [Översikt över AVS-privata moln](cloudsimple-private-cloud.md).

**Var finns AVS-tjänsten?**

AVS är tillgängligt i regionerna östra USA, västra USA och västra Europa med ytterligare regioner som kommer snart.

**Hur gör jag för att aktivera min prenumeration för AVS?**

Du kan kontakta din Microsoft-konto representant på [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) för att aktivera din prenumeration för AVS-tjänsten. Ange ditt prenumerations-ID i e-postmeddelandet där du vill att AVS-tjänsten är aktive rad 

**Hur gör jag för att åtkomst till AVS-portalen?**

Du kommer åt AVS-portalen från Azure Portal. Mer information finns i [komma åt VMware Solutions (AVS) portal från Azure Portal](access-cloudsimple-portal.md).

**Hur gör jag för att öka kapaciteten för ett privat moln i molnet?**

Du ökar kapaciteten genom att köpa ytterligare noder från Azure Portal och sedan använda noderna för att expandera ditt moln i molnet i AVS-portalen. Du kan lägga till ytterligare noder i ett befintligt vSphere-kluster eller lägga till dem i ett nytt vSphere-kluster. Mer information finns i [expandera ett privat moln i molnet](expand-private-cloud.md).

**Vad händer med mitt molnets privata moln under underhållet?**

AVS tillhandahåller meddelanden flera dagar före ett schemalagt underhålls intervall. Underhåll sker på ett icke-störande sätt för att säkerställa tillgängligheten för ditt AVS-privata moln. Underhåll kan vara av följande typer:

* **AVS-infrastruktur**. AVS-infrastrukturen är utformad för hög tillgänglighet. Vid den här typen av underhålls intervall uppdateras redundanta komponenter en i taget för att undvika avbrott i tjänsten. Du upprätthåller åtkomst till din lokala Cloud-moln vCenter, alla virtuella datorer, Internet anslutningen från ditt moln-privata moln och anslutningar till lokalt eller Azure.
* **AVS-Portal**. Under den här typen av underhålls intervall kan vissa funktioner på AVS-portalen vara inaktiverade eller otillgängliga. Meddelandet före underhålls intervallet innehåller information om funktions begränsningar medan underhåll sker.

## <a name="connectivity"></a>Anslutningsmöjlighet

**Vilka är mina anslutnings alternativ till AVS-områdets nätverk?**

AVS tillhandahåller följande anslutnings alternativ för att ansluta till ditt AVS-områdes nätverk. Flera alternativ kan användas samtidigt.

* **ExpressRoute anslutning från ditt lokala data Center till AVS-regionens nätverk**. Detta är en säker privat anslutning med hög hastighet och låg latens som använder Global Reach för att överbrygga din lokala ExpressRoute-krets till din AVS ExpressRoute-krets. Instruktioner för hur du konfigurerar anslutningen finns i [ansluta från lokalt till AVS med ExpressRoute](on-premises-connection.md).
* **ExpressRoute anslutning från ditt virtuella Azure-nätverk till ditt AVS-områdes nätverk**. Detta är en säker privat anslutning med hög hastighet och låg latens som använder virtuella nätverks-gatewayer för att överbrygga ditt virtuella nätverk på Azure till din AVS ExpressRoute-krets. Instruktioner för hur du konfigurerar anslutningen finns i [ansluta din moln miljö för molnet till det virtuella Azure-nätverket med ExpressRoute](azure-expressroute-connection.md).
* **Plats-till-plats-VPN-anslutning från ditt lokala data Center till ditt AVS-områdes nätverk**. Det här är ett säkert virtuellt privat nätverk från din lokala VPN-enhet till molnets moln region. Mer information finns i [Konfigurera VPN-gateways i AVS-nätverk](vpn-gateway.md).

**Hur gör jag för att ansluta till ett privat AVS-moln?**

Du kan visa information om ditt privata moln i AVS-portalen. Om du vill ansluta till den vCenter som motsvarar ditt AVS-privata moln måste du först kontrol lera att en nätverks anslutning upprättas med plats-till-plats-VPN, punkt-till-plats-VPN eller ExpressRoute. Starta sedan AVS-portalen från Azure Portal och klicka på **Starta vSphere-klient** på Start sidan eller på informations sidan om molnets privata moln.

**Vad är fördelen med ExpressRoute-kretsar?**

En Azure ExpressRoute-krets är en snabb, låg latens, säker anslutning med hög hastighet. AVS tillhandahåller en dedikerad ExpressRoute-krets per region per kund. Med den här kretsen kan du upprätta en säker anslutning från en lokal plats eller din Azure-prenumeration.

**Vilka är nätverks kostnaderna för att ansluta till AVS? Gäller eventuella utgående avgifter mellan AVS och Azure, eller mellan regioner?**

Det finns ingen AVS-avgift för utgående nätverk. Azure standard rates gäller för all utgående trafik från ditt virtuella nätverk eller från din lokala ExpressRoute-krets.

## <a name="networking"></a>Nätverk

**Vilka nätverksfunktioner är tillgängliga för mitt molnets privata moln?**

Du kan etablera VLAN (och deras undernät) och brand Väggs tabeller och tilldela offentliga IP-adresser som mappar till en virtuell dator som körs i ditt moln privata moln. Mer information om nätverksfunktioner finns i [Översikt över VLAN och undernät](cloudsimple-vlans-subnets.md), översikt [över brand Väggs tabeller](cloudsimple-firewall-tables.md)och [Översikt över offentliga IP-adresser](cloudsimple-public-ip-address.md).

**Hur gör jag för att konfigurera olika undernät för mina program i mitt moln privata moln?**

Du skapar VLAN i ditt moln privata moln från AVS-portalen. När du har skapat ett VLAN kan du skapa en distribuerad port grupp i ditt molns privata moln vCenter med hjälp av VLAN och skapa virtuella datorer som är anslutna till den distribuerade port gruppen. Du kan aktivera brand Väggs tabeller för VLAN/undernät och definiera brand Väggs regler för säker nätverks trafik.

**Vilka brand Väggs inställningar är tillgängliga för mina AVS-privata moln?**

Du kan konfigurera regler för Nord-Syd-och öst-väst-trafik. Reglerna definieras i en brand Väggs tabell. Brand Väggs tabellen kan kopplas till VLAN i ditt moln privata moln. Mer information finns i [Konfigurera brand Väggs tabeller och regler för AVS-privata moln](firewall.md).

**Kan jag tilldela offentliga IP-adresser till virtuella datorer i min AVS-miljö för privata moln?**

I AVS-portalen kan du allokera en ny offentlig IP-adress och associera den med den privata IP-adressen för en virtuell dator eller en installation. Du kan också skapa nya brand Väggs regler eller använda befintliga brand Väggs regler för att tillåta trafik från vissa portar och IP-adresser i portalen. Mer information finns i [allokera offentliga IP-adresser för molnets privata moln miljö](public-ips.md).

## <a name="security"></a>Säkerhet

**Vilka är mina säkerhets alternativ i AVS?**

AVS innehåller följande säkerhetsfunktioner för att skydda din moln miljö i molnet:

* **Data vid rest-kryptering**. Du kan kryptera data i vila på virtuellt San-lagringen i ditt moln privata moln. Virtuellt San stöder hanterings servrar för externa nycklar, som kan distribueras i din Azure vNet eller lokala miljö. Mer information finns i [Konfigurera virtuellt San-kryptering för ditt AVS-privata moln](vsan-encryption.md).
* **Nätverks säkerhet**. Kontrol lera nätverks trafikflödet med brand Väggs regler som gäller mellan ditt moln privata moln och Internet, ditt moln i molnet och den lokala miljön, eller inom undernät i ditt moln privata moln.
* **Säker, privat anslutning**. En säker, privat anslutning upprättas mellan ditt lokala nätverk och din Azure-prenumeration.

## <a name="compute"></a>Databearbetning

**Vilken typ av värdar är tillgängliga?**

AVS erbjuder följande värd typer:

* **CS28-nod:** PROCESSOR: 2x 2,2 GHz, totalt 28 kärnor, 48 HT.  RAM: 256 GB.  Lagring: 1600 GB NVMe-cache, 5760 GB data (alla-blinkar). Nätverk: 4x25Gbe NIC
* **CS36-nod:** CPU 2x 2,3 GHz, totalt 36 kärnor, 72 HT.  RAM: 512 GB.  Lagring: 3200 GB NVMe cache 11520 GB data (alla-blinkar).  Nätverk: 4x25Gbe NIC
* **CS36m-nod:** CPU 2x 2,3 GHz, totalt 36 kärnor, 72 HT.  RAM: 576 GB.  Lagring: 3200 GB NVMe cache 13360 GB data (alla-blinkar).  Nätverk: 4x25Gbe NIC

**Hur hanteras eventuella maskin varu fel?**

All AVS-infrastruktur övervakas kontinuerligt av AVS-plattformen och våra tjänst drifts team. Om ett maskin varu fel upptäcks läggs en ny nod till i ditt moln privata moln och den felande noden tas bort.

## <a name="storage"></a>Lagring

**Vilken typ av lagrings utrymme stöds i ett privat AVS-moln?**

AVS erbjuder all-Flash VMware virtuellt SAN-lagring med varje AVS-privat moln. Varje vSphere skapas med ett eget virtuellt San-datalager. Mer information finns i avsnittet om [VMware-komponenter i AVS-virtuellt SAN-lagring](vmware-components.md#vsan-storage).

**Stöds kryptering av data?**
Ja. Du kan ställa in virtuellt San-lagringen på ditt moln privata moln om du vill använda en nyckel hanterings Server (KMS) som distribueras lokalt eller på Azure för att kryptera data som lagras på virtuellt San.

**Hur hanteras de felande diskarna?**

AVS övervakar kontinuerligt alla maskin varu komponenter i det privata AVS-molnet. Om ett diskfel upptäcks eller en disk identifieras som misslyckad (baserat på heuristik) läggs en ny nod automatiskt till i det privata moln molnet. Noden med den felande eller felaktiga disken tas bort från det privata molnet i AVS-molnet.

## <a name="vmware"></a>VMware

**Hur gör jag för att utföra storskalig uppladdning eller migrering av program och data från lokala enheter?**

AVS tillhandahåller en inbyggd VMware vSphere lösning. Alla VMware-verktyg för datamigrering kan användas med ditt AVS-privata moln. Alternativen är:

* VMware-HCX för Mass migrering av data.
* Kall migrering av data med hjälp av Storage vMotion från lokalt till AVS.

**Kan jag installera alla VMware-verktyg?**

AVS tillhandahåller en inbyggd VMware vSphere lösning. Alla VMware-verktyg som används för att hantera din lokala vSphere-miljö kan användas i AVS. AVS stöder en BYOL-modell (a-Your-License) för att installera VMware-verktyg.

**Hur hanteras uppdateringar och uppgraderingar?**

AVS hanterar och uppdaterar alla infrastruktur komponenter i ditt AVS-privata moln på ett smidigt sätt. Alla uppdateringar och säkerhets uppdateringar som publicerats av VMware-eller infrastruktur leverantörer är schemalagda för uppdatering så snart de kvalificeras av AVS.

AVS utför inte uppgraderingar eller uppdateringar av program som är installerade i det privata moln molnet.

## <a name="azure-integration"></a>Azure-integrering

**Vilka Azure-tjänster stöds?**

AVS tillhandahåller en Azure ExpressRoute-anslutning till din prenumeration på Azure. Alla tjänster som körs i din prenumeration kan ansluta till ditt AVS-privata moln. Till exempel:

* **Azure Active Directory** som identitets källa för din AVS vCenter.
* **Azure Storage** för lagring av säkerhets kopior, avbildningar och andra data från ditt moln privata moln.
* **Hybrid program** med en program arkitektur som sträcker sig över offentliga och AVS-privata moln. Du kan till exempel skapa webbservrar i Azure som ansluter till program-och databas servrar i ditt moln privata moln.
* **Azure Monitor** och **Azure Security Center** för arbets belastningar som körs på VMware support-loggning, prestanda mått och säkerhets hantering.

**Hur gör jag för att mappar jag mina VMware-klienter till Azure?**

AVS tillhandahåller den unika möjligheten att hantera virtuella VMware-datorer i ett privat AVS-moln från Azure Portal. En vCenter-resurspool som kon figurer ATS med önskade resurs begränsningar kan mappas till din prenumeration av din globala administratör. 

**Vilka licensierings fördelar får jag med Azure?**

Med AVS kan du dra nytta av Azure Hybrid Usage-förmånen och Spara upp till 90% på licenser. Den här förmånen bevarar din investering i Microsoft-licenser och sänker din TCO i förhållande till andra moln lösningar. Du får också utökade säkerhets uppdateringar för Windows Server 2008 och Microsoft SQL Server 2008. BYOL-modellen hjälper dig att hålla kostnaderna nere för vanliga appar, till exempel Veeam och Zerto. 
