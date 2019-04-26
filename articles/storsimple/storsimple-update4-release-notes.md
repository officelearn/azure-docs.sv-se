---
title: StorSimple 8000 Series Update 4 viktig information | Microsoft Docs
description: Beskriver de nya funktionerna, problem och lösningar för StorSimple 8000-serien uppdatering 4.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: ef95ca7b9f94690b607e37fbf5d9378c2f2bcfda
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60530977"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>Viktig information om StorSimple 8000-serien uppdatering 4

## <a name="overview"></a>Översikt

Följande viktiga information beskriver de nya funktionerna och identifiera kritiska öppna ärenden för StorSimple 8000-serien uppdatering 4. De innehåller också en lista över de StorSimple-programuppdateringar som ingår i denna version. 

Uppdatering 4 kan tillämpas på alla StorSimple-enhet som kör versionen (GA) eller uppdatering 0.1 via Update 3.1. Enhetsversion som är associerade med uppdatering 4 är 6.3.9600.17820.

Granska informationen i den viktiga informationen innan du distribuerar uppdateringen i din StorSimple-lösning.

> [!IMPORTANT]
> * Uppdatering 4 har enhetsprogrammet, USM inbyggd programvara, LSI-drivrutinen och inbyggd programvara, inbyggda programvaran för disken, Storport och Spaceport, säkerhet och andra OS-uppdateringar. Det tar cirka 4 timmar att installera uppdateringen. Uppdatering av inbyggd programvara för disk är en störande uppdatering och resulterar i ett driftstopp för din enhet. Vi rekommenderar att du tillämpar uppdatering 4 för att hålla din enhet uppdaterade. 
> * För nya versioner, kan du inte se uppdateringar direkt, eftersom vi gör en stegvis distribution av uppdateringar. Vänta några få dagar och sedan söker efter uppdateringar igen som dessa är snart tillgängligt.

## <a name="whats-new-in-update-4"></a>Vad är nytt i uppdatering 4

Följande viktiga förbättringar och felkorrigeringar har gjorts i uppdatering 4.

* **Smartare automatiserad algoritmer för frigöring av utrymme** – i uppdatering 4, den automatiserade utrymmesåtertagning algoritmer har förbättrats för att justera utrymme frigöring cykler som baseras på det förväntade återvunnet tillgängligt utrymmet i molnet. 

* **Prestandaförbättringar för lokalt fästa volymer** – uppdatering 4 har bättre prestanda hos lokalt fixerade volymer i scenarier som har hög datainmatning (data jämförbar volymens storlek).

* **Termisk karta-baserad återställning** – i tidigare versioner, följa en katastrofåterställning (DR), dina data har återställts från molnet baserat på åtkomstmönster vilket resulterar i en långsam prestanda. 

    En ny funktion har implementerats i uppdatering 4 att spårar data som ofta används för att skapa en termisk karta när enheten är i innan de DR (mest använda datasegment har hög termisk mindre segment som används har låg termisk). Efter DR använder StorSimple den termiska kartan för att automatiskt återställa och rehydrate data från molnet. 

    Alla återställningar är nu den termiska kartan baserat återställningar. Mer information om hur du frågar och avbryta jobb för den termiska kartan baserad återställning och återuppväckning går du till [Windows PowerShell för StorSimple cmdlet-referens](https://technet.microsoft.com/library/dn688168.aspx).

* **StorSimple diagnostikverktyget** – i uppdatering 4, en StorSimple diagnostikverktyget släpps för att möjliggöra enkel diagnostisering och felsökning av problem som rör system, nätverk, prestanda och maskinvara komponentens hälsostatus. Det här verktyget körs via Windows PowerShell för StorSimple. Mer information går du till [Felsök med hjälp av StorSimple diagnostikverktyget](storsimple-8000-diagnostics.md).

* **UI-baserad StorSimple Migreringsverktyget** -innan den här versionen, behövde användare att köra en del av arbetsflödet för migrering med hjälp av Azure PowerShell-gränssnittet av migrering av data från 5000 – 7000-serien. I den här versionen har görs en enkel att använda UI-baserad StorSimple Migreringsverktyget tillgänglig för stöd för att underlätta migreringen samma arbetsflöde. Det här verktyget kan också för konsolidering av recovery buckets. 

* **FIPS-relaterade ändringar** – den här versionen och senare, FIPS är aktiverat som standard på alla enheter för StorSimple 8000-serien för både Microsoft Azure Government och Azure offentliga molnet konton.

* **Uppdatera ändringarna** -i den här versionen buggar som rör uppdatera fel har åtgärdats.

* **Avisering för diskfel** – en ny avisering som varnar användare för nära förestående diskfel läggs till i den här versionen. Om du stöter på den här aviseringen kan du kontakta Microsoft Support om du vill skicka en ersättningsdisk. Mer information går du till [maskinvara aviseringar på StorSimple-enheten](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Controller ersättning ändringar** – en cmdlet som används att fråga status för controller ersättningsprocessen har lagts till i den här versionen. Mer information går du till den [cmdlet för att fråga status för ersättning av domänkontrollanten](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Problem som åtgärdas i uppdatering 4

Följande tabell innehåller en översikt över problem som har åtgärdats i uppdatering 4.    

| Nej | Funktion | Problem | Gäller för fysisk enhet | Gäller för virtuella enheten |
| --- | --- | --- | --- | --- |
| 1 |Redundans |I den tidigare versionen efter redundansen kan det uppstod ett problem relaterat till rensning som observerats på kundwebbplatsen. Det här problemet löses i den här versionen. |Ja |Ja |
| 2 |Lokalt fixerade volymer |I den föregående versionen gick det inte att skapa relaterade volymer för lokalt fästa volymer som skulle resultera i fel när skapas för volymen. Det här problemet har orsakades av rotcertifikatutfärdare och åtgärdas i den här versionen. |Ja |Nej |
| 3 |Supportpaket |Det fanns problem som rör supportpaket som skulle resultera i ett System.OutOfMemory undantag eller andra fel vilket resulterar i en Support paketet fel vid skapande av i föregående versionen. De här felen åtgärdas i den här versionen. |Ja |Ja |
| 4 |Övervakning |I föregående versionen har fästa det ett problem som rör övervakning diagram för lokalt volymer där förbrukning visades i EB. Det här felet har åtgärdats i den här versionen. |Ja |Ja |
| 5 |Migrering |Det fanns flera problem som rör tillförlitligheten för migrering från 5000 – 7000-serien 8000-serien enheter i tidigare version. De här problemen har åtgärdats i den här versionen. |Ja |Ja |
| 6 |Uppdatering |I tidigare versioner, om det var ett uppdateringsfel, styrenheterna skulle styrenheterna i återställningsläge och därför användaren kunde inte fortsätta med uppdateringen och skulle behöva kontakta Microsoft Support. <br> Det här beteendet har ändrats i den här versionen. Om användaren har ett uppdateringsfel när båda styrenheterna kör samma version (uppdatering 4), styrenheterna försätts inte i återställningsläge. Om du påträffar felet, rekommenderar vi att de vänta en stund och försök sedan uppdateringen. Åtgärden kan lyckas. Om åtgärden misslyckas, bör de kontakta Microsoft Support. |Ja |Ja |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Kända problem i uppdatering 4 från tidigare versioner

Det finns inga nya kända problem i uppdatering 4. En lista över problem som överföras till uppdatering 4 från tidigare versioner, går du till [uppdatering 3 viktig](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Seriellt ansluten SCSI (SAS) domänkontrollant och firmware-uppdateringar i uppdatering 4

Den här versionen har SAS-styrenhet och LSI-drivrutinen och firmware-uppdateringar. Mer information om hur du installerar de här uppdateringarna finns i [installera uppdatering 4](storsimple-install-update-4.md) på StorSimple-enheten.

## <a name="virtual-device-updates-in-update-4"></a>Uppdateringar av virtuell enhet i uppdatering 4

Den här uppdateringen kan inte tillämpas på StorSimple-Molninstallationen (även känt som den virtuella enheten). Nya virtuella enheter måste skapas. 

## <a name="next-step"></a>Nästa steg

Lär dig hur du [installera uppdatering 4](storsimple-install-update-4.md) på StorSimple-enheten.

