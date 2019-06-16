---
title: Microsoft Azure StorSimple Virtual Array uppdatering 1.2 viktig information | Microsoft Docs
description: Beskriver viktiga öppna problem och lösningar för StorSimple Virtual Array som kör uppdatering 1.2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66420610"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>Viktig information om StorSimple Virtual Array uppdatering 1.2

Följande viktiga information identifiera kritiska öppna ärenden och löst problem för Microsoft Azure StorSimple Virtual Array-uppdateringar.

Den viktiga informationen uppdateras kontinuerligt. Allteftersom allvarliga problem som kräver en lösning upptäcks, läggs de till. Innan du distribuerar din StorSimple Virtual Array noga igenom uppgifterna i viktig information.

Uppdatering 1.2 motsvarar programvaruversionen **10.0.10311.0**.

> [!IMPORTANT]
> - Uppdateringarna är störande och starta om enheten. Om i/o som pågår, sker enheten. Detaljerad information om paket som används för att utföra den här uppdateringen går du till [hämta uppdatera 1.2](#download-update-12).
> - Uppdatering 1.2 är tillgängliga för dig via Azure portal endast om enheten kör version 1.0 eller 1.1.

## <a name="whats-new-in-update-12"></a>Vad är nytt i uppdatering 1.2

Den här uppdateringen innehåller följande felkorrigeringar:

- Förbättrad flexibilitet vid bearbetning av borttagna filer.
- Förbättrad hantering av undantag i koden Start sökväg som leder till minskade fel i säkerhetskopiering, återställning, moln-läsningar och automatisk frigöring av utrymme.

## <a name="download-update-12"></a>Ladda ned uppdatering 1.2

Hämta den här uppdateringen, går du till den [Microsoft Update-katalogen](https://www.catalog.update.microsoft.com/Home.aspx) server och KB4502035 paketet. Det här paketet innehåller följande paket:

 - **KB4493446** som innehåller kumulativa uppdateringar för Windows för 2012 R2 upp till April 2019. Mer information om vad som ingår i den här samlade går du till [April månatliga insamling av säkerhet](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067** som är en Microsoft Update fristående paketfil WindowsTH-KB3011067-x64. Den här filen används för att uppdatera enhetens programvara.

Hämta KB4502035 och följa instruktionerna [uppdateringen via lokalt webbgränssnitt](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Problem som åtgärdas i uppdatering 1.2

Följande tabell innehåller en översikt över problem som åtgärdas i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |Ta bort| I tidigare versioner av programvaran uppstod ett problem när användningen av enheten inte ändras även om filer har tagits bort. Det här problemet löses i den här versionen. Lagringsnivåer kodsökvägar gjordes mer robust vid bearbetning av borttagna filer.|
| 2 |Undantagshantering| I tidigare versioner av programvaran uppstod det ett problem som följer den omstart av systemet som kan leda till fel i säkerhetskopiering, återställning, läsning från molnet, och automatiserade frigöring av utrymme. Den här versionen innehåller ändringar för hur undantagen hanterades i startsökvägen.|

## <a name="known-issues-in-update-12"></a>Kända problem i uppdatering 1.2

Inga nya problem har versionen anges i uppdatering 1.2. Alla versionen anges problem överförs från tidigare versioner. Om du vill visa en sammanfattning av kända problem från tidigare versioner, går du till [kända problem i uppdatering 1.1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Nästa steg

Ladda ned KB4502035 och [uppdateringen via lokalt webbgränssnitt](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Referenser

Letar du efter en äldre dokumentet? Gå till:
* [StorSimple Virtual Array Update 1.1 viktig information](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple Virtual Array uppdatering 1.0 viktig information](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0,6 viktig information](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0,5 viktig information](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0,4 viktig information](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 viktig information](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array uppdatering 0.1 och 0,2 viktig information](storsimple-ova-update-01-release-notes.md)
* [StorSimple virtuell matris allmän tillgänglighet viktig information](storsimple-ova-pp-release-notes.md)
