---
title: Säkerhets kopierings guide för SAP HANA på Azure Virtual Machines | Microsoft Docs
description: Säkerhets kopierings guiden för SAP HANA ger två stora säkerhets kopierings möjligheter för SAP HANA på virtuella Azure-datorer
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: hermannd
ms.openlocfilehash: 8de83cbb7060e6ca5390720a4a241be71bb9dc92
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617431"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Säkerhets kopierings guide för SAP HANA på Azure Virtual Machines

## <a name="getting-started"></a>Komma igång

Säkerhets kopierings guiden för SAP HANA som körs på virtuella Azure-datorer kommer bara att beskriva Azure-specifika ämnen. Information om allmänna SAP HANA säkerhets kopiering av objekt finns i SAP HANA-dokumentationen (se _SAP HANA backup-dokumentationen_ senare i den här artikeln).

Den här artikeln fokuserar på två huvudsakliga säkerhets kopierings möjligheter för SAP HANA på virtuella Azure-datorer:

- HANA-säkerhetskopiering till fil systemet på en virtuell Azure Linux-dator (se [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md))
- HANA-säkerhetskopiering baserat på lagrings ögonblicks bilder med hjälp av ögonblicks bild funktionen Azure Storage BLOB manuellt eller Azure Backup tjänsten (se [SAP HANA säkerhets kopiering baserat på lagrings ögonblicks bilder](sap-hana-backup-storage-snapshots.md))

SAP HANA erbjuder ett säkerhets kopierings-API som gör det möjligt för säkerhets kopierings verktyg från tredje part att integrera direkt med SAP HANA. (Det är inte inom omfånget för den här hand boken.) Det finns ingen direkt integrering av SAP HANA med Azure Backup-tjänsten tillgänglig just nu baserat på detta API.

SAP HANA stöds officiellt på olika typer av virtuella Azure-datorer, t. ex. Azure M-serien. En fullständig lista över SAP HANA certifierade virtuella Azure-datorer finns i [hitta certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Den här artikeln kommer att uppdateras när nya erbjudanden för SAP HANA på Azure blir tillgängliga.

Det finns också en SAP HANA hybrid lösning som är tillgänglig på Azure, där SAP HANA körs icke-virtualiserade på fysiska servrar. Detta SAP HANA Azure Backup-guiden täcker dock en ren Azure-miljö där SAP HANA körs i en virtuell Azure-dator, inte SAP HANA som körs på &quot;stora instanser.&quot; se [Översikt och arkitektur för SAP HANA (stora instanser) i Azure](hana-overview-architecture.md) för mer information om den här säkerhets kopierings lösningen på &quot;stora instanser&quot; baserat på lagrings ögonblicks bilder.

Allmän information om SAP-produkter som stöds i Azure finns i [SAP Note 1928533](https://launchpad.support.sap.com/#/notes/1928533).

Följande tre figurer ger en översikt över de SAP HANA säkerhets kopierings alternativen med inbyggda Azure-funktioner för närvarande, och visar även tre möjliga framtida säkerhets kopierings scenarier. Relaterade artiklar [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md) och [SAP HANA säkerhets kopia som baseras på lagrings ögonblicks bilder](sap-hana-backup-storage-snapshots.md) beskriver dessa alternativ i detalj, inklusive storlek och prestanda överväganden för SAP HANA säkerhets kopior som är flera terabyte stora.

![Den här bilden visar två möjligheter att spara den aktuella VM-statusen](media/sap-hana-backup-guide/image001.png)

Den här bilden visar möjligheten att spara den aktuella VM-statusen, antingen via Azure Backup tjänst eller manuell ögonblicks bild av virtuella dator diskar. Med den här metoden behöver en,&#39;t. ex. inte hantera SAP HANA säkerhets kopieringar. Utmaningen med disk ögonblicks bild scenariot är konsekvens i fil systemet och ett programkonsekvent disk tillstånd. Konsekvens avsnittet beskrivs i avsnittet _SAP HANA data konsekvens när du tar lagrings ögonblicks bilder_ senare i den här artikeln. Funktioner och begränsningar för Azure Backup tjänst som är relaterade till SAP HANA säkerhets kopieringar beskrivs också längre fram i den här artikeln.

![Den här bilden visar alternativ för att göra en SAP HANA säkerhets kopia av filen i den virtuella datorn](media/sap-hana-backup-guide/image002.png)

Den här bilden visar alternativ för att göra en SAP HANA säkerhets kopiering i den virtuella datorn och sedan lagra den HANA-säkerhetskopierade filer någon annan stans med hjälp av olika verktyg. Att ta en HANA-säkerhetskopiering kräver mer tid än en ögonblicks bild baserad säkerhets kopierings lösning, men den har fördelar med integritet och konsekvens. Mer information finns längre fram i den här artikeln.

![Den här bilden visar ett möjligt framtida SAP HANA säkerhets kopierings scenario](media/sap-hana-backup-guide/image003.png)

Den här bilden visar ett möjligt framtida SAP HANA säkerhets kopierings scenario. Om SAP HANA tillåts att göra säkerhets kopior från en sekundär replikering, lägger den till ytterligare alternativ för säkerhets kopierings strategier. Det är för närvarande inte möjligt enligt ett inlägg i SAP HANA wiki:

_&quot;kan du göra säkerhets kopior på den sekundära Sidan?_

_Nej, för närvarande kan du bara ta säkerhets kopior av data och loggar på den primära sidan. Om automatisk logg säkerhets kopiering är aktive rad skrivs logg säkerhets kopiorna automatiskt när de har överköps till den sekundära sidan.&quot;_

## <a name="sap-resources-for-hana-backup"></a>SAP-resurser för HANA-säkerhetskopiering

### <a name="sap-hana-backup-documentation"></a>Dokumentation om SAP HANA backup

- [Introduktion till SAP HANA administration](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planera din säkerhets kopierings-och återställnings strategi](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Schemalägg HANA-säkerhetskopiering med ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Schemalägg data säkerhets kopieringar (SAP HANA cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Vanliga frågor och svar om SAP HANA säkerhets kopiering i [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Vanliga frågor om SAP HANA databas-och lagrings ögonblicks bilder i [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Olämpliga nätverks fil system för säkerhets kopiering och återställning i [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Varför SAP HANA säkerhets kopiering?

Azure Storage erbjuder tillgänglighet och tillförlitlighet (mer information om Azure Storage finns i [Introduktion till Microsoft Azure Storage](../../../storage/common/storage-introduction.md) ).

Minimi kravet för &quot;säkerhets kopierings&quot; är att förlita dig på Azure-service avtal och behålla SAP HANA data och loggfiler på Azure-VHD: ar kopplade till den virtuella SAP HANA servern. Den här metoden omfattar VM-fel, men inte potentiell skada på SAP HANA data och loggfiler, eller logiska fel som att ta bort data eller filer av misstag. Säkerhets kopieringar krävs också för efterlevnad eller juridiska skäl. I korthet krävs det alltid SAP HANA säkerhets kopieringar.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Så här verifierar du att SAP HANA säkerhets kopiering är korrekt
När du använder lagrings ögonblicks bilder rekommenderas du att köra en test återställning på ett annat system. Den här metoden gör det möjligt att säkerställa att en säkerhets kopia är korrekt och interna processer för säkerhets kopiering och återställning fungerar som förväntat. Även om det här är en betydande Hurdle lokalt, är det mycket enklare att utföra i molnet genom att tillhandahålla nödvändiga resurser tillfälligt för detta ändamål.

Tänk på att det inte räcker med att göra en enkel återställning och kontrol lera om HANA är igång. Vi rekommenderar att du kör en konsekvens kontroll för en tabell för att vara säker på att den återställda databasen fungerar. SAP HANA erbjuder flera olika typer av konsekvens kontroller som beskrivs i [SAP anmärkning 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Information om konsekvens kontroll av tabell finns också på SAP-webbplatsen i [konsekvens kontroller för tabeller och kataloger](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

För säkerhets kopiering av standard fil behövs ingen test återställning. Det finns två SAP HANA-verktyg som hjälper dig att kontrol lera vilken säkerhets kopia som kan användas för återställning: hdbbackupdiag och hdbbackupcheck. Se [manuellt kontrol lera om det](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) går att göra en återställning för mer information om dessa verktyg.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>För-och nack delar med HANA-säkerhetskopiering jämfört med lagrings ögonblicks bilder

SAP har ingen&#39;preferens till antingen Hana-säkerhetskopiering jämfört med lagrings ögonblicks bilder. Den innehåller en lista med sina respektive-och nack delar, så att du kan avgöra vilken som ska användas beroende på situationen och tillgänglig lagrings teknik (se [Planera din strategi för säkerhets kopiering och återställning](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

På Azure bör du vara medveten om det faktum att funktionen för ögonblicks bilder av&#39;Azure-Blob gör att fil systemets konsekvens är konsekvent (se [använda BLOB-ögonblicksbilder med PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). I nästa avsnitt _SAP HANA data konsekvens när du tar lagrings ögonblicks bilder_, diskuteras några saker om den här funktionen.

Dessutom måste en vara medveten om fakturerings konsekvenserna när du arbetar ofta med BLOB-ögonblicksbilder enligt beskrivningen i den här artikeln: [förstå hur ögonblicks bilder debiteras](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)– det&#39;inte är t as är självklart som att använda virtuella Azure-diskar.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA data konsekvens när du tar lagrings ögonblicks bilder

Fil system-och program konsekvens är ett komplext problem när du tar lagrings ögonblicks bilder. Det enklaste sättet att undvika problem är att stänga SAP HANA, eller kanske till och med hela den virtuella datorn. En avstängning kan vara doable med en demo eller prototyp, eller till och med ett utvecklings system, men det är inget alternativ för ett produktions system.

På Azure måste en vara medveten om att funktionen för ögonblicks bilder av Azure-Blob&#39;gör att fil systemet är konsekvent. Det fungerar med SAP HANA ögonblicks bilds funktionen, så länge det bara finns en enda virtuell disk som ingår. Men även om du har en enskild disk måste du kontrol lera ytterligare objekt. [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883) innehåller viktig information om SAP HANA säkerhets kopieringar via ögonblicks bilder av lagring. Det innebär till exempel att, med fil systemet XFS, är det nödvändigt att köra **XFS\_frysa** innan en lagrings ögonblicks bild startas för att garantera konsekvens (se [xfs\_frys (8) – Linux man-sidan](https://linux.die.net/man/8/xfs_freeze) för information om **xfs\_frysa**).

Ämnet för konsekvens blir ännu mer utmanande i ett fall där ett enda fil system sträcker sig över flera diskar/volymer. Till exempel med hjälp av mdadm eller LVM och randning. SAP-noteringen som nämns ovan:

_&quot;men tänk på att lagrings systemet måste garantera I/O-konsekvens när du skapar en lagrings ögonblicks bild per SAP HANA data volym, d.v.s. snapshotting av en SAP HANA tjänstespecifika data volym måste vara en atomisk åtgärd.&quot;_

Om det finns ett XFS fil system som sträcker sig över fyra virtuella Azure-diskar, ger följande steg en enhetlig ögonblicks bild som representerar data arean HANA:

- HANA-ögonblicks bild förbereds
- Lås fil systemet (till exempel Använd **xfs\_frysa**)
- Skapa alla nödvändiga BLOB-ögonblicksbilder på Azure
- Låsa upp fil systemet
- Bekräfta HANA-ögonblicksbilden

Rekommendationen är att använda proceduren ovan i alla fall som på den säkra sidan, oavsett vilket fil system. Eller om det är en enskild disk eller striping, via mdadm eller LVM över flera diskar.

Det är viktigt att bekräfta HANA-ögonblicksbilden. På grund av &quot;kopiering vid skrivning kan&quot; SAP HANA inte kräva ytterligare disk utrymme i det här läget för ögonblicks bild förberedelse. Det&#39;går inte heller att starta nya säkerhets kopior förrän SAP HANA ögonblicks bilden har bekräftats.

Azure Backup tjänsten använder Azure VM-tillägg för att ta hand om fil systemets konsekvens. De här VM-tilläggen är inte tillgängliga för fristående användning. En måste ändå hantera SAP HANA konsekvens. Mer information finns i relaterad artikel [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md) .

### <a name="sap-hana-backup-scheduling-strategy"></a>Strategi för SAP HANA säkerhets kopierings planering

SAP HANA artikel som [planerar säkerhets kopierings-och återställnings strategin](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) är en grundläggande plan för att göra säkerhets kopior:

- Lagrings ögonblicks bild (varje dag)
- Fullständig säkerhets kopiering av data med fil-eller bacint format (en gång i veckan)
- Automatisk logg säkerhets kopiering

Du kan också gå helt utan lagrings ögonblicks bilder. de kan ersättas av HANA delta säkerhets kopior, t. ex. stegvisa eller differentiella säkerhets kopieringar (se [delta säkerhets kopior](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

I administratörs guiden för HANA finns en exempel lista. Det föreslår att en återställning SAP HANA till en viss tidpunkt med hjälp av följande sekvens av säkerhets kopior:

1. Fullständig data säkerhets kopiering
2. Differentiell säkerhetskopia
3. Stegvis säkerhets kopia 1
4. Stegvis säkerhets kopia 2
5. Logg säkerhets kopior

För ett exakt schema när och hur ofta en specifik säkerhets kopierings typ ska inträffa, är det inte möjligt att ge en allmän rikt linje, den är för kundspecifik och beror på hur många data ändringar som förekommer i systemet. En grundläggande rekommendation från SAP-sidan, som kan ses som allmän vägledning, är att göra en fullständig HANA-säkerhetskopiering en gång i veckan.
Information om säkerhets kopior av loggar finns i [säkerhets kopior](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)av SAP HANA dokumentations loggen.

SAP rekommenderar också att du gör en del underhåll av säkerhets kopierings katalogen för att hålla den från att växa oändligt (se [underhåll för säkerhets kopierings katalogen och lagring av säkerhets kopior](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>SAP HANA konfigurationsfiler

Som anges i vanliga frågor och svar i [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148), ingår inte SAP HANA konfigurationsfilerna i en standard Hana-säkerhetskopiering. De är inte nödvändiga för att återställa ett system. Konfigurationen HANA kan ändras manuellt efter återställningen. Om en skulle vilja hämta samma anpassade konfiguration under återställnings processen, är det nödvändigt att säkerhetskopiera HANA-konfigurationsfilerna separat.

Om standard HANA-säkerhetskopieringar går till ett dedikerat HANA-säkerhetskopierat fil system, kan en kopia också kopiera konfigurationsfilerna till samma säkerhets kopierings fil system och sedan Kopiera allting till det slutliga lagrings målet som cool Blob Storage.

### <a name="sap-hana-cockpit"></a>SAP HANA cockpit

SAP HANA cockpit ger möjlighet att övervaka och hantera SAP HANA via en webbläsare. Du kan också hantera SAP HANA säkerhets kopior och kan därför användas som ett alternativ till att SAP HANA Studio-och ABAP-DBACOCKPIT (se [SAP HANA cockpit](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) för mer information).

![Den här bilden visar fönstret SAP HANA cockpit för databas administration](media/sap-hana-backup-guide/image004.png)

Den här bilden visar fönstret SAP HANA cockpit för databas administration och säkerhets kopierings panelen till vänster. Det krävs lämpliga användar behörigheter för inloggnings kontot för att se säkerhets kopierings panelen.

![Säkerhets kopieringar kan övervakas i SAP HANA cockpit medan de pågår](media/sap-hana-backup-guide/image005.png)

Säkerhets kopieringar kan övervakas i SAP HANA cockpit medan de pågår och när det är klart är all säkerhets kopierings information tillgänglig.

![Ett exempel som använder Firefox på en virtuell Azure SLES 12-dator med gnome desktop](media/sap-hana-backup-guide/image006.png)

Föregående skärm bilder gjordes från en virtuell Azure Windows-dator. Det här är ett exempel som använder Firefox på en virtuell Azure SLES 12-dator med gnome desktop. Det visar hur du definierar SAP HANA säkerhets kopierings scheman i SAP HANA cockpit. Eftersom en kan också se, föreslår det datum/tid som prefix för säkerhetskopieringsfilerna. I SAP HANA Studio är standardprefixet &quot;klart\_DATA\_säkerhets kopierings&quot; när du gör en fullständig fil säkerhets kopiering. Vi rekommenderar att du använder ett unikt prefix.

### <a name="sap-hana-backup-encryption"></a>SAP HANA kryptering av säkerhets kopiering

SAP HANA erbjuder kryptering av data och logg. Om SAP HANA data och loggen inte krypteras krypteras inte säkerhets kopiorna. Det är upp till kunden att använda någon form av en lösning från tredje part för att kryptera SAP HANA säkerhets kopior. Läs mer om SAP HANA kryptering i [data-och logg volym kryptering](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) .

På Microsoft Azure kan kunden använda krypterings funktionen IaaS VM för att kryptera. En kan till exempel använda dedikerade data diskar som är anslutna till den virtuella datorn, som används för att lagra SAP HANA säkerhets kopior och sedan göra kopior av dessa diskar.

Azure Backups tjänsten kan hantera krypterade virtuella datorer/diskar (se [säkerhetskopiera och återställa krypterade virtuella datorer med Azure Backup](../../../backup/backup-azure-vms-encryption.md)).

Ett annat alternativ är att underhålla SAP HANA VM och dess diskar utan kryptering, och lagra SAP HANA säkerhetskopierade filer i ett lagrings konto som kryptering har Aktiver ATS för (se [Azure Storage tjänst kryptering för data i vila](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Testa installationen

### <a name="test-virtual-machine-on-azure"></a>Testa en virtuell dator på Azure

För att kunna utföra våra tester användes en SAP HANA-installation i en virtuell Azure GS5-dator för följande säkerhets kopierings-och återställnings test. Principerna är desamma som för virtuella datorer i M-serien.

![Den här bilden visar en del av Azure Portal översikt för den virtuella HANA-test datorn](media/sap-hana-backup-guide/image007.png)

Den här bilden visar en del av Azure Portal översikt för den virtuella HANA-test datorn.

### <a name="test-backup-size"></a>Testa säkerhets kopierings storlek

![Den här bilden har hämtats från säkerhets kopierings konsolen i HANA Studio och visar säkerhets kopians storlek på 229 GB för HANA-index servern](media/sap-hana-backup-guide/image008.png)

En dummy-tabell har fyllts med data för att få en total säkerhets kopierings storlek på över 200 GB för att härleda realistiska prestanda data. Bilden har hämtats från säkerhets kopierings konsolen i HANA Studio och visar säkerhets kopians storlek på 229 GB för HANA-index servern. För testerna användes standardprefixet för säkerhets kopiering "COMPLETE_DATA_BACKUP" i SAP HANA Studio. I verkliga produktions system bör ett mer användbart prefix definieras. Cockpit för SAP HANA föreslår datum/tid.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Test verktyg för att kopiera filer direkt till Azure Storage

För att överföra SAP HANA säkerhetskopierade filer direkt till Azure Blob Storage, eller Azure-filresurser, användes verktyget blobxfer eftersom det stöder båda målen och det kan enkelt integreras i Automation-skript på grund av dess kommando rads gränssnitt. Verktyget blobxfer finns på [GitHub](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Testa storleks uppskattning för säkerhets kopiering

Det är viktigt att uppskatta säkerhets kopierings storleken för SAP HANA. Den här uppskattningen hjälper till att förbättra prestanda genom att definiera den största storleken på säkerhets kopian för ett antal säkerhetskopieringsfiler, på grund av parallellitet under en fil kopiering. (Informationen beskrivs längre fram i den här artikeln.) Du måste också bestämma om du vill göra en fullständig säkerhets kopiering eller en delta säkerhets kopia (stegvis eller differentiell).

Lyckligt vis finns det ett enkelt SQL-uttryck som beräknar storleken på de säkerhetskopierade filerna: **välj \* från M\_säkerhets kopiering\_storlek\_uppskattningar** (se [uppskatta det utrymme som krävs i fil systemet för en data säkerhets kopia](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![Utdata från det här SQL-uttrycket matchar nästan exakt den verkliga storleken på disken med fullständig säkerhets kopiering av data](media/sap-hana-backup-guide/image009.png)

I test systemet matchar utdata för detta SQL-uttryck nästan exakt den verkliga storleken på disken med fullständig säkerhets kopiering.

### <a name="test-hana-backup-file-size"></a>Testa HANA säkerhets kopians fil storlek

![Säkerhets kopierings konsolen för HANA Studio tillåter att en maximal fil storlek på HANA-säkerhetskopieringsfiler begränsas](media/sap-hana-backup-guide/image010.png)

Säkerhets kopierings konsolen för HANA Studio tillåter att en maximal fil storlek på HANA-säkerhetskopieringsfiler begränsas. I exempel miljön gör funktionen det möjligt att få flera mindre säkerhetskopieringsfiler i stället för en säkerhets kopia med 1 230 GB. Mindre fil storlek har en betydande inverkan på prestanda (se relaterad artikel [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Sammanfattning

Baserat på test resultaten visar följande tabeller för-och nack delar med lösningar för att säkerhetskopiera en SAP HANA databas som körs på virtuella Azure-datorer.

**Säkerhetskopiera SAP HANA till fil systemet och kopiera säkerhetskopierade filer efteråt till det sista säkerhets kopierings målet**

|Lösning                                           |Experter                                 |Nackdelar                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Behåll HANA-säkerhetskopieringar på VM-diskar                      |Inga ytterligare hanterings åtgärder     |Eats disk utrymme för lokal virtuell dator           |
|Blobxfer-verktyg för att kopiera säkerhetskopierade filer till Blob Storage |Parallellitet för att kopiera flera filer, Välj att använda cool Blob Storage | Ytterligare verktyg underhåll och anpassat skript | 
|BLOB-kopia via PowerShell eller CLI                    |Det finns inga ytterligare verktyg som behövs, kan utföras via Azure PowerShell eller CLI |manuell process måste kunden ta hand om skript och hantering av kopierade blobbar för återställning|
|Kopiera till NFS-resurs                                  |Efter bearbetning av säkerhetskopieringsfiler på andra virtuella datorer utan påverkan på HANA-servern|Långsam kopierings process|
|Blobxfer kopiera till Azure File Service                |Tar inte upp utrymme på lokala virtuella dator diskar|Inget direkt skrivnings stöd av HANA-säkerhetskopiering, storleks begränsning för fil resurs som för närvarande är 5 TB|
|Azure Backup Agent                                 | Är en lämplig lösning         | För närvarande inte tillgängligt i Linux    |



**Säkerhetskopiera SAP HANA baserat på lagrings ögonblicks bilder**

|Lösning                                           |Experter                                 |Nackdelar                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Azure Backup Service                               | Tillåter säkerhets kopiering av virtuella datorer baserat på BLOB-ögonblicksbilder | Om du inte använder återställning på filnivå måste du skapa en ny virtuell dator för återställnings processen, vilket innebär att du behöver en ny SAP HANA licens nyckel|
|Manuella BLOB-ögonblicksbilder                              | Flexibilitet för att skapa och återställa specifika VM-diskar utan att ändra det unika VM-ID: t|Allt manuellt arbete, som måste göras av kunden|

## <a name="next-steps"></a>Nästa steg
* [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md) beskriver det filbaserade säkerhets kopierings alternativet.
* [SAP HANA säkerhets kopiering baserat på lagrings ögonblicks bilder](sap-hana-backup-storage-snapshots.md) beskriver alternativet lagrings ögonblicks bild-baserad säkerhets kopiering.
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på Azure (stora instanser) finns i [SAP HANA (stora instanser) hög tillgänglighet och haveri beredskap på Azure](hana-overview-high-availability-disaster-recovery.md).
