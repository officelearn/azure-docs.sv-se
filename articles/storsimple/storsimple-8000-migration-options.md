---
title: Utvärdera alternativ för att migrera data från StorSimple 5000-7000-serien | Microsoft Docs
description: Översikt över alternativ för att migrera data från StorSimple 5000-7000-serien.
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
ms.date: 10/30/2018
ms.author: alkohli
ms.openlocfilehash: 5166e154e1a67cea777933b6bf8757661a9c3ad5
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242967"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Alternativ för att migrera data från StorSimple 5000-7000-serien 

> [!IMPORTANT]
> Den 31 juli 2019 når StorSimple 5000/7000-serien slutet av supportstatus (EOS). Vi rekommenderar att StorSimple 5000/7000-kunder som migrerar till en av de alternativ som beskrivs i dokumentet.

StorSimple 5000-7000-serien når [supporten](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) i juli 2019. Kunder som använder StorSimple 5000-7000-serien har ett alternativ för att uppgradera till andra Azure part först hybridtjänster. Den här artikeln beskrivs Azure hybrid-alternativen att migrera data. 

## <a name="migration-options"></a>Migreringsalternativ

De kunder som använder StorSimple 5000-7000-serien har du följande alternativ:

- **Alternativ för Azure**:

    - **Uppgradera till StorSimple 8000-serien** – uppgradera till StorSimple 8000-serien och således att fortsätta på StorSimple-plattformen.  Den här uppgraderingsvägen kräver kunder för att ersätta sina serieenheter för 5000 – 7000-med 8000-serien. Data migreras från 5000 – 7000-serieenhet med hjälp av migreringsverktyget. När migreringen är slutförd fortsätter StorSimple 8000-serieenheter att Datacenter till Azure Blob Storage. 

    Mer information om hur du migrerar data med hjälp av en StorSimple 8000-serien, går du till [migrera data från StorSimple 5000-7000-serien 8000-serien enheten](storsimple-8000-migrate-from-5000-7000.md).

    - **Migrera till Azure File Sync** – den här helt nya migreringsalternativ ger kunder möjlighet att lagra sin organisations filresurser i Azure-filer. Dessa filresurser är sedan centraliserade för lokal åtkomst med hjälp av Azure File Sync (AFS). AFS kan distribueras på en Windows Server-värd. Faktiska datamigreringen utförs sedan som en värd kopia eller med hjälp av migreringsverktyget.

    Mer information om hur du migrerar data till Azure File Sync går du till [migrera data från StorSimple 5000-7000-serien till Azure File Sync](https://aka.ms/StorSimpleMigrationAFS).

- **Alternativ för tredjeparts**:

    - **Migrera till Panzura frihet NAS** -StorSimple 5000-7000-kunder kan välja att migrera till Panzura frihet NAS att behålla sina data i Azure. Panzura frihet-lösningen innehåller en NAS-lösning som sträcker sig över datacenter, kontor, offentliga och privata moln. Den här lösningen kan lokala, hybrid och data i molnet arbetsflöden för NFS, SMB och mobila klienter. Den här migreringen stöds av Panzura och kunder kan komma igång genom att begära Migreringsstöd för från den [Panzura webbplats](https://panzura.com/storsimple-migration/).

## <a name="migration---frequently-asked-questions"></a>Migrering – vanliga frågor och svar

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>F. När StorSimple 5000- och 7000-serieenheter når slutet av tjänsten? 

A. StorSimple 5000-7000-serien nå [slutet av tjänsten](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) i juli 2019. Slutet av tjänsten innebär att Microsoft inte längre att kunna ge stöd för både maskin- och programvara för dessa enheter efter juli 2019. Vi rekommenderar starkt att du börjar utforma en plan för att migrera data från dina enheter nu.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>F. Vad händer med de data som jag har lagras i Azure?  

A. Du kan fortsätta att använda data i Azure när du migrerar till en nyare tjänst. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>F. Vad händer med de data som jag har lagras lokalt på min StorSimple-enhet? 

A. De data som finns på den lokala enheten kan kopieras till den nyare servicen enligt beskrivningen i dokument för migrering.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>F. Vad händer om jag vill behålla Mina StorSimple 5000/7000-serien installation? 

A. Tjänsterna kan fortsätta att fungera, kommer Microsoft inte längre att tillhandahålla stöd för maskinvara och programvara. Migrering rekommenderas för affärskontinuitet.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>F. Vilka alternativ är tillgängliga för att migrera data från StorSimple 5000-7000-serieenheter? 

A. Beroende på deras scenario har StorSimple 5000-7000-serien användare följande migreringsalternativ. 

 - **Uppgradera till 8000-serien**: Använd det här alternativet när du vill fortsätta på StorSimple-plattformen. 
 - **Migrera till Azure File Sync**: Använd det här alternativet när du vill växla till Azure ursprungligt format. Du kan använda Azure File Sync för central hantering av filresurser. 

Du kan kontakta Microsoft Support om du vill diskutera migreringsalternativ som inte visas här.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>F. Är migrering till andra lagringslösningar som stöds?

A. Ja. Migrering till andra lagringslösningar med värden kopia av informationen som stöds.

### <a name="q-is-migration-supported-by-microsoft"></a>F. Migrering som stöds av Microsoft? 

A. Migrera från 5000 och 7000-serien finns fullständigt stöd för. Microsoft rekommenderar att du i själva verket kontaktar supporten innan du påbörjar migreringen. Migreringen är för närvarande en assisterad åtgärd. Om du planerar att migrera data från StorSimple 5000-7000-serieenhet, [öppna ett supportärende](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>F. Vad är prismodellen för båda migreringsalternativen?

A. Migrering varierar beroende på vilket alternativ som väljs. Medan migreringen själva är kostnadsfri, om du vill uppgradera till en StorSimple 8000-serien kommer det att kostnaden för maskinvarans. 

Prenumerationsavgiften för tjänsten kan på liknande sätt kan tillkomma när du använder Azure File Sync. I båda fallen kan måste kunder även betala pågående lagringskostnader. Se en uppskattning följande: 
- [Priser för StorSimple](https://azure.microsoft.com/pricing/details/storsimple/)  
- [AFS priser]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>F.  Hur lång tid tar det för att slutföra en migrering?

A. Dags att migrera data beror på mängden data och uppgraderingsalternativet om du har valt. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>F. Vad är Support upphör datum för StorSimple 8000-serien?

A. I slutet av Support för StorSimple 8000-serien är publiceringsdatum [här](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).


## <a name="next-steps"></a>Nästa steg
 - [Migrera data från en StorSimple 5000-7000-serien till en enhet för 8000-serien](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrera data från en StorSimple 5000-7000-serien till Azure File Sync](storsimple-5000-7000-afs-migration.md)
