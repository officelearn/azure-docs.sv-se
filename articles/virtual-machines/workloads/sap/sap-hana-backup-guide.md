---
title: Säkerhetskopiering guide för SAP HANA på Azure Virtual Machines | Microsoft Docs
description: Säkerhetskopiering guiden för SAP HANA innehåller två huvudsakliga säkerhetskopiering möjligheter för SAP HANA på Azure virtual machines
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 9e5b124643b753f404ba6012d3df998f567be59a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Säkerhetskopieringsguide för SAP HANA på Azure Virtual Machines

## <a name="getting-started"></a>Komma igång

Guiden Säkerhetskopiering för SAP HANA körs på Azure virtual Machines beskriver endast Azure-specifik information. Allmän SAP HANA säkerhetskopiering relaterade objekt, dokumentationen om SAP HANA (se _SAP HANA säkerhetskopiering dokumentationen_ senare i den här artikeln).

Den här artikeln fokuserar på två huvudsakliga säkerhetskopiering möjligheter för SAP HANA på virtuella Azure-datorer:

- HANA säkerhetskopiering till filsystemet i en Azure Linux-dator (se [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md))
- HANA säkerhetskopiering baserat på lagring ögonblicksbilder med hjälp av funktionen Azure storage blob ögonblicksbild manuellt eller Azure Backup Service (se [SAP HANA säkerhetskopiering baserat på lagring ögonblicksbilder](sap-hana-backup-storage-snapshots.md))

SAP HANA erbjuder en säkerhetskopia API, vilket gör att tredje parts säkerhetskopieringsverktyg integreras direkt med SAP HANA. (Som är inte inom omfånget för den här handboken.) Det finns ingen direkt SAP HANA med Azure Backup-tjänsten tillgänglig höger nu baserat på den här API-integrering.

SAP HANA stöds officiellt på Azure VM typ GS5 som instans med en ytterligare begränsning för OLAP-arbetsbelastningar (se [hitta certifierade IaaS-plattformar](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) på webbplatsen SAP). Den här artikeln kommer att uppdateras som nya erbjudanden för SAP HANA på Azure som blir tillgängliga.

Det finns också en SAP HANA-hybridlösning på Azure, där SAP HANA körs icke-virtualiserade på fysiska servrar. Men den här säkerhetskopiering SAP HANA Azure-handboken innehåller en ren Azure-miljön där SAP HANA körs i en Azure VM inte SAP HANA körs på &quot;stora instanser.&quot; Se [SAP HANA (stora instanser) översikt och arkitektur för Azure](hana-overview-architecture.md) för mer information om denna lösning för säkerhetskopiering på &quot;stora instanser&quot; baserat på lagring ögonblicksbilder.

Allmän information om SAP-produkter som stöds i Azure finns i [SAP Obs 1928533](https://launchpad.support.sap.com/#/notes/1928533).

Följande tre siffror ger en översikt över säkerhetskopieringsalternativ för SAP HANA som för närvarande använder inbyggda funktioner i Azure och visar också tre möjliga scenarier för framtida säkerhetskopiering. Relaterade artiklar [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md) och [SAP HANA säkerhetskopiering baserat på lagring ögonblicksbilder](sap-hana-backup-storage-snapshots.md) beskrivs de olika alternativen i detalj, inklusive storlek och prestandaöverväganden för SAP HANA-säkerhetskopior som är flera terabyte i storlek.

![Den här bilden visar två möjligheter för att spara det aktuella tillståndet för virtuell dator](media/sap-hana-backup-guide/image001.png)

Den här bilden visar möjligheten att spara det aktuella tillståndet för virtuell dator, antingen via Azure Backup-tjänsten eller manuell ögonblicksbilder av Virtuella diskar. Med den här metoden en&#39;har att hantera SAP HANA-säkerhetskopiering. Anrop av disk ögonblicksbild scenariot är filsystemkonsekvens och tillståndet programkonsekventa disk. Konsekvenskontroll avsnittet beskrivs i avsnittet _SAP HANA datakonsekvens när du tar lagring ögonblicksbilder_ senare i den här artikeln. Funktioner och begränsningar för Azure Backup-tjänsten som är relaterade till SAP HANA säkerhetskopior också beskrivs senare i den här artikeln.

![Den här bilden visar alternativ för att ta en SAP HANA filsäkerhetskopia inuti den virtuella datorn](media/sap-hana-backup-guide/image002.png)

Den här bilden visar alternativ för tar en SAP HANA säkerhetskopiering inuti den virtuella datorn och sedan lagra den HANA säkerhetskopior någon annan använder olika verktyg. Ta en säkerhetskopia HANA tar längre tid än en ögonblicksbild-baserad lösning för säkerhetskopiering, men den har fördelar om integritet och konsekvens. Mer information finns nedan.

![Den här bilden visar eventuella framtida SAP HANA säkerhetskopiering scenario](media/sap-hana-backup-guide/image003.png)

Den här bilden illustrerar ett potentiella framtida SAP HANA säkerhetskopiering scenario. Om du SAP HANA säkerhetskopiering från en sekundär replikering, det lägger till ytterligare alternativ för säkerhetskopieringsstrategier. För närvarande kan inte enligt ett inlägg i SAP HANA-Wiki:

_&quot;Är det möjligt att göra säkerhetskopior på den sekundära sidan?_

_Nej, för närvarande kan endast ta data och loggsäkerhetskopior på den primära sidan. Om automatisk säkerhetskopiering är aktiverad när övertag till sekundär sida skrivs automatiskt säkerhetskopiorna av det.&quot;_

## <a name="sap-resources-for-hana-backup"></a>SAP-resurser för HANA säkerhetskopiering

### <a name="sap-hana-backup-documentation"></a>Säkerhetskopiering SAP HANA-dokumentation

- [Introduktion till SAP HANA-Administration](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planera säkerhetskopiering och återställning strategi](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Schemalägga HANA säkerhetskopia med ABAP DBACOCKPIT](http://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Schema för säkerhetskopiering av Data (SAP HANA Cockpit)](http://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Vanliga frågor om SAP HANA-säkerhetskopiering på [SAP Obs 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Vanliga frågor och svar om ögonblicksbilder för SAP HANA-databasen och lagring i [SAP Obs 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Olämpliga nätverket filsystem för säkerhetskopiering och återställning i [SAP Obs 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Varför SAP HANA-säkerhetskopiering?

Azure storage erbjuder tillgänglighet och tillförlitlighet ur lådan (se [introduktion till Microsoft Azure Storage](../../../storage/common/storage-introduction.md) mer information om Azure storage).

Minimum för &quot;säkerhetskopiering&quot; är att lita på Azure SLA: er, hålla SAP HANA data och loggfilen filerna på Azure virtuella hårddiskar anslutna till SAP HANA-servern VM. Den här metoden omfattar VM fel, men inte eventuella skador till SAP HANA-data och loggfiler eller logiska fel som tar bort data eller filer av misstag. Säkerhetskopieringar krävs för efterlevnad eller juridiska skäl. Kort sagt: det behövs alltid en SAP HANA-säkerhetskopieringar.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Så här kontrollerar du är korrekt SAP HANA-säkerhetskopiering
När du använder lagring ögonblicksbilder, bör köra en test-återställning på en annan dator. Den här metoden ger ett sätt att kontrollera att en säkerhetskopia är rätt och interna processer för säkerhetskopiering och återställning fungerar som förväntat. Även om detta är en betydande överskrida lokalt, är det mycket enklare att utföra i molnet genom att tillhandahålla nödvändiga resurser tillfälligt för detta ändamål.

Behåll i åtanke som gör en enkel återställning och kontrollera om HANA är igång och körs är inte tillräckligt. Vi rekommenderar bör en köra en konsekvenskontroll för tabellen för att kontrollera att den återställda databasen är bra. SAP HANA erbjuder flera typer av konsekvenskontroller som beskrivs i [SAP Obs 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Information om konsekvenskontrollen tabellen finns också på SAP-webbplats på [tabell och katalogen konsekvenskontroller](http://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

En test-återställning är inte nödvändigt för vanliga filsäkerhetskopieringsverktyg. Det finns två SAP HANA-verktyg som hjälper till att kontrollera vilka säkerhetskopian kan användas för återställning: hdbbackupdiag och hdbbackupcheck. Se [manuellt kontrollerar om en återställning är möjligt](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) för mer information om dessa verktyg.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>För- och nackdelar HANA säkerhetskopiering jämfört med lagring ögonblicksbild

SAP&#39;t prioritera antingen HANA säkerhetskopiering jämfört med lagring ögonblicksbild. Visas en lista med sina för- och nackdelar, så en kan fastställa som ska användas, beroende på situation och tillgängliga lagringsteknik (se [planera din säkerhetskopiering och återställning strategi](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

I Azure, Tänk på att det faktum att Azure-blobben ögonblicksbild funktionen&#39;t garanti filsystemkonsekvens (se [Using blob-ögonblicksbilder med PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). I nästa avsnitt, _SAP HANA datakonsekvens när du tar lagring ögonblicksbilder_, beskriver vissa överväganden om den här funktionen.

Dessutom har att förstå fakturering effekter när du arbetar ofta med blob ögonblicksbilder som beskrivs i den här artikeln: [förstå hur ögonblicksbilder påförs kostnader](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)– den inte är&#39;t som uppenbara som använder Azure virtuella diskar.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA datakonsekvens när du tar ögonblicksbilder för lagring

System- och konsekvens i filen är ett komplicerat problem när du tar ögonblicksbilder för lagring. Det enklaste sättet att undvika problem att stänga av SAP HANA eller kanske även hela virtuella datorn. En avstängning kanske doable med en demonstration eller prototyp eller även utvecklingssystemet, men det är inte ett alternativ för ett produktionssystem.

På Azure, har du bör tänka på att Azure-blobben ögonblicksbild funktionen&#39;filsystemkonsekvens för t-garantin. Den fungerar dock ögonblicksbild SAP HANA med hjälp av funktionen, så länge det finns bara en virtuell disk som ingår. Men även med en enskild disk ytterligare objekt som ska kontrolleras. [SAP Obs 2039883](https://launchpad.support.sap.com/#/notes/2039883) har viktig information om SAP HANA säkerhetskopieringar via storage snapshots. Exempelvis nämns att med filsystemet XFS är det nödvändigt att köra **xfs\_låsa** innan du startar en ögonblicksbild av lagring för att garantera konsekvens (se [xfs\_freeze(8) - Linux man sidan](https://linux.die.net/man/8/xfs_freeze) information om **xfs\_låsa**).

Avsnittet konsekvens blir ännu mer utmanande i fall där en enda filsystemet sträcker sig över flera diskar/volymer. Till exempel med hjälp av mdadm eller LVM och striping. SAP-kommentar som nämns ovan tillstånd:

_&quot;Men kom ihåg att lagringssystemet måste garantera konsekvens för i/o när du skapar en ögonblicksbild av lagring per volym för SAP HANA-data, t.ex. ögonblicksbilder för en SAP HANA tjänstspecifika datavolymen måste vara en atomisk åtgärd.&quot;_

Följande steg ange under förutsättning att det finns ett XFS filsystem utsträckning fyra Azure virtuella diskar, en programkonsekvent ögonblicksbild som representerar HANA dataområdet:

- Förbereda HANA ögonblicksbild
- Låsa filsystemet (till exempel använda **xfs\_låsa**)
- Skapa alla nödvändiga blob-ögonblicksbilder på Azure
- Lås upp filsystemet
- Bekräfta HANA ögonblicksbild

Rekommendation är att använda proceduren ovan i samtliga fall för att vara på den säkra sidan, oavsett vilket filsystem. Eller om det är en enskild disk eller striping via mdadm eller LVM över flera diskar.

Det är viktigt att bekräfta HANA ögonblicksbilden. På grund av att den &quot;kopiering vid skrivning,&quot; SAP HANA kanske inte kräver ytterligare diskutrymme i detta förbereda läge ögonblicksbild. Den&#39;också inte går att starta nya säkerhetskopior förrän SAP HANA ögonblicksbilden har bekräftats.

Azure Backup-tjänsten använder Azure VM-tillägg för att ta hand om filsystemkonsekvens. Dessa VM-tillägg är inte tillgängliga för fristående användning. Ett måste hantera SAP HANA-konsekvenskontroll. Se relaterade artikel [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md) för mer information.

### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA schemaläggning säkerhetskopieringsstrategi

SAP HANA-artikel [planera din säkerhetskopiering och återställning strategi](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) anger en grundläggande åtgärdsplan säkerhetskopiera:

- Lagring ögonblicksbild (varje dag)
- Fullständig säkerhetskopiering med hjälp av filen eller bacint format (en gång i veckan)
- Automatisk loggsäkerhetskopior

Du kan också kan en gå helt utan ögonblicksbilder av lagring de kan ersättas av HANA delta säkerhetskopieringar som inkrementella eller differentiella säkerhetskopieringar (se [Delta säkerhetskopieringar](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

HANA Administrationsguide innehåller en exempel-lista. Det tyder på att en återställa SAP HANA till en specifik tidpunkt med hjälp av följande sekvens av säkerhetskopiering:

1. Fullständig säkerhetskopiering
2. Differentiell säkerhetskopiering
3. Inkrementell säkerhetskopiering 1
4. Inkrementell säkerhetskopiering 2
5. Loggsäkerhetskopior

Om ett exakt schema för när och hur ofta en viss typ av säkerhetskopiering ska hända, det går inte att ge allmänt – det är för kundspecifika och beror på hur många data ändras i systemet. En grundläggande rekommendation från SAP-sida, vilket kan ses som en allmän vägledning är att se en fullständig HANA säkerhetskopiering en gång i veckan.
Om säkerhetskopior, finns i dokumentationen för SAP HANA [Loggsäkerhetskopior](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm).

SAP rekommenderar även göra vissa underhåll av säkerhetskopiering katalogen så att den blir oändligt (se [Housekeeping för säkerhetskopieringskatalogen och Säkerhetskopieringslagring](http://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>Konfigurationsfiler för SAP HANA

Enligt informationen i vanliga frågor i [SAP Obs 1642148](https://launchpad.support.sap.com/#/notes/1642148), SAP HANA-konfigurationsfilerna inte är en del av en standard säkerhetskopiering HANA. De är inte nödvändigt att återställa ett system. HANA konfigurationen kan ändras manuellt efter återställningen. Det är nödvändigt att säkerhetskopiera konfigurationsfiler HANA separat om någon vill ha samma anpassad konfiguration under återställningsprocessen.

Om vanlig HANA säkerhetskopiering ska en dedikerad HANA säkerhetskopian system, en kan också kopiera konfigurationsfilerna till samma säkerhetskopiering filsystemet och sedan kopiera allt tillsammans till den slutliga lagringsplats som lågfrekvent blob storage.

### <a name="sap-hana-cockpit"></a>SAP HANA Cockpit

SAP HANA Cockpit erbjuder möjligheten att övervaka och hantera SAP HANA via en webbläsare. Det kan också hantering av SAP HANA-säkerhetskopiering och kan därför användas som ett alternativ till SAP HANA Studio och ABAP DBACOCKPIT (se [SAP HANA Cockpit](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) mer information).

![Den här bilden visar skärmen SAP HANA Cockpit databasen Administration](media/sap-hana-backup-guide/image004.png)

Den här bilden visar skärmen SAP HANA Cockpit databasen Administration och panelen säkerhetskopiering till vänster. Se panelen backup kräver lämpliga användarbehörigheter för inloggningskontot.

![Säkerhetskopieringar kan övervakas i SAP HANA Cockpit medan de är pågående](media/sap-hana-backup-guide/image005.png)

Säkerhetskopieringar kan övervakas i SAP HANA Cockpit medan de är pågående, och när den är klar, säkerhetskopiering allt är tillgängliga.

![Ett exempel på en Azure SLES 12 VM med gör väldigt lätt desktop Firefox](media/sap-hana-backup-guide/image006.png)

De föregående skärmbilderna har gjorts från en Windows Azure-VM. Det här är ett exempel på en Azure SLES 12 VM med gör väldigt lätt desktop Firefox. Möjlighet att definiera scheman för SAP HANA-säkerhetskopiering i SAP HANA Cockpit visas. Som en kan också se föreslår datum/tid som ett prefix för säkerhetskopiorna. SAP HANA Studio är standard prefixet &quot;Slutför\_DATA\_säkerhetskopiering&quot; när du gör en fullständig säkerhetskopia. Du bör använda ett unikt prefix.

### <a name="sap-hana-backup-encryption"></a>SAP HANA säkerhetskopia av krypteringsnyckeln

SAP HANA erbjuder kryptering av data och loggfilen. Om SAP HANA-data och loggfilen inte krypteras kan sedan krypteras säkerhetskopieringarna också inte. Det är att använda någon form av lösning från tredje part för att kryptera säkerhetskopiorna för SAP HANA. Se [Data och loggfilen volymkryptering](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) vill veta mer om SAP HANA-kryptering.

En kund kan använda funktionen IaaS VM kryptering för att kryptera i Microsoft Azure. Exempelvis kan en använda dedicerade datadiskar som är anslutna till den virtuella datorn som används för att lagra säkerhetskopior för SAP HANA och sedan göra kopior av dessa diskar.

Azure Backup-tjänsten kan hantera krypterade VMs-diskar (se [säkerhetskopiera och återställa krypterade virtuella datorer med Azure Backup](../../../backup/backup-azure-vms-encryption.md)).

Ett annat alternativ är att upprätthålla SAP HANA-VM och dess diskar utan kryptering och lagra säkerhetskopiorna för SAP HANA i ett lagringskonto som kryptering aktiverades (se [Azure Storage Service-kryptering för Data i vila](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Testa installationen

### <a name="test-virtual-machine-on-azure"></a>Testa virtuell dator på Azure

En SAP HANA-installation i en Azure GS5 VM användes för följande säkerhetskopiering/återställning tester.

![Den här bilden visar en del av Azure portal översikten för testet HANA VM](media/sap-hana-backup-guide/image007.png)

Den här bilden visar en del av Azure portal översikt för den Virtuella testdatorn i HANA.

### <a name="test-backup-size"></a>Testa säkerhetskopieringsstorlek

![Den här bilden har kopplats från konsolen säkerhetskopiering i HANA Studio och visar säkerhetskopian storleken på 229 GB för HANA indexserver](media/sap-hana-backup-guide/image008.png)

En dummy tabell fylldes med data som ska hämtas en säkerhetskopiering storlek totala data över 200 GB att härleda realistiska prestandadata. Bilden har kopplats från konsolen säkerhetskopiering i HANA Studio och visar säkerhetskopian storleken på 229 GB för HANA indexserver. För tester användes säkerhetskopiering standardprefix ”COMPLETE_DATA_BACKUP” i SAP HANA Studio. Ett mer användbar prefix ska vara definierat i verklig produktionssystem. SAP HANA Cockpit föreslår datum/tid.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Testa verktyg för att kopiera filer direkt till Azure storage

Verktyget blobxfer användes för att överföra SAP HANA säkerhetskopierade filer direkt till Azure blob storage eller Azure-filresurser, eftersom den stöder båda mål och enkelt kan integreras i automatiseringsskript på grund av dess kommandoradsgränssnitt. Blobxfer-verktyget är tillgängligt på [GitHub](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Testa säkerhetskopieringsstorlek uppskattning

Det är viktigt att beräkna storleken på säkerhetskopian SAP HANA. Denna uppskattning hjälper oss för att förbättra prestanda genom att definiera den högsta säkerhetskopia storleken för ett antal säkerhetskopior på grund av parallellitet under en filkopia. (Dessa uppgifter beskrivs senare i den här artikeln.) En måste också bestämma om du vill göra en fullständig säkerhetskopia eller en delta-säkerhetskopiering (inkrementell eller differentiell).

Lyckligtvis finns en enkel SQL-instruktion som beräknar storleken på de säkerhetskopiera filerna: **Välj \* från M\_säkerhetskopiering\_storlek\_UPPSKATTNINGAR** (se [beräkna utrymmet som krävs i filsystemet för en säkerhetskopiering Data](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![Utdata från den här SQL-instruktionen matchar nästan exakt den verkliga storleken på fullständig säkerhetskopiering på disk](media/sap-hana-backup-guide/image009.png)

Utdata från den här SQL-instruktionen matchar för testsystemet nästan exakt den verkliga storleken på fullständig säkerhetskopiering på disk.

### <a name="test-hana-backup-file-size"></a>Testa HANA säkerhetskopians storlek

![Konsolen HANA Studio backup går det att begränsa den maximala filstorleken på HANA säkerhetskopior](media/sap-hana-backup-guide/image010.png)

Konsolen HANA Studio backup går det att begränsa den maximala filstorleken på HANA säkerhetskopior. I exempel-miljö som funktionen gör det möjligt att få flera mindre säkerhetskopierade filer i stället för en 230 GB säkerhetskopian. Mindre filstorlek har en betydande inverkan på prestanda (se relaterade artikel [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Sammanfattning

Baserat på resultaten i följande tabeller visas- och nackdelar med lösningar för att säkerhetskopiera en SAP HANA-databas som körs på virtuella Azure-datorer.

**Säkerhetskopiera SAP HANA till filsystemet och därefter kopiera säkerhetskopieringsfilerna till slutdestinationen för säkerhetskopiering**

|Lösning                                           |Tekniker                                 |Nackdelar                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Behåll HANA säkerhetskopior på Virtuella diskar                      |Inget ytterligare arbete     |Eats lokala VM diskutrymme           |
|Blobxfer för att kopiera säkerhetskopieringsfilerna till blob storage |Parallellitet kopiera flera filer, möjlighet att använda kall blob storage | Ytterligare verktyget underhåll och anpassade skript | 
|BLOB-kopiering via Powershell eller CLI                    |Inget ytterligare verktyg som behövs, kan utföras via Azure Powershell eller CLI |manuell process kunden måste ta hand om skript och hantering av kopierade BLOB för återställning|
|Kopiera till NFS-resurs                                  |Efter bearbetning av säkerhetskopior på andra Virtuella utan påverkan på HANA-server|Långsam kopieringsprocessen|
|Kopiera Blobxfer till Azures Filtjänst                |Inte äta utrymme på den lokala Virtuella diskar|Ingen direkt skriva stöd av HANA säkerhetskopiering, storleksbegränsningen på filresursen för närvarande på 5 TB|
|Azure Backup-agenten                                 | Är en bättre lösning         | För närvarande inte tillgänglig på Linux    |



**Säkerhetskopiering SAP HANA baserat på lagring ögonblicksbilder**

|Lösning                                           |Tekniker                                 |Nackdelar                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Azure Backup Service                               | Kan säkerhetskopiering baserat på blob ögonblicksbilder | När du använder inte filen nivån återställning, krävs skapas en ny virtuell dator för återställningen, vilket innebär behovet av en ny nyckel för SAP HANA-licens|
|Manuell blob ögonblicksbilder                              | Möjlighet att skapa och återställa specifika Virtuella diskar utan att ändra unikt ID för VM|Alla manuellt arbete som måste göras av kunden|

## <a name="next-steps"></a>Nästa steg
* [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md) beskriver av filbaserade säkerhetskopieringen.
* [SAP HANA säkerhetskopiering baserat på lagring ögonblicksbilder](sap-hana-backup-storage-snapshots.md) beskriver det ögonblicksbild-baserad säkerhetskopiering lagringsalternativet.
* Information om hur du upprättar och planera för katastrofåterställning för SAP HANA i Azure (stora instanser) med hög tillgänglighet finns [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md).
