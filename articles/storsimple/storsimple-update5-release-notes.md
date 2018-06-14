---
title: StorSimple 8000 Series uppdatering 5 viktig information | Microsoft Docs
description: Beskriver nya funktioner, problem och lösningar för StorSimple 8000 Series uppdatering 5.
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
ms.openlocfilehash: 672757e82bcf645b705f46a9975e09c9dc5eef92
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
ms.locfileid: "24518927"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>StorSimple 8000 Series uppdatering 5 viktig information

## <a name="overview"></a>Översikt

Följande information beskriver de nya funktionerna och identifiera kritiska öppna problem för StorSimple 8000 Series uppdatering 5. De innehåller också en lista över StorSimple-programuppdateringar som ingår i den här versionen.

Uppdatering 5 kan tillämpas på alla StorSimple-enhet som kör uppdatering 0.1 via uppdatering 4. Den enhet som är associerade med uppdatering 5 är 6.3.9600.17845.

Granska informationen som finns i viktig information innan du distribuerar uppdateringen i din StorSimple-lösning.

> [!IMPORTANT]
> * Uppdatering 5 är en obligatorisk uppdatering och måste installeras omedelbart. Mer information finns i så här [gäller uppdatering 5](storsimple-8000-install-update-5.md).
> * Uppdatering 5 har enhetsprogrammet, disk inbyggd programvara, OS-säkerhet och andra uppdateringar av OS. Det tar cirka 4 timmar att installera uppdateringen. Disk firmware-uppdatering är en störande uppdatering och resulterar i ett driftstopp för din enhet. Vi rekommenderar att du installerar uppdatering 5 om du vill att enheten alltid är uppdaterade.
> * För nya versioner, kan du inte se uppdateringar omedelbart eftersom vi gör en stegvis distribution av uppdateringar. Vänta några dagar och sedan söka efter uppdateringar som de här uppdateringarna igen är snart tillgängligt.

## <a name="whats-new-in-update-5"></a>Vad är nytt i uppdatering 5

Följande viktiga förbättringar och felkorrigeringar har gjorts i uppdatering 5.

* **Användning av Azure Active Directory (AAD) att autentisera med StorSimple Enhetshanteraren tjänsten** – från uppdatering 5 och senare, Azure Active Directory används för att autentisera med tjänsten StorSimple Enhetshanteraren. Den gamla autentiseringsmekanismen att bli inaktuell med December 2017. Alla användare måste inkludera de nya URL: er för autentisering i sina brandväggsregler. Mer information finns på [autentisering URL: er som anges i nätverkskrav för din StorSimple-enhet](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Om URL: en för autentisering inte ingår i brandväggsreglerna, ser användarna en kritisk varning som sin StorSimple-enhet inte kunde autentisera med tjänsten. Om användarna kan se den här aviseringen, behöver de ta den nya URL för autentisering. Mer information finns på [StorSimple nätverk aviseringar](storsimple-8000-manage-alerts.md#networking-alerts).

* **Ny version av StorSimple Snapshot Manager** -en ny version av StorSimple Snapshot Manager släpps med uppdatering 5 och är kompatibel med alla StorSimple-enheter som kör uppdatering 4 eller senare. Vi rekommenderar att du uppdaterar till den här versionen. Den tidigare versionen av StorSimple Snapshot Manager är för StorSimple-enheter som kör uppdatering 3 eller tidigare. [Hämta den aktuella versionen av StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) och referera till [distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problem som åtgärdas i uppdatering 5

Följande tabell innehåller en översikt över problem som har åtgärdats i uppdatering 5.

| Nej | Funktion | Problem | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- |
| 1 |Windows PowerShell-fjärrkommunikation |I den tidigare versionen, skulle en användare får ett felmeddelande när du försöker upprätta en fjärranslutning till StorSimple-enhet via Windows PowerShell för molnet. Det här problemet var roten orsakade och fast i den här versionen. |Nej |Ja |
| 2 |Bandbredd-mallar |Det uppstod ett problem med bandbredden mallar som resulterade i mindre bandbredd än enheten har konfigurerats för i tidigare version. Det här problemet är löst i den här versionen. |Ja |Ja |
| 3 |Redundans |I tidigare version, när en enhet med ett stort antal volymer har redundansväxlats till en annan enhet som kör uppdatering 4 misslyckas processen vid försök att använda access control-poster. Det här problemet löses i den här versionen. |Ja |Ja |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Kända problem i uppdatering 5 från tidigare versioner

Det finns inga nya kända problem i uppdatering 5. En lista över problem som överförs till uppdatering 5 från tidigare versioner, gå till [uppdatering 3 viktig information](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Serieansluten SCSI (SAS) domänkontrollant och firmware-uppdateringar i uppdatering 5

Den här versionen har SAS-styrenhet och LSI drivrutinen och firmware-uppdateringar. Mer information om hur du installerar uppdateringarna finns [installera uppdatering 5](storsimple-8000-install-update-5.md) på StorSimple-enheten.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>StorSimple-enhet för molnet uppdateringar i uppdatering 5

Den här uppdateringen kan inte användas till molnet StorSimple-enhet (kallas även den virtuella enheten). Nya installationer av molnet måste skapas med uppdatering 5-bild. Information om hur du skapar en StorSimple-enhet för molnet går du till [distribuera och hantera en StorSimple-enhet för molnet](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Nästa steg

Lär dig hur du [installera uppdatering 5](storsimple-8000-install-update-5.md) på StorSimple-enheten.

