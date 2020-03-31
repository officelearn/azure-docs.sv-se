---
title: Alternativ för datamigrering från StorSimple 5000-7000-serieenheter
description: Ger en översikt över alternativen för att migrera data från StorSimple 5000-7000-serien.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 44a05ecb273bdf7582300c3b6a9110e2ada0994c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471830"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Alternativ för att migrera data från StorSimple 5000-7000-serien 

> [!IMPORTANT]
> Den 9 juli 2019 kommer StorSimple 5000/7000-serien att nå slutet av supportstatusen (EOS). Vi rekommenderar att StorSimple 5000/7000-seriens kunder migrerar till något av alternativen som beskrivs i dokumentet.

StorSimple 5000-7000-serien når [slutet av supporten](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) i juli 2019. De kunder som kör StorSimple 5000-7000-serien har möjlighet att uppgradera till andra Azure-hybridtjänster från första part. I den här artikeln beskrivs de Azure-hybridalternativ som är tillgängliga för att migrera data. 

## <a name="migration-options"></a>Migreringsalternativ

Kunderna som använder StorSimple 5000-7000-serien har Azure- eller tredjepartsalternativ.

### <a name="azure-options"></a>Azure-alternativ

#### <a name="upgrade-to-storsimple-8000-series"></a>Uppgradera till StorSimple 8000-serien

Uppgradera till StorSimple 8000-serien och fortsätt därmed på StorSimple-plattformen.  Den här uppgraderingsvägen kräver att kunderna ersätter sina enheter i 5 000-7000-serien med en 8000-serie. Data migreras från enheten i 5000-7000-serien med hjälp av migreringsverktyget. När migreringen har slutförts fortsätter StorSimple 8000-seriens enheter att nivådata till Azure Blob Storage. 

Mer information om hur du migrerar data med en StorSimple 8000-serie finns i [Migrera data från StorSimple 5000-7000-serien till 8000-serien](storsimple-8000-migrate-from-5000-7000.md).

#### <a name="migrate-to-azure-file-sync"></a>Migrera till Azure File Sync

Med det här helt nya migreringsalternativet kan kunder lagra organisationens filresurser i Azure-filerna. Dessa filresurser centraliseras sedan för lokal åtkomst med hjälp av Azure File Sync (AFS). AFS kan distribueras på en Windows Server-värd. Den faktiska datamigrering utförs sedan som en värdkopia eller med migreringsverktyget.

Mer information om hur du migrerar data till Azure File Sync finns i [Migrera data från StorSimple 5000-7000-serien till Azure File Sync](storsimple-5000-7000-afs-migration.md).

### <a name="third-party-options"></a>Alternativ från tredje part

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrera till Panzura Freedom NAS

StorSimple 5000-7000 kunder kan välja att migrera till Panzura Freedom NAS för att behålla sina data i Azure. Panzura Freedom-lösningen tillhandahåller en NAS-lösning som sträcker sig över datacenter, kontor, offentliga och privata moln. Lösningen möjliggör lokala, hybrida och i molnet dataarbetsflöden för NFS, SMB och mobila klienter. 

Den här migreringen stöds av Panzura och kunder kan komma igång genom att begära migreringsstöd från [Panzura-webbplatsen](https://panzura.com/storsimple-migration/).

#### <a name="migrate-to-cohesity"></a>Migrera till sammanhållning

Med Samhöm kan du migrera data från din nuvarande StorSimple 5000–7000 till Cohesity Data Platform på Azure. Cohesity Data Platform är en programvarudefinierad webbskalalösning som konsoliderar filer, säkerhetskopior, objekt och virtuella datorer till en enda molnbaserad lösning. Efter migreringen till dataplattformen kan du hantera, skydda och etablera data och appar från molnet till kärnan via en enda glasruta. Med Cohesity, börja med så få som tre noder. 

Läs mer om [migrering till Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### <a name="migrate-to-nasuni"></a>Migrera till Nasuni

Nasuni gör det enkelt för StorSimple 5000-7000-kunder att migrera och behålla sina data i Azure.  Nasuni är en ledande Azure-baserad NAS-lagringslösning som ger kunderna den prestanda och säkerhet de förväntar sig av on-prem-lösningar, med molnekonomi och skala.  Förutom högpresterande fillagring hanterar Nasuni och Azure säkerhetskopiering och DR, samtidigt som du kan dela och samarbeta om dina data runt om i världen med centraliserad fillagringshantering. 

Nasuni har erfarenheten att göra din migration enkel – kom igång idag:https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>Migrera till Talon FAST

Talon gör det enkelt för StorSimple 5000-7000-kunder att fortsätta att utnyttja de fördelar de värderade så mycket i StorSimple-plattformen (litet fotavtryck på plats som backas upp av obegränsade molnresurser) med ännu större funktion.  Med Talon FAST-lösningen kan kunderna migrera och behålla sina data i Azure, samtidigt som de nu har ett ännu mindre programvarufotavtryck på plats och lägger till fördelar som global fillåsning, globalt namnområde och samarbete med flera webbplatser.  Talon är en ledande Azure-ekosystemlösning som arbetar med globala kunder för att migrera sina lokala filserverarbetsbelastningar till ett konsoliderat Azure-baserat fotavtryck utan att kompromissa med användararbetsflödet eller användarupplevelsen.  

Läs mer om hur du utvecklas till https://www.talonstorage.com/alliances/microsoft-storsimpleett molnbefäst företag på .


## <a name="migration---frequently-asked-questions"></a>Migration - Vanliga frågor och svar

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>F. När når StorSimple 5000- och 7000-seriens enheter tjänsten? 

A. StorSimple 5000-7000-serien når [slutet av tjänsten](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) i juli 2019. Tjänstens innebär att Microsoft inte längre kommer att kunna ge stöd för både maskinvara och programvara för dessa enheter efter juli 2019. Vi rekommenderar starkt att du börjar formulera en plan för att migrera data från dina enheter nu.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>F. Vad händer med de data som jag har lagrat i Azure?  

A. Du kan fortsätta att använda data i Azure när du migrerar dem till en nyare tjänst. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>F. Vad händer med de data som jag har lagrat lokalt på min StorSimple-enhet? 

A. Data som finns på den lokala enheten kan kopieras till den nyare tjänsten enligt beskrivningen i migreringsdokumenten.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>F. Vad händer om jag vill behålla min StorSimple 5000/7000-serieapparat? 

A. Tjänsterna kan fortsätta att fungera, men Microsoft kan inte längre tillhandahålla maskinvaru- och programvarusupport. Migrering rekommenderas starkt för kontinuitet i verksamheten.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>F. Vilka alternativ är tillgängliga för att migrera data från StorSimple 5000-7000-seriens enheter? 

A. Beroende på deras scenario har StorSimple 5000-7000-serieanvändare följande migreringsalternativ. 

 - **Uppgradera till 8000-serien:** Använd det här alternativet när du vill fortsätta på StorSimple-plattformen. 
 - **Migrera till Azure File Sync**: Använd det här alternativet när du vill växla till Azure-inbyggt format. Du kan använda Azure File Sync för centraliserad hantering av filresurser. 

Du kan kontakta Microsoft Support för att diskutera migreringsalternativ som inte finns med i listan här.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>F. Stöds migrering till andra lagringslösningar?

A. Ja. Migrering till andra lagringslösningar med hjälp av värdkopia av data stöds.

### <a name="q-is-migration-supported-by-microsoft"></a>F. Stöds migreringen av Microsoft? 

A. Att migrera från 5000 eller 7000-serien är en fullt stödd åtgärd. Faktum är att Microsoft rekommenderar att du når ut till supporten innan du startar migreringen. Migreringen är för närvarande en assisterad åtgärd. Öppna [en supportbiljett](storsimple-8000-contact-microsoft-support.md)om du tänker migrera data från StorSimple 5000-7000-serien .

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>F. Vad är prismodellen för båda migreringsalternativen?

A. Kostnaden för migrering varierar beroende på vilket alternativ du väljer. Även om själva migreringen är gratis, om du bestämmer dig för att uppgradera till en StorSimple 8000-serie, kommer det att finnas kostnaden för maskinvaruenheten. 

På samma sätt kan prenumerationsavgifterna för tjänsten tillkomma när du använder Azure File Sync. I varje enskilt fall måste kunderna också betala löpande lagringskostnader. Se följande för en uppskattning: 
- [StorSimple prissättning](https://azure.microsoft.com/pricing/details/storsimple/)  
- [AFS prissättning]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>F.  Hur lång tid tar det att slutföra en migrering?

A. Tiden för att migrera data beror på mängden data och det uppgraderingsalternativ som valts. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>F. Vad är slutdatum för StorSimple 8000-serien?

A. Supportdatumet för StorSimple 8000-serien publiceras [här](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).


## <a name="next-steps"></a>Nästa steg
 - [Migrera data från en StorSimple 5000-7000-serie till en enhet i 8000-serien](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrera data från en StorSimple 5000-7000-serie till Azure File Sync](storsimple-5000-7000-afs-migration.md)
