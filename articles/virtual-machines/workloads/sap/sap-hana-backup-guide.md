---
title: Säkerhetskopieringsguide för SAP HANA på Azure Virtual Machines | Microsoft Docs
description: Säkerhetskopieringsguide för SAP HANA tillhandahåller två viktiga säkerhetskopiering möjligheter för SAP HANA på Azure virtual machines
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 9d72bc885bdaaed521042df236dd722b80533186
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867009"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Säkerhetskopieringsguide för SAP HANA på Azure Virtual Machines

## <a name="getting-started"></a>Komma igång

Säkerhetskopieringsguide för SAP HANA som körs på virtuella datorer i Azure beskriver endast Azure-specifika ämnen. Allmän SAP HANA relaterade objekt att säkerhetskopiera, finns i dokumentationen för SAP HANA (se _dokumentation om backup-SAP HANA_ senare i den här artikeln).

Fokus i den här artikeln finns på två större säkerhetskopiering möjligheter för SAP HANA på Azure virtual machines:

- HANA-säkerhetskopiering till filsystemet i en Azure Linux-dator (se [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md))
- HANA-säkerhetskopia baserat på ögonblicksbilder av lagring med hjälp av Azure storage blob snapshot-funktionen manuellt eller Azure Backup Service (se [SAP HANA-säkerhetskopia baserat på ögonblicksbilder av lagring](sap-hana-backup-storage-snapshots.md))

SAP HANA erbjuder en säkerhetskopia API, vilket gör att säkerhetskopiering verktyg från tredje part att integrera direkt med SAP HANA. (Det är inte inom omfånget för den här handboken.) Det finns ingen direkt integrering av SAP HANA med Azure Backup-tjänsten som är tillgängliga direkt nu baserat på detta API.

SAP HANA stöds officiellt på olika typer av Azure virtuella datorer, som Azure M-serien. En fullständig lista över SAP HANA-certifierade virtuella datorer i Azure, kan du ta en titt [hitta certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Den här artikeln kommer att uppdateras som nya erbjudanden för SAP HANA på Azure som blir tillgängliga.

Det finns också en hybridlösning för SAP HANA på Azure, där SAP HANA körs icke-virtualiserade på fysiska servrar. Men den här säkerhetskopieringsguide för SAP HANA Azure omfattar en ren Azure-miljön där SAP HANA som körs i en Azure VM inte SAP HANA som körs på &quot;stora instanser.&quot; Se [SAP HANA (stora instanser) översikt och arkitektur på Azure](hana-overview-architecture.md) för mer information om den här lösning för säkerhetskopiering på &quot;stora instanser&quot; baserat på ögonblicksbilder av lagring.

Allmän information om SAP-produkter som stöds i Azure finns i [SAP anteckning 1928533](https://launchpad.support.sap.com/#/notes/1928533).

Följande tre bilder ger en översikt över SAP HANA alternativ för säkerhetskopiering med intern Azure-funktioner för närvarande och visar också tre möjliga framtida säkerhetskopieringsscenarier. Relaterade artiklar [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md) och [SAP HANA-säkerhetskopia baserat på ögonblicksbilder av lagring](sap-hana-backup-storage-snapshots.md) beskrivs de olika alternativen i detalj, inklusive storlek- och prestandaöverväganden för SAP HANA säkerhetskopieringar som är flera terabyte i storlek.

![Den här bilden visar två möjligheter för att spara det aktuella tillståndet för virtuell dator](media/sap-hana-backup-guide/image001.png)

Den här bilden visar möjligheten att spara det aktuella tillståndet för virtuell dator, antingen via Azure Backup-tjänsten eller manuell ögonblicksbild av VM-diskar. Med den här metoden en&#39;har att hantera SAP HANA-säkerhetskopiering. Den stora utmaningen med disk ögonblicksbild scenariot är filsystemkonsekvens och en programkonsekvent diskstatusen. Konsekvens-avsnittet beskrivs i avsnittet _SAP HANA-datakonsekvens när du tar ögonblicksbilder av lagring_ senare i den här artikeln. Funktioner och begränsningar för Azure Backup-tjänsten som är relaterade till SAP HANA-säkerhetskopior som också beskrivs senare i den här artikeln.

![Den här bilden visar alternativ för att utföra en SAP HANA säkerhetskopiering på den virtuella datorn](media/sap-hana-backup-guide/image002.png)

Den här bilden visar alternativ för att utföra en säkerhetskopiering av SAP HANA-filer på den virtuella datorn, och sedan lagra den HANA säkerhetskopieringsfilerna någonstans genom att använda olika verktyg. Gör en HANA-säkerhetskopia tar längre tid än en ögonblicksbild-baserade lösning för säkerhetskopiering, men den har fördelar avseende integritet och konsekvens. Mer information finns senare i den här artikeln.

![Den här bilden illustrerar ett möjliga framtida SAP HANA säkerhetskopiering scenario](media/sap-hana-backup-guide/image003.png)

Den här bilden illustrerar ett möjliga framtida SAP HANA säkerhetskopiering scenario. Om SAP HANA säkerhetskopiering från en sekundär replikering måste den skulle lägga till ytterligare alternativ för säkerhetskopiering strategier. För närvarande kan inte enligt ett inlägg i SAP HANA-Wiki:

_&quot;Är det möjligt att göra säkerhetskopior på den sekundära sidan?_

_Nej, för närvarande kan endast ta data och loggsäkerhetskopior på den primära sidan. Om automatisk säkerhetskopiering är aktiverat efter ta över till den sekundära sidan skrivs loggsäkerhetskopior automatiskt det.&quot;_

## <a name="sap-resources-for-hana-backup"></a>Resurser som SAP för HANA-säkerhetskopiering

### <a name="sap-hana-backup-documentation"></a>Dokumentation om backup-SAP HANA

- [Introduktion till SAP HANA-Administration](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planera säkerhetskopiering och strategi](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Schemalägg HANA säkerhetskopiering via ABAP DBACOCKPIT](http://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Schema för säkerhetskopiering av Data (SAP HANA Cockpit)](http://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Vanliga frågor och svar om SAP HANA-säkerhetskopiering på [SAP anteckning 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Vanliga frågor och svar om SAP HANA-databas och lagring ögonblicksbilder i [SAP anteckning 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Olämpliga nätverk filsystem för säkerhetskopiering och återställning i [SAP anteckning 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Varför SAP HANA-säkerhetskopiering?

Azure storage erbjuder tillgänglighet och tillförlitlighet direkt ur lådan (se [introduktion till Microsoft Azure Storage](../../../storage/common/storage-introduction.md) för mer information om Azure storage).

Minimum för &quot;säkerhetskopiering&quot; som förlitar sig på Azure-serviceavtal som SAP HANA data och loggfiler lagras på Azure virtuella hårddiskar anslutna till SAP HANA-server-dator. Den här metoden beskrivs VM-fel, men inte eventuella skador till SAP HANA-data och loggfiler eller logiska fel som tar bort data eller filer av misstag. Säkerhetskopior krävs också för efterlevnad eller juridiska skäl. Kort sagt så finns det alltid ett behov av SAP HANA-säkerhetskopieringar.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Så här verifierar du för SAP HANA-säkerhetskopia
När du använder storage-ögonblicksbilder, bör köra en test-återställning på ett annat system. Den här metoden ger ett sätt att kontrollera att en säkerhetskopia är rätt och interna processer för säkerhetskopiering och Återställ fungerar som förväntat. Även om det är en betydande överskrida lokalt, är det mycket enklare att utföra i molnet genom att tillhandahålla nödvändiga resurser tillfälligt för detta ändamål.

Behåll i åtanke som gör en enkel återställning och kontrollerar om HANA är igång och körs är inte tillräckligt. Vi rekommenderar bör en köra en konsekvenskontroll för tabellen för att se till att den återställda databasen är bra. SAP HANA erbjuder flera olika typer av konsekvenskontroller som beskrivs i [SAP anteckning 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Information om konsekvenskontrollen tabellen finns också på SAP-webbplats på [tabell och Catalog konsekvenskontroller](http://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

För vanliga filsäkerhetskopieringsverktyg behövs inte en test-återställning. Det finns två SAP HANA-verktyg som hjälper dig för att kontrollera vilka backup kan användas för återställning: hdbbackupdiag och hdbbackupcheck. Se [manuellt kontrollerar om en återställning är möjlig](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) för mer information om dessa verktyg.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>- Och nackdelar med HANA-säkerhetskopia jämfört med storage-ögonblicksbilder

SAP&#39;t prioritera antingen HANA-säkerhetskopiering jämfört med storage-ögonblicksbilder. Visas en lista med sina för- och nackdelar, så en kan fastställa vilken ska användas beroende på situationen och tillgängliga lagringsteknik (se [strategi och planera din Backup](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

På Azure, Tänk på det faktum att Azure-blobben ögonblicksbild funktionen&#39;t garanti filsystemkonsekvens (se [med hjälp av blob-ögonblicksbilder med PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). Nästa avsnitt, _SAP HANA-datakonsekvens när du tar ögonblicksbilder av lagring_, beskriver vissa överväganden om den här funktionen.

Dessutom kan en är att förstå fakturering konsekvenserna när du ofta arbetar med blob-ögonblicksbilder som beskrivs i den här artikeln: [förstå hur ögonblicksbilder påförs avgifter](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)– det är&#39;t som är så uppenbar som med hjälp av Azure virtuella diskar.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA-datakonsekvens när du tar ögonblicksbilder av lagring

Filen systemets och programmets konsekvens är ett komplicerat problem när du tar ögonblicksbilder av lagring. Det enklaste sättet att undvika problem är att Stäng av SAP HANA eller kanske även den hela virtuella datorn. En avstängning kanske doable med en demonstration eller prototyp eller även utvecklingssystem, men det är inte ett alternativ för ett produktionssystem.

På Azure, en har att tänka på att Azure-blobben ögonblicksbild funktionen&#39;t garanti filsystemkonsekvens. Det fungerar bra men ögonblicksbild SAP HANA med hjälp av funktionen, så länge det finns bara en enda virtuell disk. Men även med en enskild disk har ytterligare objekt som ska kontrolleras. [SAP anteckning 2039883](https://launchpad.support.sap.com/#/notes/2039883) har viktig information om SAP HANA-säkerhetskopior via ögonblicksbilder av lagring. Till exempel den nämner att med filsystemet XFS är det nödvändigt att köra **xfs\_Lås** innan du startar en storage-ögonblicksbilder för att garantera konsekvens (se [xfs\_freeze(8) - Linux man sidan ](https://linux.die.net/man/8/xfs_freeze) mer information om **xfs\_Lås**).

I avsnittet med konsekvens blir ännu mer utmanande i fall där en enda filsystemet sträcker sig över flera diskar/volymer. Till exempel genom att använda mdadm eller LVM och striping. SAP-kommentar som nämns ovan tillstånd:

_&quot;Men tänk på att lagringssystemet har att garantera i/o-konsekvens när du skapar en storage-ögonblicksbilder per datavolym för SAP HANA, d.v.s. ögonblicksbilder av en volym för SAP HANA-tjänstspecifika data måste vara en atomisk åtgärd.&quot;_

Om vi antar att det finns ett XFS filsystem utsträckning fyra virtuella Azure-diskar, finns i följande steg en konsekvent ögonblicksbild som representerar dataområdet HANA:

- Förbereda HANA-ögonblicksbilder
- Lås filsystemet (till exempel använda **xfs\_Lås**)
- Skapa alla nödvändiga blobögonblicksbilder på Azure
- Lås upp filsystemet
- Bekräfta HANA-ögonblicksbilder

Rekommendationen är att använda proceduren ovan i samtliga fall för att vara på den säkra sidan, oavsett vilket filsystem. Eller om det är en enskild disk eller striping via mdadm eller LVM över flera diskar.

Det är viktigt att bekräfta HANA-ögonblicksbilder. På grund av den &quot;kopiering vid skrivning,&quot; SAP HANA kanske inte kräver ytterligare diskutrymme medan i den här ögonblicksbilden – förbereda läge. Den&#39;också inte går att starta nya säkerhetskopior förrän SAP HANA-ögonblicksbilder har bekräftats.

Azure Backup-tjänsten använder Azure VM-tillägg för att ta hand om filsystemkonsekvens. Dessa VM-tillägg är inte tillgängliga för fristående användning. Ett måste fortfarande hantera SAP HANA konsekvens. Se den relaterade artikeln [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md) för mer information.

### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA-strategi för säkerhetskopiering schemaläggning

SAP HANA-artikeln [strategi och planera din Backup](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) anger en grundläggande åtgärdsplan att säkerhetskopiera:

- Storage ögonblicksbild (dagligen)
- Fullständig säkerhetskopiering med hjälp av fil- eller bacint-format (en gång i veckan)
- Automatiska säkerhetskopior

Du kan också kan en gå helt utan ögonblicksbilder av lagring de kan ersättas av HANA delta säkerhetskopior som inkrementella eller differentiella säkerhetskopieringar (se [Delta säkerhetskopior](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

I HANA Administration guide innehåller en exempel-lista. Det tyder på att en återställa SAP HANA till en viss tidpunkt med hjälp av följande sekvens med säkerhetskopior:

1. Fullständig säkerhetskopiering
2. Differentiell säkerhetskopia
3. Inkrementell säkerhetskopiering 1
4. Inkrementell säkerhetskopiering 2
5. Loggsäkerhetskopior

Om en exakt schema om när och hur ofta en viss typ av säkerhetskopiering ska ske, det går inte att ge en generell riktlinje – det är för kundspecifika och beror på hur många dataändringar uppstå i systemet. En grundläggande rekommendation från SAP-sida, vilket kan ses som en allmän vägledning är att se en fullständig HANA-säkerhetskopia en gång i veckan.
Angående loggsäkerhetskopior finns i dokumentationen för SAP HANA [Loggsäkerhetskopior](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm).

SAP rekommenderar också göra vissa underhåll av säkerhetskopieringskatalogen att hålla den växer oändligt (se [Housekeeping för säkerhetskopieringskatalogen och lagring av säkerhetskopior](http://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>SAP HANA-konfigurationsfiler

Enligt informationen i den vanliga frågor och svar i [SAP anteckning 1642148](https://launchpad.support.sap.com/#/notes/1642148), SAP HANA-konfigurationsfiler ingår inte i en standard HANA-säkerhetskopia. De är inte nödvändigt för att återställa ett system. HANA-konfigurationen kan ändras manuellt efter återställningen. Det är nödvändigt att säkerhetskopiera konfigurationsfiler HANA separat ifall någon vill få samma anpassade konfiguration under återställningsprocessen.

Om standard HANA säkerhetskopieringar ska till ett dedikerat HANA-filsystem för säkerhetskopiering, en kan också kopiera filerna till samma säkerhetskopiering filsystemet och sedan kopiera allt tillsammans att sista lagringsplats som lågfrekvent blob-lagring.

### <a name="sap-hana-cockpit"></a>SAP HANA Cockpit

SAP HANA Cockpit erbjuder möjligheten att övervaka och hantera SAP HANA via en webbläsare. Det kan också hantering av SAP HANA säkerhetskopieringar och därför kan användas som ett alternativ till SAP HANA-Studio och ABAP DBACOCKPIT (se [SAP HANA Cockpit](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) för mer information).

![Den här bilden visar skärmen SAP HANA för Administration av Cockpit-databas](media/sap-hana-backup-guide/image004.png)

Den här bilden visar skärmen SAP HANA för Administration av Cockpit-databasen och säkerhetskopiering panelen till vänster. Se panelen backup kräver lämpliga användarbehörigheter för inloggningskonto.

![Säkerhetskopieringar kan övervakas i SAP HANA Cockpit medan de är pågående](media/sap-hana-backup-guide/image005.png)

Säkerhetskopieringar kan övervakas i SAP HANA Cockpit medan de är pågående, och när den är klar, alla säkerhetskopieringsinformation är tillgängliga.

![Ett exempel med Firefox på en Azure SLES 12 VM med gör väldigt lätt desktop](media/sap-hana-backup-guide/image006.png)

De föregående skärmbilderna hittar har gjorts från en Windows Azure-dator. Det här är ett exempel med Firefox på en Azure SLES 12 VM med gör väldigt lätt desktop. Den visar möjlighet att definiera scheman för säkerhetskopiering av SAP HANA i SAP HANA Cockpit. När en kan också se föreslår datum/tid som ett prefix för de säkerhetskopiera filerna. I SAP HANA Studio standardprefixet är &quot;Slutför\_DATA\_säkerhetskopiering&quot; när du gör en fullständig säkerhetskopia. Du bör använda ett unikt prefix.

### <a name="sap-hana-backup-encryption"></a>Kryptering av SAP HANA-säkerhetskopiering

SAP HANA erbjuder kryptering av data och loggfiler. Om SAP HANA-data och loggfiler inte är krypterade, sedan krypteras säkerhetskopiorna som också inte. Det är upp till kunden att använda någon form av lösning från tredje part för att kryptera säkerhetskopiorna SAP HANA. Se [Data och Log volymkryptering](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) och lär dig mer om SAP HANA-kryptering.

En kund kan använda funktionen för kryptering av IaaS VM för att kryptera på Microsoft Azure. En kan till exempel använda dedikerade diskar som är anslutna till den virtuella datorn som används för att lagra säkerhetskopior av SAP HANA och sedan göra kopior av dessa diskar.

Azure Backup-tjänsten kan hantera krypterade virtuella datorer/diskar (se [säkerhetskopiera och återställa krypterade virtuella datorer med Azure Backup](../../../backup/backup-azure-vms-encryption.md)).

Ett annat alternativ är att upprätthålla SAP HANA VM och dess diskar utan kryptering och lagra de säkerhetskopiera filerna för SAP HANA i ett lagringskonto som kryptering aktiverades (se [Azure Storage Service Encryption för vilande Data](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Inställningar för

### <a name="test-virtual-machine-on-azure"></a>Testa virtuell dator på Azure

För att kunna utföra våra tester, användes en SAP HANA-installation i en Azure virtuell GS5-dator för följande säkerhetskopiering/återställning tester. Principerna är desamma som för virtuella datorer i M-serien.

![Den här bilden illustrerar en del av Azure portal översikten för den Virtuella testdatorn i HANA](media/sap-hana-backup-guide/image007.png)

Den här bilden illustrerar en del av Azure portal översikten för den Virtuella testdatorn i HANA.

### <a name="test-backup-size"></a>Testa säkerhetskopians storlek

![Den här bilden har kopplats från säkerhetskopieringskonsol i HANA Studio och visar den säkerhetskopiera filen storleken 229 GB för indexservern HANA](media/sap-hana-backup-guide/image008.png)

En dummytabell har fylls med data för att få en totala säkerhetskopierade datastorleken på över 200 GB att härleda realistisk prestandadata. Bilden har kopplats från säkerhetskopieringskonsol i HANA Studio och visar den säkerhetskopiera filen storleken 229 GB för indexservern HANA. För tester, har säkerhetskopiering standardprefixet ”COMPLETE_DATA_BACKUP” i SAP HANA-Studio använts. Ett mer användbart prefix ska definieras i verkliga produktionssystem. SAP HANA Cockpit föreslår datum/tid.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Testa för att kopiera filer direkt till Azure storage

Verktyget blobxfer användes för att överföra säkerhetskopiorna för SAP HANA direkt till Azure blob storage eller Azure-filresurser, eftersom den stöder båda mål och enkelt kan integreras i automatiseringsskript på grund av dess kommandoradsgränssnittet. Blobxfer-verktyget finns på [GitHub](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Testa säkerhetskopians storlek uppskattning

Det är viktigt att uppskatta säkerhetskopiering storleken på SAP HANA. Den här beräkningen hjälper till att förbättra prestanda genom att definiera den maximala säkerhetskopia storleken för ett antal säkerhetskopior på grund av parallellitet under en filkopia. (Detaljer förklaras nedan.) En måste också bestämma om du vill göra en fullständig säkerhetskopia eller en delta-säkerhetskopiering (inkrementell eller differentiell).

Som tur är kan det finns ett enkelt SQL-uttryck som beräknar storleken på de säkerhetskopiera filerna: **Välj \* från M\_säkerhetskopiering\_storlek\_UPPSKATTNINGAR** (se [uppskattning Utrymmet som krävs i filsystemet för en säkerhetskopiering av Data](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![Utdata från den här SQL-instruktionen matchar nästan exakt den verkliga storleken på den fullständig säkerhetskopieringen på disk](media/sap-hana-backup-guide/image009.png)

Utdata från den här SQL-instruktionen matchar för testsystemet nästan exakt den verkliga storleken på den fullständig säkerhetskopieringen på disk.

### <a name="test-hana-backup-file-size"></a>Testa HANA säkerhetskopians storlek

![HANA-Studio säkerhetskopieringskonsol går det att begränsa max filstorlek på HANA säkerhetskopior](media/sap-hana-backup-guide/image010.png)

HANA-Studio säkerhetskopieringskonsol går det att begränsa max filstorlek på HANA säkerhetskopior. I exemplet-miljö som funktionen gör det möjligt att hämta flera mindre säkerhetskopierade filer i stället för en 230 GB-säkerhetskopian. Mindre filstorlek har en betydande inverkan på prestanda (finns i den relaterade artikeln [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Sammanfattning

Baserat på resultaten av följande tabeller visar- och nackdelar med lösningar för säkerhetskopiering av en SAP HANA-databas som körs på virtuella Azure-datorer.

**Säkerhetskopiering av SAP HANA till filsystemet och därefter kopiera säkerhetskopieringsfilerna till slutdestinationen för säkerhetskopiering**

|Lösning                                           |Experter                                 |Nackdelar                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Behåll HANA säkerhetskopior på VM-diskar                      |Inget ytterligare arbete     |Eats upp diskutrymme för lokal virtuell dator           |
|Blobxfer verktyg för att kopiera säkerhetskopieringsfilerna till blob storage |Parallelliteten och kopiera flera filer, möjlighet att använda lågfrekvent åtkomstnivå av blob-lagring | Ytterligare verktyget underhåll och anpassade skript | 
|BLOB-kopiering via Powershell eller CLI                    |Inget ytterligare verktyg som behövs, kan utföras via Azure Powershell eller CLI |manuell process kund har att ta hand om skript och hantering av kopierade BLOB för återställning|
|Kopiera till NFS-resurs                                  |Efter bearbetning av säkerhetskopior på andra virtuella datorer utan påverkan på HANA-servern|Långsam kopieringsprocessen|
|Blobxfer kopiera till Azure File Service                |Inte äta upp utrymme på den lokala Virtuella diskar|Ingen direkt skriva support med HANA säkerhetskopiering, storleksbegränsningen på filresursen för närvarande i 5 TB|
|Azure Backup-agenten                                 | Är en bättre lösning         | För närvarande inte tillgängligt i Linux    |



**Säkerhetskopiering SAP HANA baserat på ögonblicksbilder av lagring**

|Lösning                                           |Experter                                 |Nackdelar                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Azure Backup Service                               | Gör säkerhetskopiering av virtuella datorer baserat på blob-ögonblicksbilder | När du inte använder nivå filåterställning, krävs att skapa en ny virtuell dator för återställningsprocessen, vilket innebär behovet av en ny nyckel för SAP HANA-licens|
|Manuell blobögonblicksbilderna                              | Flexibilitet för att skapa och återställa specifika VM-diskar utan att ändra unikt ID för virtuell dator|Alla manuellt arbete som har utförts av kunden|

## <a name="next-steps"></a>Nästa steg
* [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md) beskriver av filbaserade säkerhetskopieringen.
* [SAP HANA-säkerhetskopia baserat på ögonblicksbilder av lagring](sap-hana-backup-storage-snapshots.md) beskriver ögonblicksbildbaserade backup alternativet.
* Läs hur du etablerar hög tillgänglighet och planera för katastrofåterställning av SAP HANA på Azure (stora instanser) i [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md).
