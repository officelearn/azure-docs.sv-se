---
title: Översikt över och arkitektur för SAP HANA i Azure (stora instanser) | Microsoft Docs
description: Översikt över arkitekturen att distribuera SAP HANA i Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e3342f3057917202d81359a27accf47ba288b128
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Översikt över SAP HANA (stora instanser) och arkitektur på Azure

## <a name="what-is-sap-hana-on-azure-large-instances"></a>Vad är SAP HANA i Azure (stora instanser)?

SAP HANA i Azure (stora instanser) är en unik lösning till Azure. Förutom att tillhandahålla virtuella datorer för distribution och körning SAP HANA ger Azure dig möjlighet att köra och distribuera SAP HANA på bare metal-servrar som är dedikerad till dig. SAP HANA i Azure (stora instanser) lösningen bygger på icke-delade/server host bare metal-maskinvara som är tilldelad till dig. Servermaskinvaran är inbäddad i större stämplar som innehåller beräknings-server, nätverk och infrastruktur. Som en kombination är skräddarsydda HANA center dataintegrering (TDI) certifierade. SAP HANA i Azure (stora instanser) tillhandahåller annan server SKU: er eller storlekar. Enheter kan ha 72 processorer och 768 GB minne och gå till enheter som har 960 processorer och 20 TB minne.

Kunden isolering inom infrastruktur stämpeln utförs i klienter, som ser ut som:

- **Nätverk**: isolering av kunder i infrastrukturen stapel via virtuella nätverk per kund tilldelats klient. En klient har tilldelats en kund. En kund kan ha flera innehavare. Nätverksisolering av innehavare som förhindrar nätverkskommunikation mellan klienter i stämpel infrastrukturnivå även om innehavarna som tillhör samma kund.
- **Lagringskomponenter**: isolering via lagring virtuella datorer som har tilldelats lagringsvolymer. Lagringsvolymer kan tilldelas till lagring för en virtuell dator. En virtuell dator för lagring är tilldelad till en enskild klient i SAP HANA TDI certifierade infrastruktur stacken. Därför kan lagringsvolymer som tilldelats till en virtuell dator för lagring användas i en specifik och relaterade klienten endast. De inte visas mellan de olika distribuerade klienterna.
- **Server eller en värd**: en server eller en värd enhet inte delas mellan kunder eller klienter. En server eller en värd som har distribuerats till en kund är en atomisk bare metal-beräknings-enhet som är tilldelad till en enskild klient. *Inte* maskinvara partitionering eller mjuka partitionering används som kan resultera i du delar en värd eller en server med en annan kund. Lagringsvolymer som är kopplade till den virtuella datorn för lagring av specifika innehavaren är monterade på denna server. En klient kan ha ett till många server enheter av olika SKU: er som tilldelats exklusivt.
- Många olika klienter är inom en SAP HANA i Azure (stora instanser) infrastruktur stämpel distribueras och isolerade mot varandra via klient begrepp på nätverk, lagring och beräkning nivå. 


Dessa enheter bare metal-server stöds endast kör SAP HANA. SAP programnivå eller arbetsbelastning i mitten-ware layer körs i virtuella datorer. De stämplar som infrastruktur som kör SAP HANA på Azure (stora instanser)-enheter är anslutna till Azure-nätverk services backbones. På så sätt kan har låg latens anslutningen mellan SAP HANA på Azure (stora instanser)-enheter och virtuella datorer angetts.

Det här dokumentet är ett av flera dokument som beskriver SAP HANA i Azure (stora instanser). Det här dokumentet beskriver de grundläggande arkitektur, ansvarsområden och tjänster som tillhandahålls av lösningen. Avancerade funktionerna i lösningen beskrivs också. För de flesta andra områden, till exempel nätverk och anslutning, omfattar fyra dokument information och detaljerad information. I dokumentationen för SAP HANA i Azure (stora instanser) omfattar inte aspekter av SAP NetWeaver installationen eller distributioner av SAP NetWeaver i virtuella datorer. SAP NetWeaver på Azure ingår i separata dokument som finns i samma behållare i Azure-dokumentationen. 


Olika dokument på HANA stora instans vägledning täcker följande områden:

- [Översikt över SAP HANA (stora instanser) och arkitektur på Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastruktur för SAP HANA (stora instanser) och anslutningar på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Installera och konfigurera SAP HANA (stora instanser) på Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Felsökning för SAP HANA (stora instanser) och övervakning på Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Hög tillgänglighet som angetts i SUSE med hjälp av STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [OS-säkerhetskopiering och återställning för typ II SKU: er](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>Definitioner

Flera gemensamma definitioner används ofta i guiden arkitektur och teknisk distribution. Observera följande villkor och deras innebörd:

- **IaaS**: infrastruktur som en tjänst.
- **PaaS**: plattform som en tjänst.
- **SaaS**: programvara som en tjänst.
- **SAP komponenten**: ett enskilda SAP-program, till exempel ERP centrala komponenten (ECC), Business Warehouse (BW), lösning Manager eller Enterprise Portal (EP). SAP-komponenter kan baseras på traditionell ABAP eller Java-teknik eller icke-NetWeaver baserat program, till exempel Business-objekt.
- **SAP-miljö**: en eller flera SAP-komponenter som är logiskt grupperade för att utföra en business-funktion, till exempel utveckling, kvalitet, säkerhet, träning, katastrofåterställning eller produktion.
- **SAP liggande**: refererar till hela SAP tillgångarna i din IT-miljön. SAP-liggande innehåller alla produktions- och icke-produktionsmiljöer.
- **SAP-systemet**: kombinationen av DBMS lager- och programnivå av exempelvis en SAP ERP-utvecklingssystemet, ett testsystem för SAP BW och en SAP CRM-systemet för produktion. Azure-distributioner stöds inte mellan dessa två lager mellan lokala och Azure. En SAP-system är distribuerade lokalt eller dess distribuerade i Azure. Du kan distribuera de olika system avseende en SAP liggande i Azure eller lokalt. Du kan till exempel distribuera SAP CRM-utveckling och testsystem i Azure medan du distribuerar den SAP CRM produktion system lokalt. Den är avsedd att värd SAP programlager för SAP-system i virtuella datorer och relaterade SAP HANA-instans på en enhet i SAP HANA i Azure (stora instanser) stämpel för SAP HANA i Azure (stora instanser).
- **Stora instans stämpel**: maskinvara infrastruktur-stacken, som är certifierade SAP HANA TDI och dedikerad kör SAP HANA-instanser i Azure.
- **SAP HANA i Azure (stora instanser):** officiellt namn för erbjudandet i Azure att köra HANA instanser i på SAP HANA TDI-certifierad maskinvara som distribueras i stora instans tidsstämplar i olika Azure-regioner. Relaterade termen *HANA stora instans* är kort för *SAP HANA i Azure (stora instanser)* och som ofta används i den här guiden för teknisk distribution.
- **Anslutningar mellan lokala**: Beskriver ett scenario där virtuella datorer distribueras till en Azure-prenumeration som har plats-till-plats, flera platser eller Azure ExpressRoute-anslutning mellan lokala datacenter och Azure. Gemensamma Azure-dokumentationen dessa typer av distributioner beskrivs också som mellan lokala scenarier. Anslutningen orsaken är att utöka lokala domäner, lokala Azure Active Directory/OpenLDAP och lokala DNS i Azure. Lokala liggande utökas till Azure-prenumerationer Azure tillgångar. Med det här tillägget kan de virtuella datorerna inte ingå i den lokala domänen. 

   Domänanvändare på den lokala domänen kan åtkomst till servrarna och köra tjänster på dessa virtuella datorer (till exempel DBMS services). Kommunikation och namnmatchning mellan virtuella datorer distribueras lokalt och Azure distribuerade virtuella datorer är möjligt. Det här scenariot är typiska för det sätt som de flesta SAP resurser har distribuerats. Mer information finns i [planerings- och för Azure VPN-Gateway](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) och [skapa ett virtuellt nätverk med en plats-till-plats-anslutning med hjälp av Azure portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Klient**: en kund som distribuerats i HANA stora instans stämpel hämtar isolerade i en *klient.* En klient är separat i nätverk, lagring och beräkning lagret från andra klienter. Lagrings- och enheter som tilldelats till olika klienter kan inte se varandra eller kommunicera med varandra på HANA stora stämpel instansnivå. En kund kan du ha distributioner till olika klienter. Det finns även sedan ingen kommunikation mellan klienter i HANA stora stämpel instansnivå.
- **SKU-kategori**: för HANA stort instans, erbjuds följande två kategorier av SKU: er:
    - **Typen I klassen**: S72, S72m, S144, S144m, S192 och S192m
    - **Skriv II klassen**: S384, S384m, S384xm, S576m, S768m och S960m


En mängd ytterligare resurser är tillgängliga på hur du distribuerar en SAP arbetsbelastning i molnet. Om du planerar att köra en SAP HANA-distribution i Azure måste vara erfarenhet och medveten om principerna om Azure IaaS och distributionen av SAP arbetsbelastningar på Azure IaaS. Innan du fortsätter kan du se [Använd SAP-lösningar på Azure virtual machines](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för mer information. 

## <a name="certification"></a>Certifiering

Förutom NetWeaver-certifiering kräver SAP en särskild certifikatutfärdare för SAP HANA till stöd för SAP HANA i vissa infrastrukturer, till exempel Azure IaaS.

Core SAP-kommentar på NetWeaver och till en SAP HANA certifikatutfärdare grad är [SAP Obs #1928533 – SAP-program i Azure: produkter och Virtuella Azure-typer stöds](https://launchpad.support.sap.com/#/notes/1928533).

Den [SAP Obs #2316233 - SAP HANA i Microsoft Azure (stora instanser)](https://launchpad.support.sap.com/#/notes/2316233/E) är också viktig. Den omfattar lösningen som beskrivs i den här guiden. Dessutom stöds om du vill köra SAP HANA i GS5 VM-typen av Azure. Information om det här fallet har publicerats på [webbplatsen SAP](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

SAP HANA i Azure (stora instanser)-lösningen som anges i SAP Obs #2316233 ger Microsoft och SAP kunder möjlighet att distribuera stora SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA eller andra SAP HANA-arbetsbelastningar i Azure. Lösningen är baserad på stämpel för SAP HANA-certifierade dedikerad maskinvara ([SAP HANA skräddarsydda center dataintegrering – TDI](https://scn.sap.com/docs/DOC-63140)). Om du kör en SAP HANA TDI konfigurerade lösning fungerar alla SAP HANA-baserade program (till exempel SAP Business Suite på SAP HANA, SAP BW på SAP HANA och S4/HANA BW4/HANA) på maskinvara infrastruktur.

Den här lösningen har jämfört med SAP HANA i virtuella datorer en fördel. Den innehåller för mycket större volymer minne. Om du vill aktivera den här lösningen måste du förstå följande viktiga aspekter:

- SAP application layer och icke-SAP program körs i virtuella datorer som är värdar för stämplarna som vanligt Azure maskinvara.
- Kunden lokal infrastruktur, Datacenter och distribution av program som är ansluten till molnplattform via ExpressRoute (rekommenderas) eller ett virtuellt privat nätverk (VPN). Active Directory och DNS har också utökats till Azure.
- SAP HANA-databasinstans för HANA arbetsbelastning körs på SAP HANA i Azure (stora instanser). Stora instans stämpeln är ansluten till Azure-nätverk så att program som körs i virtuella datorer kan interagera med den HANA-instans som körs i stor HANA-instansen.
- Maskinvara för SAP HANA i Azure (stora instanser) är dedikerad maskinvara som anges i en IaaS med SUSE Linux Enterprise Server eller Red Hat Enterprise Linux förinstallerat. Precis som med virtuella datorer, ytterligare är uppdateringar och underhåll av operativsystemet ansvarig för.
- Installation av HANA eller de ytterligare komponenterna som behövs för att köra SAP HANA på enheter för stora HANA-instansen är ansvarig för. Alla respektive pågående åtgärder och administration av SAP HANA i Azure är också ansvarig.
- Du kan installera andra komponenter i din Azure-prenumeration som ansluter till SAP HANA i Azure (stora instanser) utöver de lösningar som beskrivs här. Exempel är komponenter som aktiverar kommunikation med eller direkt till SAP HANA databasservrar, till exempel hopp servrar RDP, SAP HANA Studio SAP datatjänster för SAP BI scenarier eller nätverk övervakar lösningar.
- Som i Azure har HANA stora instans stöd för hög tillgänglighet och katastrofåterställning återställning.

## <a name="architecture"></a>Arkitektur

SAP HANA i Azure (stora instanser)-lösningen har SAP Applikationsnivån som finns i virtuella datorer på en hög nivå. Lagret databasen finns på SAP TDI konfigurerade maskinvara som finns i en stor instans stämpel i samma Azure-region som är ansluten till Azure IaaS.

> [!NOTE]
> Distribuera SAP programnivå i samma Azure-region som SAP DBMS-lager. Den här regeln är väl dokumenterat i publicerad information om SAP arbetsbelastningar i Azure. 

Den övergripande arkitekturen för SAP HANA i Azure (stora instanser) innehåller en SAP TDI-certifierade maskinvarukonfigurationen, vilket är en icke-virtualiserade, bare metal, högpresterande server för SAP HANA-databas. Det ger också möjlighet och flexibilitet i Azure att skala resurser för SAP Applikationsnivån dina behov.

![Översikt över arkitekturen för SAP HANA i Azure (stora instanser)](./media/hana-overview-architecture/image1-architecture.png)

Arkitektur som visas är indelat i tre delar:

- **Höger**: Visar en lokal infrastruktur som kör olika program i data resurser så att slutanvändarna kan komma åt LOB-program, till exempel SAP. Vi rekommenderar detta lokala infrastruktur sedan är ansluten till Azure med [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Center**: Visar Azure IaaS och i så fall använder virtuella datorer som värd för SAP eller andra program som använder SAP HANA som ett DBMS-system. Mindre HANA-instanser som fungerar med det minne som innehåller virtuella datorer distribueras på virtuella datorer tillsammans med deras programnivå. Mer information om virtuella datorer finns [virtuella datorer](https://azure.microsoft.com/services/virtual-machines/).

   Azure nätverkstjänster för att gruppera SAP-system tillsammans med andra program i virtuella nätverk. Dessa virtuella nätverk att ansluta till lokalt system samt att SAP HANA i Azure (stora instanser).

   SAP NetWeaver program och databaser som stöds för att köras i Azure finns [SAP stöd Obs #1928533 – SAP-program i Azure: produkter och Virtuella Azure-typer stöds](https://launchpad.support.sap.com/#/notes/1928533). Dokumentation om hur du distribuerar SAP-lösningar i Azure finns:

  -  [Använda SAP på virtuella Windows-datorer](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Använda SAP-lösningar på virtuella Azure-datorer](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Vänster**: Visar SAP HANA TDI-certifierad maskinvara i Azure stora instans stämpeln. HANA stora instans-enheter är anslutna till de virtuella nätverken för din prenumeration genom att använda samma teknik som anslutningen från lokala till Azure.

Azure stora instans stämpeln själva kombinerar följande komponenter:

- **Beräkna**: servrar som är baserade på Intel Xeon E7-8890v3 eller Intel Xeon E7-8890v4 processorer som ger den nödvändiga kapaciteten för databehandling och är SAP HANA-certifierad.
- **Nätverket**: en enhetlig snabb nätverksinfrastruktur som sammanbinder bearbetning, lagring och LAN-komponenter.
- **Lagring**: en lagringsinfrastruktur som nås via en enhetlig nätverksinfrastruktur. Viss lagringskapacitet som tillhandahålls är beroende av den specifika SAP HANA på Azure (stora instanser) konfiguration som har distribuerats. Mer lagringskapacitet är tillgänglig på en månatlig extra kostnad.

Kunder distribueras i flera innehavare-infrastruktur för stora instans stämpeln som isolerade klienter. Vid distribution av klienten namnge en Azure-prenumeration i Azure-registrering. Den här Azure-prenumeration är det som HANA stora instans faktureras mot. Dessa klienter ha en 1:1-relation till Azure-prenumerationen. Det är möjligt att komma åt en enhet för HANA stora instans distribuerad i en klient i en Azure-region från olika virtuella nätverk som tillhör olika Azure-prenumerationer för ett nätverk. De Azure-prenumerationerna måste tillhöra samma Azure registreringen. 

Precis som med virtuella datorer, erbjuds SAP HANA i Azure (stora instanser) i Azure-regioner. För att erbjuda disaster recovery-funktioner, kan du välja. Olika stora instans stämplar inom en geo politiska region är anslutna till varandra. Till exempel är HANA stora instans tidsstämplar i West för oss och vi Öst anslutna via ett dedikerat nätverkslänk för disaster recovery replikering. 

Precis som du kan välja mellan VM av olika typer med Azure Virtual Machines, kan du välja mellan olika SKU: er av HANA stora-instans som är skräddarsydda för arbetsbelastning för olika typer av SAP HANA. SAP gäller minne till processor socket förhållandet för olika arbetsbelastningar baserat på Intel processor generationer. I följande tabell visas vilka SKU som erbjuds.

Från och med juli 2017 är SAP HANA i Azure (stora instanser) tillgänglig i flera konfigurationer i Azure-regioner oss Väst och oss Öst, östra, Australien, sydost, västra Europa och Norra Europa.

| SAP-lösning | Processor | Minne | Storage | Tillgänglighet |
| --- | --- | --- | --- | --- |
| Optimerad för OLAP-: SAP BW BW/4HANA<br /> eller SAP HANA för allmänna OLAP-arbetsbelastning | SAP HANA på Azure S72<br /> – 2 x Intel Xeon®-Processor E7 8890 v3<br /> 36 CPU-kärnor och 72 CPU-trådar |  768 GB |  3 TB | Tillgänglig |
| --- | SAP HANA på Azure S144<br /> – 4 x Intel Xeon®-Processor E7 8890 v3<br /> 72 CPU-kärnor och 144 CPU-trådar |  1,5 TB |  6 TB | Erbjuds inte längre |
| --- | SAP HANA på Azure S192<br /> – 4 x Intel Xeon®-Processor E7 8890 v4<br /> 96 CPU-kärnor och 192 CPU-trådar |  2.0 TB |  8 TB | Tillgänglig |
| --- | SAP HANA på Azure S384<br /> – 8 x Intel Xeon®-Processor E7 8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  4.0 TB |  16 TB | Tillgänglig |
| Optimerad för OLTP: SAP Business Suite<br /> på SAP HANA eller S/4HANA (OLTP)<br /> allmän OLTP | SAP HANA på Azure S72m<br /> – 2 x Intel Xeon®-Processor E7 8890 v3<br /> 36 CPU-kärnor och 72 CPU-trådar |  1,5 TB |  6 TB | Tillgänglig |
|---| SAP HANA på Azure S144m<br /> – 4 x Intel Xeon®-Processor E7 8890 v3<br /> 72 CPU-kärnor och 144 CPU-trådar |  3.0 TB |  12 TB | Erbjuds inte längre |
|---| SAP HANA på Azure S192m<br /> – 4 x Intel Xeon®-Processor E7 8890 v4<br /> 96 CPU-kärnor och 192 CPU-trådar  |  4.0 TB |  16 TB | Tillgänglig |
|---| SAP HANA på Azure S384m<br /> – 8 x Intel Xeon®-Processor E7 8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  6.0 TB |  18 TB | Tillgänglig |
|---| SAP HANA på Azure S384xm<br /> – 8 x Intel Xeon®-Processor E7 8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  8.0 TB |  22 TB |  Tillgänglig |
|---| SAP HANA på Azure S576m<br /> – 12 x Intel Xeon®-Processor E7 8890 v4<br /> 288 CPU-kärnor och 576 CPU-trådar |  12,0 TB |  28 TB | Tillgänglig |
|---| SAP HANA på Azure S768m<br /> – 16 x Intel Xeon®-Processor E7 8890 v4<br /> 384 CPU-kärnor och 768 CPU-trådar |  16,0 TB |  36 TB | Tillgänglig |
|---| SAP HANA på Azure S960m<br /> – 20 x Intel Xeon®-Processor E7 8890 v4<br /> 480 CPU-kärnor och 960 CPU-trådar |  20,0 TB |  46 TB | Tillgänglig |

- CPU-kärnor = summan av icke-hypertrådade CPU-kärnor av summan av processorer för server-enhet.
- CPU-trådar = summan av beräknings-trådar som tillhandahålls av hyper-threaded processorkärnor av summan av processorer för server-enhet. Alla enheter som är konfigurerade som standard att använda flertrådsteknik.


Specifika konfigurationer som valts är beroende av arbetsbelastning, CPU-resurser och önskade minne. Det är möjligt för OLTP-arbetsbelastning att använda SKU: er som är optimerade för OLAP-arbetsbelastning. 

Maskinvara för alla erbjudanden är SAP HANA TDI-certifierad. Två olika klasser av maskinvara delar upp SKU: er till:

- S72, S72m, S144, S144m, S192 och S192m, som kallas ”typen I klassen” av SKU: er.
- S384, S384m, S384xm, S576m, S768m och S960m som kallas den ”typ II klass” av SKU: er.

En fullständig HANA stora instans stämpel exklusivt är inte tilldelade för en kund&#39;s användning. Detta gäller racken av beräkning och lagring resurser som är anslutna via en nätverksinfrastruktur samt distribuerade i Azure. HANA stora instans infrastruktur som Azure, distribuerar annan kund &quot;hyresgäster&quot; som är isolerade från varandra i följande tre nivåer:

- **Nätverket**: isolering via virtuella nätverk i stämpeln HANA stora instans.
- **Lagring**: isolering via lagring virtuella datorer som har tilldelats lagringsvolymer och isolera lagringsvolymer mellan klienter behålls.
- **Beräkna**: dedikerad tilldelning av server-enheter till en enskild klient. Nej hårddisk eller soft partitionering av server-enheter. Inga delar av en enskild server eller en värd enhet mellan klienter behålls. 

Distributioner av HANA stora instans enheter mellan olika klienter är inte synliga för varandra. HANA stora instans enheter som distribueras i olika klienter inte kan kommunicera direkt med varandra på HANA stora stämpel instansnivå. Endast HANA stora instans enheter inom en klient kan kommunicera med varandra på HANA stora stämpel instansnivå.

En distribuerad klient i stora instans stämpeln tilldelas till en Azure-prenumeration för fakturering. Ett nätverk i nås den från virtuella nätverk för andra Azure-prenumerationer inom samma Azure registreringen. Om du distribuerar med en annan Azure-prenumeration i samma Azure-region måste kan också du be om en åtskilda HANA stora instans-klient.

Det finns betydande skillnader mellan kör SAP HANA på HANA stora instansen och SAP HANA körs på virtuella datorer som distribueras i Azure:

- Det finns ingen virtualiseringsskiktet för SAP HANA i Azure (stora instanser). Du får den underliggande maskinvaran bare metal-prestanda.
- Till skillnad från Azure, är SAP HANA på Azure (stora instanser)-server dedikerad till en viss kund. Det går inte att en server-enhet eller värden är hårt eller soft partitionerade. Därför används en HANA stora instans-enhet som tilldelats som helhet till en klient och som du. En omstart eller avstängning av servern leda inte automatiskt till operativsystemet och SAP HANA som distribueras på en annan server. (För typ I klassen SKU: er, det enda undantaget är om en server stöter på problem och Omdistributionen måste utföras på en annan server.)
- Till skillnad från Azure, där värden processor väljs för förhållandet bästa pris och prestanda, är processor-typer som valts för SAP HANA i Azure (stora instanser) raden Intel E7v3 och E7v4 processor högsta prestanda.


### <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Köra flera SAP HANA-instanser på en HANA stora instans-enhet
Det är möjligt att ha mer än en aktiv SAP HANA-instans på HANA stora instans enheter. För att ge funktioner för ögonblicksbilder för lagring och katastrofåterställning, kräver en sådan konfiguration en volym som angetts per instans. För närvarande kan HANA stora instans enheter delas upp på följande sätt:

- **S72, S72m, S144, S192**: I steg om 256 GB, med 256 GB minsta första enhet. Inkrement, till exempel 256 GB och 512 kan kombineras till maximalt minne på enheten.
- **S144m och S192m**: I steg om 256 GB med 512 GB den minsta enheten. Inkrement som 512 GB och 768 kan kombineras till maximalt minne på enheten.
- **Skriv II klassen**: I steg om 512 GB, med den minsta första enheten 2 TB. Inkrement som 512 GB, 1 TB och 1,5 TB kan kombineras till maximalt minne på enheten.

Några exempel på flera SAP HANA-instanser körs kan se ut ungefär så här.

| SKU | Minnesstorlek | Lagringsstorlek | Storlekar med flera databaser |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 x 768 GB HANA instans<br /> eller 1 x 512 GB instans + 1 x 256 GB instans<br /> eller 3 x 256 GB instanser | 
| S72m | 1,5 TB | 6 TB | 3x512GB HANA instanser<br />eller 1 x 512 GB instans + 1 x 1 TB instans<br />eller 6 x 256 GB instanser<br />eller 1x1.5 TB instans | 
| S192m | 4 TB | 16 TB | 8 x 512 GB instanser<br />eller 4 x 1 TB instanser<br />eller 4 x 512 GB instanser + 2 x 1 TB instanser<br />eller 4 x 768 GB instanser + 2 x 512 GB instanser<br />eller 1 × 4 TB-instans |
| S384xm | 8 TB | 22 TB | 4 x 2 TB instanser<br />eller 2 × 4 TB instanser<br />eller instanser 2 x 3 TB + 1 x 2 TB instanser<br />eller instanser 2x2.5 TB + 1 x 3 TB instanser<br />eller 1 x 8 TB instans |


Det finns även andra ändringar. 

### <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Använd SAP HANA data skiktning och tillägget noder
SAP stöder en lagringsnivåer datamodell för SAP BW i olika utgåvor av SAP NetWeaver och SAP BW/4HANA. Mer information om lagringsnivåer datamodellen Se SAP-dokumentet [SAP BW/4HANA och SAP BW på HANA med SAP HANA tillägget noder](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Du kan använda alternativ 1 konfigurationen av noder för SAP HANA-tillägg som beskrivs i vanliga frågor och SAP blogg dokument med stor HANA-instans. Alternativ 2 konfigurationer kan ställas in med de följande HANA stora instans SKU: er: S72m, S192, S192m, S384 och S384m. 

När du granskar dokumentationen kanske fördelen inte visas omedelbart. Men när du tittar på SAP sizing riktlinjer du kan se en fördel med hjälp av alternativet-1 och alternativ 2 SAP HANA tillägget noder. Här följer exempel:

- Riktlinjer för SAP HANA sizing kräver vanligen dubbelt så mycket datavolym som minne. När du kör din SAP HANA-instans med varm data du har bara 50 procent eller mindre minne fylls med data. Resten av minnet som helst lagras för SAP HANA gör sitt arbete.
- Innebär att i en HANA stora instans S192 enhet med 2 TB minne, kör en SAP BW-databas du endast har 1 TB som datavolym.
- Om du använder en ny nod i alternativ 1 gäller SAP HANA-tillägg också en S192 HANA stora instans SKU, den ger dig en ytterligare 2 TB kapacitet för datavolym. I alternativ 2-konfiguration får du en ytterligare 4 TB för varmt datavolym. Jämfört med den aktiva noden kan fullständig minneskapacitet för tilläggsnoden ”varmt” användas för lagring av data för alternativet-1. Dubbla minnet som kan användas för datavolymen i alternativ 2 nodkonfiguration för SAP HANA-tillägget.
- Du lyckas med en kapacitet på 3 TB för dina data och förhållandet hot till varmt 1:2 för alternativet-1. Du har 5 TB data och förhållandet 1:4 med nodkonfiguration alternativ 2-tillägget.

Ju högre datavolym jämfört med minnet, desto högre risken är att du ber för varmt data lagras på diskutrymmet.


## <a name="operations-model-and-responsibilities"></a>Modellen verksamhet och ansvarsområden

Den tillhandahållna med SAP HANA i Azure (stora instanser) är justerad mot Azure IaaS-tjänster. Du får en instans av en stor HANA-instans med ett installerat operativsystem som är optimerad för SAP HANA. Som med Azure IaaS-VM de flesta av härdning av Operativsystemet, installera ytterligare programvara, installera HANA, OS och HANA och uppdatera OS och HANA uppgifter är ansvarig för. Microsoft göra inte OS-uppdateringar eller HANA uppdateringar på du.

![Ansvaret för SAP HANA i Azure (stora instanser)](./media/hana-overview-architecture/image2-responsibilities.png)

I diagrammet visas är SAP HANA i Azure (stora instanser) en flera innehavare erbjuder IaaS. Till största del är divisionen av ansvar i kolumnrubrikens OS-infrastruktur. Microsoft ansvarar för alla aspekter av tjänsten under raden i operativsystemet. Du är ansvarig för alla aspekter av tjänsten ovanför raden. Du är ansvarig för Operativsystemet. Du kan fortsätta att använda den mest aktuella lokalt metoder du kan använda för kompatibilitet, säkerhet, programhantering, bas och OS-hantering. System som visas som om de finns i nätverket alla angående.

Den här tjänsten är optimerad för SAP HANA så att det finns områden där du behöver arbeta med Microsoft för att använda funktionerna för underliggande infrastruktur för bästa resultat.

I följande lista finns mer information på varje lager och dina ansvarsområden:

**Nätverk**: alla interna nätverk för stora instans stämpeln kör SAP HANA. Ditt ansvar inbegriper tillgång till lagring, anslutning mellan instanser (för skalbar och andra funktioner), anslutning till liggande och anslutningen till Azure där lager för SAP-program finns i virtuella datorer. Den omfattar också WAN-anslutning mellan Azure-Datacenter för disaster recovery syften replikering. Alla nätverk partitioneras av klienten och har tjänstkvalitet tillämpas.

**Lagring**: virtualiserade partitionerad lagring för alla volymer som krävs av SAP HANA-servrar och för ögonblicksbilder. 

**Servrar**: särskilda fysiska servrar att köra SAP HANA DBs som tilldelats till klienter. Servrar i typen I klassen SKU: er är maskinvara tas ut. Med dessa typer av servrar, serverkonfigurationen samlas in och underhålls i profiler som kan flyttas från en fysisk maskinvara till en annan fysisk maskinvara. Sådana en (manuella) flyttning av en profil av åtgärder kan jämföras lite med återställning av Azure-tjänsten. Servrar i SKU: er för typ II klassen erbjuder inte en sådan funktion.

**SDDC**: programvaran för hantering som används för att hantera data datacenter som programvarudefinierade enheter. Tillåter Microsoft att resurser för skala, tillgänglighet och prestanda.

**Operativsystem**: OS du väljer (SUSE Linux eller Red Hat Linux) som körs på servrarna. Operativsystemsavbildningar som du angav med tillhandahölls av enskilda Linux-leverantören till Microsoft för att köra SAP HANA. Du måste ha en prenumeration med Linux-leverantören för den specifika bilden för SAP HANA-optimerad. Du ansvarar för att registrera avbildningar med OS-leverantören. 

Du är ansvarig för eventuella ytterligare korrigering av Linux-operativsystem från punkten på övergång av Microsoft. Denna uppdatering innehåller ytterligare paket som kan krävas för en lyckad SAP HANA-installation och som inte togs med specifika Linux-leverantör i sina SAP HANA optimerade operativsystemsavbildningar. (Mer information finns i SAP: s HANA dokumentationen och SAP anteckningar.) 

Du är ansvarig för OS-korrigering på grund av fel eller optimering av Operativsystemet och drivrutiner i förhållande till specifika servermaskinvaran. Du är också ansvarig för säkerhets- eller funktionella korrigering av OS. 

Ditt ansvar innehåller också övervakning och kapacitetsplanering för:

- CPU-resursanvändningen.
- Minnesanvändningen.
- Volymer som är relaterade till ledigt utrymme, IOPS och svarstid.
- Volymen nätverkstrafik mellan HANA stora instans och programnivå SAP.

Den underliggande infrastrukturen för stora HANA-instansen innehåller funktioner för säkerhetskopiering och återställning av systemvolymen. Med den här funktionen är också ansvarig för.

**Mellanprogram**: SAP HANA instansen i första hand. Är du ansvarig för administration, åtgärder och övervakning. Du kan använda de angivna funktionerna för att använda ögonblicksbilder för lagring för säkerhetskopiering och återställning och disaster recovery. Dessa funktioner tillhandahålls av infrastrukturen. Dina ansvarsområden också innefatta skapar hög tillgänglighet och katastrofåterställning med dessa funktioner, övervakning och använda dem att avgöra om lagring ögonblicksbilder har lyckats.

**Data**: dina data hanteras av SAP HANA och andra data, till exempel säkerhetskopieringar delar filer i volymer eller fil. Dina ansvarsområden innehåller övervaka ledigt diskutrymme och hantera innehåll på volymerna. Du är också ansvarig för övervakning av säkerhetskopior av volymer på diskar och lagring ögonblicksbilder har körts. Datareplikering till katastrofåterställningsplatser har körts ansvarar av Microsoft.

**Program:** programinstanser SAP eller, för icke-SAP-program, programlager för programmen. Dina ansvarsområden innehåller distribution, administration, åtgärder och övervakning av dessa program. Du är ansvarig för kapacitetsplanering för CPU-resursanvändningen, minnesförbrukning, Azure Storage-förbrukning och förbrukningen av nätverksbandbredd i virtuella nätverk. Du är också ansvarig för kapacitetsplanering för förbrukning av nätverksresurser från virtuella nätverk till SAP HANA i Azure (stora instanser).

**WAN**: anslutningar som du har skapat från lokal till Azure-distributioner för arbetsbelastningar. Alla kunder med HANA stora instans använder Azure ExpressRoute-anslutningen. Den här anslutningen är inte en del av SAP HANA i Azure (stora instanser)-lösningen. Du är ansvarig för installationen av den här anslutningen.

**Arkivera**: du kanske föredrar att arkivera kopior av data med egna metoder i storage-konton. Arkivering kräver hantering, kompatibilitet, kostnader och åtgärder. Du ansvarar för att arkivera kopior och säkerhetskopieringar på Azure och lagra dem på ett sätt som är kompatibla.

Finns det [SLA för SAP HANA i Azure (stora instanser)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Storleksändring

Storlek för stora HANA-instansen är inte annorlunda än storleken för HANA i allmänhet. För befintliga och distribuerade system som du vill flytta från andra RDBMS till HANA, SAP innehåller ett antal rapporter som körs på din befintliga SAP-system. Om databasen flyttas till HANA rapporterna Kontrollera informationen och beräkna minneskrav för HANA-instansen. Läs följande SAP-information för mer information om hur du kör dessa rapporter och hämta de senaste korrigeringsfiler eller versioner:

- [SAP-kommentar #1793345 - storlek för SAP Suite på HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Obs #1872170 - Suite HANA och S/4 HANA storlek för rapporten](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP-kommentar #2121330 – vanliga frågor och svar: SAP BW på HANA storleksanpassa rapport](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Obs #1736976 - storlek för rapporten för BW på HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Obs #2296290 - storleksanpassa ny rapport för BW på HANA](https://launchpad.support.sap.com/#/notes/2296290)

För grön fältet implementeringar är SAP snabbt ange storlek tillgänglig att beräkna minneskrav för implementering av SAP-program ovanpå HANA.

Minneskrav för HANA öka när datavolymen växer. Tänk på din aktuella minnesanvändningen för att förutsäga vad den ska vara i framtiden. Baserat på minneskrav kan sedan du mappa din begäran till en HANA-SKU: er för stort instans.

## <a name="requirements"></a>Krav

Den här listan monterar krav för SAP HANA i Azure (större instanser).

**Microsoft Azure**

- En Azure-prenumeration som kan kopplas till SAP HANA i Azure (stora instanser).
- Microsoft Premier support-kontrakt. Specifik information som rör körs SAP i Azure finns [SAP stöd Obs #2015553 – SAP på Microsoft Azure: stöd för krav](https://launchpad.support.sap.com/#/notes/2015553). Om du använder HANA stora instans enheter med 384 och fler processorer, måste du också att utöka Premier support-kontrakt med Azure snabba svar.
- Medvetenhet om den HANA stora instans SKU: er måste när du har en storlek Övning med SAP.

**Nätverksanslutning**

- ExpressRoute mellan lokala till Azure: för att ansluta din lokala datacentret till Azure, se till att sortera på minst 1 Gbit/s-anslutning från Leverantören. 

**Operativsystem**

- Licenser för SUSE Linux Enterprise Server 12 för SAP-program.

   > [!NOTE] 
   > Operativsystem som levereras av Microsoft är inte registrerad med SUSE. Det är inte ansluten till en prenumeration hanteringsverktyg-instans.

- SUSE Linux prenumeration hanteringsverktyg distribuerade i Azure på en virtuell dator. Det här verktyget kan du för SAP HANA i Azure (stora instanser) ska registreras och respektive uppdateras av SUSE. (Det finns ingen internet-åtkomst inom datacentret HANA stora instans.) 
- Licenser för Red Hat Enterprise Linux 6.7 eller 7.2 för SAP HANA.

   > [!NOTE]
   > Operativsystem som levereras av Microsoft är inte registrerad med Red Hat. Det är inte ansluten till en instans för Red Hat prenumeration Manager.

- Red Hat prenumeration Manager distribueras i Azure på en virtuell dator. Red Hat prenumeration Manager innehåller möjligheten för SAP HANA i Azure (stora instanser) ska registreras och respektive uppdateras av Red Hat. (Det finns ingen direkt Internetåtkomst från innehavaren distribueras på Azure stora instans stämpel.)
- SAP måste du ha en supportbegäran kontrakt med leverantören Linux. Det här kravet är inte bort av lösningen HANA stora instans eller det faktum att du kör Linux i Azure. Till skillnad från vissa galleriavbildningar Linux Azure avgifter är *inte* ingår i erbjudandet för lösning av stora HANA-instansen. Det är ditt ansvar att uppfylla kraven för SAP om supportavtal med Linux-distributören. 
   - Leta upp supportavtal i krav för SUSE Linux [SAP Obs #1984787 - SUSE Linux Enterprise Server 12: installationsinformation](https://launchpad.support.sap.com/#/notes/1984787) och [SAP Obs #1056161 - SUSE prioritetsstöd för SAP-program](https://launchpad.support.sap.com/#/notes/1056161).
   - Du måste ha nivåerna korrekt prenumeration som innehåller stöd för och hantera uppdateringar i operativsystem för stora HANA-instansen för Red Hat Linux. Red Hat rekommenderar Red Hat Enterprise Linux för [SAP-lösningar] (https://access.redhat.com/solutions/3082481 prenumeration. 

Stöd matrix för de olika SAP HANA-versionerna med de olika versionerna av Linux, se [SAP Obs #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Kompatibilitetsmatrix för operativsystemet och HLI inbyggd programvara och drivrutin versioner finns [OS uppgradering för HLI](os-upgrade-hana-large-instance.md).


**Databas**

- Licenser och installation av programkomponenter för SAP HANA (plattform eller enterprise edition).

**Program**

- Licenser och installation av programkomponenter för SAP-program som ansluter till SAP HANA och relaterade SAP supportavtal.
- Licenser och programvarukomponenter i installationen för alla icke-SAP-program används i förhållande till SAP HANA i Azure (stora instanser) miljöer och relaterade supportavtal.

**Kunskaper**

- Erfarenhet av och kunskap om Azure IaaS och dess komponenter.
- Erfarenhet av och kunskap om hur du distribuerar en SAP arbetsbelastning i Azure.
- Installationen av SAP HANA certifierad personal.
- SAP systemarkitekt kunskaper för att utforma hög tillgänglighet och disaster recovery runt SAP HANA.

**SAP**

- Förutsättningen är att du är en SAP-kund och har stöd för ett kontrakt med SAP.
- Kontakta SAP på versioner av SAP HANA och eventuell konfigurationer i stor skala upp maskinvara särskilt för implementeringar av klassen typ II av HANA stora instans SKU: er.


## <a name="storage"></a>Storage

Lagringslayout för SAP HANA i Azure (stora instanser) har konfigurerats av SAP HANA på den klassiska distributionsmodellen via SAP rekommenderade riktlinjer. Riktlinjerna som finns dokumenterade i den [lagringskraven för SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) vitboken.

HANA stora instans av typen I klassen har fyra gånger minne volymen lagringsvolym. Lagringen är fyra gånger större för klassen typ II HANA stora instans enheter. Enheterna som har en volym som är avsedd för att lagra HANA säkerhetskopieringar av transaktionsloggen. Mer information finns i [installera och konfigurera SAP HANA (stora instanser) på Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Se tabellen nedan vad gäller lagringsallokering. Tabellen innehåller grov kapacitet för olika volymer med olika HANA stora instans-enheter.

| HANA stora instans SKU | Hana-data | Hana/logg | Hana/delat | Hana/loggsäkerhetskopiering |
| --- | --- | --- | --- | --- |
| S72 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1 280 GB | 768 GB |
| S192 | 4 608 GB | 1 024 GB | 1,536 GB | 1 024 GB |
| S192m | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384 | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384m | 12 000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S384xm | 16 000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S576m | 20 000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S768m | 28 000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S960m | 36,000 GB | 4,100 GB | 2,050 GB | 4,100 GB |


Faktiska distribuerade volymer kan variera beroende på distribution och verktyg som används för att visa Volymstorlekar.

Om du dela upp en HANA stora instans SKU kan se några exempel på uppdelning delar ut:

| Minnespartition i GB | Hana-data | Hana/logg | Hana/delat | Hana/loggsäkerhetskopiering |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1 024 GB | 640 GB |
| 1,536 | 3,328 GB | 768 GB | 1 280 GB | 768 GB |


De här storlekarna är grov volym siffror som kan variera något baserat på distributionen och de verktyg som används för att titta på volymerna. Det finns också andra partitionsstorlek, till exempel 2,5 TB. Dessa lagringsstorlekar beräknas med en formel som liknar den som användes för tidigare partitioner. Termen ”partitioner” betyder att operativsystem, minne eller CPU-resurser på något sätt partitioneras. Anger det partitioner för lagring för olika HANA instanser kanske du vill distribuera på en enda HANA stora instans-enhet. 

Du kan behöva mer lagringsutrymme. Du kan lägga till lagring genom att köpa ytterligare lagringsutrymme i enheter som 1 TB. Den här ytterligare lagringsutrymme kan läggas till som ytterligare volym. Den kan även användas för att utöka en eller flera av de befintliga volymerna. Det går inte att minska storleken på volymerna som ursprungligen har distribuerats och främst dokumenterats i föregående tabell. Det är inte heller går att ändra namnen på volymerna eller montera namn. Lagringsvolymer som beskrevs tidigare är kopplade till HANA stora instans-enheter som NFS4 volymer.

Du kan använda ögonblicksbilder för lagring för säkerhetskopiering och återställning och disaster recovery. Mer information finns i [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="encryption-of-data-at-rest"></a>Kryptering av vilande data
Det lagringsutrymme som används för HANA stora instansen kan en transparent kryptering av data som lagras på diskarna. När en enhet HANA stora instans har distribuerats kan aktivera du den här typen av kryptering. Du kan också ändra till krypterade volymer när distributionen har inträffat. Flytta från en icke-krypterade till krypterade volymer är transparent och behöver inte driftstopp. 

Typen I klassen av SKU: er, volymen Start LUN lagras på är krypterad. För typ II-klassen för SKU: er av HANA stora instans måste du kryptera Start LUN med OS-metoder. Mer information kontaktar du Microsoft Service Management-teamet.


## <a name="networking"></a>Nätverk

Arkitekturen i Azure nätverkstjänster är en viktig del av distributionen av SAP-program på stora HANA-instansen. SAP HANA på Azure (stora instanser)-distributioner har vanligtvis en större SAP-liggande med flera olika SAP-lösningar med olika storlekar för databaser, CPU-resursanvändningen och minnesanvändning. Det är troligt att inte alla dessa SAP-system är baserade på SAP HANA. SAP-liggande är förmodligen en hybridlösning som använder:

- Distribuerade SAP system lokalt. På grund av deras storlek får systemen för närvarande inte finnas i Azure. Ett exempel är en produktion SAP ERP-system som körs på SQL Server (som databasen) och kräver mer resurser som CPU eller minne än vad som virtuella datorer.
- Distribuera SAP HANA-baserade SAP system lokalt.
- Distribuerade SAP-system i virtuella datorer. Dessa system kan vara utveckling, testning, sandbox eller produktion instanser för något SAP NetWeaver-baserade program som kan distribuera i Azure (på VM: ar), baserat på resursen användnings- och behov. Dessa system kan också baseras på databaser som SQL Server. Mer information finns i [SAP stöd Obs #1928533 – SAP-program i Azure: produkter och Virtuella Azure-typer stöds](https://launchpad.support.sap.com/#/notes/1928533/E). Och dessa system kan baseras på databaser, till exempel SAP HANA. Mer information finns i [SAP HANA certifierade IaaS plattformar](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
- Distribuera SAP programservrar i Azure (på virtuella datorer) som utnyttjar SAP HANA i Azure (stora instanser) i Azure stora instans stämplar.

En hybrid SAP liggande med fyra eller flera olika distributionsscenarier är vanliga. Det finns också kunden ofta av fullständig SAP landskap som körs i Azure. När virtuella datorer blir mer kraftfulla, ökar antalet kunder som flyttar alla sina SAP-lösningar på Azure.

Azure-nätverk i samband med SAP-system som distribueras i Azure är inte svårt. Den är baserad på följande principer:

- Virtuella Azure-nätverk måste vara ansluten till ExpressRoute-kretsen som ansluter till ett lokalt nätverk.
- En ExpressRoute-krets som ansluter lokalt till Azure vanligtvis bör ha en bandbredd på 1 Gbit/s eller högre. Den här minimal bandbredd kan tillräcklig bandbredd för överföring av data mellan system som kör på virtuella datorer och lokalt system. Det gör också tillräcklig bandbredd för anslutning till Azure system från lokala användare.
- SAP-system i Azure måste ställas in i virtuella nätverk ska kunna kommunicera med varandra.
- Active Directory och DNS finns lokalt har utökats till Azure via ExpressRoute från lokala.


> [!NOTE] 
> Endast en Azure-prenumeration kan länkas till endast en klient i en stor instans stämpel i en viss Azure-region från fakturering synvinkel. En enda stor instans stämpel klient kan däremot länkas till endast en Azure-prenumeration. Det här kravet är konsekvent med andra fakturerbar objekt i Azure.

Om SAP HANA i Azure (stora instanser) distribueras i flera olika Azure-regioner, distribueras en separat klient i stora instans stämpeln. Du kan köra båda under samma Azure-prenumerationen så länge dessa instanser är en del av samma SAP liggande. 

> [!IMPORTANT] 
> Azure Resource Manager distribution stöds med SAP HANA i Azure (stora instanser).

 

### <a name="additional-virtual-network-information"></a>Information om ytterligare virtuella nätverk

För att ansluta ett virtuellt nätverk till ExpressRoute, måste du skapa en Azure-gateway. Mer information finns i [om virtuella nätverksgatewayerna expressroute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 

En Azure-gateway kan användas med ExpressRoute till en infrastruktur utanför Azure eller till en stor Azure-instans stämpel. En Azure-gateway kan också användas för att ansluta mellan virtuella nätverk. Mer information finns i [konfigurera nätverket att nätverksanslutningen för Resource Manager med hjälp av PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Du kan ansluta Azure gateway till högst fyra olika ExpressRoute-anslutningar så länge dessa anslutningar kommer från olika Microsoft enterprise edge routrar. Mer information finns i [SAP HANA (stora instanser) infrastruktur och anslutningar på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Genomflödet ger en Azure-gateway är olika för både användningsfall. Mer information finns i [om VPN-Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Maximalt dataflöde som du kan åstadkomma med en virtuell nätverksgateway är 10 Gbit/s med hjälp av en ExpressRoute-anslutning. Kopiering av filer mellan en virtuell dator som finns i ett virtuellt nätverk och ett system lokalt (som en enda kopia-ström) inte uppnå det totala genomflödet i en annan gateway-SKU: er. Använda flera strömmar för att kunna utnyttja fullständig bandbredden för den virtuella nätverksgatewayen. Eller kopierar olika filer i parallella strömmar av en enskild fil.


### <a name="networking-architecture-for-hana-large-instance"></a>Nätverksarkitekturen för stora HANA-instans
Nätverk arkitekturen för HANA stora instans kan delas in i fyra olika delar:

- Lokalt nätverk och ExpressRoute-anslutning till Azure. Den här delen är kundens domän och är ansluten till Azure via ExpressRoute. Finns det nedre högra hörnet i följande bild.
- Azure nätverkstjänster som tidigare diskuterats, virtuella nätverk som har gateways igen. Den här delen är ett område där du vill söka efter lämpliga Designer för programkrav-, säkerhets- och efterlevnadskrav. Om du använder HANA stora instans är en annan plats att tänka på vad gäller antalet virtuella nätverk och Azure gateway SKU: er kan välja mellan. Finns det övre högra hörnet i bilden.
- Anslutningar för stora HANA-instansen för ExpressRoute-teknik till Azure. Den här delen distribueras och hanteras av Microsoft. Allt du behöver göra är att tillhandahålla vissa IP-adressintervall efter distributionen av dina tillgångar i HANA stora instans ansluta ExpressRoute-kretsen för de virtuella nätverken. Mer information finns i [SAP HANA (stora instanser) infrastruktur och anslutningar på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Nätverk i stora HANA-instans som är främst transparent för dig.

![Virtuella nätverk är anslutna till SAP HANA på Azure (stora instanser) och lokalt](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Kravet att dina lokala tillgångar måste ansluta via ExpressRoute till Azure ändrar inte eftersom du använder HANA stora instans. Kravet på att ha en eller flera virtuella nätverk som kör virtuella datorer, som är värd för programmet lagret som ansluter till HANA-instanser som finns i HANA stora instans enheter, även ändras inte. 

Skillnader i SAP-distributioner i Azure är:

- HANA stora instans enheter av din klientorganisation, kund är anslutna via en annan ExpressRoute-krets i ditt virtuella nätverk. För att avgränsa belastningstillstånd dela inte lokal till virtuella nätverk ExpressRoute och länkar mellan virtuella nätverk och HANA stora instans samma routrar.
- Profilen arbetsbelastningen mellan SAP programlager och den stora HANA-instans är av olika slag med många små begäranden och belastning som dataöverföringar (resultatmängder) från SAP HANA i Applikationsnivån.
- Arkitektur för SAP-program är mer känslig för nätverks-svarstid än vanliga scenarier där data överförs mellan lokala och Azure.
- Den virtuella nätverksgatewayen har minst två ExpressRoute-anslutningar. Både anslutningar dela Maximal bandbredd för inkommande data för den virtuella nätverksgatewayen.

Nätverksfördröjning uppstod mellan virtuella datorer och HANA stora enheter kan vara högre än en typisk fördröjningen för VM-VM-nätverk. Beroende på Azure-regionen, de mätvärdena kan överstiga 0,7 ms fördröjningen klassificeras som under medel i [SAP Obs #1100926 – vanliga frågor och svar: nätverksprestanda](https://launchpad.support.sap.com/#/notes/1100926/E). Kunderna distribuera dock SAP HANA-baserade SAP produktionsprogram på stora SAP HANA-instans. Kunder som distribuerats rapporten bra förbättringar av sina SAP-program som körs på SAP HANA med hjälp av HANA stora instans enheter. Kontrollera att du testar dina affärsprocesser noggrant i Azure HANA stora instans.
 
För att ge deterministiska Nätverksfördröjningen mellan virtuella datorer och HANA stort instans, valet av den virtuella nätverksgatewayen SKU är viktigt. Till skillnad från trafikmönster mellan lokala och virtuella datorer utveckla trafikmönstret mellan virtuella datorer och HANA stora instans små men hög belastning för begäranden och datavolymer som ska överföras. Om du vill hantera sådana belastning och rekommenderar vi användning av UltraPerformance gateway SKU. Användningen av UltraPerformance gateway SKU som en virtuell nätverksgateway är obligatoriskt för klassen typ II av HANA stora instans SKU: er.

> [!IMPORTANT] 
> Total nätverkstrafik mellan SAP-program och databasen lager får stöds endast HighPerformance eller UltraPerformance gateway-SKU: er för virtuella nätverk för att ansluta till SAP HANA i Azure (stora instanser). För HANA stora instans typ II SKU: er stöds endast UltraPerformance gateway SKU som en virtuell nätverksgateway.



### <a name="single-sap-system"></a>Enda SAP-system

Den lokala infrastruktur som tidigare visas är anslutna via ExpressRoute i Azure. ExpressRoute-kretsen ansluter till en företagsgränsroutern. Mer information finns i [teknisk översikt för ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). När vägen upprättas anslutningen till Azure basen och alla Azure-regioner är tillgängliga.

> [!NOTE] 
> Kör SAP landskap i Azure genom att ansluta till företagsgränsroutern närmast Azure-regionen i SAP-miljön. Azure stora instans stämplar är anslutna via dedikerade enterprise router-gränsenheterna för att minimera Nätverksfördröjningen mellan virtuella datorer i Azure IaaS och stora instans stämplar.

Den virtuella nätverksgatewayen för de virtuella datorerna som är värdar för SAP programinstanser är ansluten till ExpressRoute-kretsen. Samma virtuella nätverk är ansluten till en separat företagsgränsroutern dedikerad till att ansluta till stora instans stämplar.

Det här systemet är ett enkelt exempel på ett enda SAP-system. Lager för SAP-program finns i Azure. SAP HANA-databas som körs på SAP HANA i Azure (stora instanser). Förutsättningen är att den virtuella nätverksbandbredden gateway 2 Gbit/s eller 10 Gbit/s genomströmning inte representerar en flaskhals.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Flera SAP-system eller stora SAP-system

Om flera SAP-system eller stora SAP system distribueras för att ansluta till SAP HANA i Azure (stora instanser), kan genomflödet av den virtuella nätverksgatewayen bli en flaskhals. I så fall kan dela program lager i flera virtuella nätverk. Du kan också skapa ett särskilt virtuellt nätverk som ansluter den till HANA stora fall som:

- Säkerhetskopiering direkt från HANA-instanser i stora HANA-instansen till en virtuell dator i Azure som värd för NFS-resurser.
- Kopiera stora säkerhetskopieringar och andra filer från HANA stora instans enheter till diskutrymme som hanteras i Azure.

Använd ett separat virtuellt nätverk till virtuella datorer som hanterar lagring-värden. Den här ordningen undviker effekterna av stora filer eller dataöverföring från HANA stora instans till Azure på den virtuella nätverksgatewayen som hanterar de virtuella datorer som kör SAP programnivå. 

För en mer skalbar nätverksarkitektur:

- Använda flera virtuella nätverk för ett enda, större SAP programmet lager.
- Distribuera en separat virtuellt nätverk för varje SAP-system som har distribuerats, jämfört med att kombinera dessa SAP-system i separata undernät under samma virtuella nätverk.

 En nätverk Mer skalbar arkitektur för SAP HANA i Azure (stora instanser):

![Distribuera SAP programnivå över flera virtuella nätverk](./media/hana-overview-architecture/image4-networking-architecture.png)

Bilden visar SAP programnivå eller komponenter, distribueras över flera virtuella nätverk. Den här konfigurationen introducerade oundvikligt latens kostnader som inträffade under kommunikation mellan de program som finns i de virtuella nätverk. Som standard nätverkstrafiken mellan virtuella datorer finns i olika virtuella nätverk vidarebefordra via routrar i utkanten enterprise i den här konfigurationen. Eftersom September 2016 kommer kan den här routning optimeras. 

Sättet att optimera och minska svarstiden för kommunikationen mellan två virtuella nätverk är att peering virtuella nätverk inom samma region. Den här metoden fungerar även om de virtuella nätverk finns i olika prenumerationer. Med virtuella nätverk peering kan kommunikationen mellan virtuella datorer i två olika virtuella nätverk använda Azure-nätverk basen kommunicera direkt med varandra. Latens visas som om de virtuella datorerna finns i samma virtuella nätverk. Trafik som IP-adressintervall som är anslutna via Azure virtuella nätverkets gateway-adresser dirigeras via enskilda virtuella nätverks-gatewayen för det virtuella nätverket. 

Mer information om virtuellt nätverk peering finns [virtuella nätverk peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Routning i Azure

Tre routning Nätverksöverväganden är viktiga för SAP HANA i Azure (stora instanser):

* SAP HANA i Azure (stora instanser) kan nås bara via virtuella datorer och den dedikerade ExpressRoute-anslutningen inte direkt från lokala. Direktåtkomst från lokal till HANA stora instans-enheter som levereras av Microsoft för dig, kan inte göras omedelbart. Transitiva routning begränsningarna beror på den aktuella Azure nätverksarkitekturen används för SAP HANA stora instans. Vissa klienter för administration och alla program som behöver direktåtkomst, till exempel SAP lösning Manager som körs lokalt, kan inte ansluta till SAP HANA-databas.

* Om du har stora HANA-instans enheter som distribueras i två olika Azure-regioner för katastrofåterställning gäller samma tillfälligt routning begränsningar. Med andra ord dirigeras IP-adresser för en enhet HANA stora instans i en region (till exempel oss West) inte till en enhet för stora HANA-instans distribuerad i en annan region (till exempel oss East). Den här begränsningen är oberoende av användningen av Azure-nätverk peering över regioner eller cross-anslutning ExpressRoute-kretsar som ansluter HANA stora instans enheter till virtuella nätverk. En grafisk representation finns i bild i avsnittet ”Använd HANA stora instans enheter i flera områden”. Den här begränsningen som medföljer distribuerad arkitektur förhindrar omedelbar användning av HANA System replikering när disaster recovery-funktioner.

* SAP HANA på Azure (stora instanser)-enheter har en tilldelad IP-adress från servern IP-pool-adressintervall som du har skickat. Mer information finns i [SAP HANA (stora instanser) infrastruktur och anslutningar på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Denna IP-adress är tillgänglig via Azure-prenumerationer och ExpressRoute som ansluter virtuella nätverk till HANA i Azure (stora instanser). IP-adress som tilldelats av server IP-adresspoolintervall tilldelas direkt till enheten maskinvara. Den har *inte* tilldelats via NAT längre skett i första distributionen av den här lösningen. 

> [!NOTE] 
> Om du vill lösa begränsningen i tillfälliga routning som beskrivs i de första två objekt, använder du ytterligare komponenter för routning. Komponenter som kan användas för att lösa begränsningen kan vara:

> * En omvänd-proxy som kan vidarebefordra data till och från. Exempelvis F5 BIG-IP, NGINX med Traffic Manager distribueras i Azure som en virtuell brandväggen-trafik routning lösning.
> * Med hjälp av [IPTables regler](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) i en Linux-VM för att aktivera routning mellan lokala platser och HANA stora instans enheter eller mellan HANA stora instans enheter i olika regioner.

> Tänk på att implementering och stöd för anpassade lösningar som berör från tredje part nätverks-utrustning eller IPTables inte tillhandahålls av Microsoft. Stöd måste tillhandahållas av tillverkaren av den komponent som används eller Integratorn. 

### <a name="internet-connectivity-of-hana-large-instance"></a>Internet-anslutning för stora HANA-instansen
HANA stora instans har *inte* har direkt Internetanslutning. Den här begränsningen kan exempelvis begränsa möjligheten att registrera den OS-avbildningen direkt med OS-leverantören. Du kan behöva arbeta med dina lokala hanteringsverktyg för SUSE Linux Enterprise Server-prenumeration servern eller Red Hat Enterprise Linux prenumeration Manager.

### <a name="data-encryption-between-vms-and-hana-large-instance"></a>Kryptering av data mellan virtuella datorer och stora HANA-instans
Data som överförs mellan HANA stora instansen och virtuella datorer är inte krypterad. Endast för exchange mellan HANA DBMS sida och JDBC/ODBC-baserade program, kan du aktivera kryptering av trafik. Mer information finns i [denna dokumentation av SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

### <a name="use-hana-large-instance-units-in-multiple-regions"></a>Använd HANA stora instans enheter i flera områden

Du kanske skälen till att distribuera SAP HANA i Azure (stora instanser) i flera Azure-regioner än för katastrofåterställning. Kanske vill du komma åt HANA stora instans från var och en av de virtuella datorerna som distribueras i olika virtuella nätverk i regioner. IP-adresser som hör till olika HANA stora instans enheterna sprids inte utöver de virtuella nätverk som är anslutna direkt via deras gateway till instanserna. Detta innebär introduceras en mindre ändring i virtuell nätverksdesign. En virtuell nätverksgateway kan hantera fyra olika ExpressRoute-kretsar utanför routrar i utkanten olika enterprise. Varje virtuellt nätverk som är ansluten till någon av stämplarna som stora instans kan vara ansluten till stora instans stämpel i en annan Azure-region.

![Virtuella nätverk är anslutna till Azure stora instans tidsstämplar i olika Azure-regioner](./media/hana-overview-architecture/image8-multiple-regions.png)

Bilden visar hur olika virtuella nätverk i båda områden är anslutna till två olika ExpressRoute-kretsar som används för att ansluta till SAP HANA i Azure (stora instanser) i både Azure-regioner. Nyligen införda anslutningar är rektangulär röda linjer. Med dessa anslutningar utanför de virtuella nätverk på virtuella datorer som körs i något av dessa virtuella nätverk har åtkomst till var och en av de olika HANA stora instans-enheter som distribueras i de två regionerna. Eftersom bilden visar förutsätts det att du har två ExpressRoute anslutningar från lokal till två Azure-regioner. Den här ordningen rekommenderas för disaster recovery orsaker.

> [!IMPORTANT] 
> Om du använder flera ExpressRoute-kretsar ska AS sökväg tillagt och lokala inställningar BGP inställningar användas för att säkerställa rätt routning av trafik.


