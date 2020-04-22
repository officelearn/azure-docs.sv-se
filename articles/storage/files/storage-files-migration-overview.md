---
title: Migrera till Azure-filresurser
description: Lär dig mer om migreringar till Azure-filresurser och hitta din migreringsguide.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d6141d48d67dd44c348961c6e09acf4e2531a61e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685994"
---
# <a name="migrate-to-azure-file-shares"></a>Migrera till Azure-filresurser

Den här artikeln beskriver de grundläggande aspekterna av en migrering till Azure-filresurser.

Den här artikeln innehåller grunderna för migrering och en tabell med migreringsguider. Dessa guider hjälper dig att flytta dina filer till Azure-filresurser. Guiderna är ordnade baserat på var dina data finns och vilken distributionsmodell (endast molnet eller hybrid) du flyttar till.

## <a name="migration-basics"></a>Grunderna i migrering

Azure har flera tillgängliga typer av molnlagring. En grundläggande aspekt av filmigreringar till Azure är att avgöra vilket Azure-lagringsalternativ som är rätt för dina data.

[Azure-filresurser](storage-files-introduction.md) är lämpliga för fildata för allmänt ändamål. Dessa data innehåller allt du använder en lokal SMB- eller NFS-resurs för. Med [Azure File Sync](storage-sync-files-planning.md)kan du cachelagra innehållet i flera Azure-filresurser på servrar som kör Windows Server lokalt.

För en app som för närvarande körs på en lokal server kan det vara ett bra val att lagra filer i en Azure-filresurs. Du kan flytta appen till Azure och använda Azure-filresurser som delad lagring. Du kan också överväga [Azure-diskar](../../virtual-machines/windows/managed-disks-overview.md) för det här scenariot.

Vissa molnappar är inte beroende av SMB eller åtkomst till datorlokala data eller delad åtkomst. För dessa appar är objektlagring som [Azure-blobbar](../blobs/storage-blobs-overview.md) ofta det bästa valet.

Nyckeln i en migrering är att fånga alla tillämpliga filåtergivning när du flyttar dina filer från sin nuvarande lagringsplats till Azure. Hur mycket återgivning azure-lagringsalternativet stöder och hur mycket ditt scenario kräver hjälper dig också att välja rätt Azure-lagring. Fildata för allmänna ändamål beror traditionellt på filmetadata. Appdata kanske inte gör det.

Här är de två grundläggande komponenterna i en fil:

- **Dataström:** Dataströmmen för en fil lagrar filinnehållet.
- **Filmetadata**: Filmetadata har dessa underkomponenter:
   * Filattribut som skrivskyddade
   * Filbehörigheter, som kan kallas *NTFS-behörigheter* eller *ACL:er* för fil och mapp
   * Tidsstämplar, framför allt skapande och senast modifierade tidsstämplar
   * En alternativ dataström, som är ett utrymme för att lagra större mängder icke-standardiserade egenskaper

Filåtergivning i en migrering kan definieras som möjligheten att:

- Lagra all tillämplig filinformation på källan.
- Överför filer med migreringsverktyget.
- Lagra filer i mållagringen för migreringen.

För att säkerställa att migrering fortsätter smidigt identifierar du [det bästa kopieringsverktyget för dina behov](#migration-toolbox) och matchar ett lagringsmål med källan.

Med hänsyn till den tidigare informationen kan du se att mållagringen för filer med allmänt syfte i Azure är [Azure-filresurser](storage-files-introduction.md).

Till skillnad från objektlagring i Azure-blobbar kan en Azure-filresurs lagra filmetadata inbyggt. Azure-filresurser bevarar också fil- och mapphierarkin, attributen och behörigheterna. NTFS-behörigheter kan lagras i filer och mappar eftersom de är lokala.

En användare av Active Directory, som är deras lokala domänkontrollant, kan komma åt en Azure-filresurs. Det kan även en användare av Azure Active Directory Domain Services (Azure AD DS). Var och en använder sin aktuella identitet för att få åtkomst baserat på resursbehörigheter och på fil- och mapp-ACL:er. Det här problemet liknar en användare som ansluter till en lokal filresurs.

Den alternativa dataströmmen är den primära aspekten av filåtergivning som för närvarande inte kan lagras på en fil i en Azure-filresurs. Den bevaras lokalt när Azure File Sync används.

Läs mer om [Azure AD-autentisering](storage-files-identity-auth-active-directory-enable.md) och [Azure AD DS-autentisering](storage-files-identity-auth-active-directory-domain-service-enable.md) för Azure-filresurser.

## <a name="migration-guides"></a>Migreringsguider

I följande tabell visas detaljerade migreringsguider.

Så här använder du tabellen:

1. Leta reda på raden för källsystemet som filerna för närvarande lagras på.

1. Välj ett av följande mål:

   - En hybriddistribution med Azure File Sync för att cachelagra innehållet i Azure-filresurser lokalt
   - Azure-filresurser i molnet

   Välj den målkolumn som matchar ditt val.

1. I skärningspunkten mellan källa och mål visar en tabellcell tillgängliga migreringsscenarier. Välj en om du vill länka direkt till den detaljerade migreringsguiden.

Ett scenario utan en länk har ännu inte en publicerad migreringsguide. Kontrollera den här tabellen ibland för uppdateringar. Nya guider publiceras när de är tillgängliga.

| Källa | Mål: </br>Hybriddistribution | Mål: </br>Distribution endast för molnet |
|:---|:--|:--|
| | Verktygskombination:| Verktygskombination: |
| Windows Server 2012 R2 och senare | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Azure File Sync och Azure Data Box](storage-sync-offline-data-transfer.md)</li><li>Tjänsten För synkronisering och lagring i Azure-fil</li></ul> | <ul><li>Azure File Sync</li><li>Synkronisering av Azure-filer och dataruta</li><li>Tjänsten För synkronisering och lagring i Azure-fil</li><li>Robocopy</li></ul> |
| Windows Server 2012 och tidigare | <ul><li>Synkronisering av Azure-filer och dataruta</li><li>Tjänsten För synkronisering och lagring i Azure-fil</li></ul> | <ul><li>Tjänsten För synkronisering och lagring i Azure-fil</li><li>Robocopy</li></ul> |
| Nätverksansluten lagring (NAS) | <ul><li>[Synkronisering av Azure-filer och RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Linux eller Samba | <ul><li>[Synkronisering av Azure-filer och RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Microsoft Azure StorSimple Cloud Appliance 8100 eller StorSimple Cloud Appliance 8600 | <ul><li>[Azure File Sync och StorSimple Cloud Appliance 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Verktygslåda för migrering

### <a name="file-copy-tools"></a>Verktyg för filkopiering

Det finns flera filkopieringsverktyg tillgängliga från Microsoft och andra. Om du vill välja rätt verktyg för migreringsscenariot måste du tänka på följande grundläggande frågor:

* Stöder verktyget käll- och målplatserna för filkopian?

* Stöder verktyget nätverkssökvägen eller tillgängliga protokoll (till exempel REST, SMB eller NFS) mellan käll- och mållagringsplatserna?

* Bevarar verktyget den nödvändiga filåtergivningen som stöds av käll- och målplatserna?

    I vissa fall stöder mållagringen inte samma trohet som källan. Om mållagringen är tillräcklig för dina behov måste verktyget bara matcha målets filåtergivningsfunktioner.

* Har verktyget funktioner som låter det passa in i din migreringsstrategi?

    Tänk till exempel på om verktyget gör att du kan minimera stilleståndstiden.
    
    När ett verktyg stöder ett alternativ för att spegla en källa till ett mål kan du ofta köra den flera gånger på samma källa och mål medan källan förblir tillgänglig.

    Första gången du kör verktyget kopieras huvuddelen av data. Den här inledande körningen kan pågå ett tag. Det varar ofta längre än du vill för att ta datakällan offline för dina affärsprocesser.

    Genom att spegla en källa till ett mål (som med **robocopy /MIR)** kan du köra verktyget igen på samma källa och mål. Körningen är mycket snabbare eftersom den behöver bara transportera källändringar som inträffar efter föregående körning. Om du kör ett kopieringsverktyg på det här sättet kan du minska stilleståndstiden avsevärt.

I följande tabell klassificeras Microsoft-verktyg och deras nuvarande lämplighet för Azure-filresurser:

| Rekommenderas | Verktyg | Stöd för Azure-filresurser | Bevarande av filåtergivning |
| :-: | :-- | :---- | :---- |
|![Ja, rekommenderas](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Stöds. Azure-filresurser kan monteras som nätverksenheter. | Full trohet.* |
|![Ja, rekommenderas](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Integrerat i Azure-filresurser. | Full trohet.* |
|![Ja, rekommenderas](media/storage-files-migration-overview/circle-green-checkmark.png)| Tjänsten Lagringsmigrering | Indirekt stöds. Azure-filresurser kan monteras som nätverksenheter på SMS-målservrar. | Full trohet.* |
|![Rekommenderas inte fullt ut](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Data Box | Stöds. | Kopierar inte metadata. [Data Box kan användas med Azure File Sync](storage-sync-offline-data-transfer.md). |
|![Rekommenderas inte](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | Stöds. | Kopierar inte metadata. |
|![Rekommenderas inte](media/storage-files-migration-overview/circle-red-x.png)| Azure Lagringsutforskaren | Stöds. | Kopierar inte metadata. |
|![Rekommenderas inte](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Stöds. | Kopierar inte metadata. |
|||||

*\*Fullständig återgivning: uppfyller eller överskrider Azure-fildelningsfunktioner.*

### <a name="migration-helper-tools"></a>Verktyg för migreringshjälp

I det här avsnittet beskrivs verktyg som hjälper dig att planera och köra migreringar.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy från Microsoft Corporation

RoboCopy är ett av de verktyg som är mest tillämpliga på filmigreringar. Det kommer som en del av Windows. Den huvudsakliga [RoboCopy dokumentationen](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) är en användbar resurs för det här verktygets många alternativ.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize från JAM Software GmbH

Azure File Sync skalas främst med antalet objekt (filer och mappar) och inte med det totala lagringsbeloppet. Med TreeSize-verktyget kan du bestämma antalet objekt på Windows Server-volymerna.

Du kan använda verktyget för att skapa ett perspektiv innan en [Azure File Sync-distribution](storage-sync-files-deployment-guide.md). Du kan också använda den när molnnivådelning aktiveras efter distribution. I det scenariot ser du antalet objekt och vilka kataloger som använder servercachen mest.

Den testade versionen av verktyget är version 4.4.1. Den är kompatibel med filer på molnnivå. Verktyget kommer inte att orsaka återkallande av nivåindelade filer under normal drift.

## <a name="next-steps"></a>Nästa steg

1. Skapa en plan för vilken distribution av Azure-filresurser (endast molnet eller hybrid) du vill ha.
1. Granska listan över tillgängliga migreringsguider för att hitta den detaljerade guiden som matchar din källa och distribution av Azure-filresurser.

Här finns mer information om Azure Files-teknikerna som nämns i den här artikeln:

* [Översikt över Azure-filresurs](storage-files-introduction.md)
* [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
* [Synkronisering av Azure-filer: Molnbaserad nivåindelning](storage-sync-cloud-tiering.md)
