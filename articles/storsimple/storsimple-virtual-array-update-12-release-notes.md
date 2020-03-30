---
title: Microsoft Azure StorSimple Virtual Array Update 1.2-versionsanteckningar| Microsoft-dokument
description: Beskriver kritiska öppna problem och lösningar för StorSimple Virtual Array som kör Uppdatering 1.2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66420610"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>Viktig information om StorSimple Virtual Array Update 1.2

I följande viktig information identifieras de kritiska öppna problemen och de lösta problemen för Microsoft Azure StorSimple Virtual Array-uppdateringar.

Viktig information uppdateras kontinuerligt. Allteftersom lösningar på de allvarliga problemen upptäcks, läggs de till. Innan du distribuerar den virtuella storsimple-matrisen bör du noggrant granska informationen i viktig information.

Uppdatering 1.2 motsvarar programvaruversionen **10.0.10311.0**.

> [!IMPORTANT]
> - Uppdateringarna är störande och startar om enheten. Om I/O pågår ådrar sig enheten driftstopp. Detaljerade instruktioner om paket som används för att installera den här uppdateringen finns i [Hämta uppdatering 1.2](#download-update-12).
> - Uppdatering 1.2 är endast tillgänglig för dig via Azure-portalen om din enhet kör Uppdatering 1.0 eller 1.1.

## <a name="whats-new-in-update-12"></a>Nyheter i uppdatering 1.2

Den här uppdateringen innehåller följande buggfixar:

- Förbättrad återhämtning när du bearbetar borttagna filer.
- Förbättrade hanteringsundigheter i startsökvägen för kod som leder till minskade fel i säkerhetskopior, återställning, molnläsningar och automatisk utrymmesåterklamation.

## <a name="download-update-12"></a>Ladda ner Uppdatering 1,2

Om du vill hämta den här uppdateringen går du till [Microsoft Update Catalog-servern](https://www.catalog.update.microsoft.com/Home.aspx) och hämtar KB4502035-paketet. Det här paketet innehåller följande paket:

 - **KB4493446** som innehåller kumulativa Windows-uppdateringar för 2012 R2 fram till april 2019. Mer information om vad som ingår i den här sammanslagningen finns i [april månadsvis säkerhetssamarbete](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067** som är en Fristående Microsoft Update-paketfil WindowsTH-KB3011067-x64. Den här filen används för att uppdatera enhetsprogrammet.

Ladda ner KB4502035 och följ dessa instruktioner för att [installera uppdateringen via lokalt webbgränssnitt](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Problem som åtgärdats i uppdatering 1.2

I följande tabell finns en sammanfattning av problem som åtgärdats i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |Borttagning| I de tidigare versionerna av programvaran uppstod ett problem när användningen av enheten inte ändrades ens när filer togs bort. Det här problemet åtgärdas i den här versionen. Nivåindelad kodsökväg blev mer flexibel vid bearbetning av borttagna filer.|
| 2 |Undantagshantering| I tidigare versioner av programvaran uppstod ett problem efter omstarten av systemet som potentiellt kan leda till fel i säkerhetskopior, återställning, läsning från molnet och automatisk utrymmesåterklamation. Den här versionen innehåller ändringar om hur undantagen hanterades i startsökvägen.|

## <a name="known-issues-in-update-12"></a>Kända problem i uppdatering 1.2

Inga nya problem har släppts i uppdatering 1.2. Alla utgivna problem överförs från tidigare versioner. Om du vill se sammanfattningen av kända problem som ingår i tidigare versioner går du till [Kända problem i uppdatering 1.1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Nästa steg

Ladda ner KB4502035 och [installera uppdateringen via lokalt webbgränssnitt](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Referenser

Letar du efter en äldre release anteckning? Gå till:
* [Viktig information om StorSimple Virtual Array Update 1.1](storsimple-virtual-array-update-11-release-notes.md)
* [Viktig information om StorSimple Virtual Array Update 1.0](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0.6 Viktig information](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 Viktig information](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 Viktig information](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Viktig information](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 och 0.2 Viktig information](storsimple-ova-update-01-release-notes.md)
* [Viktig information om storsimple virtuell matris allmän tillgänglighet](storsimple-ova-pp-release-notes.md)
