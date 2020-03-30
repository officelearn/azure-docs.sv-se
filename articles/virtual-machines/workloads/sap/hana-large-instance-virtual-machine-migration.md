---
title: Migrera SAP HANA på Azure (stora instanser) till virtuella Azure-datorer| Microsoft-dokument
description: Migrera SAP HANA på Azure (stora instanser) till virtuella Azure-datorer
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd1267711871b3e55f1a6229e46ae27b360322f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617043"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA på Azure Large Instance-migrering till virtuella Azure-datorer
I den här artikeln beskrivs möjliga Azure Large Instance-distributionsscenarier och erbjuder planerings- och migreringsmetod med minimerad övergångsstopptid

## <a name="overview"></a>Översikt
Sedan meddelandet av Azure Large Instances för SAP HANA (HLI) i september 2016 har många kunder antagit den här maskinvaran som ett tjänsteerbjudande för sin beräkningsplattform i minnet.  Under de senaste åren har Azure VM-storlekstillägget tillsammans med stöd för HANA-utskalningsdistribution överskridit de flesta företagskunders ERP-databaskapacitetsbehov.  Vi börjar se kunder som uttrycker intresse för att migrera sin SAP HANA-arbetsbelastning från fysiska servrar till virtuella Azure-datorer.
Den här guiden är inte ett steg-för-steg-konfigurationsdokument. Den beskriver de gemensamma distributionsmodellerna och erbjuder planerings- och migreringsråd.  Avsikten är att ropa ut nödvändiga överväganden för förberedelse för att minimera övergången driftstopp.

## <a name="assumptions"></a>Antaganden
Denna artikel gör följande antaganden:
- Det enda intresse som beaktas är en homogen HANA-databasberäkningstjänstmigrering från Hana Large Instance (HLI) till Azure VM utan betydande uppgradering eller korrigering av programvara. Dessa mindre uppdateringar inkluderar användning av en nyare OS-version eller HANA-version som uttryckligen anges som stöds av relevanta SAP-anteckningar. 
- Alla uppdateringar/uppgraderingar måste göras före eller efter migreringen.  TILL exempel SAP HANA MCOS konvertera till MDC-distribution. 
- Migreringsmetoden som skulle erbjuda minst driftstopp är SAP HANA System Replication. Andra migreringsmetoder ingår inte i dokumentets omfattning.
- Denna vägledning gäller för både Rev3 och Rev4 SKU:er för HLI.
- HANA-distributionsarkitekturen förblir i första hand oförändrad under migreringen.  Det vill än, ett system med dr i en instans kommer att förbli på samma sätt vid målet.
- Kunderna har granskat och förstått servicenivåavtalet (SLA) för målarkitekturen (to-be). 
- Kommersiella villkor mellan HLI och virtuella datorer är olika. Kunder bör övervaka användningen av sina virtuella datorer för kostnadshantering.
- Kunderna förstår att HLI är en dedikerad beräkningsplattform medan virtuella datorer körs på delad men isolerad infrastruktur.
- Kunder har validerat att virtuella mål virtuella datorer stöder din avsedda arkitektur. Om du vill se alla VM-SKU:er som stöds som certifikatas för SAP HANA-distribution finns i [maskinvarukatalogen för SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Kunderna har validerat design- och migreringsplanen.
- Planera för virtuell dator med haveriberedskap tillsammans med den primära platsen.  Kunder kan inte använda HLI som DR-nod för den primära platsen som körs på virtuella datorer efter migreringen.
- Kunderna kopierade de nödvändiga säkerhetskopieringsfilerna för att rikta in sig på virtuella datorer, baserat på krav på återställning av företag och efterlevnadskrav. Med vm-tillgängliga säkerhetskopior möjliggör det återställning i point-in-time under övergångsperioden.
- För HSR HA måste kunderna konfigurera STONITH-enheten enligt SAP HANA HA-guider för [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) och [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker).  Det är inte förkonfigurerad som HLI fallet.
- Den här migreringsmetoden täcker inte HLI-SKU:erna med Optane-konfigurationen.

## <a name="deployment-scenarios"></a>Distributionsscenarier
Vanliga distributionsmodeller med HLI-kunder sammanfattas i följande tabell.  Migrering till virtuella Azure-datorer för alla HLI-scenarier är möjlig.  För att dra nytta av kompletterande Azure-tjänster tillgängliga kan mindre arkitektoniska ändringar krävas.

| Scenario-ID | HLI-scenario | Vill du migrera till VM- ordagrant? | Anmärkning |
| --- | --- | --- | --- |
| 1 | [Enkel nod med ett SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | Ja | - |
| 2 | [Enkel nod med MCOS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | Ja | - |
| 3 | [Enkel nod med DR med lagringsreplikering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | Inga | Lagringsreplikering är inte tillgängligt med virtuell Azure-plattform, ändra aktuell DR-lösning till antingen HSR eller säkerhetskopiering/återställning |
| 4 | [Enkel nod med DR (multifunktion) med lagringsreplikering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | Inga | Lagringsreplikering är inte tillgängligt med virtuell Azure-plattform, ändra aktuell DR-lösning till antingen HSR eller säkerhetskopiering/återställning |
| 5 | [HSR med STONITH för hög tillgänglighet](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | Ja | Ingen förkonfigurerad SBD för virtuella mål virtuella datorer.  Välj och distribuera en STONITH-lösning.  Möjliga alternativ: Azure Fäktning Agent (stöds för både [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker), [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)), SBD |
| 6 | [HA med HSR, DR med lagringsreplikering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | Inga | Ersätt lagringsreplikering för DR-behov med antingen HSR eller säkerhetskopiering/återställning |
| 7 | [Automatisk redundans värd (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | Ja | Använda ANF för delad lagring med virtuella Azure-datorer |
| 8 | [Skala ut med vänteläge](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | Ja | BW/4HANA med M128s, M416s, M416ms virtuella datorer med ANF endast för lagring |
| 9 | [Utskalning utan vänteläge](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | Ja | BW/4HANA med M128s, M416s, M416ms virtuella datorer (med eller utan användning av ANF för lagring) |
| 10 | [Skala ut med DR med lagringsreplikering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | Inga | Ersätt lagringsreplikering för DR-behov med antingen HSR eller säkerhetskopiering/återställning |
| 11 | [Enkel nod med DR med HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | Ja | - |
| 12 | [Enkel nod HSR till DR (kostnadsoptimerad)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | Ja | - |
| 13 | [HA och DR med HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | Ja | - |
| 14 | [HA och DR med HSR (kostnadsoptimerad)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Ja | - |
| 15 | [Skala ut med DR med HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | Ja | BW/4HANA med M128s. M416s, M416ms virtuella datorer (med eller utan användning av ANF för lagring) |


## <a name="source-hli-planning"></a>Planering av källa (HLI)
När du gick in på en HLI-server gick både Microsoft Service Management och kunder igenom planeringen av inställningarna för beräkning, nätverk, lagring och OS för att köra SAP HANA-databasen.  Liknande planering måste ske för migreringen till Azure VM.

### <a name="sap-hana-housekeeping"></a>SAP HANA hushållning 
Det är en bra operativ praxis för att snygga till databasinnehållet så att oönskade, inaktuella data eller inaktuella loggar inte migreras till den nya databasen.  Hushållning innebär i allmänhet att ta bort eller arkivera gamla, utgångna eller inaktiva data.  Dessa åtgärder för datahygien bör testas i icke-produktionssystem för att validera deras datatrimningsgiltighet före produktionsanvändning.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Tillåt nätverksanslutning för nya virtuella datorer och, eller virtuellt nätverk 
I en kunds HLI-distribution har nätverket konfigurerats baserat på den information som beskrivs i artikeln [SAP HANA (Large Instances) nätverksarkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Dessutom utförs nätverkstrafik routning på det sätt som beskrivs i avsnittet "Routning i Azure".
- När du ställer in en ny virtuell dator som migreringsmål krävs ingen ytterligare anslutningsuppdatering om den placeras i det befintliga virtuella nätverket med IP-adressintervall som redan har tillåtits ansluta till HLI.
- Om den nya virtuella Azure-datorn placeras i ett nytt Virtuellt Microsoft Azure-nätverk kan det finnas i en annan region och peer-avsnittet med det befintliga virtuella nätverket, kan ExpressRoute-tjänstnyckeln och resurs-ID:t från den ursprungliga HLI-etableringen användas för att ge åtkomst för den här nya virtuella nätverkets IP-intervall.  Samordna med Microsoft Service Management för att aktivera det virtuella nätverket till HLI-anslutning.  Om du vill minimera nätverksfördröjningen mellan program- och databaslagren måste både program- och databaslagren finnas i samma virtuella nätverk.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Befintlig tillgänglighetsuppsättning för applager, tillgänglighetszoner och ppg (Proximity Placement Group)
Den aktuella distributionsmodellen görs för att uppfylla vissa servicenivåmål.  I det här steget, se till att målinfrastrukturen kommer att uppfylla eller överträffa de fastställda målen.  
Mer troligt än inte, kunder SAP-programservrar placeras i en tillgänglighetsuppsättning.  Om den aktuella driftsättningstjänstnivån är tillfredsställande och 
- Om måldatorn tar på sig värdnamnet för det logiska HLI-namnet skulle det fungera att uppdatera DNS-adressmatchningen (Domain Name Service) som pekar på den virtuella datorns IP utan att uppdatera några SAP-profiler
- Om du inte använder PPG måste du placera alla program- och DB-servrar i samma zon för att minimera nätverksfördröjningen.
- Om du använder PPG läser du avsnittet i det här dokumentet: "Målplanering, Tillgänglighetsuppsättning, tillgänglighetszoner och PPG (Proximity Placement Group".

### <a name="storage-replication-discontinuance-process-if-used"></a>Lagringsreplikeringsavbrottsprocess (om den används)
Om lagringsreplikering används som DR-lösning bör den avslutas (oplanerad) efter att SAP-programmet har stängts av.  Dessutom har den senaste SAP HANA-katalogen, loggfilen och datasäkerhetskopiorna replikerats till fjärr-DR HLI-lagringsvolymer.  Att göra det som en försiktighetsåtgärd om en katastrof inträffar under den fysiska servern till Azure VM-övergången.

### <a name="data-backups-preservation-consideration"></a>Bevarande av datasäkerheter
Efter cut-over till SAP HANA på Azure VM, alla ögonblicksbild-baserade data eller logga säkerhetskopior på HLI är inte lättillgängliga eller återställas till en virtuell dator om det behövs. Under den tidiga övergångsperioden, innan den Azure-baserade säkerhetskopian skapar tillräckligt med historik för att uppfylla point-in-time-återställningskrav, rekommenderar vi att du tar säkerhetskopiering på filnivå utöver ögonblicksbilder på HLI, dagar eller veckor före cut-over.  Få dessa säkerhetskopior kopierade till ett Azure Storage-konto tillgängligt för den nya VIRTUELLA SAP HANA-datorn.
Förutom att säkerhetskopiera HLI-innehållet är det klokt att ha fullständiga säkerhetskopior av SAP-landskapet lättillgängligt om en återställning behövs.

### <a name="adjusting-system-monitoring"></a>Justera systemövervakning 
Kunder använder många olika verktyg för att övervaka och skicka varningsmeddelanden för system inom sitt SAP-landskap.  Det här objektet är bara en uppmaning till lämpliga åtgärder för att införliva ändringar för övervakning och uppdatering av mottagarna av varningsmeddelanden om det behövs.

### <a name="microsoft-operations-team-involvement"></a>Engagemang från Microsoft Operations-teamet 
Öppna en biljett från Azure-portalen baserat på den befintliga HLI-instansen.  När supportbiljetten har skapats kommer en supporttekniker att kontakta dig via e-post.  

### <a name="engage-microsoft-account-team"></a>Engagera Microsoft-kontoteamet
Planera migrrering nära årsdagens förnyelsetid för HLI-kontraktet för att minimera onödiga överkostnader för beräkningsresurs. För att avveckla HLI-bladet krävs det att du samordnar uppsägning av avtal och faktisk avstängning av enheten.

## <a name="destination-planning"></a>Planering av destination
Att stå upp för en ny infrastruktur för att ta plats i en befintlig förtjänar en del tänkande för att se till att det nya tillskottet passar in i det stora tingens ordning.  Nedan följer några viktiga punkter för kontemplation.

### <a name="resource-availability-in-the-target-region"></a>Resurstillgänglighet i målregionen 
Den aktuella SAP-programservrar distributionsregionen är vanligtvis i närheten av tillhörande HLIs.  HLI erbjuds dock på färre platser än tillgängliga Azure-regioner.  När du migrerar den fysiska HLI till Azure VM, är det också ett bra tillfälle att "finjustera" närhetsavståndet för alla relaterade tjänster för prestandaoptimering.  Samtidigt som man gör det är en viktig faktor att se till att den valda regionen har alla nödvändiga resurser.  Till exempel tillgängligheten för vissa VM-familjen eller erbjudandet av Azure Zoner för hög tillgänglighet setup.

### <a name="virtual-network"></a>Virtuellt nätverk 
Kunderna måste välja om de vill köra den nya HANA-databasen i ett befintligt virtuellt nätverk eller skapa en ny.  Den primära avgörande faktorn är den aktuella nätverkslayouten för SAP-landskapet.  Även när infrastrukturen går från en zon till två zoner distribution och använder PPG, innebär det arkitektoniska förändringar. Mer information finns i artikeln [Azure PPG för optimal nätverksfördröjning med SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).   

### <a name="security"></a>Säkerhet
Oavsett om den nya VIRTUELLA SAP HANA-datorn landar på ett nytt eller befintligt vnet/undernät representerar den en ny affärskritisk tjänst som kräver skydd.  Åtkomstkontroll som är kompatibel med företagets informationssäkerhetsprincip bör utvärderas och distribueras för den här nya tjänsten.

### <a name="vm-sizing-recommendation"></a>Rekommendation om storleksändring för virtuella datorer
Den här migreringen är också en möjlighet att rätt storlek på din HANA-beräkningsmotor.  Man kan använda HANA [systemvyer](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) tillsammans med HANA Studio för att förstå systemets resursförbrukning, vilket möjliggör rätt storlek för att driva utgifterna effektivitet.

### <a name="storage"></a>Lagring 
Lagringsprestanda är en av de faktorer som påverkar SAP-programmets användarupplevelse.  Bas på en viss VM SKU, det finns minsta lagringslayout publicerade [SAP HANA Azure virtuella dator lagringskonfigurationer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). Vi rekommenderar att du granskar dessa minimispecifikationer och jämför med den befintliga HLI-systemstatistiken för att säkerställa tillräcklig IO-kapacitet och prestanda för den nya HANA-virtuella datorn.

Om du konfigurerar PPG för den nya HANA-virtuella datorn och dess associerade avskiljare skickar du en supportbiljett för att inspektera och säkerställa samlokaliseringen av lagringen och den virtuella datorn. Eftersom din säkerhetskopieringslösning kan behöva ändras, bör lagringskostnaden också ses över för att undvika att utgifterna överraskas.

### <a name="storage-replication-for-disaster-recovery"></a>Lagringsreplikering för haveriberedskap
Med HLI erbjöds lagringsreplikering som standardalternativ för haveriberedskap. Den här funktionen är inte standardalternativet för SAP HANA på Azure VM. Tänk på HSR, säkerhetskopiering/återställning eller andra lösningar som stöds som uppfyller dina affärsbehov.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Tillgänglighetsuppsättningar, tillgänglighetszoner och närhetsplaceringsgrupper 
För att minska avståndet mellan programlagret och SAP HANA för att hålla nätverksfördröjningen till ett minimum, bör den nya databasen VM och de aktuella SAP-programservrarna placeras i en PPG. Se [Proximity Placement Group](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) om du vill veta mer om hur Azure-tillgänglighetsuppsättning och tillgänglighetszoner fungerar med PPG för SAP-distributioner.
Om medlemmar i mål-HANA-systemet distribueras i mer än en Azure-zon bör kunderna ha en tydlig bild av svarstidsprofilen för de valda zonerna. Placeringen av SAP-systemkomponenter är optimal när det gäller proximalt avstånd mellan SAP-applikationen och databasen.  Testverktyget [för tillgänglighetszonen Tillgänglighetszon](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) gör mätningen enklare.  


### <a name="backup-strategy"></a>Strategi för säkerhetskopiering
Många kunder använder redan säkerhetskopieringslösningar från tredje part för SAP HANA på HLI.  I så fall behöver endast ytterligare en skyddad virtuell dator och HANA-databaser konfigureras.  Pågående HLI-säkerhetskopieringsjobb kan nu vara oplanerade om datorn inaktiveras efter migreringen.
Azure Backup för SAP HANA på den virtuella datorn är nu allmänt tillgänglig.  Se de här länkarna för detaljerad information om: [Säkerhetskopiering](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database), [Återställning](https://docs.microsoft.com/azure/backup/sap-hana-db-restore), [Hantera](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) SAP HANA-säkerhetskopiering i virtuella Azure-datorer.

### <a name="dr-strategy"></a>DR strategi
Om dina servicenivåmål rymmer en längre återställningstid är en enkel säkerhetskopiering för att blobba lagring och återställa på plats eller återställa till en ny virtuell dator den enklaste och billigaste DR-strategin.  
Liksom den stora instansplattformen där HANA DR vanligtvis görs med HSR; På Azure VM är HSR också den mest naturliga och inbyggda SAP HANA DR-lösningen.  Oavsett om källdistributionen är en instans eller klustrade krävs en replik av källinfrastrukturen i DR-regionen.
Den här DR-repliken konfigureras när den primära HLI-till VM-migreringen är klar.  DR HANA DB registrerar sig till den primära SAP HANA-instansen på vm som en sekundär replikeringsplats.  

### <a name="sap-application-server-connectivity-destination-change"></a>Måländring för SAP-programserveranslutning
HSR-migr-migr resulterar i en ny HANA DB-värd och därmed ett nytt DB-värdnamn för programlagret, SAP-profiler måste ändras för att återspegla det nya värdnamnet.  Om växlingen görs med namnmatchning som bevarar värdnamnet krävs ingen profiländring.

### <a name="operating-system"></a>Operativsystem
Operativsystemets avbildningar för HLI och VM, trots att de är på samma utgivningsnivå, SLES 12 SP4 till exempel, är inte identiska. Kunder måste validera de paket som krävs, snabbkorrigeringar, korrigeringar, kärnor och säkerhetskorrigeringar på HLI för att installera samma paket på målet.  Det stöds för att använda HSR för att replikera från ett äldre operativsystem till en virtuell dator med en nyare OS-version.  Verifiera de specifika versionerna som stöds genom att granska [SAP note 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Ny SAP-licensbegäran
En enkel länkning för att begära en ny SAP-licens för det nya HANA-systemet nu när det har migrerats till virtuella datorer.

### <a name="service-level-agreement-sla-differences"></a>Skillnader i servicenivåavtal (SLA)
Författarna vill ropa ut skillnaden i tillgänglighet SLA mellan HLI och Azure VM.  Kluster-HLIs HA-par erbjuder till exempel 99,99 % tillgänglighet. För att uppnå samma SLA måste man distribuera virtuella datorer i tillgänglighetszoner. I den här [artikeln](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) beskrivs tillgänglighet med associerade distributionsarkitekturer så att kunderna kan planera sin målinfrastruktur i enlighet med detta.


## <a name="migration-strategy"></a>Migreringsstrategi
I det här dokumentet täcker vi endast HANA System Replication-metoden för migreringen från HLI till Azure VM.  Beror på vilken mållagringslösning som används, processen skiljer sig något. Stegen på hög nivå beskrivs nedan.

### <a name="vm-with-premiumultra-disks-for-data"></a>Virtuell dator med premium/ultradiskar för data
För virtuella datorer som distribueras med premium- eller ultradiskar gäller standardkonfigurationen för SAP HANA-systemreplikering för att konfigurera HSR.  [Sap-hjälpartikeln](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) ger en översikt över de steg som ingår i att konfigurera systemreplikering, ta över ett sekundärt system, inte gå tillbaka till den primära och inaktivera systemreplikering.  För migreringen behöver vi bara installationen, tar över och inaktiverar replikeringssteg.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>VM med ANF för data- och loggvolymer
På en hög nivå måste de senaste HLI-lagringsögonblicksbilderna av de fullständiga data- och loggvolymerna kopieras till Azure Storage där de är tillgängliga och kan återställas av målet HANA VM.  Kopieringsprocessen kan göras med alla inbyggda Linux-kopieringsverktyg.  

> [!IMPORTANT]
> Kopiering och dataöverföring kan ta timmar beror på HANA-databasens storlek och nätverksbandbredd.  Huvuddelen av kopieringsprocessen bör göras före den primära HANA DB driftstopp.

### <a name="mcos-to-mdc-conversion"></a>MCOS till MDC-konvertering
Distributionsmodellen för flera komponenter i ett system (MCOS) användes av några av våra HLI-kunder.  Motiveringen var att kringgå mdc-lagringsbegränsningen (Multiple Databases Container) för tidigare SAP HANA-versioner.  I MCOS-modellen staplas flera oberoende SAP HANA-instanser upp i ett HLI-blad.  Använda HSR för migreringen skulle fungera bra men resulterar i flera HANA virtuella datorer med en klient DB vardera.  Denna slutstat gör för ett livligare landskap än vad en kund kan ha varit anklagad för.  Med SAP HANA 2.0 standarddistribution är MDC, ett lönsamt alternativ är att utföra [HANA-klientflytt](https://launchpad.support.sap.com/#/notes/2472478) efter HSR-migreringen.  Denna process "konsoliderar" dessa oberoende HANA-databaser till medspänmedel i en enda HANA-behållare.  

### <a name="application-layer-consideration"></a>Hänsyn till programlager
DB-servern visas som mitten av ett SAP-system.  Alla programservrar bör finnas nära SAP HANA DB.  I vissa fall när ny användning av PPG önskas, flytta befintliga programservrar till PPG där HANA VM kan krävas.  Att skapa nya programservrar kan vara enklare om du redan har distributionsmallar till hands.  
Om befintliga programservrar och den nya VIRTUELLA HANA-datorn är optimalt placerade behöver inga nya programservrar byggas om det inte krävs ytterligare kapacitet.  
Om en ny infrastruktur byggs för att förbättra tjänstens tillgänglighet kan de befintliga programservrarna bli onödiga och bör stängas av och tas bort.
Om mål-VM-värdnamnet har ändrats och skiljer sig från HLI-värdnamnet måste SAP-programserverprofiler justeras så att de pekar på den nya värden.  Om bara HANA DB IP-adressen har ändrats behövs en DNS-postuppdatering för att leda inkommande anslutningar till den nya HANA-virtuella datorn.

### <a name="acceptance-test"></a>Acceptanstest
Även om migreringen från HLI till virtuell dator inte gör någon väsentlig ändring av databasinnehållet jämfört med en heterogen migrering, rekommenderar vi fortfarande att du validerar viktiga funktioner och prestandaaspekten i den nya installationen.  

### <a name="cutover-plan"></a>Cutover plan
Även om denna migration är rakt fram innebär den dock att en befintlig budgetförslag avvecklas.  Noggrann planering för att bevara källsystemet med tillhörande innehåll och säkerhetskopieringsavbildningar är avgörande om reserv kan bli nödvändigt.  Bra planering erbjuder en snabbare återföring.   


## <a name="post-migration"></a>Efter migreringen
Migreringsjobbet görs inte förrän vi säkert har frikopplat några HLI-beroende tjänster eller anslutningsmöjligheter för att säkerställa att dataintegriteten bevaras.  Stäng också av onödiga tjänster.  Det här avsnittet innehåller några objekt i åtanke.

### <a name="decommissioning-the-hli"></a>Avveckling av HLI
Efter en lyckad migrering av HANA DB till Azure VM säkerställer du att inga produktiva affärstransaktioner körs på HLI DB.  Att hålla HLI igång under en viss tid är dock ett säkert sätt att säkerställa snabbare återställning om det behövs.  Först då bör HLI-bladet tas ur bruk.  Kunder bör ingå sina HLI-åtaganden med Microsoft genom att kontakta sina Microsoft-representanter.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Ta bort alla proxy (t.ex. Iptables, BIGIP) som konfigurerats för HLI 
Om en proxytjänst som IPTables används för att dirigera lokal trafik till och från HLI behövs den inte längre efter den lyckade migreringen till den virtuella datorn.  Denna anslutningstjänst bör dock behållas så länge HLI-bladet fortfarande står redo.  Stäng endast av tjänsten efter att HLI-bladet är helt ur drift.

### <a name="remove-global-reach-for-hli"></a>Ta bort global räckvidd för HLI 
Global Reach används för att ansluta kundernas ExpressRoute-gateway med HLI ExpressRoute-gatewayen.  Det gör det möjligt för kundernas lokala trafik att nå HLI-klienten direkt utan att använda en proxytjänst.  Den här anslutningen behövs inte längre i avsaknad av HLI-enheten efter migreringen.  Liksom fallet med IPTables proxytjänst bör GlobalReach också behållas tills HLI-bladet är helt ur drift.

### <a name="operating-system-subscription--movereuse"></a>Abonnemang för operativsystem – flytta/återanvända
När VM-servrarna står upp och HLI-bladen är inaktiverade kan OS-abonnemangen bytas ut eller återanvändas för att undvika dubbel betalning av OS-licenser.



## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:
- [SAP HANA-infrastrukturkonfigurationer och åtgärder på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
- [SAP-arbetsbelastningar på Azure: checklista för planering och distribution](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist).
