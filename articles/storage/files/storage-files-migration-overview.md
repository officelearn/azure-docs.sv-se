---
title: Migrera till Azure-filresurser
description: Lär dig mer om migreringar till Azure-filresurser och hitta din migreringsguide.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 903ce52120fce7c23c6a3754498b81fc6fc2430f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247324"
---
# <a name="migrate-to-azure-file-shares"></a>Migrera till Azure-filresurser

Den här artikeln beskriver de grundläggande aspekterna av en migrering till Azure-filresurser.

Tillsammans med grunderna för migrering innehåller den här artikeln en lista över befintliga, individualiserade migreringsguider. Dessa migreringsguider hjälper dig att flytta dina filer till Azure-filresurser och är ordnade efter var dina data finns idag och vilken distributionsmodell (endast molnet eller hybrid) som du planerar att flytta till.

## <a name="migration-basics"></a>Grunderna i migrering

Det finns flera olika typer av molnlagring som är tillgängliga i Azure. En grundläggande aspekt av en migrering av filer till Azure är att avgöra vilket Azure-lagringsalternativ som är rätt för dina data.

Azure-filresurser är bra för allmänna fildata. Verkligen något du använder en lokal SMB eller NFS aktie för. Med [Azure File Sync](storage-sync-files-planning.md)kan du eventuellt cachela innehållet i flera Azure-filresurser på flera lokala Windows-servrar.

Om du har ett program som körs på en lokal server kan lagring av filer i Azure-filresurser vara rätt för dig, beroende på programmet. Du kan lyfta programmet för att köras i Azure och använda Azure-filresurser som delad lagring. Du kan också överväga [Azure-diskar](../../virtual-machines/windows/managed-disks-overview.md) för det här scenariot. För molnfödda program som inte är beroende av SMB eller datorlokal åtkomst till sina data eller delad åtkomst är objektlagring, till exempel [Azure-blobbar,](../blobs/storage-blobs-overview.md)ofta det bästa valet.

Nyckeln i en migrering är att fånga alla tillämpliga filåtergivning när du migrerar dina filer från deras aktuella lagringsplats till Azure. En hjälp med att välja rätt Azure-lagring är också en aspekt av hur mycket återgivning som stöds av Azure-lagringsalternativet och krävs av ditt scenario. Fildata för allmänt ändamål beror traditionellt på filmetadata. Programdata kanske inte gör det. Det finns två grundläggande komponenter i en fil:

- **Dataström:** Dataströmmen för en fil lagrar filinnehållet.
- **Filmetadata**: Filens metadata har flera underkomponenter:
   * Filattribut: Skrivskyddad, till exempel.
   * Filbehörigheter: Kallas *NTFS-behörigheter* eller *ACL:er*för fil och mapp .
   * Tidsstämplar: Framför allt *de skapa-* och sista ändrade tidsstämplarna. *last modified-*
   * Alternativ dataström: Ett utrymme för att lagra större mängder icke-standardiserade egenskaper.

Filåtergivning, i en migrering, kan därför definieras som möjligheten att lagra all tillämplig filinformation på källan, möjligheten att överföra dem med migreringsverktyget och möjligheten att lagra dem på mållagringen av migreringen.

Om du vill vara säker på att migreringsningen fortsätter så smidigt som möjligt kan du identifiera [det bästa kopieringsverktyget för dina behov](#migration-toolbox) och matcha ett lagringsmål med källan.

Med hänsyn till den tidigare informationen blir det tydligt vad mållagringen för allmänna filer i Azure är: [Azure-filresurser](storage-files-introduction.md). Jämfört med objektlagring i Azure-blobbar kan filmetadata lagras inbyggt på filer i en Azure-filresurs.

Azure-filresurser bevarar också fil- och mapphierarkin. Dessutom:
* NTFS-behörigheter kan lagras på filer och mappar när de är lokala.
* AD-användare (eller Azure AD DS-användare) kan komma åt en Azure-filresurs internt. 
    De använder sin nuvarande identitet och får åtkomst baserat på resursbehörigheter samt fil- och mapp-ACL:er. Ett beteende som inte skiljer sig från när användare ansluter till en lokal filresurs.
*  Den alternativa dataströmmen är den primära aspekten av filåtergivning som för närvarande inte kan lagras på en fil i en Azure-filresurs.
   Den bevaras lokalt när Azure File Sync är inblandad.

* [Läs mer om AD-autentisering för Azure-filresurser](storage-files-identity-auth-active-directory-enable.md)
* [Läs mer om AAD DS-autentisering (Azure Active Directory Domain Services) för Azure-filresurser](storage-files-identity-auth-active-directory-domain-service-enable.md)

## <a name="migration-guides"></a>Migreringsguider

I följande tabell visas detaljerade migreringsguider.

Navigera den genom att:
1. Leta reda på raden för källsystemet som filerna för närvarande lagras på.
2. Bestäm om du inriktar dig på en hybriddistribution där du använder Azure File Sync för att cachelagra innehållet i en eller flera Azure-filresurser lokalt, eller om du gillar att använda Azure-filresurser direkt i molnet. Välj den målkolumn som återspeglar ditt beslut.
3. I skärningspunkten mellan källa och mål visar en tabellcell tillgängliga migreringsscenarier. Välj en av dem om du vill länka direkt till den detaljerade migreringsguiden.

Ett scenario utan länk har ännu inte en publicerad migreringsguide. Kontrollera den här tabellen ibland för uppdateringar. Nya guider kommer att publiceras när de är tillgängliga.

| Källa | Mål: </br>Hybriddistribution | Mål: </br>Distribution endast för molnet |
|:---|:--|:--|
| | Verktygskombination:| Verktygskombination: |
| Windows Server 2012 R2 och nyare | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Synkronisering av Azure-filer + DataBox](storage-sync-offline-data-transfer.md)</li><li>Migreringstjänst för lagring + Synkronisering av Azure-filer</li></ul> | <ul><li>Azure File Sync</li><li>Synkronisering av Azure-filer + DataBox</li><li>Migreringstjänst för lagring + Synkronisering av Azure-filer</li><li>Robocopy</li></ul> |
| Windows Server 2012 och äldre | <ul><li>Synkronisering av Azure-filer + DataBox</li><li>Migreringstjänst för lagring + Synkronisering av Azure-filer</li></ul> | <ul><li>Migreringstjänst för lagring + Synkronisering av Azure-filer</li><li>Robocopy</li></ul> |
| Nätverksansluten lagring (NAS) | <ul><li>[Synkronisering av Azure-filer + RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Linux / Samba | <ul><li>[RoboCopy + Azure-filsynkronisering](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| StorSimple 8100 / 8600 | <ul><li>[Virtuell Azure-filsynkronisering + 8020 virtuell installation](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple 1200 | <ul><li>[Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Verktygslåda för migrering

### <a name="file-copy-tools"></a>Verktyg för filkopiering

Det finns flera microsoft- och filkopieringsverktyg som inte kommer från Microsoft. För att välja rätt filkopieringsverktyg för migreringsscenariot måste du ta hänsyn till tre grundläggande frågor:

* Stöder kopieringsverktyget källan och målplatsen för en viss filkopia? 
    * Stöder den nätverkssökvägen och/eller tillgängliga protokoll (till exempel REST/SMB/NFS) till och från käll- och mållagringsplatserna?
* Bevarar kopieringsverktyget den nödvändiga filåtergivningen som stöds av käll-/målplatsen? I vissa fall stöder mållagringen inte samma trohet som källan. Du har redan fattat beslutet att mållagringen är tillräcklig för dina behov, därav kopieringsverktyget behöver bara matcha målen filåtergivningsfunktioner.
* Har kopieringsverktyget funktioner som gör att det passar in i min migreringsstrategi? 
    * Tänk till exempel på om det finns alternativ som gör att du kan minimera driftstopp. En bra fråga att ställa är: Kan jag köra den här kopian flera gånger på samma, av användare aktivt nås plats? Om så är fallet kan du minska antalet driftstopp avsevärt. Jämför det med en situation där du bara kan starta kopian när källan slutar ändras, för att garantera en fullständig kopia.

I följande tabell klassificeras Microsoft-verktyg och deras nuvarande lämplighet för Azure-filresurser:

| Rekommenderas | Verktyg | Stöder Azure-filresurser | Bevarar filåtergivning |
| :-: | :-- | :---- | :---- |
|![Ja, rekommenderas.](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Stöds. Azure-filresurser kan monteras som nätverksenheter. | Fullständig trohet* |
|![Ja, rekommenderas.](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Integrerat i Azure-filresurser. | Fullständig trohet* |
|![Ja, rekommenderas.](media/storage-files-migration-overview/circle-green-checkmark.png)| Migreringstjänst för lagring (SMS) | Indirekt stöds. Azure-filresurser kan monteras som nätverksenheter på en SMS-målserver. | Fullständig trohet* |
|![Rekommenderas inte fullt ut.](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure-databox | Stöds. | Kopierar inte metadata. [Kan användas i kombination med Azure File Sync](storage-sync-offline-data-transfer.md). |
|![Rekommenderas ej.](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | Stöds. | Kopierar inte metadata. |
|![Rekommenderas ej.](media/storage-files-migration-overview/circle-red-x.png)| Azure Lagringsutforskaren | Stöds. | Kopierar inte metadata. |
|![Rekommenderas ej.](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Stöds. | Kopierar inte metadata. |
|||||

*\*Fullständig återgivning: uppfyller eller överskrider Azure-fildelningsfunktioner.*

### <a name="migration-helper-tools"></a>Verktyg för migreringshjälp

I det här avsnittet visas verktyg som hjälper till att planera och utföra migreringar.

* **RoboCopy, från Microsoft Corporation**

    Ett av de mest tillämpliga kopieringsverktygen för filmigreringar kommer som en del av Microsoft Windows. På grund av de många alternativen i det här verktyget är den viktigaste [RoboCopy-dokumentationen](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) en användbar källa.

* **TreeSize, från JAM Software GmbH**

    Azure File Sync skalas främst med antalet objekt (filer och mappar) och mindre så med det totala TiB-beloppet. Verktyget kan användas för att bestämma antalet filer och mappar på Dina Windows Server-volymer. Dessutom kan den användas för att skapa ett perspektiv före en [Azure File Sync-distribution](storage-sync-files-deployment-guide.md) - men också efter, när molnnivådelning är engagerad och du vill se inte bara antalet objekt utan också där kataloger din servercache används mest.
    Det här verktyget (testad version 4.4.1) är kompatibelt med filer på molnnivå. Det kommer inte att orsaka återkallande av nivåindelade filer under sin normala drift.


## <a name="next-steps"></a>Nästa steg

1. Skapa en plan som distribution av Azure-filresurser (endast molnet eller hybrid) du strävar efter.
2. Granska listan över tillgängliga migreringsguider för att hitta den detaljerade guiden som matchar din källa och distribution av Azure-filresurser.

Det finns mer information om Azure Files-teknikerna som nämns i den här artikeln:

* [Översikt över Azure-filresurs](storage-files-introduction.md)
* [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
* [Synkronisering av Azure-filer: Molnbaserad nivåindelning](storage-sync-cloud-tiering.md)
