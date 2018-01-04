---
title: "Översikt över och arkitektur för SAP HANA i Azure (stora instanser) | Microsoft Docs"
description: "Översikt över arkitekturen att distribuera SAP HANA i Azure (stora instanser)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 09198355ecd862c73b728d8119bbf9d56e3b9f69
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Översikt över SAP HANA (stora instanser) och arkitektur på Azure

## <a name="what-is-sap-hana-on-azure-large-instances"></a>Vad är SAP HANA i Azure (stora instanser)?

SAP HANA i Azure (stora instans) är en unik lösning till Azure. Förutom att tillhandahålla virtuella datorer i Azure för att distribuera och köra SAP HANA ger Azure dig möjlighet att köra och distribuera SAP HANA på bare metal-servrar som är dedikerad till att du som kund. SAP HANA i Azure (stora instanser) lösningen bygger på icke-delade/server host bare metal-maskinvara som är tilldelad till dig som en kund. Servermaskinvaran är inbäddad i större stämplar som innehåller beräknings-server, nätverk och infrastruktur. Vilket, som en kombination är HANA TDI certifierade. Tjänsterbjudande för SAP HANA i Azure (stora instanser) tillhandahåller olika SKU: er för annan server eller storlekar som börjar med enheter som har 72 processorer och 768 GB minne till enheter som har 960 processorer och 20 TB minne.

Kunden isolering inom infrastruktur stämpeln utförs i klienter, som i detalj ser ut:

- Nätverk: Isolering av kunder i infrastrukturen stapel via virtuella nätverk per kund tilldelats klient. En klient har tilldelats en kund. En kund kan ha flera innehavare. Nätverksisolering av innehavare som förhindrar nätverkskommunikation mellan klienter i stämpel infrastrukturnivå. Även om klienter som tillhör samma kund.
- Lagringskomponenter: isolering via lagring virtuella datorer som har tilldelats lagringsvolymer. Lagringsvolymer kan tilldelas till lagring för en virtuell dator. En virtuell dator för lagring är tilldelad till en enskild klient i SAP HANA TDI certifierade infrastruktur stacken. Därför kan lagringsvolymer som tilldelats till en virtuell dator för lagring användas i en specifik och relaterade klienten endast. Och visas mellan de olika distribuerade klienterna.
- Server eller en värd: en server eller en värd enhet delas inte mellan kunder eller klienter. En server eller en värd som har distribuerats till en kund är en atomisk bare metal-beräknings-enhet som är tilldelad till en enskild klient. **Inte** partitionering av maskinvara eller soft-partitionering används som kan resultera i att du som kund kan dela en värd eller en server med en annan kund. Lagringsvolymer som är kopplade till den virtuella datorn för lagring av specifika innehavaren är monterade på denna server. En klient kan ha ett till många server enheter av olika SKU: er som tilldelats exklusivt.
- Många olika klienter är inom en SAP HANA i Azure (stora instans) infrastruktur stämpel distribueras och isolerade mot varandra via klient begrepp på nätverk, lagring och beräkning nivå. 


Dessa enheter bare metal-server stöds endast kör SAP HANA. SAP programnivå eller arbetsbelastning i mitten-ware layer körs i Microsoft Azure Virtual Machines. Stämplarna infrastruktur körs SAP HANA på Azure (stora instans)-enheter är anslutna till de Azure Network backbones, så som låg latens anslutning mellan Azure virtuella datorer och SAP HANA på Azure (stora instans)-enheter har angetts.

Det här dokumentet är ett av flera dokument som täcker SAP HANA i Azure (stora instans). I det här dokumentet går vi genom grundläggande arkitektur, ansvar, tjänster och på en hög nivå via funktionerna i lösningen. För de flesta av områdena som nätverk och anslutning, fyra dokument täcker information och öka detaljnivån listrutor. I dokumentationen för SAP HANA i Azure (stora instans) omfattar inte aspekter av SAP NetWeaver installation eller distribution av SAP NetWeaver i virtuella Azure-datorer. SAP NetWeaver på Azure ingår i separata dokument som finns i samma behållare i Azure-dokumentationen. 


Olika dokument på HANA stora instans vägledning täcker följande områden:

- [Översikt över SAP HANA (stora instans) och arkitektur på Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastruktur för SAP HANA (stora instanser) och anslutningar på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Installera och konfigurera SAP HANA (stora instanser) på Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (stora instanser) med hög tillgänglighet och katastrofåterställning i Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Felsökning för SAP HANA (stora instanser) och övervakning på Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Hög tillgänglighet som angetts i SUSE med hjälp av STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [OS-säkerhetskopiering och återställning för typ II SKU: er](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>Definitioner

Flera gemensamma definitioner används ofta i guiden arkitektur och teknisk distribution. Observera följande villkor och deras innebörd:

- **IaaS:** infrastruktur som en tjänst
- **PaaS:** plattform som en tjänst
- **SaaS:** programvara som en tjänst
- **SAP komponent:** ett enskilt SAP-program, till exempel ECC BW, lösning Manager eller EP. SAP-komponenter kan baseras på traditionell ABAP eller Java-teknik eller icke-NetWeaver baserat program, till exempel Business-objekt.
- **SAP-miljö:** en eller flera SAP-komponenter som är logiskt grupperade för att utföra en business-funktion, till exempel utveckling, QAS, träning, DR eller produktion.
- **SAP liggande:** refererar till hela SAP tillgångarna i din IT-miljön. SAP-liggande innehåller alla produktions- och icke-produktionsmiljöer.
- **SAP System:** kombinationen av DBMS lager- och programnivå en SAP ERP-utvecklingssystemet SAP BW testsystemet, SAP CRM produktionssystem, osv. Azure-distributioner stöder inte mellan dessa två lager mellan lokala och Azure. : Ett SAP-system är antingen distribuerade lokalt eller den har distribuerats i Azure. Du kan dock distribuera olika system för en SAP liggande i Azure eller lokalt. Du kan till exempel distribuera SAP CRM system för utveckling och testning i Azure, vid distribution av den SAP CRM produktion system lokalt. Den är avsedd att värd SAP programlager för SAP-system i virtuella Azure-datorer och relaterade SAP HANA-instans på en enhet i stämpeln HANA stora instans för SAP HANA i Azure (stora instanser).
- **Stora instans stämpel:** maskinvara infrastruktur-stacken, som är SAP HANA TDI certifierad och avsett för att köra SAP HANA-instanser i Azure.
- **SAP HANA i Azure (stora instanser):** officiellt namn för erbjudandet i Azure att köra HANA instanser i på SAP HANA TDI certifierad maskinvara som distribueras i stora instans tidsstämplar i olika Azure-regioner. Relaterade termen **HANA stora instans** kort för SAP HANA i Azure (stora instanser) och är ofta används den här tekniska distributionsguiden.
- **Anslutningar mellan lokala:** beskriver ett scenario där virtuella datorer distribueras till en Azure-prenumeration som har plats-till-plats, flera platser eller ExpressRoute-anslutningen mellan lokala datacenter(s) och Azure. Gemensamma Azure-dokumentationen dessa typer av distributioner beskrivs också som mellan lokala scenarier. Anslutningen orsaken är att utöka lokala domäner, lokala Active Directory/OpenLDAP och lokala DNS i Azure. Lokala liggande utökats till Azure-prenumeration(er) Azure tillgångar. Med det här tillägget, kan de virtuella datorerna vara en del av den lokala domänen. Domänanvändare på den lokala domänen kan komma åt servrar och tjänster kan köra på dessa virtuella datorer (till exempel DBMS services). Det är möjligt kommunikation och namnmatchning mellan virtuella datorer som distribueras på lokalt och distribuerade virtuella Azure-datorer. Det är vanligt scenario i vilka de flesta SAP tillgångar distribueras. Se stödlinjer av [planering och design för VPN-Gateway](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) och [skapa ett VNet med en plats-till-plats-anslutning med hjälp av Azure portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mer detaljerad information.
- **Klient:** en kund som distribuerats i HANA stora instanser stämpel hämtar isolerade till en ”klient”. En klient är separat i nätverk, lagring och beräkning lagret från andra klienter. Så att lagring och beräkning av de enheter som tilldelats till olika klienter inte kan se varandra eller kommunicera med varandra på HANA stora stämpel instansnivå. En kund kan du ha distributioner till olika klienter. Det finns även sedan ingen kommunikation mellan klienter i HANA stora stämpel instansnivå.
- **SKU-kategori:** för stora instanser HANA erbjuds följande två kategorier av SKU: er.
    - **Typ I klassen:** S72, S72m, S144, S144m, S192 och S192m
    - **Typ II klass:** S384, S384m, S384xm, S576, S768 och S960


Det finns ett antal ytterligare resurser som har publicerats på distribuera SAP arbetsbelastningen på Microsoft Azure offentligt moln. Vi rekommenderar att alla planerings- och köra en SAP HANA-distribution i Azure är erfarna och medveten om ägare av Azure IaaS och distributionen av SAP arbetsbelastningar på Azure IaaS. Följande resurser ger mer information och bör refereras innan du fortsätter:


- [Med hjälp av SAP-lösningar på Microsoft Azure-datorer](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>Certifiering

Förutom NetWeaver-certifiering kräver SAP en särskild certifikatutfärdare för SAP HANA till stöd för SAP HANA i vissa infrastrukturer, till exempel Azure IaaS.

Core SAP-kommentar på NetWeaver och till en SAP HANA certifikatutfärdare grad är [SAP Obs #1928533 – SAP-program i Azure: produkter och Virtuella Azure-typer](https://launchpad.support.sap.com/#/notes/1928533).

Detta [SAP Obs #2316233 - SAP HANA i Microsoft Azure (stora instanser)](https://launchpad.support.sap.com/#/notes/2316233/E) är också viktig. Den omfattar lösningen som beskrivs i den här guiden. Dessutom stöds om du vill köra SAP HANA i GS5 VM-typen av Azure. [Information om det här fallet publiceras på webbplatsen SAP](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

SAP HANA i Azure (stora instanser)-lösningen som anges i SAP Obs #2316233 ger Microsoft och SAP kunder möjlighet att distribuera stora SAP Business Suite, SAP Business Warehouse (BW), S/4 HANA, BW/4HANA eller andra SAP HANA-arbetsbelastningar i Azure. Lösningen är baserad på stämpel för SAP HANA-certifierade dedikerad maskinvara ([SAP HANA skräddarsydda Datacenter integrering – TDI](https://scn.sap.com/docs/DOC-63140)). Körs som en SAP HANA TDI konfigurerade lösningen ger du säkert att veta som alla SAP HANA-baserade program (inklusive SAP Business Suite på SAP HANA, SAP Business Warehouse (BW) för SAP HANA, S4/HANA och BW4/HANA) kommer att fungera med maskinvara infrastruktur.

Jämfört med SAP HANA i Azure virtuella datorer körs den här lösningen har en fördel – den innehåller för mycket större volymer minne. Om du vill aktivera den här lösningen finns några viktiga aspekter att förstå:

- SAP application layer och icke-SAP program körs i Azure Virtual Machines (virtuella datorer) som finns i stämplarna som vanligt Azure maskinvara.
- Kunden lokal infrastruktur, Datacenter och distribution av program är ansluten till Microsoft Azure cloud platform via Azure ExpressRoute (rekommenderas) eller virtuella privata nätverk (VPN). Active Directory (AD) och DNS också har utökats till Azure.
- SAP HANA-databasinstans för HANA arbetsbelastning körs på SAP HANA i Azure (stora instanser). Stora instans stämpeln är ansluten till Azure-nätverk så att program som körs i virtuella Azure-datorer kan interagera med HANA-instans som körs i HANA stora instanser.
- Maskinvara för SAP HANA i Azure (stora instanser) är dedikerad maskinvara som anges i en infrastruktur som en tjänst (IaaS) med SUSE Linux Enterprise Server eller Red Hat Enterprise Linux förinstallerat. Som är med Azure Virtual Machines, ytterligare uppdateringar och underhåll av operativsystemet ansvarig för.
- Installation av HANA eller de ytterligare komponenterna som behövs för att köra SAP HANA på enheterna för stora HANA instanser är ditt ansvar, som är alla respektive pågående åtgärder och myndigheter SAP HANA på Azure.
- Du kan installera andra komponenter i din Azure-prenumeration som ansluter till SAP HANA i Azure (stora instanser) utöver de lösningar som beskrivs här.  Till exempel komponenter som aktiverar kommunikation med och/eller direkt till SAP HANA-databas (hopp servrar, RDP-servrar, SAP HANA Studio SAP datatjänster för SAP BI scenarier eller nätverk övervakar lösningar).
- Som Azure erbjuder HANA stora instanser stöder funktioner för hög tillgänglighet och katastrofåterställning.

## <a name="architecture"></a>Arkitektur

Vid en hög nivå har SAP HANA i Azure (stora instanser)-lösningen den SAP-program som finns i virtuella Azure-datorer samt databasen lagrets på SAP TDI konfigurerad maskinvara finns i en stor instans stämpel i samma Azure-Region som är ansluten till Azure IaaS .

> [!NOTE]
> Du behöver distribuera SAP programnivå i samma Azure-Region som SAP DBMS-lagret. Den här regeln är väl dokumenterat i publicerad information om SAP arbetsbelastningen på Azure. 

Den övergripande arkitekturen för SAP HANA i Azure (stora instanser) ger en SAP TDI certifierad maskinvarukonfiguration (icke-virtualiserade, bare metal, högpresterande server för SAP HANA-databas) och möjligheten och flexibilitet att skala resurser för Azure SAP Applikationsnivån dina behov.

![Översikt över arkitekturen för SAP HANA i Azure (stora instanser)](./media/hana-overview-architecture/image1-architecture.png)

Arkitektur som visas är indelat i tre delar:

- **Höger:** en lokal infrastruktur som kör olika program i datacenter med slutanvändarna att öppna LOB-program (till exempel SAP). Vi rekommenderar detta lokala infrastruktur sedan är ansluten till Azure med Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Center:** visar Azure IaaS och i så fall använder virtuella datorer i Azure som värd för SAP eller andra program som använder SAP HANA som ett DBMS-system. Mindre HANA instanser med funktionen med minnet som virtuella Azure-datorer distribueras på virtuella Azure-datorer tillsammans med deras programnivå. Lär dig mer om [virtuella datorer](https://azure.microsoft.com/services/virtual-machines/).
<br />Azure-nätverk används för att gruppera SAP-system tillsammans med andra program i Azure virtuella nätverk (Vnet). Dessa Vnet ansluta till lokalt system samt att SAP HANA i Azure (stora instanser).
<br />SAP NetWeaver program och databaser som stöds för att köras i Microsoft Azure finns [SAP stöd Obs #1928533 – SAP-program i Azure: produkter och Virtuella Azure-typer](https://launchpad.support.sap.com/#/notes/1928533). Granska följande dokumentation om hur du distribuerar SAP-lösningar i Azure:

  -  [Med hjälp av SAP på virtuella Windows-datorer (VM)](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Med hjälp av SAP-lösningar på Microsoft Azure-datorer](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Vänster:** visar SAP HANA TDI certifierad maskinvara i Azure stora instans stämpeln. HANA stora instans-enheter är anslutna till virtuella Azure-nätverk för din prenumeration som använder samma teknik som anslutningen från lokala till Azure.

Azure stora instans stämpeln själva kombinerar följande komponenter:

- **Datorupplevelse:** servrar som är baserade på Intel Xeon E7-8890v3 eller Intel Xeon E7-8890v4 processorer som ger den nödvändiga kapaciteten för databehandling och är SAP HANA-certifierad.
- **Nätverk:** en enhetlig snabb nätverksinfrastruktur som sammanbinder bearbetning, lagring och LAN-komponenter.
- **Lagring:** lagringsinfrastruktur som nås via en enhetlig nätverksinfrastruktur. Viss lagringskapacitet tillhandahålls beroende på den specifika SAP HANA på Azure (stora instanser) konfiguration som distribueras (mer lagringskapacitet är en extra månatliga kostnad).

Kunder distribueras i flera innehavare-infrastruktur för stora instans stämpeln som isolerade klienter. Vid distribution av klienten måste att namnge en Azure-prenumeration i Azure-registrering. Det här kommer att Azure-prenumerationen, HANA stora instanser kommer att debiteras mot. Dessa klienter ha en 1:1-relation till Azure-prenumerationen. Nätverk klokt är det möjligt att komma åt en HANA stora instans enhet distribueras i en klient i ett Azure-Region från olika Azure-Vnet som tillhör olika Azure-prenumerationer. Även om de Azure-prenumerationerna måste tillhöra samma Azure registreringen. 

SAP HANA i Azure (stora instanser) erbjuds som virtuella Azure-datorer i Azure-regioner. Du kan välja att välja för att kunna erbjuda Disaster Recovery-funktioner. Olika stora instans stämplar inom en geo politiska region är anslutna till varandra. Till exempel är HANA stora instans tidsstämplar i West för oss och vi Öst anslutna via en dedikerad nätverkslänk för DR-replikering. 

Precis som du kan välja mellan VM av olika typer med Azure Virtual Machines, kan du välja mellan olika SKU: er av HANA stora instanser som är skräddarsydda för arbetsbelastning för olika typer av SAP HANA. SAP gäller minne socket processorer för olika arbetsbelastningar baserat på Intel processor generationer. I följande tabell visas vilka SKU som erbjuds.

Från och med juli 2017 finns SAP HANA i Azure (stora instanser) i flera konfigurationer i Azure-regioner för oss Väst och oss Öst, östra, Australien, sydost, västra Europa och Norra Europa:

| SAP-lösning | Processor | Minne | Lagring | Tillgänglighet |
| --- | --- | --- | --- | --- |
| Optimerad för OLAP-: SAP BW BW/4HANA<br /> eller SAP HANA för allmänna OLAP-arbetsbelastning | SAP HANA på Azure S72<br /> – 2 x Intel Xeon®-Processor E7 8890 v3<br /> 36 CPU-kärnor och 72 CPU-trådar |  768 GB |  3 TB | Tillgänglig |
| --- | SAP HANA på Azure S144<br /> – 4 x Intel Xeon®-Processor E7 8890 v3<br /> 72 CPU-kärnor och 144 CPU-trådar |  1,5 TB |  6 TB | Erbjuds inte längre |
| --- | SAP HANA på Azure S192<br /> – 4 x Intel Xeon®-Processor E7 8890 v4<br /> 96 CPU-kärnor och 192 CPU-trådar |  2.0 TB |  8 TB | Tillgänglig |
| --- | SAP HANA på Azure S384<br /> – 8 x Intel Xeon®-Processor E7 8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  4.0 TB |  16 TB | Redo att ordning |
| Optimerad för OLTP: SAP Business Suite<br /> på SAP HANA eller S/4HANA (OLTP)<br /> allmän OLTP | SAP HANA på Azure S72m<br /> – 2 x Intel Xeon®-Processor E7 8890 v3<br /> 36 CPU-kärnor och 72 CPU-trådar |  1,5 TB |  6 TB | Tillgänglig |
|---| SAP HANA på Azure S144m<br /> – 4 x Intel Xeon®-Processor E7 8890 v3<br /> 72 CPU-kärnor och 144 CPU-trådar |  3.0 TB |  12 TB | Erbjuds inte längre |
|---| SAP HANA på Azure S192m<br /> – 4 x Intel Xeon®-Processor E7 8890 v4<br /> 96 CPU-kärnor och 192 CPU-trådar  |  4.0 TB |  16 TB | Tillgänglig |
|---| SAP HANA på Azure S384m<br /> – 8 x Intel Xeon®-Processor E7 8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  6.0 TB |  18 TB | Redo att ordning |
|---| SAP HANA på Azure S384xm<br /> – 8 x Intel Xeon®-Processor E7 8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  8.0 TB |  22 TB |  Redo att ordning |
|---| SAP HANA på Azure S576<br /> – 12 x Intel Xeon®-Processor E7 8890 v4<br /> 288 CPU-kärnor och 576 CPU-trådar |  12,0 TB |  28 TB | Redo att ordning |
|---| SAP HANA på Azure S768<br /> – 16 x Intel Xeon®-Processor E7 8890 v4<br /> 384 CPU-kärnor och 768 CPU-trådar |  16,0 TB |  36 TB | Redo att ordning |
|---| SAP HANA på Azure S960<br /> – 20 x Intel Xeon®-Processor E7 8890 v4<br /> 480 CPU-kärnor och 960 CPU-trådar |  20,0 TB |  46 TB | Redo att ordning |

- CPU-kärnor = summan av icke-hypertrådade CPU-kärnor av summan av processorer för server-enhet.
- CPU-trådar = summan av beräknings-trådar som tillhandahålls av hyper-threaded processorkärnor av summan av processorer för server-enhet. Alla enheter som är konfigurerade som standard att använda Hyper-Threading.


De olika konfigurationer som har ovan som är tillgänglig eller 'erbjuds inte längre' refererar till [SAP stöd Obs #2316233 – SAP HANA i Microsoft Azure (stora instanser)](https://launchpad.support.sap.com/#/notes/2316233/E). Konfigurationer som har markerats som ”redo att ordna' kommer att hitta träder i SAP-kommentar snart. Men kan dessa instans SKU: er beställas redan för de sex olika Azure-regionerna HANA stora instans-tjänsten är tillgänglig.

Specifika konfigurationer som valts är beroende av arbetsbelastning, CPU-resurser och önskade minne. Det är möjligt för OLTP-arbetsbelastning att använda SKU: er som är optimerade för OLAP-arbetsbelastning. 

Maskinvara basen för alla erbjudanden är SAP HANA TDI certifierade. Men skilja vi mellan två olika klasser av maskinvara, vilket delar upp SKU: er till:

- S72, S72m, S144, S144m, S192 och S192m som vi kallar ”typen I klassen' av SKU: er.
- S384, S384m, S384xm, S576, S768 och S960 som vi kallar ”typ II class-av SKU: er.

Det är viktigt att Observera att en fullständig HANA stora instans stämpel inte uteslutande har allokerats för en kund &#39; s användning. Detta gäller racken av beräkning och lagring resurser som är anslutna via en nätverksinfrastruktur samt distribuerade i Azure. HANA stora instanser infrastruktur som Azure, distribuerar annan kund &quot;hyresgäster&quot; som är isolerade från varandra i följande tre nivåer:

- Nätverk: Isolering av via virtuella nätverk i stämpeln HANA stora instans.
- Lagring: Isolering via lagring virtuella datorer som har lagringsvolymer tilldelas och isolera lagringsvolymer mellan klienter behålls.
- Beräkning: Dedikerade tilldelning av server-enheter på en enskild klient. Nej hårddisken eller soft partitionering av server-enheter. Inga delar av en enskild server eller en värd enhet mellan klienter behålls. 

Därför visas inte distributioner av HANA stora instanser enheter mellan olika klienter till varandra. Inte heller kan HANA stora instans enheter som distribueras i olika klienter kommunicera direkt med varandra på HANA stora stämpel instansnivå. Endast HANA stora instans enheter inom en klient kan kommunicera med varandra på HANA stora stämpel instansnivå.
En distribuerad klient i stora instans stämpeln tilldelas fakturering klokt att en Azure-prenumeration. Network dock klokt den kan nås från Azure Vnet för andra Azure-prenumerationer inom samma Azure registreringen. Om du distribuerar med en annan Azure-prenumeration i samma Azure-region måste kan också du be om en åtskilda HANA stora instans-klient.

Det finns betydande skillnader mellan kör SAP HANA HANA stora instanser och SAP HANA körs på Azure virtuella datorer som distribueras i Azure:

- Det finns ingen virtualiseringsskiktet för SAP HANA i Azure (stora instanser). Du får den underliggande maskinvaran bare metal-prestanda.
- Till skillnad från Azure, är SAP HANA på Azure (stora instanser)-server dedikerad till en viss kund. Det går inte att en server-enhet eller värden är svårt eller soft-partitionerad. Därför används en HANA stora instans-enhet som tilldelats som helhet till en klient och som du som kund. En omstart eller avstängning av servern leder inte automatiskt till operativsystemet och SAP HANA som distribueras på en annan server. (För typ I klassen SKU: er, det enda undantaget är om problem kan uppstå i en server och Omdistributionen måste utföras på en annan server.)
- Till skillnad från Azure, där värden processor väljs för förhållandet bästa pris och prestanda, är processor-typer som valts för SAP HANA i Azure (stora instanser) raden Intel E7v3 och E7v4 processor högsta prestanda.


### <a name="running-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Kör flera SAP HANA-instanser i en HANA stora instans-enhet
Det är möjligt att ha mer än en aktiv SAP HANA-instans på HANA stora instans-enheter. För att den fortfarande innehåller funktioner för ögonblicksbilder för lagring och katastrofåterställning, kräver en sådan konfiguration en volym som angetts per instans. Från och med nu HANA stora instans-enheter kan delas upp på följande sätt:

- S72 S72m S144, S192: I steg om 256 GB med 256 GB minsta första enhet. Inkrement som 256 GB, 512 GB och så vidare kan kombineras till maximalt minne på enheten.
- S144m och S192m: I steg om 256 GB med 512 GB den minsta enheten. Inkrement som 512 GB, 768 GB och så vidare kan kombineras till maximalt minne på enheten.
- Skriv II klass: I steg om 512 GB med den minsta första enheten 2 TB. Inkrement som 512 GB, 1 TB, 1,5 TB och så vidare kan kombineras till maximalt minne på enheten.

Några exempel på flera SAP HANA-instanser körs kan se ut så:

| SKU | Minnesstorlek | Lagringsstorlek | Storlekar med flera databaser |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 x 768 GB HANA instans<br /> eller 1 x 512 GB instans + 1 x 256 GB instans<br /> eller 3 x 256 GB instanser | 
| S72m | 1,5 TB | 6 TB | 3x512GB HANA instanser<br />eller 1 x 512 GB instans + 1 x 1 TB instans<br />eller 6 x 256 GB instanser<br />eller 1x1.5 TB instans | 
| S192m | 4 TB | 16 TB | 8 x 512 GB instanser<br />eller 4 x 1 TB instanser<br />eller 4 x 512 GB instanser + 2 x 1 TB instanser<br />eller 4 x 768 GB instanser + 2 x 512 GB instanser<br />eller 1 × 4 TB-instans |
| S384xm | 8 TB | 22 TB | 4 x 2 TB instanser<br />eller 2 × 4 TB instanser<br />eller instanser 2 x 3 TB + 1 x 2 TB instanser<br />eller instanser 2x2.5 TB + 1 x 3 TB instanser<br />eller 1 x 8 TB instans |


Nyckelkunder. Det finns visserligen samt andra ändringar. 

### <a name="using-sap-hana-data-tiering-and-extension-nodes"></a>Med hjälp av tillägget och SAP HANA Data skiktning noder
SAP stöder en Data skiktning modell för SAP BW i olika utgåvor av SAP NetWeaver och SAP BW/4HANA. Information om att Data skiktning modellen finns i dokumentet och refererar till i det här dokumentet SAP-bloggen: [SAP BW/4HANA och SAP BW ON HANA med SAP HANA tillägget noder](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Du kan använda alternativet-1-konfiguration för SAP HANA tillägget noder enligt anvisningarna i den här vanliga frågor och SAP blogg dokument med stor HANA-instanser. Alternativ 2 konfigurationer kan ställas in med de följande HANA stora instans SKU: er: S72m, S192, S192m, S384 och S384m.  
Titta i dokumentationen fördelen kanske inte visas omedelbart. Men söker riktlinjer för SAP-storlek, du kan se en fördel med hjälp av alternativet-1 och alternativ 2 SAP HANA tillägget noder. Här ett exempel:

- Riktlinjer för SAP HANA sizing kräver vanligen dubbelt så mycket datavolym som minne. När du kör din SAP HANA-instans med varm data du bara ha 50% eller mindre minne fylls med data på Internet. Resten av minnet som helst lagras för SAP HANA gör sitt arbete.
- Innebär att i en HANA stora instans S192 enhet med 2 TB minne, kör en SAP BW-databas du endast har 1 TB som datavolym.
- Om du använder ytterligare SAP HANA tillägget en nod i alternativ 1 också en S192 HANA stora instans SKU, det skulle ge en ytterligare 2 TB kapacitet för datavolym. I alternativ 2 configuration även och ytterligare 4 TB för varmt datavolym. Jämfört med den aktiva noden kan fullständig minneskapacitet för tilläggsnoden 'varm-användas för data lagring för alternativet-1 och dubbla minnet som kan användas för datavolymen i alternativ 2 nodkonfiguration för SAP HANA-tillägget.
- Därför lyckas du med en kapacitet på 3 TB för dina data och förhållandet hot till varmt 1:2 för alternativet-1 och 5 TB data och förhållandet 1:4 i alternativ 2 tillägget nodkonfiguration.

Ju högre datavolym jämfört med minne, desto högre risken är att varmt data som du vill att för lagras dock på diskutrymmet.


## <a name="operations-model-and-responsibilities"></a>Modellen verksamhet och ansvarsområden

Den tillhandahållna med SAP HANA i Azure (stora instanser) är justerad mot Azure IaaS-tjänster. Du får en stor instanser av HANA-instans med ett installerat operativsystem som är optimerad för SAP HANA. Precis som med Azure IaaS-VM, de flesta åtgärder av härdning av Operativsystemet, installera ytterligare programvara du behöver installera HANA, OS och HANA, och är ansvarig för att uppdatera OS och HANA. Microsoft tvingar inte OS-uppdateringar eller HANA uppdateringar på du.

![Ansvaret för SAP HANA i Azure (stora instanser)](./media/hana-overview-architecture/image2-responsibilities.png)

Som du ser i diagrammet ovan för är SAP HANA i Azure (stora instanser) en infrastruktur med flera innehavare som ett tjänsterbjudande. Och därför divisionen av ansvar i kolumnrubrikens OS-infrastruktur till största del. Microsoft ansvarar för alla aspekter av tjänsten under raden i operativsystemet och du är ansvarig ovanför raden, inklusive operativsystemet. Så kan senaste lokalt metoder du kan använda för kompatibilitet, säkerhet, programhantering, bas och hantering av OS fortsätta att användas. System som visas som om de finns i nätverket alla angående.

Men är den här tjänsten optimerad för SAP HANA så att det finns områden där dig och Microsoft måste arbetar tillsammans för att använda funktionerna för underliggande infrastruktur för bästa resultat.

I följande lista finns mer information på varje lager och dina ansvarsområden:

**Nätverk:** interna nätverk för stora instans stämpeln kör SAP HANA, dess åtkomst till lagring, anslutning mellan instanser (för skalbar och andra funktioner), anslutning till liggande och anslutningen till Azure där lager för SAP-program finns i Azure Virtual Machines. Den omfattar också WAN-anslutning mellan Azure-Datacenter för katastrofåterställning syften replikering. Alla nätverk partitioneras av klienten och har installerat QOS.

**Lagring:** virtualiserade partitionerad lagring för alla volymer som krävs av SAP HANA-servrar och för ögonblicksbilder. 

**Servrar:** särskilda fysiska servrar att köra SAP HANA DBs som tilldelats till klienter. Servrar i typen I klassen SKU: er är maskinvara tas ut. Med dessa typer av servrar, serverkonfigurationen samlas in och underhålls i profiler som kan flyttas från en fysisk maskinvara till en annan fysisk maskinvara. Sådana en (manuella) flyttning av en profil av åtgärder kan jämföras lite med Azure Service återställning. Servrar i SKU: er för typ II klassen erbjuder inte en sådan funktion.

**SDDC:** programvaran för hantering som används för att hantera data datacenter som programvarudefinierade enheter. Tillåter Microsoft att resurser för skala, tillgänglighet och prestanda.

**Operativsystem:** Operativsystemet som du väljer (SUSE Linux eller Red Hat Linux) som körs på servrarna. Operativsystemsavbildningar som du angav är bilder som tillhandahålls av leverantören enskilda Linux till Microsoft för att köra SAP HANA. Du måste ha en prenumeration med Linux-leverantören för den specifika bilden för SAP HANA-optimerad. Dina ansvarsområden inkluderar registrerar avbildningar med OS-leverantören. Från punkten övergång av Microsoft ansvarar du också för alla ytterligare korrigering av Linux-operativsystem. Korrigering också omfattar ytterligare paket som kan krävas för en lyckad installation för SAP HANA (Se SAP: s HANA dokumentationen och SAP anteckningar) och som inte har inkluderats specifika Linux-leverantör i sina SAP HANA optimerad OS bilder. Kunden ansvar även korrigering av Operativsystemet som är relaterade till fel/optimering av Operativsystemet och dess drivrutiner som är relaterade till specifika servermaskinvaran. Eller säkerhet eller funktionella korrigering av OS. Kundens ansvar är samt övervaknings- och kapacitetsplanering för:

- CPU-resursanvändningen
- Minnesförbrukning
- Volymer som är relaterade till ledigt utrymme, IOPS och svarstid
- Volymen nätverkstrafik mellan HANA stora instans och SAP programnivå

Den underliggande infrastrukturen HANA stora instanser tillhandahåller funktioner för säkerhetskopiering och återställning av systemvolymen. Med den här funktionen är också ansvarig för.

**Mellanprogram:** SAP HANA instansen i första hand. Är du ansvarig för administration, åtgärder och övervakning. Det tillhandahålls funktioner som gör att du kan använda ögonblicksbilder för lagring för säkerhetskopiering/återställning och katastrofåterställning. Dessa funktioner tillhandahålls av infrastrukturen. Dina ansvarsområden är dock också utforma hög tillgänglighet och katastrofåterställning med dessa funktioner, använda dem och övervakning lagring ögonblicksbilder har körts utan problem.

**Data:** dina data hanteras av SAP HANA och andra data, till exempel säkerhetskopieringar delar filer i volymer eller fil. Dina ansvarsområden innehåller övervaka ledigt diskutrymme och hantera innehåll på volymerna och övervaka säkerhetskopior av volymer på diskar och lagring ögonblicksbilder har körts. Dock ansvarar har körts för datareplikering till DR platser för Microsoft.

**Program:** programinstanser SAP eller vid ej SAP-program, programlager för programmen. Dina ansvarsområden inkluderar distribution, administration, åtgärder och övervakning av programmen som rör kapacitetsplanering för CPU-resursanvändningen, minnesförbrukning, Azure Storage-förbrukning och förbrukningen av nätverksbandbredd i Azure Vnet och från Azure Vnet till SAP HANA i Azure (stora instanser).

**WAN:** anslutningar som du har skapat från lokal till Azure-distributioner för arbetsbelastningar. Våra kunder med HANA stora instanser använda Azure ExpressRoute-anslutningen. Den här anslutningen ingår inte i SAP HANA i Azure (stora instanser)-lösningen, så att du är ansvarig för installationen av den här anslutningen.

**Arkivera:** kanske du föredrar att arkivera kopior av data med egna metoder i storage-konton. Arkivering kräver hantering, kompatibilitet, kostnader och åtgärder. Du ansvarar för att generera Arkiv kopior och säkerhetskopieringar på Azure och lagrar dem på ett sätt som är kompatibla.

Finns det [SLA för SAP HANA i Azure (stora instanser)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Storlek

Storlek för stora HANA-instanser är inte annorlunda än storleken för HANA i allmänhet. För befintliga och distribuerade system, du vill flytta från andra RDBMS till HANA, SAP innehåller ett antal rapporter som körs på din befintliga SAP-system. Om databasen flyttas till HANA rapporterna Kontrollera informationen och beräkna minneskrav för HANA-instansen. Läs följande SAP-information för att få mer information om hur du kör dessa rapporter och hur du hämtar de senaste korrigeringar/versionerna:

- [SAP-kommentar #1793345 - storlek för SAP Suite på HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Obs #1872170 - Suite HANA och S/4 HANA storlek för rapporten](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP-kommentar #2121330 – vanliga frågor och svar: SAP BW på HANA storleksanpassa rapport](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Obs #1736976 - storlek för rapporten för BW på HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Obs #2296290 - ny storlek för rapport för BW på HANA](https://launchpad.support.sap.com/#/notes/2296290)

För grön fältet implementeringar är SAP snabbt ange storlek tillgänglig att beräkna minneskrav för implementering av SAP-program ovanpå HANA.

Minneskrav för HANA ökar när datavolymen växer, så du vara medveten om minnesförbrukning nu och kunna förutse vad den ska vara i framtiden. Baserat på minneskraven, kan du sedan mappa din begäran till en HANA-SKU: er för stort instans.

## <a name="requirements"></a>Krav

Den här listan monterar krav för SAP HANA i Azure (större instanser).

**Microsoft Azure:**

- En Azure-prenumeration som kan kopplas till SAP HANA i Azure (stora instanser).
- Microsoft Premier Support-kontrakt. Se [SAP stöd Obs #2015553 – SAP på Microsoft Azure: stöd för krav](https://launchpad.support.sap.com/#/notes/2015553) specifik information som rör körs SAP i Azure. Använda HANA stora instans enheter med 384 och fler processorer, måste du också att utöka Premier Support-kontrakt med Azure snabba svar (ARR).
- Medvetenhet om HANA stora instanser SKU: er du behöver när du utför en storlek utöva med SAP.

**Nätverksanslutning:**

- Azure ExpressRoute mellan lokala till Azure: för att ansluta din lokala datacenter till Azure, se till att sortera på minst 1 Gbit/s-anslutning från Leverantören. 

**Operativsystem:**

- Licenser för SUSE Linux Enterprise Server 12 för SAP-program.

> [!NOTE] 
> Operativsystem som levereras av Microsoft har inte registrerats med SUSE eller ansluten med en SMT-instans.

- SUSE Linux prenumeration Management Tool (SMT) distribuerade i Azure på Azure-VM. Det gör möjligheten för SAP HANA i Azure (stora instanser) ska registreras och respektive uppdateras av SUSE (eftersom det inte finns någon internet-anslutning i HANA stora instanser datacenter). 
- Licenser för Red Hat Enterprise Linux 6.7 eller 7.2 för SAP HANA.

> [!NOTE]
> Operativsystem som levereras av Microsoft har inte registrerats med Red Hat eller ansluten till en instans för Red Hat prenumeration Manager.

- Red Hat prenumeration Manager distribueras i Azure på Azure-VM. Red Hat prenumeration Manager gör möjligheten för SAP HANA i Azure (stora instanser) ska registreras och respektive uppdateras av Red Hat (eftersom det finns ingen direkt Internetåtkomst från innehavaren distribueras på Azure stora instans stämpel).
- SAP måste du ha en supportbegäran kontrakt med leverantören Linux. Det här kravet tas inte bort av lösningen HANA stora instanser eller faktumet som din kör Linux i Azure. Till skillnad från med några av galleriavbildningar Linux Azure ingår avgifter inte i lösningen-erbjudandet om HANA stora instanser. Det är på du som kund att uppfylla kraven för SAP om supportavtal med Linux-distributören.   
   - Leta upp support-kontrakt i krav för SUSE Linux [SAP Obs #1984787 - SUSE LINUX Enterprise Server 12: installationsinformation](https://launchpad.support.sap.com/#/notes/1984787) och [SAP Obs #1056161 - SUSE prioritet stöd för SAP-program](https://launchpad.support.sap.com/#/notes/1056161).
   - För Red Hat Linux måste du ha nivåerna korrekt prenumeration med stöd som tjänsten (uppdateringar för operativsystem för HANA stora instanser. Red Hat rekommenderar att du hämtar ett ”RHEL för [SAP-lösningar](https://access.redhat.com/solutions/3082481)” prenumeration. 

Stöd matrix för olika SAP HANA-version med olika Linux-versioner, kontrollera [SAP Obs #2235581](https://launchpad.support.sap.com/#/notes/2235581).


**Databas:**

- Licenser och installation av programkomponenter för SAP HANA (plattform eller enterprise edition).

**Program:**

- Licenser och installation av programkomponenter för SAP program ansluter till SAP HANA och relaterade SAP supportavtal.
- Licenser och programvarukomponenter i installationen för alla icke-SAP-program används i förhållande till SAP HANA på Azure (stora instanser)-miljön och relaterade supportavtal.

**Kunskaper:**

- Erfarenhet och kunskap om Azure IaaS och dess komponenter.
- Erfarenhet och kunskap om hur du distribuerar SAP arbetsbelastning i Azure.
- Certifierad personliga SAP HANA-Installation.
- SAP systemarkitekt kunskaper för design hög tillgänglighet och katastrofåterställning runt SAP HANA.

**SAP:**

- Förutsättningen är att du är en SAP-kund och har stöd för ett kontrakt med SAP
- Särskilt för implementeringar på klassen typ II av HANA stora instans SKU: er rekommenderas att rådgöra med SAP på versioner av SAP HANA och eventuell konfigurationer på stora storlek skala upp maskinvara.


## <a name="storage"></a>Lagring

Lagringslayout för SAP HANA i Azure (stora instanser) konfigureras av SAP HANA på Azure Service Management via SAP rekommenderade riktlinjer som beskrivs i den [lagringskraven för SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) vitboken.

HANA stora instanser av typen I klassen har fyra gånger minne volymen som lagringsvolym. För typen II enhetsklass HANA stora instans ska lagringen inte vara fyra gånger större. Enheterna som har en volym som är avsedd för att lagra HANA säkerhetskopieringar av transaktionsloggen. Mer information i [hur du installerar och konfigurerar SAP HANA (stora instanser) på Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Se tabellen nedan vad gäller lagringsallokering. Tabellen innehåller ungefär kapacitet för olika volymer med olika HANA stora instans-enheter.

| HANA stora instans SKU | Hana-data | Hana/logg | Hana/delat | Hana/loggsäkerhetskopiering |
| --- | --- | --- | --- | --- |
| S72 | 1280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3328 GB | 768 GB |1280 GB | 768 GB |
| S192 | 4608 GB | 1024 GB | 1536 GB | 1024 GB |
| S192m | 11,520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384 | 11,520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384m | 12 000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xm | 16 000 GB | 2050 GB | 2050 GB | 2040 GB |
| S576 | 20 000 GB | 3100 GB | 2050 GB | 3100 GB |
| S768 | 28 000 GB | 3100 GB | 2050 GB | 3100 GB |
| S960 | 36,000 GB | 4100 GB | 2050 GB | 4100 GB |


Faktiska distribuerade volymer kan variera något beroende på distribution och verktyg som används för att visa Volymstorlekar.

Om du dela upp en HANA stora instans SKU ser några exempel på uppdelning delar ut som:

| Minnespartition i GB | Hana-data | Hana/logg | Hana/delat | Hana/loggsäkerhetskopiering |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1024 | 1792 GB | 640 GB | 1024 GB | 640 GB |
| 1536 | 3328 GB | 768 GB | 1280 GB | 768 GB |


De här storlekarna är grov volym siffror som kan variera något baserat på distribution och verktyg som används för att titta på volymerna. Det är också andra partitionsstorlek thinkable som 2,5 TB. Dessa lagringsstorlekar skulle beräknas med en liknande formel som används för partitioner ovan. Termen partitioner anger inte att operativsystem, minne eller CPU-resurser som är i något sätt partitionerad. Det anger bara partitioner för lagring för olika HANA instanser kanske du vill distribuera på en enda HANA stora instans-enhet. 

Du som en kund kan ha för mer lagringsutrymme kan du ha möjlighet att lägga till lagringsenheter om du vill köpa ytterligare lagringsutrymme i enheter som 1 TB. Den här ytterligare lagringsutrymme kan läggas till som en ytterligare volym eller kan användas för att utöka en eller flera av de befintliga volymerna. Det går inte att minska storleken på volymerna som ursprungligen har distribuerats och främst dokumenterats i tabellerna ovan. Det är också inte går att ändra namnen på volymerna eller montera namn. Lagringsvolymer som beskrivs ovan är kopplade till HANA stora instans-enheter som NFS4 volymer.

Som en kund kan du använda ögonblicksbilder för lagring av säkerhetskopiering/återställning och katastrofåterställning återställning. Mer information ges i [SAP HANA (stora instanser) med hög tillgänglighet och katastrofåterställning i Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="encryption-of-data-at-rest"></a>Kryptering av vilande data
Det lagringsutrymme som används för HANA stora instanser kan en transparent kryptering av data som lagras på diskarna. Vid tidpunkten för distribution av en HANA stora instans har möjlighet att ha den här typen av kryptering aktiverat. Du kan också välja att ändra till krypterade volymer efter distributionen redan. Flytta från en icke-krypterade till krypterade volymer är transparent och kräver inte ett driftstopp. 

Typen I klassen SKU: er, volymen Start LUN som är lagrad på, är krypterad. Vid typ II klass av SKU: er av HANA stora instanser måste du kryptera Start LUN med OS-metoder. Mer information kontaktar du Microsoft Service Management-teamet.


## <a name="networking"></a>Nätverk

Arkitekturen i Azure-nätverk är en viktig del i lyckad distribution av SAP-program på HANA stora instanser. SAP HANA på Azure (stora instanser)-distributioner har vanligtvis en större SAP-liggande med flera olika SAP-lösningar med olika storlekar för databaser, CPU-resursanvändningen och minnesanvändning. Inte alla dessa SAP-system är sannolikt baserat på SAP HANA så att din SAP-liggande skulle förmodligen en hybridlösning som använder:

- Distribuerade SAP system lokalt. På grund av deras storlek kan inte systemen för närvarande finnas i Azure; ett exempel är ett produktionssystem SAP ERP som körs på Microsoft SQL Server (som databasen) som kräver mer CPU eller minne som virtuella Azure-datorer kan ge.
- Distribuera SAP HANA-baserade SAP system lokalt.
- Distribuerade SAP-system i virtuella Azure-datorer. Dessa system kan vara utveckling, testning, sandbox eller produktion instanser för något SAP NetWeaver-baserade program som kan distribuera i Azure (på VM: ar), baserat på resursen användnings- och behov. Dessa system kan också baseras på databaser som SQL Server (se [SAP stöd Obs #1928533 – SAP-program på Azure: produkter och Virtuella Azure-typer](https://launchpad.support.sap.com/#/notes/1928533/E)) eller SAP HANA (finns [SAP HANA certifierade IaaS plattformar](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)).
- Distribuera SAP programservrar i Azure (på virtuella datorer) som utnyttjar SAP HANA i Azure (stora instans) i Azure stora instans stämplar.

Hybrid SAP liggande (med fyra eller flera olika distributionsscenarier) är typiska, finns men det många kunden fall av fullständig SAP liggande körs i Azure. Eftersom Microsoft Azure virtuella datorer blir allt mer kraftfulla, ökar antalet kunder flytta sina SAP-lösningar på Azure.

Azure-nätverk i samband med SAP-system som distribueras i Azure är inte komplicerad. Den är baserad på följande principer:

- Virtuella Azure-nätverk (Vnet) måste vara anslutna till Azure ExpressRoute-kretsen som ansluter till lokala nätverk.
- En ExpressRoute-krets vanligtvis ansluta lokalt till Azure ska ha en bandbredd på 1 Gbit/s eller högre. Den här minimal bandbredd kan tillräcklig bandbredd för överföring av data mellan system som körs på virtuella Azure-datorer (samt anslutningen till Azure-system från slutanvändare lokala) och lokala system.
- SAP-system i Azure måste ställas in i virtuella Azure-nätverk för att kommunicera med varandra.
- Active Directory och DNS finns lokalt har utökats till Azure via ExpressRoute från lokalt.


> [!NOTE] 
> Endast en enda Azure-prenumeration kan länkas till en enskild klient i en stor instans stämpel i en viss Azure-region fakturering synvinkel och omvänt en enda stor instans stämpel klient kan länkas endast till en Azure-prenumeration. Detta skiljer sig inte till andra fakturerbar objekt i Azure

Distribuera SAP HANA i Azure (stora instanser) i flera olika Azure-regioner resulterar i en separat klient som distribueras i stora instans stämpeln. Du kan dock köra både under samma Azure-prenumerationen så länge dessa instanser är en del av samma SAP liggande. 

> [!IMPORTANT] 
> Azure Resource Manager-distribution stöds med SAP HANA i Azure (stora instanser).

 

### <a name="additional-azure-vnet-information"></a>Ytterligare information i Azure VNet

För att ansluta ett virtuellt Azure-nätverk till ExpressRoute, måste du skapa en Azure gateway (se [om virtuella nätverksgatewayerna expressroute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). En Azure-gateway kan användas med ExpressRoute till en infrastruktur utanför Azure (eller en stor Azure instans stämpel) eller att ansluta mellan virtuella Azure-nätverk (se [och konfigurera en VNet-till-VNet-anslutning för Resource Manager med hjälp av PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Du kan ansluta Azure gateway till högst fyra olika ExpressRoute-anslutningar så länge dessa anslutningar kommer från olika MS Enterprise kanter (MSEE) routrar.  Mer information finns i [SAP HANA (stora instanser) infrastruktur och anslutningar på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Genomströmning ger en Azure-gateway är olika för både användningsfall (se [om VPN-Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Maximalt dataflöde vi kan åstadkomma med en gateway för virtuellt nätverk är 10 Gbit/s med hjälp av en ExpressRoute-anslutning. Kom ihåg att kopiera filer mellan en Azure VM som finns i ett Azure VNet och ett system lokalt (som en enda kopia-ström) inte innebär det totala genomflödet i en annan gateway-SKU: er. För att kunna utnyttja fullständig bandbredden för gateway för virtuellt nätverk, måste du använda flera strömmar eller kopiera olika filer i parallella strömmar av en enskild fil.


### <a name="networking-architecture-for-hana-large-instances"></a>Nätverksarkitekturen för HANA stora instanser
Nätverk arkitekturen för HANA stora instanser kan som visas nedan, delas upp i fyra olika delar:

- Lokala nätverk och ExpressRoute-anslutning till Azure. Den här delen är domänen som kunder och ansluten till Azure via ExpressRoute. Detta är en del i det nedre högra hörnet av grafik nedan.
- Azure-nätverk så kort som beskrivs ovan med virtuella Azure-nätverk, som har gateways igen. Detta är ett område där du vill söka efter lämpliga Designer för krav på program-, säkerhets- och efterlevnadskrav. Med stora HANA-instanser är ett annat övervägande vad gäller antalet Vnet och Azure gateway SKU: er kan välja mellan. Detta är en del i det övre högra grafik.
- Anslutningar av HANA stora instanser för ExpressRoute-teknik till Azure. Den här delen distribueras och hanteras av Microsoft. Allt du behöver göra när en kund är att tillhandahålla vissa IP-adressintervall och efter distributionen av dina tillgångar i HANA stora instanser ansluter ExpressRoute-krets till Azure-VNet(s) (se [SAP HANA (stora instanser) infrastruktur och anslutningen på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 
- Nätverk i HANA stora instanser som är mest transparent du som kund.

![Azure-VNet som är anslutna till SAP HANA på Azure (stora instanser) och lokalt](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Det faktum att du använder HANA stora instanser ändras inte kravet på att hämta dina lokala tillgångar som är anslutna via ExpressRoute till Azure. Också ändras inte kravet för att ha en eller flera Vnet som kör virtuella Azure-datorer vilken värd programlager som ansluter till HANA instanser finns i HANA stora instans enheter. 

Skillnaden till SAP-distributioner i ren Azure medföljer fakta som:

- HANA stora instans enheter av din klientorganisation, kund är anslutna via en annan ExpressRoute-krets i Azure-VNet(s). För att avgränsa belastningstillstånd delar lokalt till Azure Vnet ExpressRoute och länkar mellan Azure Vnet och HANA stora instanser inte samma routrar.
- Profilen arbetsbelastningen mellan SAP programlager och HANA-instans är en annan typ av många små begäranden och burst som data överförs (resultatmängder) från SAP HANA i Applikationsnivån.
- Arkitektur för SAP-program är mer känslig för nätverks-svarstid än vanliga scenarier där data hämtar utbyts mellan lokala och Azure.
- VNet-gateway har minst två ExpressRoute-anslutningar och både anslutningar delar Maximal bandbredd för inkommande data för gateway för virtuellt nätverk.

Nätverkslatensen erfarna mellan virtuella Azure-datorer och enheter för stora HANA instans kan vara högre än en typisk fördröjningen för VM-VM-nätverk. Beroende på Azure-regionen, de mätvärdena kan överstiga 0.7 ms fördröjningen klassificeras som under medel i [SAP Obs #1100926 – vanliga frågor och svar: nätverksprestanda](https://launchpad.support.sap.com/#/notes/1100926/E). Kunder distribuera ändå SAP HANA-baserade SAP produktionsprogram mycket på SAP HANA stora instanser. Kunder som distribuerats rapporterats bra förbättringar av deras SAP-program som körs på SAP HANA HANA stora instans enheter. Dock bör du testa dina affärsprocesser noggrant i Azure HANA stora instanser.
 
Valet av Azure VNet Gateway-SKU är viktigt för att ge deterministiska Nätverksfördröjningen mellan virtuella Azure-datorer och stora HANA-instans. Till skillnad från trafikmönster mellan lokala och virtuella Azure-datorer utveckla trafikmönstret mellan virtuella Azure-datorer och HANA stora instanser små men hög belastning för begäranden och datavolymer som ska överföras. För att få dessa belastning som hanteras och rekommenderar vi användning av Gateway-SKU UltraPerformance. Användningen av UltraPerformance gateway SKU som Azure VNet Gateway för typ II HANA stora instans SKU: er är obligatoriskt.  

> [!IMPORTANT] 
> Total nätverkstrafik mellan SAP-program och databasen lager får stöds endast HighPerformance eller UltraPerformance gateway-SKU: er för VNets för att ansluta till SAP HANA i Azure (stora instanser). För stora HANA instans typ II SKU: er stöds endast UltraPerformance Gateway-SKU som Azure VNet Gateway.



### <a name="single-sap-system"></a>Enda SAP-system

Den lokala infrastruktur som visas ovan är anslutna via ExpressRoute till Azure och ExpressRoute-kretsen ansluter till en Microsoft Enterprise Edge Router (MSEE) (se [teknisk översikt för ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). När etablerade, den rutten ansluter till Microsoft Azure-stamnät och alla Azure-regioner är tillgängliga.

> [!NOTE] 
> Ansluta till MSEE närmast Azure-regionen i SAP-miljön för att köra SAP landskap i Azure. Azure stora instans stämplar är anslutna via dedicerade MSEE enheter för att minimera Nätverksfördröjningen mellan Azure virtuella datorer i Azure IaaS och stora instans stämplar.

VNet-gateway för virtuella Azure-datorer, SAP programinstanser värd är ansluten till den ExpressRoute-kretsen och samma virtuella nätverk som är ansluten till en separat MSEE Router som är dedikerad till att ansluta till stora instans stämplar.

Detta är ett enkelt exempel på ett enda SAP-system där lager för SAP-program finns i Azure och SAP HANA-databas som körs på SAP HANA i Azure (stora instanser). Förutsättningen är att VNet gateway bandbredden för 2 Gbit/s eller 10 Gbit/s genomströmning är inte en flaskhals.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Flera SAP-system eller stora SAP-system

Om flera SAP-system eller stora SAP-system är distribuerade ansluta till SAP HANA på Azure (stora instanser), den &#39; kan s rimligt att anta genomflödet av gateway för virtuellt nätverk bli en flaskhals. I sådana fall måste du dela program lager i flera virtuella Azure-nätverk. Det kan även vara recommendable att skapa särskilda Vnet som ansluter till HANA stora instanser i de fall som:

- Utföra säkerhetskopieringar direkt från HANA instanserna i HANA stora instanser till en virtuell dator i Azure som värd för NFS-resurser
- Kopiera stora säkerhetskopieringar och andra filer från HANA stora instans enheter till diskutrymme som hanteras i Azure.

Med hjälp av separata Vnet som värd för virtuella datorer som hanterar lagring undviker effekten av stora filer eller dataöverföring från HANA stora instanser till Azure på Gateway för virtuellt nätverk som hanterar de virtuella datorerna kör programnivå SAP. 

För en mer skalbar nätverksarkitektur:

- Använda flera virtuella Azure-nätverk för ett enda, större SAP programmet lager.
- Distribuera en separat Azure VNet för varje SAP-system som har distribuerats, jämfört med att kombinera dessa SAP-system i separata undernät under samma virtuella nätverk.

 En nätverk Mer skalbar arkitektur för SAP HANA i Azure (stora instanser):

![Distribuera SAP programnivå över flera virtuella Azure-nätverk](./media/hana-overview-architecture/image4-networking-architecture.png)

Distribuera SAP programnivå eller komponenter, över flera virtuella Azure-nätverk som visas ovan, introducerade oundvikligt latens kostnader som inträffade under kommunikation mellan de program som finns i de virtuella Azure-nätverk. Som standard nätverkstrafiken mellan virtuella Azure-datorer finns i olika Vnet vidarebefordra via MSEE routrar i den här konfigurationen. Men eftersom September 2016 kan denna routning optimeras. Sätt att optimera och minska svarstiden för kommunikationen mellan två Vnet är av peering Azure Vnet i samma region. Även om dessa Vnet i olika prenumerationer. Med Azure VNet-peering för använda kommunikationen mellan virtuella datorer i två olika Azure VNets Azure network-stamnät kommunicera direkt med varandra. Visar vilket liknande svarstid som om de virtuella datorerna är i samma virtuella nätverk. Medan trafik, IP-adressintervall som är anslutna via Azure VNet-gateway-adresser dirigeras via VNet enskilda VNet-gateway. Du kan få information om Azure VNet-peering i artikeln [VNet-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Routning i Azure

Det finns tre viktiga routning Nätverksöverväganden för SAP HANA i Azure (stora instanser):

1. SAP HANA i Azure (stora instanser) kan endast nås via virtuella Azure-datorer och via dedikerade ExpressRoute-anslutningen. inte direkt från lokalt. Direktåtkomst från lokal till HANA stora instans-enheter är som levereras av Microsoft, inte möjlig direkt på grund av tillfälliga routning begränsningar för den aktuella Azure nätverksarkitekturen används för SAP HANA stora instanser. Vissa klienter för administration och alla program som behöver direktåtkomst, till exempel SAP lösning Manager som körs lokalt, kan inte ansluta till SAP HANA-databas.

2. Om du har stora HANA-instans enheter som distribuerats i två olika Azure-regioner för Disaster Recovery gäller samma tillfälligt routning begränsningar. Eller IP-adresser för en enhet HANA stora instans i en region (t.ex. oss West) kommer med andra ord inte dirigeras till en stor HANA-instans distribuerad du i en annan region (t.ex. oss East). Detta är oberoende av användningen av Azure-nätverk mellan regioner eller mellan ansluter ExpressRoute-kretsar som ansluter HANA stora instans enheter till Azure-Vnet-peering. Som visas lite mer ned i den här dokumentationen. Den här begränsningen som medföljer distribuerad arkitektur kommer förhindrar omedelbar användningen av HANA System Replication när Disaster recovery-funktioner.

3. SAP HANA på Azure (stora instanser)-enheter har en tilldelad IP-adress från Serverpoolen IP-adress vara du som kund har skickats (se [SAP HANA (stora instanser) infrastruktur och anslutningar på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) information).  Denna IP-adress är tillgänglig via Azure-prenumerationer och ExpressRoute som ansluter Azure Vnet till HANA i Azure (stora instanser). IP-adress som tilldelats utanför servern IP-poolen adressintervallet tilldelas direkt till enheten maskinvara och inte är NAT'ed längre eftersom det var skiftläget för de första distributionerna av den här lösningen. 

> [!NOTE] 
> Om du behöver lösa begränsningen i tillfälliga routning som beskrivs i de första två listobjekt ovan, måste du använda ytterligare komponenter för routning. Komponenter som kan användas för att lösa begränsningen kan vara: en omvänd proxy att vidarebefordra data till och från. Exempelvis F5 BIG-IP, NGINX med Traffic Manager distribueras i Azure som en virtuell brandväggen-trafik routning lösning.
> Med hjälp av [IPTables regler](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) i en Linux-VM för att aktivera routning mellan lokala platser och HANA stora instans enheter eller mellan HANA stora instans enheter i olika regioner.
> Tänk på att implementering och stöd för anpassade lösningar från tredje part nätverksinstallationer eller IPTables inte tillhandahålls av Microsoft. Stöd måste tillhandahållas av tillverkaren av den komponent som används eller integrator. 

### <a name="internet-connectivity-of-hana-large-instances"></a>Internet-anslutning av HANA stora instanser
HANA stora instanser har inte direkt Internetanslutning. Detta är att begränsa din förmåga att till exempel registrera OS-avbildningen direkt med OS-leverantören. Du kan därför behöver arbeta med lokala SLES SMT-servern eller RHEL prenumeration Manager

### <a name="data-encryption-between-azure-vms-and-hana-large-instances"></a>Kryptering av data mellan virtuella Azure-datorer och HANA stora instanser
Data som överförs mellan HANA stora instanser och virtuella datorer i Azure krypteras inte. Endast för exchange mellan HANA DBMS sida och JDBC/ODBC-baserat program kan du aktivera kryptering av trafik. Referens [denna dokumentation av SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)

### <a name="using-hana-large-instance-units-in-multiple-regions"></a>Använda HANA stora instans enheter i flera regioner

Du kan ha andra orsaker att distribuera SAP HANA i Azure (stora instanser) i flera Azure-regioner, förutom katastrofåterställning. Kanske vill du komma åt HANA stora instanser från var och en av de virtuella datorerna som distribueras i olika Vnet i regioner. Eftersom IP-adresser som hör till olika HANA stora instanser enheterna inte sprids utöver den virtuella Azure-nätverk (som är anslutna direkt via deras gateway till instanserna), är små ändringar VNet designen introduceras ovan: ett Azure VNet gateway kan hantera fyra olika ExpressRoute-kretsar utanför olika MSEEs och varje VNet som är ansluten till någon av stämplarna som stora instans kan vara ansluten till stora instans stämpel i en annan Azure-region.

![Azure Vnet som är anslutna till Azure stora instans tidsstämplar i olika Azure-regioner](./media/hana-overview-architecture/image8-multiple-regions.png)

Ovanstående bild visar hur olika Azure-Vnet i båda områden är anslutna till två olika ExpressRoute-kretsar som används för att ansluta till SAP HANA i Azure (stora instanser) i både Azure-regioner. Nyligen införda anslutningar är rektangulär röda linjer. Med dessa anslutningar utanför Azure-Vnet de virtuella datorerna som körs i något av dessa Vnet har åtkomst till var och en av de olika HANA stora instanser enheter som distribueras i de två regionerna. Som du ser i graphics ovan, förutsätts att du har två ExpressRoute anslutningar från lokal till två Azure regioner. Vi rekommenderar för katastrofåterställning orsaker.

> [!IMPORTANT] 
> Om flera ExpressRoute-kretsar används ska AS sökväg tillagt och lokala inställningar BGP inställningar användas för att säkerställa rätt routning av trafik.


