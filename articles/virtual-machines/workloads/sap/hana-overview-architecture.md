---
title: Översikt och arkitektur för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Översikt över arkitekturen att distribuera SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/27/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d28b6ea5612a3db539c51d2603c3f12282ca519
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43090424"
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>SAP HANA (stora instanser) översikt och arkitektur på Azure

## <a name="what-is-sap-hana-on-azure-large-instances"></a>Vad är SAP HANA på Azure (stora instanser)?

SAP HANA på Azure (stora instanser) är en unik lösning till Azure. Förutom att tillhandahålla virtuella datorer för att distribuera och köra SAP HANA, ger Azure dig möjlighet att köra och distribuera SAP HANA på bare metal-servrar som är dedikerade till dig. SAP HANA på Azure (stora instanser) lösningen bygger på icke-delade/värdservern bare metal-maskinvara som är tilldelad till dig. Servermaskinvaran är inbäddad i större stämplar som innehåller beräkning/server-, nätverks- och lagringsinfrastrukturen. Som en kombination är det HANA skräddarsydda center dataintegrering (TDI) certifierade. SAP HANA på Azure (stora instanser) erbjuder olika server-SKU: er eller storlekar. Enheter kan ha 36 Intel CPU-kärnor och 768 GB minne och gå upp till enheter som har upp till 480 Intel CPU-kärnor och upp till 24 TB minne.

Kundisolering inom infrastruktur stämpeln utförs i klienter, som ser ut som:

- **Nätverk**: isolering av kunder i infrastrukturen stack via virtuella nätverk per som tilldelats kundklient. En klient har tilldelats en enda kund. En kund kan ha flera klienter. Nätverksisolering med klienter förbjuder nätverkskommunikation mellan klienter i stämpel infrastrukturnivå, även om innehavarna som tillhör samma kund.
- **Lagringskomponenter**: isolering via storage virtuella datorer som har lagringsvolymer som tilldelats. Lagringsvolymer kan tilldelas till en virtuell dator. En virtuell dator för lagring har tilldelats exklusivt avsedd för en enskild klient i SAP HANA TDI-certifierade infrastruktur-stacken. Därför är lagringsvolymer som tilldelats en virtuell dator för lagring tillgängliga i en specifik och relaterade-klient. De är inte synliga mellan de olika distribuerade klienterna.
- **Servern eller värden**: en enhet för servern eller värden delas inte mellan kunder eller klienter. En server eller en värd som har distribuerats till en kund är en atomisk bare metal-beräkningsenhet som är tilldelad till en enda klient. *Inte* maskinvara partitionering eller mjuk partitionering används som kan resultera i att du delar en värd eller en server med en annan kund. Lagringsvolymer som är kopplade till den virtuella datorn för lagring av specifik klient monteras till sådana en server. En klient kan ha en till många server enheter av olika SKU: er som tilldelats exklusivt.
- Inom en SAP HANA på Azure (stora instanser) infrastruktur stämpel, många olika klienter distribueras och isolerade mot varandra via klient-koncepten för nätverk, lagring och beräkningsnivå. 


Serverenheterna utan operativsystem stöder endast kör SAP HANA. SAP-programnivån eller arbetsbelastning mitten kod layer körs i virtuella datorer. De stämplar som infrastrukturen som kör SAP HANA på Azure (stora instanser) enheter är anslutna till de Azure-tjänster för fibernätverk. På så sätt kan tillhandahålls med låg latens anslutning mellan virtuella datorer och SAP HANA på Azure (stora instanser)-enheter.

Det här dokumentet är en av flera dokument som behandlar SAP HANA på Azure (stora instanser). Det här dokumentet beskriver de grundläggande arkitektur, ansvarsområden och tjänster som tillhandahålls i lösningen. På hög nivå diskuteras av lösningen också. För de flesta andra områden, till exempel nätverk och anslutning, täcker fyra andra dokument information och information på detaljnivå. I dokumentationen för SAP HANA på Azure (stora instanser) omfattar inte aspekter av SAP NetWeaver-installation eller distributioner av SAP NetWeaver på virtuella datorer. SAP NetWeaver på Azure täcks i separata dokument som finns i samma behållare i Azure-dokumentationen. 


Olika dokument för stora HANA-instansen vägledning täcker följande områden:

- [SAP HANA (stora instanser) översikt och arkitektur på Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (stora instanser) infrastruktur och anslutningsmöjlighet på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Installera och konfigurera SAP HANA (stora instanser) på Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (stora instanser) felsökning och övervakning i Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Hög tillgänglighet som angetts i SUSE med hjälp av STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [OS-säkerhetskopiering och återställning för typ II-SKU: er](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>Definitioner

Flera gemensamma definitioner används mycket i arkitekturen och tekniska distributionsguiden. Observera följande villkor och deras innebörd:

- **IaaS**: infrastruktur som en tjänst.
- **PaaS**: plattform som en tjänst.
- **SaaS**: programvara som en tjänst.
- **SAP-komponenten**: ett enskilt SAP-program, till exempel ERP Central komponent (ECC), Business Warehouse (BW), lösningen Manager eller Enterprise Portal (EP). SAP-komponenter kan baseras på traditionella ABAP eller Java-tekniker eller ett icke-NetWeaver-baserade program, till exempel företag objekt.
- **SAP-miljön**: en eller flera SAP-komponenter som är logiskt grupperade för att utföra en funktion för företag, utveckling, kvalitetssäkring, utbildning, haveriberedskap och produktionsmiljö.
- **SAP-landskap**: refererar till hela SAP-tillgångarna i din IT-miljön. SAP-landskap innehåller alla produktions- och icke-produktionsmiljöer.
- **SAP-system**: kombinationen av DBMS lager och programnivån av, till exempel ett SAP ERP-system för utveckling, ett testsystem för SAP BW och en SAP CRM-system för produktion. Azure-distributioner stöder inte mellan dessa två lager mellan lokala och Azure. Ett SAP-system är distribueras lokalt eller dess distribueras i Azure. Du kan distribuera olika system i ett SAP-landskap i Azure eller lokalt. Du kan till exempel distribuera SAP CRM-utveckling och testa system i Azure medan du distribuerar SAP CRM produktionsprogrammen system på plats. Den är avsedd att du vara värd för SAP-programnivån av SAP-system i virtuella datorer och relaterade SAP HANA-instans på en enhet i SAP HANA på Azure (stora instanser) stämpel för SAP HANA på Azure (stora instanser).
- **Stor instans stämpel**: en infrastruktur-stack med maskinvara som är SAP HANA TDI-certifierade och dedikerad att köra SAP HANA-instanser i Azure.
- **SAP HANA på Azure (stora instanser):** officiellt namn för erbjudandet i Azure att köra HANA-instanser i på SAP HANA TDI-certifierade maskinvara som är distribuerad i stor instans tidsstämplar i olika Azure-regioner. Relaterade termen *stora HANA-instansen* är kort för *SAP HANA på Azure (stora instanser)* och som ofta används i den här tekniska distributionsguiden.
- **Mellan lokala**: Beskriver ett scenario där virtuella datorer distribueras till en Azure-prenumeration med plats-till-plats, flera platser eller Azure ExpressRoute-anslutning mellan lokala datacenter och Azure. I vanliga Azure-dokumentation, dessa typer av distributioner beskrivs också som mellan lokala scenarier. Orsaken till att anslutningen är att utöka lokala domäner, en lokal Azure Active Directory/OpenLDAP och den lokala DNS i Azure. Lokala liggande utökas till Azure-tillgångar i Azure-prenumerationer. Med det här tillägget kan de virtuella datorerna inte ingå i den lokala domänen. 

   Domänanvändare på den lokala domänen kan komma åt servrarna och köra tjänster på dessa virtuella datorer (till exempel DBMS tjänster). Kommunikation och namnmatchning mellan virtuella datorer distribueras på plats och Azure-distribuerade virtuella datorer är möjligt. Det här scenariot är vanliga på vägen där de flesta SAP-resurser distribueras. Mer information finns i [planering och utformning för Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) och [skapa ett virtuellt nätverk med en plats-till-plats-anslutning med hjälp av Azure portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Klient**: en kund som distribuerats i stora HANA-instansen stämpel hämtar isolerade i en *klient.* En klient är isolerade i nätverk, lagring och beräkningslager från andra klienter. Lagrings- och enheter som är tilldelade till olika klienter kan inte se varandra eller kommunicera med varandra på stora HANA-instansen stämpel nivå. En kund kan du har distributioner i olika klienter. Det finns även sedan ingen kommunikation mellan klienter på stora HANA-instansen stämpel nivå.
- **SKU-kategori**: för stora HANA-instansen, erbjuds följande två typer av SKU: er:
    - **Typen som jag klassen**: S72, S72m, S144, S144m, S192, S192m och S192xm
    - **Skriv II klass**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm och S960m


Det finns massor av ytterligare resurser om hur du distribuerar en SAP-arbetsbelastning i molnet. Om du planerar att köra en distribution av SAP HANA i Azure måste vara med och medvetna om principerna för Azure IaaS och distribution av SAP-arbetsbelastningar på Azure IaaS. Innan du fortsätter Se [använda SAP-lösningar på Azure virtual machines](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för mer information. 

## <a name="certification"></a>Certifiering

Förutom NetWeaver-certifiering kräver SAP en särskild certifiering för SAP HANA för SAP HANA på vissa infrastrukturer, till exempel Azure IaaS.

Kärnan i NetWeaver och en grad certifiering för SAP HANA, SAP-kommentar är [SAP Obs! #1928533 – SAP-program i Azure: produkter och typer av Azure virtuella datorer stöds](https://launchpad.support.sap.com/#/notes/1928533).

Certification-poster för SAP HANA på Azure (stora instanser) enheter finns i den [IaaS-plattformar för SAP HANA-certifierade](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) plats. 

SAP HANA på Azure (stora instanser)-typer, som anges i SAP HANA-certifierade IaaS-plattformar plats, ger Microsoft och SAP-kunder kan distribuera stora SAP Business Suite, SAP BW, s/4 HANA, BW/4HANA eller andra SAP HANA-arbetsbelastningar i Azure. Lösningen bygger på stämpel för SAP HANA-certifierade dedikerad maskinvara ([SAP HANA skräddarsydda center dataintegrering – TDI](https://scn.sap.com/docs/DOC-63140)). Om du kör en lösning för SAP HANA TDI-konfigurerats, fungerar alla SAP HANA-baserade program (till exempel SAP Business Suite på SAP HANA, SAP BW på SAP HANA och S4/HANA BW4/HANA) på maskinvaru-infrastruktur.

Den här lösningen har jämfört med att köra SAP HANA på virtuella datorer, en fördel. Den innehåller för mycket större minne-volymer. Om du vill aktivera den här lösningen, måste du förstå följande viktiga aspekter:

- SAP-program lager och icke-SAP program körs i virtuella datorer som finns i stämplarna som vanliga Azure-maskinvaran.
- Kunden lokal infrastruktur, Datacenter, och distribution av program som är anslutna till molnplattform via ExpressRoute (rekommenderas) eller ett virtuellt privat nätverk (VPN). Active Directory och DNS har också utökats till Azure.
- SAP HANA-databasinstansen för HANA-arbetsbelastningar körs på SAP HANA på Azure (stora instanser). Stor instans stämpeln är ansluten till Azure-nätverk så programvara som körs på virtuella datorer kan interagera med den HANA-instansen som körs i stora HANA-instansen.
- Maskinvara för SAP HANA på Azure (stora instanser) är dedikerad maskinvara som anges i en IaaS med SUSE Linux Enterprise Server eller Red Hat Enterprise Linux förinstallerad. Är ditt ansvar precis som med virtuella datorer, ytterligare uppdateringar och underhåll för operativsystemet.
- Installation av HANA eller de ytterligare komponenterna som krävs för att köra SAP HANA på enheter av stora HANA-instansen är ditt ansvar. Alla respektive pågående åtgärder och administration av SAP HANA på Azure är också ditt ansvar.
- Förutom de lösningar som beskrivs här kan du installera andra komponenter i Azure-prenumerationen som ansluter till SAP HANA på Azure (stora instanser). Exempel är komponenter som aktiverar kommunikation med eller direkt till SAP HANA databasservrar, till exempel jump-servrar, RDP, SAP HANA-Studio, SAP-datatjänster för SAP BI-scenarier eller network övervakningslösningar.
- Som i Azure erbjuder stöd för hög tillgänglighet och funktioner för haveriberedskap i stora HANA-instansen.

## <a name="architecture"></a>Arkitektur

SAP HANA på Azure (stora instanser)-lösningen har SAP-programnivån som finns i virtuella datorer på en hög nivå. Lagret databasen finns på SAP TDI konfigurerade maskinvara finns i en stor instans stämpel i samma Azure-region som är ansluten till Azure IaaS.

> [!NOTE]
> Distribuera SAP-programnivån i samma Azure-region som SAP DBMS-lager. Den här regeln är väl dokumenterat i publicerad information om SAP-arbetsbelastningar på Azure. 

Den övergripande arkitekturen för SAP HANA på Azure (stora instanser) innehåller en SAP TDI-certifierade maskinvarukonfigurationen, vilket är en icke-virtualiserade, bare metal, högpresterande server för SAP HANA-databas. Det ger också möjlighet och Azures flexibilitet att skala resurser för SAP-programnivån som uppfyller dina behov.

![Översikt över arkitekturen för SAP HANA på Azure (stora instanser)](./media/hana-overview-architecture/image1-architecture.png)

Arkitekturen som visas är uppdelad i tre delar:

- **Höger**: Visar en lokal infrastruktur som kör olika program i data centers så att slutanvändarna kan komma åt LOB-program, till exempel SAP. Vi rekommenderar den här lokala infrastruktur sedan är ansluten till Azure med [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Center**: Visar Azure IaaS och i så fall använder virtuella datorer som värd för SAP eller andra program som använder SAP HANA som en DBMS system. Mindre HANA-instanser som fungerar med det minne som tillhandahåller virtuella datorer distribueras på virtuella datorer tillsammans med deras programnivån. Mer information om virtuella datorer finns i [virtuella datorer](https://azure.microsoft.com/services/virtual-machines/).

   Azure network-tjänster använder för att gruppera SAP-system tillsammans med andra program i virtuella nätverk. Dessa virtuella nätverk ansluta till lokala system samt att SAP HANA på Azure (stora instanser).

   SAP NetWeaver-program och databaser som stöds för att köra i Azure finns i [SAP Support Obs! #1928533 – SAP-program i Azure: produkter och typer av Azure virtuella datorer stöds](https://launchpad.support.sap.com/#/notes/1928533). Dokumentation om hur du distribuerar SAP-lösningar på Azure finns:

  -  [Använd SAP på Windows-datorer](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Använda SAP-lösningar på Azure virtual machines](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Vänster**: Visar den SAP HANA TDI-certifierade maskinvaran i stämpeln stora Azure-instanser. Stora HANA-instansen enheter är anslutna till de virtuella nätverken för din prenumeration genom att använda samma teknik som anslutningen från en lokal plats till Azure.

Stora Azure-instanser stämpeln själva kombinerar följande komponenter:

- **Databehandling**: servrar som baseras på Intel Xeon E7-8890v3 eller Intel Xeon E7-8890v4 processorer som ger den nödvändiga kapaciteten för databehandling och är SAP HANA-certifierade.
- **Nätverk**: A unified höghastighetsnätverk fabric som sammanbinder databehandling, lagring och LAN-komponenter.
- **Storage**: en lagringsinfrastruktur som nås via en enhetlig nätverksresurserna. Den specifika lagringskapacitet som tillhandahålls är beroende av specifika SAP HANA på Azure (stora instanser) konfiguration som har distribuerats. Mer lagringskapacitet finns på en månatlig avgift.

Kunder distribueras i flera innehavare-infrastrukturen i stor instans stämpeln som isolerade klienter. Vid distribution av klienten namnge en Azure-prenumeration i din Azure-registrering. Den här Azure-prenumeration är det som den stora HANA-instansen som faktureras mot. Dessa klienter har ett 1:1-förhållande till Azure-prenumerationen. Det är möjligt att få åtkomst till en enhet för stora HANA-instansen som distribueras i en klient i en Azure-region från olika virtuella nätverk som tillhör olika Azure-prenumerationer för ett nätverk. Dessa Azure-prenumerationer måste tillhöra samma Azure-registrering. 

Precis som med virtuella datorer, erbjuds SAP HANA på Azure (stora instanser) i flera Azure-regioner. För att erbjuda disaster recovery-funktioner, kan du anmäla dig. Olika stora instanser stämplar inom en geo-politiska region är anslutna till varandra. Till exempel är HANA stora instans tidsstämplar i västra USA och östra USA anslutna via ett dedikerat nätverkslänk för disaster recovery-replikering. 

Precis som du kan välja mellan olika typer av virtuella datorer med Azure virtuella datorer, kan du välja mellan olika SKU: er för stora HANA-instansen som är skräddarsydda för arbetsbelastningen för olika typer av SAP HANA. SAP gäller minne till processor socket förhållanden för varierande arbetsbelastningar som baseras på Intel-processor generationer. I följande tabell visas de SKU-typer som erbjuds.

SAP HANA på Azure (stora instanser) service är tillgänglig i flera konfigurationer i Azure-regioner i västra USA och östra USA, Östra Australien, sydöstra Australien, Västeuropa, Nordeuropa, östra Japan och västra Japan.

[SKU: er för HANA – stora instanser av SAP HANA-certifierade](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) lista som:

| SAP-lösning | Processor | Minne | Storage | Tillgänglighet |
| --- | --- | --- | --- | --- |
| Optimerat för OLAP: SAP BW, BW/4HANA<br /> eller SAP HANA för generisk OLAP-arbetsbelastning | SAP HANA på Azure S72<br /> – 2 x Intel® Xeon®-Processor E7-8890 v3<br /> 36 processorkärnor och 72 CPU-trådar |  768 GB |  3 TB | Tillgängligt |
| --- | SAP HANA på Azure S144<br /> – 4 x Intel® Xeon®-Processor E7-8890 v3<br /> 72 processorkärnor och 144 CPU-trådar |  1,5 TB |  6 TB | Erbjuds inte längre |
| --- | SAP HANA på Azure S192<br /> – 4 x Intel® Xeon®-Processor E7-8890 v4<br /> 96 processorkärnor och 192 CPU-trådar |  2.0 TB |  8 TB | Tillgängligt |
| --- | SAP HANA på Azure S384<br /> – 8 x Intel® Xeon®-Processor E7-8890 v4<br /> 192 processorkärnor och 384 CPU-trådar |  4.0 TB |  16 TB | Tillgängligt |
| Optimerat för OLTP: SAP Business Suite<br /> på SAP HANA eller S/4HANA (OLTP)<br /> generisk OLTP | SAP HANA på Azure S72m<br /> – 2 x Intel® Xeon®-Processor E7-8890 v3<br /> 36 processorkärnor och 72 CPU-trådar |  1,5 TB |  6 TB | Tillgängligt |
|---| SAP HANA på Azure S144m<br /> – 4 x Intel® Xeon®-Processor E7-8890 v3<br /> 72 processorkärnor och 144 CPU-trådar |  3.0 TB |  12 TB | Erbjuds inte längre |
|---| SAP HANA på Azure S192m<br /> – 4 x Intel® Xeon®-Processor E7-8890 v4<br /> 96 processorkärnor och 192 CPU-trådar  |  4.0 TB |  16 TB | Tillgängligt |
|---| SAP HANA på Azure S384m<br /> – 8 x Intel® Xeon®-Processor E7-8890 v4<br /> 192 processorkärnor och 384 CPU-trådar |  6.0 TB |  18 TB | Tillgängligt |
|---| SAP HANA på Azure S384xm<br /> – 8 x Intel® Xeon®-Processor E7-8890 v4<br /> 192 processorkärnor och 384 CPU-trådar |  8.0 TB |  22 TB |  Tillgängligt |
|---| SAP HANA på Azure S576m<br /> – 12 x Intel® Xeon®-Processor E7-8890 v4<br /> 288 processorkärnor och 576 CPU-trådar |  12.0 TB |  28 TB | Tillgängligt |
|---| SAP HANA på Azure S768m<br /> – 16 x Intel® Xeon®-Processor E7-8890 v4<br /> 384 processorkärnor och 768 CPU-trådar |  16,0 TB |  36 TB | Tillgängligt |
|---| SAP HANA på Azure S960m<br /> – 20 x Intel® Xeon®-Processor E7-8890 v4<br /> 480 processorkärnor och 960 CPU-trådar |  20.0 TB |  46 TB | Tillgängligt |


Under TDIv5 för SAP HANA kan SAP kundspecifika storlek och kundspecifika projekt som kan leda till serverkonfigurationer som inte är listade som certifierats i:

- [SAP HANA-certifierade enheter](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA-certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

I många fall har dessa kundspecifika serverkonfigurationer mer minne än de serverenheterna certifierad med SAP. Arbetar med SAP har kunderna möjlighet att få SAP support och certifiera för sina kundspecifika storlekar serverkonfigurationer. I Azure följande stora HANA-instansen standard SKU: er är tillgängliga och i Microsofts prislista för sådana TDIv5 kundspecifika storlek projekt.


| Ursprungliga SKU som kan vara <br /> utökade i minnet | Processor | Minne | Storage | Tillgänglighet |
| --- | --- | --- | --- | --- |
| S192m kan utökas till | SAP HANA på Azure S192xm<br /> – 4 x Intel® Xeon®-Processor E7-8890 v4<br /> 96 processorkärnor och 192 CPU-trådar |  6.0 TB |  16 TB | Tillgängligt |
| S384xm kan utökas till | SAP HANA på Azure S384xxm<br /> – 8 x Intel® Xeon®-Processor E7-8890 v4<br /> 192 processorkärnor och 384 CPU-trådar |  12.0 TB |  28 TB | Tillgängligt |
| S576m kan utökas till | SAP HANA på Azure S576xm<br /> – 12 x Intel® Xeon®-Processor E7-8890 v4<br /> 288 processorkärnor och 576 CPU-trådar |  18.0 TB |  41 TB | Tillgängligt |
| S768m kan utökas till | SAP HANA på Azure S768xm<br /> – 16 x Intel® Xeon®-Processor E7-8890 v4<br /> 384 processorkärnor och 768 CPU-trådar |  24.0 TB |  56 TB | Tillgängligt |

- CPU-kärnor = summan av icke-hyper-threaded processorkärnor summan av processorer med server-enhet.
- CPU-trådar = summan av beräkning trådar som tillhandahålls av hyper-threaded processorkärnor summan av processorer med server-enhet. De flesta enheter är konfigurerade som standard att använda Hyper-Threading Technology.
- Utifrån leverantör rekommendationer S768m har S768xm och S960m inte konfigurerats för att använda Hyper-Threading för att köra SAP HANA.


Vilka konfigurationer valt är beroende av arbetsbelastning, CPU-resurser och önskad minne. Det är möjligt för arbetsbelastningen för OLTP som att använda SKU: er som är optimerade för OLAP-arbetsbelastning. 

Maskinvaran som bas för betalning, förutom enheter för kundspecifika storlek projekt är SAP HANA TDI-certifierade. Två olika klasser av maskinvara dela SKU: er till:

- S72, S72m, S144, S144m, S192, S192m och S192xm, som kallas ”typen jag klassen” SKU: er.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm och S960m som kallas den ”Type II klass” SKU: er.

En fullständig stämpel för stora HANA-instansen inte är exklusivt allokeras för en enda kund&#39;s användning. Detta gäller för beräknings- och lagringsresurser som är anslutna via en nätverksinfrastruktur som distribueras i Azure samt serverrack. Infrastruktur för stora HANA-instansen, som Azure, distribuerar annan kund &quot;klienter&quot; som är isolerade från varandra i följande tre nivåer:

- **Nätverk**: isolering via virtuella nätverk i stämpeln stora HANA-instansen.
- **Storage**: isolering via storage virtuella datorer som har tilldelats lagringsvolymer och isolera lagringsvolymer mellan klienter.
- **Compute**: dedikerade tilldelningen av server-enheter till en enda klient. Inte hårt eller mjuk partitionering av server-enheter. Ingen delning av en enda enhet för servern eller värden mellan klienter. 

Distributioner av stora HANA-instansen enheter mellan olika klienter är inte synliga för varandra. Stora HANA-instansen enheter som distribueras i olika klienter kan inte kommunicera direkt med varandra på stora HANA-instansen stämpel nivå. Endast stora HANA-instansen enheter inom en klient kan kommunicera med varandra på stora HANA-instansen stämpel nivå.

En distribuerad klient i stor instans stämpeln är tilldelad till en Azure-prenumeration för fakturering. För ett nätverk, kan den nås från virtuella nätverk med andra Azure-prenumerationer i samma Azure-registrering. Om du distribuerar med en annan Azure-prenumeration i samma Azure-region, kan du också välja att be om en klient för avgränsas stora HANA-instansen.

Det finns betydande skillnader mellan att köra SAP HANA på stora HANA-instansen och SAP HANA som körs på virtuella datorer som distribueras i Azure:

- Det finns inga virtualiseringsskiktet för SAP HANA på Azure (stora instanser). Du får prestanda för den underliggande maskinvaran utan operativsystem.
- Till skillnad från Azure, är SAP HANA på Azure (stora instanser)-servern dedikerad till en viss kund. Det finns ingen risk att ett server-enhet eller en värddator är hårt eller mjuk partitionerade. Därför används en enhet för stora HANA-instansen som helhet till en klient och med som du tilldelats. En omstart eller avstängning av servern leda inte automatiskt till operativsystemet och SAP HANA som distribueras på en annan server. (För typen jag klass SKU: er, det enda undantaget är om en server påträffar problem och omdistribution måste utföras på en annan server.)
- Till skillnad från Azure, där värden processor väljs för förhållandet bästa pris/prestanda, är processor-typer som valts för SAP HANA på Azure (stora instanser) högsta prestanda av raden Intel E7v3 och E7v4 processor.


### <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Köra flera SAP HANA-instanser på en enhet för stora HANA-instansen
Det är möjligt att vara värd för flera aktiva SAP HANA-instansen på stora HANA-instansen enheter. För att tillhandahålla funktioner för ögonblicksbilder av lagring och katastrofåterställning, kräver en sådan konfiguration en volym som angetts per instans. För närvarande kan stora HANA-instansen enheter delas upp på följande sätt:

- **S72, S72m, S144, S192**: I steg om 256 GB, 256 GB är den minsta från enheten. Olika steg, till exempel 256 GB och 512 GB kan kombineras till maximalt minne på enheten.
- **S144m och S192m**: I steg om 256 GB, 512 GB är den minsta enheten. Olika steg som 512 GB och 768 GB kan kombineras till maximalt minne på enheten.
- **Skriv II klass**: I steg om 512 GB med den minsta från enheten på 2 TB. Olika steg som 512 GB, 1 TB och 1,5 TB kan kombineras till maximalt minne på enheten.

Några exempel på att köra flera SAP HANA-instanser kan se ut så här.

| SKU | Minnesstorlek | Lagringsstorlek | Storlekar med flera databaser |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 x 768 GB HANA-instans<br /> eller 1 x 512 GB instans + 1 x 256 GB-instans<br /> eller 3 x 256 GB instanser | 
| S72m | 1,5 TB | 6 TB | 3x512GB HANA-instanser<br />eller 1 x 512 GB instans + 1 x 1 TB-instans<br />eller 6 x 256 GB instanser<br />eller 1x1.5 TB-instans | 
| S192m | 4 TB | 16 TB | 8 x 512 GB instanser<br />eller 4 x 1 TB-instanser<br />eller 4 x 512 GB instanser + 2 x 1 TB-instanser<br />eller 4 x 768 GB instanser + 2 x 512 GB instanser<br />eller 1 × 4 TB-instans |
| S384xm | 8 TB | 22 TB | 4 × 2 TB-instanser<br />eller 2 × 4 TB-instanser<br />eller 2 x 3 TB-instanser + 1 x 2 TB-instanser<br />eller 2x2.5 TB-instanser + 1 x 3 TB-instanser<br />eller 1 x 8 TB-instans |


Det finns även andra varianter. 

### <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Använda SAP HANA-tillägget och lagringsnivåer datanoder
SAP har stöd för en datamodell för lagringsnivåer för SAP BW i olika utgåvor av SAP NetWeaver och SAP BW/4HANA. Mer information om lagringsnivåer datamodellen finns i SAP-dokumentet [SAP BW/4HANA och SAP BW på HANA med SAP HANA-tillägget noder](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Du kan använda alternativ 1 konfiguration av SAP HANA-tillägget noder som beskrivs i vanliga frågor och svar och SAP-bloggen dokument med stora HANA-instansen. Alternativ 2 konfigurationer kan ställas in med följande HANA stora instans SKU: er: S72m, S192, S192m, S384 och S384m. 

När du granskar dokumentationen kanske fördelen inte visas omedelbart. Men när du tittar på riktlinjer för SAP-storlek, du kan se en fördel med hjälp av alternativet-1 och alternativ 2 SAP HANA-tillägget noder. Här följer exempel:

- Riktlinjer för storleksändring av SAP HANA kräver vanligtvis dubbelt så mycket datavolym som minne. När du kör dina SAP HANA-instans med frekventa data du har bara 50 procent eller mindre minne fylld med data. Resten av minnet lagras helst för SAP HANA utför sitt arbete.
- Innebär det att i en HANA stora instans S192-enhet med 2 TB minne, köra ett SAP BW-databas du endast har 1 TB som datavolym.
- Om du använder en ny SAP HANA-tillägg-nod i alternativ 1, även en S192 HANA stora SKU instans, får du en ytterligare 2 TB kapacitet för datavolym. I alternativ 2-konfiguration får du en ytterligare 4 TB för varm datavolym. Jämfört med frekvent noden, kan fullständig minneskapaciteten för noden ”varma” tillägget användas för lagring av data för alternativ 1. Dubbla minnet som kan användas för datavolymen i alternativ 2 nodkonfiguration för SAP HANA-tillägget.
- Att avslutas med en kapacitet på 3 TB för dina data och ett hot att varmt förhållande på 1:2 för alternativ 1. Du har 5 TB med data och ett 1:4-förhållande med nodkonfiguration alternativ 2-tillägget.

Ju högre datavolym du jämfört med att minnet, desto högre risken är att frekventa data som du efterfrågar lagras på disklagring.


## <a name="operations-model-and-responsibilities"></a>Driftmodell och ansvarsområden

Den tillhandahållna med SAP HANA på Azure (stora instanser) är i linje med Azure IaaS-tjänster. Du får en instans av en stora HANA-instans med ett installerat operativsystem som är optimerad för SAP HANA. Som med Azure IaaS-VM, de flesta uppgifter härda Operativsystemet och installera ytterligare programvara, installera HANA, OS och HANA, uppdatera operativsystem och HANA är ditt ansvar. Microsoft göra inte uppdateringar av OS- eller HANA uppdateringar på du.

![Ansvaret för SAP HANA på Azure (stora instanser)](./media/hana-overview-architecture/image2-responsibilities.png)

I diagrammet visas är SAP HANA på Azure (stora instanser) en flera innehavare som erbjuder IaaS. För det mesta har divisionen av ansvar nått gränsen för OS-infrastruktur. Microsoft ansvarar för alla aspekter av tjänsten under raden i operativsystemet. Du är ansvarig för alla aspekter av tjänsten ovanför raden. Operativsystemet är ditt ansvar. Du kan fortsätta att använda den mest aktuella lokala metoder du kan använda för efterlevnad, säkerhet, programhantering, dag och OS-hantering. System som visas som om de finns i nätverket när det kommer alla.

Den här tjänsten är optimerad för SAP HANA, så det finns områden där du behöver arbeta med Microsoft för att använda funktionerna för underliggande infrastruktur för bästa resultat.

I följande lista finns mer information på varje lager och dina ansvarsområden:

**Nätverk**: alla interna nätverk för stor instans stämpeln köra SAP HANA. Ditt ansvar ger åtkomst till lagring, anslutning mellan instanser (för skalbarhet och andra funktioner), anslutning till liggande och anslutningar till Azure där SAP-programnivån finns i virtuella datorer. Den innehåller också WAN-anslutning mellan Azure-Datacenter för disaster recovery-replikering för syften. Alla nätverk partitioneras av klienten och har tjänstkvalitet tillämpas.

**Storage**: virtualiserade partitionerad lagring för alla volymer som krävs av SAP HANA-servrar, samt för ögonblicksbilder. 

**Servrar**: särskilda fysiska servrar att köra SAP HANA-databaser som tilldelats till klienter. Servrar av typen jag klassen SKU: er är maskinvara som sådant. Med den här typen av servrar, serverkonfigurationen samlas in och underhålls i profiler som kan flyttas från en fysisk maskinvara till en annan fysisk maskinvara. Sådana (manuellt) flytta för en profil av åtgärder kan jämföras lite med Azure tjänståterställning. Servrarna i SKU: er för Type II-klass erbjuder inte en sådan funktion.

**SDDC**: programvaran för hantering som används för att hantera data datacenter som programdefinierade entiteter. Det kan Microsoft poolresurser för skalbarhet, tillgänglighet och prestanda.

**Operativsystem**: OS du väljer (SUSE Linux eller Red Hat Linux) som körs på servrarna. OS-avbildningar som du levereras med tillhandahölls av enskilda Linux-Försäljare till Microsoft för att köra SAP HANA. Du måste ha en prenumeration med Linux-försäljare för den specifika bilden för SAP HANA-optimerade. Du ansvarar för att registrera avbildningarna med OS-leverantören. 

Från det datum då jourtjänstöverlämnande av Microsoft är du ansvarig för eventuella ytterligare korrigeringar av Linux-operativsystem. Denna uppdatering innehåller ytterligare paket som kan krävas för en lyckad SAP HANA-installation och som inte finns med den specifika Linux-leverantör i sina SAP HANA optimerade OS-avbildningar. (Mer information finns i SAP: s HANA installationsdokumentationen och SAP Notes.) 

Du ansvarar för underhåll av operativsystem på grund av fel på utrustningen eller dess drivrutiner i förhållande till den specifika servermaskinvaran och optimering av Operativsystemet. Du är också ansvarig för säkerhets- eller funktionella uppdatering av Operativsystemet. 

Ditt ansvar omfattar också övervakning och kapacitetsplanering för:

- CPU-resursförbrukning.
- Minnesanvändningen.
- Diskvolymer relaterar till ledigt utrymme, IOPS och svarstider.
- Volymen nätverkstrafik mellan stora HANA-instansen och SAP-programnivån.

Den underliggande infrastrukturen med stora HANA-instansen innehåller funktioner för säkerhetskopiering och återställning av systemvolymen. Med den här funktionen är också ansvarig.

**Mellanprogram**: SAP HANA-instans, främst. Är du ansvarig för administration, åtgärder och övervakning. Du kan använda de angivna funktionerna för att använda ögonblicksbilder av lagring för säkerhetskopiering, återställning och disaster recovery-syften. Dessa funktioner tillhandahålls av infrastrukturen. Dina ansvarsområden även innehålla designa hög tillgänglighet och haveriberedskap med dessa funktioner kan utnyttja dem och övervaka att avgöra om ögonblicksbilder av lagring har körts.

**Data**: dina data hanteras av SAP HANA och andra data, till exempel säkerhetskopior delar filer i volymer eller fil. Dina ansvarsområden omfattar övervaka ledigt diskutrymme och hantera innehåll på volymerna. Du är också ansvarig för att övervaka säkerhetskopior av volymer på diskar och ögonblicksbilder av lagring har körts. Lyckad körning av datareplikering till katastrofåterställningsplatser ansvarar för Microsoft.

**Program:** programinstanser SAP eller när det gäller icke-SAP-program, programlagret för programmen. Dina ansvarsområden omfattar distribution, administration, åtgärder och övervakning av dessa program. Du är ansvarig för kapacitetsplanering för CPU-resursförbrukning, minnesförbrukning, Azure Storage-förbrukning och förbrukningen av nätverksbandbredd i virtuella nätverk. Du är också ansvarig för kapacitetsplanering för resursförbrukning från virtuella nätverk till SAP HANA på Azure (stora instanser).

**WAN-nätverk**: anslutningar som du har upprättat från lokalt till Azure-distributioner för arbetsbelastningar. Alla kunder med stora HANA-instansen använder Azure ExpressRoute för anslutning. Den här anslutningen är inte en del av SAP HANA på Azure (stora instanser)-lösningen. Du är ansvarig för installationen av den här anslutningen.

**Arkivera**: kanske du föredrar att arkivera kopior av data med hjälp av dina egna metoder i storage-konton. Arkivering kräver hantering, efterlevnad, kostnader och åtgärder. Du ansvarar för att generera Arkiv kopior och säkerhetskopieringar på Azure och lagra dem på ett sätt som är kompatibla.

Se den [SLA för SAP HANA på Azure (stora instanser)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Storleksändring

Storlek för stora HANA-instansen är inte skiljer sig från att ändra storlek för HANA i allmänhet. För befintliga och distribuerade system som du vill flytta från andra RDBMS för HANA, SAP tillhandahåller ett antal rapporter som körs på dina befintliga SAP-system. Om databasen flyttas till HANA, kontrollera data som de här rapporterna och beräkna minneskraven för HANA-instans. Läs följande SAP-information för mer information om hur du kör dessa rapporter och hämta sina senaste korrigeringsfiler eller versioner:

- [SAP-kommentar #1793345 - storlek för SAP programsvit på HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Obs! #1872170 - programsvit på HANA och s/4 HANA storlek rapport](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP-kommentar #2121330 – vanliga frågor och svar: SAP BW för HANA som ändrar storlek på rapporten](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Obs! #1736976 - storlek rapporten för BW på HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Obs! #2296290 - storlek ny rapport för BW på HANA](https://launchpad.support.sap.com/#/notes/2296290)

SAP snabb Ange storlek är tillgänglig att beräkna minneskrav för implementering av SAP-programvara på HANA för implementeringar av grönt fält.

Minneskraven för HANA öka när datavolymen växer. Tänk på din aktuella minnesanvändningen för att förutse vad den ska vara i framtiden. Baserat på minneskrav, kan sedan du mappa din begäran till någon av HANA-SKU: er för stor instans.

## <a name="requirements"></a>Krav

Den här listan monterar krav för att köra SAP HANA på Azure (stora instanser).

**Microsoft Azure**

- En Azure-prenumeration som kan länkas till SAP HANA på Azure (stora instanser).
- Microsoft Premier support-kontrakt. Specifik information som rör köra SAP i Azure finns i [SAP Support Obs! #2015553 – SAP på Microsoft Azure: supportkrav](https://launchpad.support.sap.com/#/notes/2015553). Om du använder stora HANA-instansen enheter 384 och mer CPU: er kan du också behöva utöka Premier support-kontrakt med Azure Rapid Response.
- Medvetenhet om HANA stora instans SKU: er du behöver när du har utfört en storlek Övning med SAP.

**Nätverksanslutning**

- ExpressRoute mellan en lokal plats till Azure: se till att beställa på minst 1 Gbit/s-anslutning från Leverantören för att ansluta ditt lokala Datacenter till Azure. 

**Operativsystem**

- Licenser för SUSE Linux Enterprise Server 12 för SAP-program.

   > [!NOTE] 
   > Operativsystemet som levereras av Microsoft är inte registrerad med SUSE. Det är inte ansluten till en prenumeration hanteringsverktyg-instans.

- SUSE Linux prenumeration hanteringsverktyg distribueras i Azure på en virtuell dator. Det här verktyget innehåller funktioner för SAP HANA på Azure (stora instanser) ska registreras och respektive uppdaterad av SUSE. (Det finns ingen internet-åtkomst i datacenter stora HANA-instansen.) 
- Licenser för Red Hat Enterprise Linux 6.7 eller 7.x för SAP HANA.

   > [!NOTE]
   > Operativsystemet som levereras av Microsoft är inte registrerad med Red Hat. Det är inte ansluten till en Red Hat prenumeration Manager-instans.

- Red Hat prenumeration Manager distribueras i Azure på en virtuell dator. Red Hat prenumeration Manager innehåller funktioner för SAP HANA på Azure (stora instanser) ska registreras och uppdateras respektive från Red Hat. (Det finns ingen direkt Internetåtkomst från den klient som distribuerats på stora Azure-instanser stämpeln.)
- SAP kräver att du har stöd för ett avtal med din Linux-leverantör. Det här kravet tas inte bort med hjälp av lösningen på stora HANA-instansen eller det faktum att du kör Linux i Azure. Till skillnad från med några av galleriavbildningar Linux Azure serviceavgiften är *inte* ingår i erbjudandet lösning för stora HANA-instansen. Det är ditt ansvar att uppfylla kraven i SAP om supportkontrakt med Linux-distributören. 
   - För SUSE Linux, leta upp kraven för supportkontrakt i [SAP Obs! #1984787 - SUSE Linux Enterprise Server 12: installationsinformation](https://launchpad.support.sap.com/#/notes/1984787) och [SAP Obs! #1056161 - SUSE prioriterad support för SAP-program](https://launchpad.support.sap.com/#/notes/1056161).
   - Du måste ha rätt prenumerationsnivåer som omfattar support och uppdateringar för operativsystem för stora HANA-instansen av tjänsten för Red Hat Linux. Red Hat rekommenderar Red Hat Enterprise Linux för [SAP-lösningar] (https://access.redhat.com/solutions/3082481 prenumeration. 

Stödmatris för de olika versionerna för SAP HANA med olika Linux-versioner, se [SAP Obs! #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Kompatibilitetsöversikten av operativsystemet och HLI firmware/drivrutinsversion finns [uppgradering av operativsystem för HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> Versionen stöds nu för Type II enheter endast SLES 12 SP2 OS. 


**Databas**

- Licenser och software installationskomponenter för SAP HANA (plattform eller enterprise edition).

**Program**

- Licenser och programvarukomponenter i installationen för alla SAP-program som ansluter till SAP HANA och relaterade SAP supportavtal.
- Licenser och programvarukomponenter i installationen för alla icke-SAP-program används i förhållande till SAP HANA på Azure (stora instanser)-miljöer och relaterade supportavtal.

**Kunskaper**

- Erfarenhet och kunskap om Azure IaaS och dess komponenter.
- Erfarenhet och kunskap om hur du distribuerar en SAP-arbetsbelastning i Azure.
- SAP HANA-installation-certifierad personal.
- SAP architect kunskaper för att utforma hög tillgänglighet och katastrofåterställning recovery runt SAP HANA.

**SAP**

- Förutsätts att du är en SAP-kund och har stöd för ett avtal med SAP.
- Kontakta SAP på versioner av SAP HANA och eventuell konfigurationer i stor skala upp maskinvaran särskilt för implementeringar av typ II-klassen för HANA stora instans SKU: er.


## <a name="storage"></a>Storage

Lagringslayout för SAP HANA på Azure (stora instanser) har konfigurerats av SAP HANA på den klassiska distributionsmodellen via SAP rekommenderade riktlinjer. Riktlinjerna finns dokumenterade i den [lagringskrav för SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) white paper om.

Den stora HANA-instansen av typen jag klassen har fyra gånger minne volymen lagringsvolym. Lagringen inte fyra gånger mer för Type II-klassen för stora HANA-instansen enheter. Enheterna levereras med en volym som är avsedd för att lagra HANA säkerhetskopieringar av transaktionsloggen. Mer information finns i [installera och konfigurera SAP HANA (stora instanser) på Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Se tabellen nedan när det gäller lagringsallokering. I tabell visas ungefärlig kapacitet för olika volymer som ingår i de olika enheterna för stora HANA-instansen.

| Stora HANA-instansen SKU | Hana-data | Hana/log | Hana/delat | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1 280 GB | 768 GB |
| S192 | 4 608 GB | 1 024 GB | 1,536 GB | 1 024 GB |
| S192m | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S192xm |  11,520 GB |  1,536 GB |  1,792 GB |  1,536 GB |
| S384 | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384m | 12 000 GB | 2,050 GB | 2,050 GB | 2 040 GB |
| S384xm | 16 000 GB | 2,050 GB | 2,050 GB | 2 040 GB |
| S384xxm |  20 000 GB | 3 100 GB | 2,050 GB | 3 100 GB |
| S576m | 20 000 GB | 3 100 GB | 2,050 GB | 3 100 GB |
| S576xm | 31,744 GB | 4 096 GB | 2 048 GB | 4 096 GB |
| S768m | 28 000 GB | 3 100 GB | 2,050 GB | 3 100 GB |
| S768xm | 40 960 GB | 6 144 GB | 4 096 GB | 6 144 GB |
| S960m | 36,000 GB | 4,100 GB | 2,050 GB | 4,100 GB |


Faktiska distribuerade volymer kan variera beroende på distributionen och verktyg som används för att visa volymer större.

Om du dela upp en HANA SKU med stor instans kan några exempel på möjliga division delar se ut:

| Minnespartition i GB | Hana-data | Hana/log | Hana/delat | Hana/log/säkerhetskopiering |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1 024 GB | 640 GB |
| 1,536 | 3,328 GB | 768 GB | 1 280 GB | 768 GB |


Dessa storlekar är grov volym-värden som kan variera något beroende på distributionen och de verktyg som används för att titta på volymerna. Det finns även andra partitionsstorlekarna, till exempel 2,5 TB. Dessa lagringsstorlekar beräknas med en formel som liknar den som används för de föregående partitionerna. Termen ”partitioner” betyder att det operativsystem, minne eller CPU-resurser på något sätt partitioneras. Den anger partitioner för lagring för de olika HANA-instanser som du vill distribuera i en enskild enhet för stora HANA-instansen. 

Du kanske behöver mer lagringsutrymme. Du kan lägga till lagring genom att köpa ytterligare lagringsutrymme i enheter om 1 TB. Den här ytterligare lagringsutrymme kan läggas till som ytterligare volym. Det kan också användas till att utöka en eller flera av de befintliga volymerna. Det är inte möjligt att minska storleken på volymerna som ursprungligen distribueras och huvudsakligen dokumenterats i föregående tabell. Det är inte heller går att ändra namnen på volymerna eller montera namn. Lagringsvolymer som beskrivs ovan är kopplade till stora HANA-instansen-enheter som NFS4 volymer.

Du kan använda ögonblicksbilder av lagring för säkerhetskopiering, återställning och haveriberedskap återställningen. Mer information finns i [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Se [HLI stöds scenarier](hana-supported-scenario.md) för layout av lagringsinformation för ditt scenario.

### <a name="encryption-of-data-at-rest"></a>Kryptering av vilande data
Lagring för stora HANA-instansen kan en transparent kryptering av data som lagras på diskarna. När en enhet för stora HANA-instansen har distribuerats kan aktivera du den här typen av kryptering. Du kan också ändra till krypterade volymer efter distributionen äger rum. Övergången från icke-krypterade till krypterade volymer är transparent och kräver driftstopp. 

Med typen jag klassen av SKU: er, Start LUN lagras på volymen är krypterad. Du måste kryptera Start LUN med OS-metoder för Type II-klassen för SKU: er för stora HANA-instansen. Mer information kontaktar du Microsoft Service Management-teamet.


## <a name="networking"></a>Nätverk

Arkitekturen i Azure network-tjänster är en viktig del av distributionen av SAP-program på stora HANA-instansen. SAP HANA på Azure (stora instanser) distributioner har vanligtvis en större SAP-landskap med flera olika SAP-lösningar med olika storlekar av databaser, resursförbrukning för CPU och minnesanvändning. Det är troligt att inte alla dessa SAP-system baseras på SAP HANA. Ditt SAP-landskap är förmodligen en hybridlösning som använder:

- Distribuera SAP-system på plats. På grund av storlek, kan inte systemen för närvarande finnas i Azure. Ett exempel är en SAP ERP-system som körs på SQL Server (som databasen) och kräver mer resurser som CPU eller minne än vad som virtuella datorer i produktionsmiljön.
- Distribuera SAP HANA-baserade SAP system på plats.
- Distribuerade SAP-system i virtuella datorer. Dessa system kan vara utveckling, testning, sandbox-miljön, eller produktion-instanser för någon av de SAP NetWeaver-baserade program som kan distribuera i Azure (på VM: ar), baserat på användnings- och resursbehov. Dessa system kan också baseras på databaser som SQL Server. Mer information finns i [SAP Support Obs! #1928533 – SAP-program i Azure: produkter och typer av Azure virtuella datorer stöds](https://launchpad.support.sap.com/#/notes/1928533/E). Och dessa system kan baseras på databaser som SAP HANA. Mer information finns i [IaaS-plattformar för SAP HANA-certifierade](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
- Distribuera SAP-programservrar i Azure (på virtuella datorer) som utnyttjar SAP HANA på Azure (stora instanser) i Azure stora instanser stämplar.

En SAP-hybridlandskap med fyra eller flera olika distributionsscenarier är vanligt. Det finns också många kundärenden av fullständig SAP-landskap som körs i Azure. När virtuella datorer blir mer kraftfulla, ökar antalet kunder som flyttar alla sina SAP-lösningar på Azure.

Azure-nätverk i samband med SAP-system som distribueras i Azure är inte svårt. Den baseras på följande principer:

- Azure-nätverk måste vara ansluten till ExpressRoute-krets som ansluter till ett lokalt nätverk.
- En ExpressRoute-krets som ansluter vanligtvis lokalt till Azure bör ha en bandbredd 1 Gbit/s eller högre. Den här minimal bandbredd kan tillräcklig bandbredd för överföring av data mellan lokala system och system som körs på virtuella datorer. Det gör också tillräcklig bandbredd för anslutning till Azure-system från en lokal användare.
- Alla SAP-system i Azure måste konfigureras i virtuella nätverk kan kommunicera med varandra.
- Active Directory och DNS finns lokalt har utökats till Azure via ExpressRoute från en lokal plats.


> [!NOTE] 
> Från en fakturering synvinkel kan endast en Azure-prenumeration länkas till endast en klient i en stor instans stämpel i en specifik Azure-region. Däremot kan en enda stor instans stämpel klient länkas till endast en Azure-prenumeration. Det här kravet är konsekvent med andra fakturerbara objekt i Azure.

Om SAP HANA på Azure (stora instanser) distribueras i flera olika Azure-regioner, distribueras en separat klient i stämpeln stor instans. Du kan köra både under samma Azure-prenumeration, så länge dessa instanser är en del av samma SAP-landskap. 

> [!IMPORTANT] 
> Endast Azure Resource Manager-distribution stöds med SAP HANA på Azure (stora instanser).

 

### <a name="additional-virtual-network-information"></a>Information om ytterligare virtuella nätverk

Om du vill ansluta ett virtuellt nätverk till ExpressRoute, måste du skapa en Azure-gateway. Mer information finns i [om virtuella nätverksgatewayer för ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 

En Azure-gatewayen kan användas med ExpressRoute till en infrastruktur utanför Azure eller till en stämpel med stora Azure-instanser. En Azure-gateway kan också användas för att ansluta mellan virtuella nätverk. Mer information finns i [Konfigurera anslutning till ett nätverk till nätverk för Resource Manager med hjälp av PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Du kan ansluta Azure-gatewayen till högst fyra olika ExpressRoute-anslutningar så länge anslutningarna kommer från olika Microsoft enterprise-gränsroutrar. Mer information finns i [SAP HANA (stora instanser) infrastruktur och anslutningsmöjlighet på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Dataflödet som tillhandahåller en Azure-gateway skiljer sig till båda användningsfall. Mer information finns i [om VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Det maximala dataflöde som du kan uppnå med en virtuell nätverksgateway är 10 Gbit/s med hjälp av en ExpressRoute-anslutning. Kopiera filer mellan en virtuell dator som finns i ett virtuellt nätverk och ett system på plats (som en enda kopia dataström) inte uppnår fullt genomflöde av olika gateway SKU: er. Använda flera strömmar för att använda fullständig bandbredden för den virtuella nätverksgatewayen. Eller kopierar olika filer i parallella strömmar av en enda fil.


### <a name="networking-architecture-for-hana-large-instance"></a>Nätverksarkitekturen för stora HANA-instansen
Nätverk arkitekturen för stora HANA-instansen kan delas upp i fyra olika delar:

- Lokala nätverk och ExpressRoute-anslutning till Azure. Den här delen är kundens domän och är ansluten till Azure via ExpressRoute. Se längst ned till höger i följande bild.
- Azure network-tjänster, som tidigare diskuteras med virtuella nätverk som har gatewayer igen. Den här delen är ett område där du behöver hitta lämpliga Designer för programkrav-, säkerhets- och efterlevnadskrav. Om du använder stora HANA-instansen är en annan aspekt att tänka på när det gäller hur många virtuella nätverk och Azure-gateway SKU: er att välja bland. Finns i det övre högra hörnet i bilden.
- Anslutningar för stora HANA-instansen via ExpressRoute-teknik till Azure. Den här delen distribueras och hanteras av Microsoft. Allt du behöver göra är att tillhandahålla vissa IP-adressintervall efter distributionen av dina tillgångar i stora HANA-instansen ansluta ExpressRoute-kretsen till de virtuella nätverken. Mer information finns i [SAP HANA (stora instanser) infrastruktur och anslutningsmöjlighet på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Nätverk i stora HANA-instansen, som är mest transparent för dig.

![Virtuella nätverk som anslutits till SAP HANA på Azure (stora instanser) och lokala](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Kravet att dina lokala resurser måste ansluta via ExpressRoute till Azure ändras inte eftersom du använder stora HANA-instansen. Kravet på att en eller flera virtuella nätverk som kör de virtuella datorerna, som är värd för programnivån som ansluter till HANA-instanser på stora HANA-instansen enheter, även ändras inte. 

Skillnaderna för SAP-distributioner i Azure är:

- De stora HANA-instansen enheterna för din kundklient är anslutna via en annan ExpressRoute-krets i ditt virtuella nätverk. För att avgränsa belastningstillstånd, dela lokalt virtuella nätverk ExpressRoute länkar och länkarna mellan virtuella nätverk och stora HANA-instansen inte samma routrar.
- Profilen arbetsbelastningen mellan SAP-programnivån och den stora HANA-instansen är av olika slag, med många små begäranden och toppar som data som överförs (resultatmängder) från SAP HANA i programlagret.
- Arkitektur för SAP-program är mer känsliga för fördröjningar i nätverket än vanliga scenarier där data överförs mellan lokala och Azure.
- Den virtuella nätverksgatewayen har minst två ExpressRoute-anslutningar. Båda anslutningarna dela den maximala bandbredden för inkommande data för den virtuella nätverksgatewayen.

Nätverksfördröjning uppstod mellan virtuella datorer och stora HANA-instansen enheter kan vara högre än en typisk VM-till-VM network fördröjningen. Beroende på Azure-region, de mätvärdena kan överstiga 0,7-ms-fördröjningen klassificeras som under genomsnittet i [SAP Obs! #1100926 – vanliga frågor och svar: nätverksprestanda](https://launchpad.support.sap.com/#/notes/1100926/E). Kunder kan dock distribuera SAP HANA-baserade produktion SAP-program på stora SAP HANA-instansen. De kunder som distribuerats bra rapportförbättringar genom att köra sina SAP-program på SAP HANA med hjälp av enheter för stora HANA-instansen. Kontrollera noggrant testa dina affärsprocesser i Azure HANA stora instanser.
 
För att ge deterministisk Nätverksfördröjningen mellan virtuella datorer och HANA stora instanser, val av den virtuella nätverksgatewayen SKU är viktigt. Mönster för trafik mellan virtuella datorer och stora HANA-instansen kan utveckla små men hög belastning med begäranden och data volymer som ska överföras till skillnad från trafikmönster mellan lokala och virtuella datorer. För att hantera sådana toppar och rekommenderar vi användning av SKU för UltraPerformance-gateway. Användningen av SKU för UltraPerformance-gateway som en virtuell nätverksgateway är obligatoriskt för Type II-klassen för HANA stora instans SKU: er.

> [!IMPORTANT] 
> Med den övergripande nätverkstrafiken mellan SAP-programmet och databasen lager kan som bara HighPerformance eller UltraPerformance gateway SKU: er för virtuella nätverk stöds för att ansluta till SAP HANA på Azure (stora instanser). För HANA stora instans typ II-SKU: er stöds endast UltraPerformance gateway-SKU som en virtuell nätverksgateway.



### <a name="single-sap-system"></a>Enkel SAP-system

Den lokala infrastrukturen som visats tidigare är anslutet via ExpressRoute i Azure. ExpressRoute-krets som ansluter till en enterprise edge-routern. Mer information finns i [teknisk översikt över ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). När flödet har upprättats, ansluter den till Azures stamnätverk och alla Azure-regioner är tillgängliga.

> [!NOTE] 
> Kör SAP-landskap i Azure genom att ansluta till enterprise gränsroutern agerar på Azure-regioner i SAP-landskap. Azure stora instanser stämplar är anslutna via dedikerade enterprise edge-router-enheter att minimera Nätverksfördröjningen mellan virtuella datorer i Azure IaaS och stor instans stämplar.

Den virtuella nätverksgatewayen för de virtuella datorerna som är värdar för instanser av SAP-programmet är anslutet till ExpressRoute-kretsen. Samma virtuella nätverk är ansluten till en separat enterprise gränsrouter som är dedikerad att ansluta till stämplar med stor instans.

Det här systemet är ett enkelt exempel på en enda SAP-system. SAP-programnivån ligger i Azure. SAP HANA-databas som körs på SAP HANA på Azure (stora instanser). Antas att gateway-bandbredd för virtuellt nätverk med 2 Gbit/s eller 10 Gbit/s genomströmning inte representerar en flaskhals.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Flera SAP-system eller stora SAP-system

Om flera SAP-system eller stora SAP-system distribueras för att ansluta till SAP HANA på Azure (stora instanser) är kan dataflödet för den virtuella nätverksgatewayen bli en flaskhals. I detta fall är att dela programmet lager i flera virtuella nätverk. Du kan också skapa ett särskilt virtuellt nätverk som ansluter till stora HANA-instansen i de fall som:

- Säkerhetskopieringar direkt från HANA-instanser i stora HANA-instansen till en virtuell dator i Azure som är värd för NFS-resurser.
- Kopiera stora säkerhetskopieringar eller andra filer från enheter för stora HANA-instansen till diskutrymme som hanteras i Azure.

Använd ett separat virtuellt nätverk till virtuella datorer som hanterar lagring-värd. Den här ordningen undviker effekterna av stora filer eller dataöverföring från stora HANA-instansen till Azure på den virtuella nätverksgatewayen som fungerar på virtuella datorer som kör SAP-programnivån. 

För en mer skalbart nätverksarkitektur:

- Använda flera virtuella nätverk för en enda, större SAP-programnivån.
- Distribuera ett separat virtuellt nätverk för alla SAP-system som har distribuerats, jämfört med att kombinera dessa SAP-system i olika undernät under samma virtuella nätverk.

 En mer skalbart nätverk arkitektur för SAP HANA på Azure (stora instanser):

![Distribuera SAP-programnivån över flera virtuella nätverk](./media/hana-overview-architecture/image4-networking-architecture.png)

Bilden visar SAP-programnivån eller komponenter, distribuerat över flera virtuella nätverk. Den här konfigurationen introducerade oundvikligt med elasticitet som uppstod vid kommunikation mellan program som körs i dessa virtuella nätverk. Standardvärdet är nätverkstrafik mellan virtuella datorer finns i olika virtuella nätverk vidarebefordra via enterprise-gränsroutrar i den här konfigurationen. Sättet att optimera och minska svarstiden i kommunikationen mellan två virtuella nätverk är att peerkoppla virtuella nätverk inom samma region. Den här metoden fungerar även om de virtuella nätverk finns i olika prenumerationer. Med vnet-peering, använda kommunikationen mellan virtuella datorer i två olika virtuella nätverk till Azure-stamnätverket kan kommunicera direkt med varandra. Svarstid visas som om de virtuella datorerna är i samma virtuella nätverk. Trafik som åtgärdar IP-adressintervall som är anslutna via Azure vnet-gatewayen dirigeras via den enskilda virtuella nätverksgatewayen för det virtuella nätverket. 

Läs mer om virtuell nätverkspeering [peerkoppling av virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Routning i Azure

Tre routning Nätverksöverväganden är viktiga för SAP HANA på Azure (stora instanser):

* SAP HANA på Azure (stora instanser) kan bara nås via virtuella datorer och den dedikerade ExpressRoute-anslutningen inte direkt från en lokal plats. Direkt åtkomst från en lokal plats till stora HANA-instansen-enheter som levereras av Microsoft, inte är möjligt omedelbart. Transitiv routning begränsningarna är på grund av den aktuella Azure-nätverksarkitektur som används för stora SAP HANA-instansen. Vissa administration-klienter och program som behöver direkt åtkomst, till exempel SAP lösning Manager som körs lokalt, kan inte ansluta till SAP HANA-databas.

* Om du har stora HANA-instansen enheter som distribueras i två olika Azure-regioner för disaster recovery gäller samma tillfälliga routning begränsningar. IP-adresser för en enhet för stora HANA-instansen i en region (till exempel USA, västra) är med andra ord inte dirigeras till en enhet för stora HANA-instansen som distribueras i en annan region (till exempel USA, östra). Den här begränsningen är oberoende av användningen av Azure-nätverk peering mellan regioner eller cross-anslutning i ExpressRoute-kretsar som ansluter enheter för stora HANA-instansen till virtuella nätverk. En grafisk representation finns i bild i avsnittet ”använda HANA stora instanser units i flera regioner”. Den här begränsningen som medföljer distribuerad arkitektur förbjuder omedelbar användningen av HANA System Replication som funktioner för haveriberedskap.

* SAP HANA på Azure (stora instanser) enheter har en tilldelad IP-adress från servern IP-pool-adressintervall som du skickade. Mer information finns i [SAP HANA (stora instanser) infrastruktur och anslutningsmöjlighet på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Den här IP-adressen är tillgänglig via Azure-prenumerationer och ExpressRoute som ansluter virtuella nätverk till HANA på Azure (stora instanser). IP-adress som tilldelats av server IP-adressintervall i poolen tilldelas direkt till enheten för maskinvaran. Den har *inte* tilldelad via NAT längre, som var fallet i första distributionen av den här lösningen. 

> [!NOTE] 
> Om du vill lösa begränsningen i tillfälliga routning som beskrivs i de första två listobjekt, använder du ytterligare komponenter för routning. Komponenter som kan användas för att övervinna begränsningen kan vara:

> * En omvänd proxy att dirigera data till och från. Exempelvis F5 BIG-IP, NGINX med Traffic Manager som distribueras i Azure som en brandvägg/trafik i virtuella routning lösning.
> * Med hjälp av [IPTables-reglerna](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) i en Linux-VM för att aktivera routning mellan lokala platser och stora HANA-instansen enheter, eller mellan stora HANA-instansen enheter i olika regioner.

> Tänk på att implementering och stöd för anpassade lösningar som inbegriper från tredje part nätverksutrustning eller IPTables inte tillhandahålls av Microsoft. Du måste tillhandahålla support av leverantören av den komponent som används eller Integratorn. 

### <a name="internet-connectivity-of-hana-large-instance"></a>Ansluten till Internet för stora HANA-instansen
Stora HANA-instansen har *inte* har direkt Internetanslutning. Den här begränsningen kan exempelvis begränsa möjligheten att registrera den OS-avbildningen direkt med OS-leverantören. Du kan behöva arbeta med din lokala hanteringsverktyg för SUSE Linux Enterprise Server-prenumeration-servern eller Red Hat Enterprise Linux prenumeration Manager.

### <a name="data-encryption-between-vms-and-hana-large-instance"></a>Kryptering av data mellan virtuella datorer och stora HANA-instansen
Data som överförs mellan stora HANA-instansen och virtuella datorer krypteras inte. Endast för exchange mellan HANA DBMS på klientsidan och JDBC/ODBC-baserade program, kan du aktivera kryptering av trafik. Mer information finns i [den här dokumentationen av SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

### <a name="use-hana-large-instance-units-in-multiple-regions"></a>Använd stora HANA-instansen enheter i flera regioner

Du kanske skälen till att distribuera SAP HANA på Azure (stora instanser) i flera Azure-regioner utom för katastrofåterställning. Du kanske vill ansluta till stora HANA-instansen från var och en av de virtuella datorerna distribueras i olika virtuella nätverk i regioner. IP-adresser som hör till olika stora HANA-instansen enheterna sprids inte utöver de virtuella nätverk som är anslutna direkt via deras gateway till instanserna. Därför kan introduceras en liten ändring i designen för virtuellt nätverk. En virtuell nätverksgateway kan hantera fyra olika ExpressRoute-kretsar från olika enterprise-gränsroutrar. Varje virtuellt nätverk som är ansluten till en av stämplarna som stora instanser kan anslutas till stor instans stämpel i en annan Azure-region.

![Virtuella nätverk som anslutits till stämplar med stora Azure-instanser i olika Azure-regioner](./media/hana-overview-architecture/image8-multiple-regions.png)

Bilden visar hur olika virtuella nätverk i båda regionerna är anslutna till två olika ExpressRoute-kretsar som används för att ansluta till SAP HANA på Azure (stora instanser) i både Azure-regioner. De nyligen införda anslutningarna är rektangulär röda linjer. Med de här anslutningarna utanför de virtuella nätverk, virtuella datorer som körs i något av dessa virtuella nätverk har åtkomst till var och en av de olika stora HANA-instansen enheter som distribueras i två regioner. Så som visas i bilden, förutsätts att du har två ExpressRoute-anslutningar från en lokal plats till två Azure-regioner. Den här ordningen rekommenderas för disaster recovery orsaker.

> [!IMPORTANT] 
> Om du använder flera ExpressRoute-kretsar, ska AS Path-prepending och lokala inställningar BGP-inställningar användas för att se till att routningen av trafik.


