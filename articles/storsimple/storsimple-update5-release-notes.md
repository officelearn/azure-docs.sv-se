---
title: StorSimple 8000-serien uppdatering 5 viktig information | Microsoft Docs
description: Beskriver de nya funktionerna, problem och lösningar för StorSimple 8000-serien uppdatering 5.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 462cbd6261723aa91bbfd23292611e758a800ed2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844099"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Viktig information om StorSimple 8000-serien uppdatering 5

## <a name="overview"></a>Översikt

Följande viktiga information beskriver de nya funktionerna och identifiera kritiska öppna ärenden för StorSimple 8000-serien uppdatering 5. De innehåller också en lista över de StorSimple-programuppdateringar som ingår i denna version.

Uppdatering 5 kan tillämpas på alla StorSimple-enhet som kör uppdatering 0.1 via uppdatering 4. Enhetsversion som är associerade med uppdatering 5 är 6.3.9600.17845.

Granska informationen i den viktiga informationen innan du distribuerar uppdateringen i din StorSimple-lösning.

> [!IMPORTANT]
> * Uppdatering 5 är en obligatorisk uppdatering och måste installeras omedelbart. Läs mer om hur du [gäller uppdatering 5](storsimple-8000-install-update-5.md).
> * Uppdatering 5 har enhetsprogrammet, inbyggda programvaran för disken, OS-säkerhet och andra OS-uppdateringar. Det tar cirka 4 timmar att installera uppdateringen. Uppdatering av inbyggd programvara för disk är en störande uppdatering och resulterar i ett driftstopp för din enhet. Vi rekommenderar att du tillämpar uppdatering 5 för att hålla din enhet uppdaterade.
> * För nya versioner, kan du inte se uppdateringar direkt, eftersom vi gör en stegvis distribution av uppdateringar. Vänta några få dagar och sedan söker efter uppdateringar igen som de här uppdateringarna är snart tillgängligt.

## <a name="whats-new-in-update-5"></a>Vad är nytt i uppdatering 5

Följande viktiga förbättringar och felkorrigeringar har gjorts i uppdatering 5.

* **Användning av Azure Active Directory (AAD) att autentisera med StorSimple Device Manager-tjänsten** – från uppdatering 5 och senare, Azure Active Directory används för att autentisera med StorSimple Device Manager-tjänsten. Gamla autentiseringsmekanismen upphör att gälla från och med December 2017. Alla användare måste ange de nya URL: er för autentisering på sina brandväggsregler. Mer information går du till [autentisering URL: er enligt nätverkskraven för StorSimple-enheten](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Om URL: en för autentisering inte är inkluderat i brandväggsreglerna, ser användarna en kritisk varning som deras StorSimple-enheten inte kunde autentisera med tjänsten. Om användarna ser den här aviseringen, som de behöver inkludera den nya URL: en för autentisering. Mer information går du till [StorSimple nätverk aviseringar](storsimple-8000-manage-alerts.md#networking-alerts).

* **Ny version av StorSimple Snapshot Manager** – en ny version av StorSimple Snapshot Manager släpps med uppdatering 5 och är kompatibla med alla StorSimple-enheter som kör uppdatering 4 eller senare. Vi rekommenderar att du uppdaterar till den här versionen. Den tidigare versionen av StorSimple Snapshot Manager är för StorSimple-enheter som kör uppdatering 3 eller tidigare. [Hämta den aktuella versionen av StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) och referera till [distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problem som åtgärdas i uppdatering 5

Följande tabell innehåller en översikt över problem som har åtgärdats i uppdatering 5.

| Nej | Funktion | Problem | Gäller för fysisk enhet | Gäller för virtuella enheten |
| --- | --- | --- | --- | --- |
| 1 |Windows PowerShell-fjärrkommunikation |I den tidigare versionen, skulle en användare får ett felmeddelande när du försöker upprätta en fjärranslutning till StorSimple-Molninstallationen via Windows PowerShell. Det här problemet har orsakades av rotcertifikatutfärdare och åtgärdas i den här versionen. |Nej |Ja |
| 2 |Bandbreddsmallar |Det uppstod ett problem med bandbreddsmallar som resulterade i lägre bandbredd än enheten har konfigurerats för i tidigare version. Det här problemet är löst i den här versionen. |Ja |Ja |
| 3 |Redundans |I tidigare version, när en enhet med ett stort antal volymer har redundansväxlats till en annan enhet som kör uppdatering 4 skulle processen misslyckas vid försök att tillämpa åtkomstkontrollposterna. Det här problemet löses i den här versionen. |Ja |Ja |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Kända problem i uppdatering 5 från tidigare versioner

Det finns inga nya kända problem i uppdatering 5. En lista över problem som överföras till uppdatering 5 från tidigare versioner, går du till [uppdatering 3 viktig](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Seriellt ansluten SCSI (SAS) domänkontrollant och firmware-uppdateringar i uppdatering 5

Den här versionen har SAS-styrenhet och LSI-drivrutinen och firmware-uppdateringar. Mer information om hur du installerar de här uppdateringarna finns i [installera uppdatering 5](storsimple-8000-install-update-5.md) på StorSimple-enheten.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>StorSimple Cloud Appliance uppdateringar i uppdatering 5

Den här uppdateringen kan inte tillämpas på StorSimple-Molninstallationen (även känt som den virtuella enheten). Nya molninstallationer måste skapas med uppdatering 5-avbildningen. Information om hur du skapar en StorSimple-Molninstallation går du till [distribuera och hantera en StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Nästa steg

Lär dig hur du [installera uppdatering 5](storsimple-8000-install-update-5.md) på StorSimple-enheten.

