---
title: Vanliga frågor och svar - Azure VMware-lösning från CloudSimple
description: Vanliga frågor och svar om Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d8c2974ea372dd59d15a9f2cc31ed171acd932
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025069"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Vanliga frågor och svar om VMware-lösning från CloudSimple

## <a name="cloudsimple-service"></a>CloudSimple-tjänst

**Vad är Azure VMware-lösning från CloudSimple?**

Azure VMware Solution by CloudSimple omvandlar och utökar VMware-arbetsbelastningar till privata, dedikerade moln på Azure på några minuter. CloudSimple tar hand om etablering, hantering av infrastrukturen och orkestrera arbetsbelastningar mellan lokala och Azure. Eftersom dina appar kör exakt samma lokala och azure-appar drar du nytta av molnets elasticitet och tjänster utan komplexiteten i att skapa bakåt i dina appar. CloudSimple sänker din totala ägandekostnad med en molnförbrukningsmodell som tillhandahåller etablering på begäran, betalning som du växer och kapacitetsoptimering.  Se [Vad är VMware-lösning på Azure by CloudSimple](cloudsimple-vmware-solutions-overview.md) för funktioner, fördelar och scenarier.

**Vad är ett CloudSimple Private Cloud?**

Ett CloudSimple Private Cloud är ett privat, dedikerat moln som består av en högpresterande beräknings-, lagrings- och nätverksmiljö som distribueras på Microsoft Azure-infrastruktur (maskinvaru- och datacenterutrymme) på Azure-platser.  Ett privat moln tillhandahåller en inbyggd VMware -plattform som en tjänst. I VMware termer, varje privat moln innehåller exakt en instans av vCenter Server. VCenter Server hanterar flera ESXi-noder som finns i ett eller flera vSphere-kluster, tillsammans med motsvarande virtual SAN-lagring (vSAN). En CloudSimple-tjänst kan innehålla flera privata moln i din Azure-prenumeration.  Mer information finns i [Översikt över privat moln](cloudsimple-private-cloud.md).

**Var är CloudSimple-tjänsten tillgänglig?**

CloudSimple är tillgängligt i regioner i östra USA, västra USA och Västeuropa med ytterligare regioner som kommer snart.

**Hur aktiverar jag min prenumeration för CloudSimple?**

Du kan kontakta din [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) Microsoft-kontorepresentant för att aktivera din prenumeration för CloudSimple-tjänsten. Ange ditt prenumerations-ID i e-postmeddelandet som du vill ha CloudSimple-tjänsten aktiverad för.  

**Hur kommer jag åt CloudSimple-portalen?**

Du kommer åt CloudSimple-portalen från Azure-portalen.  Mer information finns [i Få tillgång till VMware-lösningen via CloudSimple-portalen från Azure-portalen](access-cloudsimple-portal.md).

**Hur ökar jag kapaciteten för ett privat moln?**

För att öka kapaciteten, köpa ytterligare noder från Azure-portalen och sedan använda noderna för att expandera ditt privata moln från CloudSimple-portalen.  Du kan lägga till ytterligare noder i ett befintligt vSphere-kluster eller lägga till dem i ett nytt vSphere-kluster.  Mer information finns i [Expandera ett CloudSimple Private Cloud](expand-private-cloud.md).

**Vad händer med mitt privata moln under underhåll?**

CloudSimple meddelar flera dagar före ett schemalagt underhållsintervall.  Underhåll sker på ett icke-störande sätt för att säkerställa tillgängligheten för ditt privata moln.  Underhåll kan vara av följande slag:

* **CloudSimple-infrastruktur**.  CloudSimple-infrastrukturen är utformad för att vara högtillgänglig.  Under den här typen av underhållsintervall uppdateras redundanta komponenter en i taget för att undvika avbrott i tjänsten. Du behåller åtkomsten till ditt privata moln vCenter, alla virtuella datorer, internetanslutningen från ditt privata moln och anslutningar till lokalt eller Azure.
* **CloudSimple-portalen**. Under den här typen av underhållsintervall kan vissa funktioner på CloudSimple-portalen vara inaktiverade eller otillgängliga.  Anmälan före underhållsintervallet innehåller information om funktionsbegränsningar under underhåll.

## <a name="connectivity"></a>Anslutning

**Vilka är mina anslutningsalternativ till CloudSimple-regionnätverket?**

CloudSimple tillhandahåller följande anslutningsalternativ för att ansluta till ditt CloudSimple-regionnätverk. Flera alternativ kan användas samtidigt.

* **ExpressRoute-anslutning från ditt lokala datacenter till CloudSimple-regionnätverket**. Detta är en säker privat anslutning med hög hastighet och låg latens som använder Global Reach för att överbrygga din lokala ExpressRoute-krets till din CloudSimple ExpressRoute-krets. Instruktioner om hur du konfigurerar anslutningen finns i [Ansluta från lokalt till CloudSimple med ExpressRoute](on-premises-connection.md).
* **ExpressRoute-anslutning från ditt virtuella Azure-nätverk till cloudsimple-regionnätverket**. Det här är en säker privat anslutning med hög hastighet och låg latens som använder virtuella nätverksgateways för att överbrygga ditt virtuella nätverk på Azure till din CloudSimple ExpressRoute-krets. Instruktioner om hur du konfigurerar anslutningen finns i [Anslut din CloudSimple Private Cloud-miljö till det virtuella Azure-nätverket med ExpressRoute](azure-expressroute-connection.md).
* **Plats-till-plats VPN-anslutning från ditt lokala datacenter till ditt CloudSimple-regionnätverk**. Det här är ett säkert virtuellt privat nätverk från din lokala VPN-enhet till din CloudSimple Private Cloud-region.  Mer information finns i [Konfigurera VPN-gateways i CloudSimple-nätverket](vpn-gateway.md).

**Hur ansluter jag till ett privat moln?**

Du kan visa information om ditt privata moln i CloudSimple-portalen. Om du vill ansluta till det virtuellacenter som motsvarar ditt privata moln kontrollerar du först att en nätverksanslutning upprättas med Site-to-Site VPN, Point-to-Site VPN eller ExpressRoute. Starta sedan CloudSimple-portalen från Azure-portalen och klicka på **Starta vSphere-klienten** på startsidan eller på informationssidan för Private Cloud.

**Vad är fördelen med ExpressRoute-kretsar?**

En Azure ExpressRoute-krets är en säker anslutning med hög hastighet och låg latens.  CloudSimple tillhandahåller en dedikerad ExpressRoute-krets per region och kund.  Med den här kretsen kan du upprätta en säker anslutning från lokala eller din Azure-prenumeration.

**Vilka är nätverkskostnaderna för att ansluta till CloudSimple?  Gäller några utgående avgifter mellan CloudSimple och Azure, eller mellan regioner?**

Det finns ingen CloudSimple-avgift för nätverksutgående.  Standardpriser för Azure gäller för all utgående trafik från ditt virtuella nätverk eller från din lokala ExpressRoute-krets.

## <a name="networking"></a>Nätverk

**Vilka nätverksfunktioner är tillgängliga för mitt privata moln?**

Du kan etablera VLAN (och deras undernät) och brandväggstabeller och tilldela offentliga IP-adresser som mappas till en virtuell dator som körs i ditt privata moln. Mer information om nätverksfunktioner finns i [VLAN och undernätsöversikt,](cloudsimple-vlans-subnets.md) [översikt över brandväggstabeller](cloudsimple-firewall-tables.md)och [översikt över offentlig IP-adress](cloudsimple-public-ip-address.md).

**Hur konfigurerar jag olika undernät för mina program i mitt privata moln?**

Du skapar VLAN på ditt privata moln från CloudSimple-portalen.  När du har skapat en VLAN kan du skapa en distribuerad portgrupp på ditt Private Cloud vCenter med hjälp av VLAN och skapa virtuella datorer som är anslutna till den distribuerade portgruppen.  Du kan aktivera brandväggstabeller för VLAN/undernätet och definiera brandväggsregler för att skydda nätverkstrafik.

**Vilka brandväggsinställningar är tillgängliga för mina privata moln?**

Du kan konfigurera regler för nord-syd- och öst-västlig trafik.  Reglerna definieras i en brandväggstabell.  Brandväggstabellen kan kopplas till VLAN i ditt privata moln.  Mer information finns i [Konfigurera brandväggstabeller och regler för privata moln](firewall.md).

**Kan jag tilldela offentliga IP-adresser till virtuella datorer i min privata molnmiljö?**

I CloudSimple-portalen kan du allokera en ny offentlig IP-adress och associera den med den privata IP-adressen för en virtuell dator eller en installation.  Du kan också skapa nya brandväggsregler eller tillämpa befintliga brandväggsregler för att tillåta trafik från specifika portar och IP-adresser i portalen. Mer information finns i [Allokera offentliga IP-adresser för privat molnmiljö](public-ips.md).

## <a name="security"></a>Säkerhet

**Vilka är mina säkerhetsalternativ på CloudSimple?**

CloudSimple tillhandahåller följande säkerhetsfunktioner för att skydda din privata molnmiljö:

* **Kryptering av data i vila**. Du kan kryptera data i vila som finns på vSAN-lagringen i ditt privata moln. vSAN stöder externa nyckelhanteringsservrar som kan distribueras i ditt Azure-vNet eller den lokala miljön.  Mer information finns i [Konfigurera vSAN-kryptering för ditt CloudSimple Private Cloud](vsan-encryption.md).
* **Nätverkssäkerhet**. Styr nätverkstrafikens flöde med brandväggsregler som gäller mellan ditt privata moln och internet, ditt privata moln och den lokala miljön eller i undernäten i ditt privata moln.
* **Säker, privat anslutning**. En säker, privat anslutning upprättas mellan ditt lokala nätverk och din Azure-prenumeration.

## <a name="compute"></a>Compute

**Vilken typ av värdar är tillgängliga?**

CloudSimple erbjuder följande värdtyper:

* **CS28-nod:** CPU:2x 2,2 GHz, totalt 28 kärnor, 48 HT.  RAM: 256 GB.  Lagring: 1600 GB NVMe-cache, 5760 GB data (All-Flash). Nätverk: 4x25Gbe NIC
* **CS36-nod:** CPU 2x 2,3 GHz, totalt 36 kärnor, 72 HT.  RAM: 512 GB.  Lagring: 3200 GB NVMe cache 11520 GB data (All-Flash).  Nätverk: 4x25Gbe NIC
* **CS36m-nod:** CPU 2x 2,3 GHz, totalt 36 kärnor, 72 HT.  RAM: 576 GB.  Lagring: 3200 GB NVMe cache 13360 GB data (All-Flash).  Nätverk: 4x25Gbe NIC

**Hur hanteras maskinvarufel?**

All CloudSimple-infrastruktur övervakas kontinuerligt av CloudSimple-plattformen och våra serviceoperationsteam.  Om ett maskinvarufel upptäcks läggs en ny nod till i ditt privata moln och den misslyckade noden tas bort.

## <a name="storage"></a>Lagring

**Vilken typ av lagring stöds i ett privat moln?**

CloudSimple erbjuder vmware-lagring med alla blinkar med alla privata moln.  Varje vSphere skapas med sitt eget vSAN-datalager.  Mer information finns i [Privata VMware-komponenter i molnet - vSAN-lagring](vmware-components.md#vsan-storage).

**Stöds kryptering av data?**
Ja.  Du kan konfigurera vSAN-lagringen i ditt privata moln så att du använder en nyckelhanteringsserver (KMS) som distribueras lokalt eller på Azure för att kryptera data som lagras på vSAN.

**Hur hanteras felaktiga diskar?**

CloudSimple övervakar kontinuerligt alla maskinvarukomponenter i det privata molnet.  Om ett diskfel upptäcks eller en disk identifieras som misslyckad (baserat på heuristik) läggs en ny nod automatiskt till i det privata molnet.  Noden med den misslyckade eller felaktiga disken tas bort från det privata molnet.

## <a name="vmware"></a>VMware

**Hur utför jag storskalig uppladdning eller migrering av program och data från lokala?**

CloudSimple tillhandahåller en inbyggd VMware vSphere-lösning.  Alla VMware-verktyg för massmigrering av data kan användas med ditt privata moln.  Alternativen är:

* VMware HCX för massmigrering av data.
* Kall migrering av data med Lagring vMotion från lokalt till CloudSimple.

**Kan jag installera några VMware verktyg?**

CloudSimple tillhandahåller en inbyggd VMware vSphere-lösning.  Alla VMware-verktyg som används för att hantera din lokala vSphere-miljö kan användas på CloudSimple.  CloudSimple stöder en BYOL-modell (bring-your-own-license) för installation av VMware-verktyg.

**Hur hanteras uppdateringar och uppgraderingar?**

CloudSimple hanterar och uppdaterar alla infrastrukturkomponenter i ditt privata moln på ett sömlöst icke-störande sätt.  Alla uppdateringar och säkerhetskorrigeringar som släpps av VMware- eller infrastrukturleverantörer är schemalagda för uppdatering så snart de har kvalificerats av CloudSimple.

CloudSimple utför inte uppgraderingar eller uppdateringar av program som är installerade på det privata molnet.

## <a name="azure-integration"></a>Azure-integrering

**Vilka Azure-tjänster stöds?**

CloudSimple tillhandahåller en Azure ExpressRoute-anslutning till din prenumeration på Azure.  Alla tjänster som körs i din prenumeration kan ansluta till ditt privata moln.  Exempel på rekommendationer:

* **Azure Active Directory** som identitetskälla för ditt CloudSimple vCenter.
* **Azure-lagring** för lagring av säkerhetskopior, avbildningar och andra data från ditt privata moln.
* **Hybridprogram** med en programarkitektur som sträcker sig över offentliga och privata moln.  Du kan till exempel skapa webbservrar i Azure som får åtkomst till program- och databasservrar i ditt privata moln.
* **Azure-övervakare** och **Azure-säkerhetscenter** för arbetsbelastningar som körs på VMware stöder loggning, prestandamått och säkerhetshantering.

**Hur mappar jag mina VMware-klienter till Azure?**

CloudSimple ger den unika möjligheten att hantera dina virtuella datorer med VMware på Private Cloud från Azure-portalen.  En vCenter-resurspool som konfigurerats med önskade resursbegränsningar kan mappas till din prenumeration av den globala administratören.  

**Vilka licensförmåner får jag med Azure?**

Med CloudSimple kan du dra nytta av Azure Hybrid Usage Benefit och spara upp till 90 % på licenser. Den här fördelen bevarar din investering i Microsoft-licenser och sänker din TCO i förhållande till andra molnlösningar. Du får också utökade säkerhetsuppdateringar för Windows Server 2008 och Microsoft SQL Server 2008.  Byol-modellen (bring-your-own-license) hjälper dig att hålla kostnaderna låga för vanliga appar som Veeam och Zerto.  
