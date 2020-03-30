---
title: Viktig information om StorSimple 8000-serien Uppdatering 5
description: Beskriver de nya funktionerna, problemen och lösningarna för Uppdatering 5 i StorSimple 8000-serien.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 4fed65e12a166c046a0c63d7c4849f86805bbe9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275158"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Viktig information om StorSimple 8000-serien Uppdatering 5

## <a name="overview"></a>Översikt

Följande viktig information beskriver de nya funktionerna och identifierar de kritiska öppna problemen för StorSimple 8000 Series Update 5. De innehåller också en lista över De StorSimple-programuppdateringar som ingår i den här versionen.

Uppdatering 5 kan tillämpas på alla StorSimple-enheter som kör Uppdatering 0.1 till uppdatering 4. Enhetsversionen som är associerad med uppdatering 5 är 6.3.9600.17845.

Granska informationen i viktig information innan du distribuerar uppdateringen i StorSimple-lösningen.

> [!IMPORTANT]
> * Uppdatering 5 är en obligatorisk uppdatering och måste installeras omedelbart. Mer information finns i hur [du installerar uppdatering 5](storsimple-8000-install-update-5.md).
> * Uppdatering 5 har enhetsprogram, disk firmware, OS-säkerhet och andra OS-uppdateringar. Det tar cirka 4 timmar att installera den här uppdateringen. Uppdatering av den inbyggda programvaran för disk är en störande uppdatering och resulterar i en stilleståndstid för enheten. Vi rekommenderar att du installerar Uppdatering 5 för att hålla enheten uppdaterad.
> * För nya versioner kanske du inte ser uppdateringar omedelbart eftersom vi gör en stegvis distribution av uppdateringarna. Vänta några dagar och sök sedan efter uppdateringar igen eftersom dessa uppdateringar blir tillgängliga snart.

## <a name="whats-new-in-update-5"></a>Nyheter i uppdatering 5

Följande viktiga förbättringar och buggfixar har gjorts i uppdatering 5.

* **Användning av Azure Active Directory (AAD) för att autentisera med Tjänsten StorSimple Device Manager** – Från uppdatering 5 och framåt används Azure Active Directory för att autentisera med Tjänsten StorSimple Device Manager. Den gamla autentiseringsmekanismen kommer att vara inaktuell i december 2017. Alla användare måste inkludera de nya autentiserings-URL:erna i sina brandväggsregler. Mer information finns [i autentiseringsadresser som anges i nätverkskraven för StorSimple-enheten](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Om autentiserings-URL:en inte ingår i brandväggsreglerna visas en kritisk avisering om att deras StorSimple-enhet inte kunde autentisera med tjänsten. Om användarna ser den här aviseringen måste de inkludera den nya autentiserings-URL:en. Mer information finns i [StorSimples nätverksaviseringar](storsimple-8000-manage-alerts.md#networking-alerts).

* **Ny version av StorSimple Snapshot Manager** - En ny version av StorSimple Snapshot Manager släpps med uppdatering 5 och är kompatibel med alla StorSimple-enheter som kör Uppdatering 4 eller senare. Vi rekommenderar att du uppdaterar till den här versionen. Den tidigare versionen av StorSimple Snapshot Manager används för StorSimple-enheter som kör uppdatering 3 eller tidigare. [Hämta lämplig version av StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) och referera till [distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problem som åtgärdats i uppdatering 5

I följande tabell finns en sammanfattning av problem som åtgärdats i uppdatering 5.

| Inga | Funktion | Problem | Gäller för fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- |
| 1 |Windows PowerShell-omsmotning |I den tidigare versionen skulle en användare få ett felmeddelande när han försökte upprätta en fjärranslutning till StorSimple Cloud Appliance via Windows PowerShell. Det här problemet orsakades av root och åtgärdades i den här versionen. |Inga |Ja |
| 2 |Bandbreddsmallar |I tidigare version uppstod ett problem med bandbreddsmallar som resulterade i lägre bandbredd än vad enheten konfigurerades för. Det här problemet är löst i den här versionen. |Ja |Ja |
| 3 |Redundans |I tidigare versioner, när en enhet med ett stort antal volymer misslyckades över till en annan enhet som kör Uppdatering 4, skulle processen misslyckas när du försöker tillämpa åtkomstkontrollposterna. Det här problemet åtgärdas i den här versionen. |Ja |Ja |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Kända problem i uppdatering 5 från tidigare versioner

Det finns inga nya kända problem i uppdatering 5. En lista över problem som överförts till uppdatering 5 från tidigare versioner finns i [viktig information om uppdatering 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Seriell-bifogade SCSI(SAS) styrenhet och uppdateringar av inbyggd programvara i uppdatering 5

Den här versionen har SAS-styrenhet och LSI-drivrutins- och firmwareuppdateringar. Mer information om hur du installerar dessa uppdateringar finns i [installera uppdatering 5](storsimple-8000-install-update-5.md) på Din StorSimple-enhet.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>StorSimple Cloud Appliance uppdateringar i uppdatering 5

Den här uppdateringen kan inte tillämpas på StorSimple Cloud Appliance (kallas även den virtuella enheten). Nya molninstallationer måste skapas med avbildningen Uppdatering 5. Information om hur du skapar en StorSimple Cloud Appliance finns i [Distribuera och hantera en StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Nästa steg

Läs om hur du [installerar uppdatering 5](storsimple-8000-install-update-5.md) på din StorSimple-enhet.

