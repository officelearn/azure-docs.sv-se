---
title: Installera uppdatering 4 på enhet i StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du installerar StorSimple 8000 Series Update 4 på din enhet i StorSimple 8000-serien.
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
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: 5b48cbd1020cfd51fe989a9be33197f2735f21f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860516"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Installera uppdatering 4 på din StorSimple-enhet

## <a name="overview"></a>Översikt

Den här självstudien beskrivs hur du installerar uppdatering 4 på en StorSimple-enhet som kör en tidigare programvaruversion via Azure-portalen och använder metoden snabbkorrigering. Metoden snabbkorrigeringen används när en gateway har konfigurerats i ett nätverksgränssnitt än DATA 0 av StorSimple-enhet och du försöker uppdatera från en 1 programvaruversion före uppdateringen.

Uppdatering 4 innehåller enhetsprogrammet, USM inbyggd programvara, LSI-drivrutinen och inbyggd programvara, Storport och Spaceport, OS säkerhetsuppdateringar och en mängd andra OS-uppdateringar.  Enhetsprogrammet, USM inbyggd programvara, Spaceport, Storport och andra OS-uppdateringar är avbrottsfria uppdateringar. Utan avbrott eller regelbundna uppdateringar kan tillämpas via Azure portal eller via metoden snabbkorrigering. Disk-uppdateringar av inbyggd programvara är störande uppdateringar och kan endast tillämpas via metoden snabbkorrigeringen med hjälp av Windows PowerShell-gränssnittet på enheten.

> [!IMPORTANT]
> * En uppsättning manuella och automatiska kontroller är klar innan du installera fastställa hälsotillståndet för enheten när det gäller maskinvara tillstånd och nätverksanslutningen. Dessa kontroller utförs endast om du installerar uppdateringarna från Azure-portalen.
> * Vi rekommenderar att du installerar programvaran och andra regelbundna uppdateringar via Azure portal. Du bör bara gå till Windows PowerShell-gränssnittet på enheten (för att installera uppdateringarna) om den före uppdateringen gateway inte i portalen. Beroende på vilken version som du uppdaterar från kan uppdateringarna kan ta 4 timmar (eller senare) att installera. Uppdateringarna i underhållsläge måste också installeras via Windows PowerShell-gränssnittet på enheten. Eftersom uppdateringar av underhållsläge är störande uppdateringar, leder dessa nertid för din enhet.
> * Om kör valfritt StorSimple Snapshot Manager kan du se till att du har uppgraderat din Snapshot Manager-version till uppdatering 4 innan du uppdaterar enheten.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Installera uppdatering 4 via Azure portal
Utför följande steg om du vill uppdatera enheten till [uppdatering 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Microsoft tar emot ytterligare diagnostikinformation från enheten. Därför när våra driftsteamet identifierar enheter som har problem, är vi bättre utrustade för att samla in information från enheten och diagnostisera problem. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Kontrollera att enheten kör **StorSimple 8000-serien uppdatering 4 (6.3.9600.17820)**. Den **senast uppdaterad datum** bör också ändras.

* Du ser nu att uppdateringarna i underhållsläge är tillgängliga (det här meddelandet kan visas fortfarande i upp till 24 timmar efter installation av uppdateringar). Uppdateringar av underhållsläge är störande uppdateringar som resulterar i driftstopp för enheten och kan endast tillämpas via Windows PowerShell-gränssnittet på enheten.

* Hämta uppdateringarna i underhållsläge med hjälp av stegen i [ladda ned snabbkorrigerar](#to-download-hotfixes) att söka efter och ladda ned KB4011837, vilka installerar disk uppdateringar av inbyggd programvara (nu de andra uppdateringarna installeras redan). Följ stegen i [installera och verifiera snabbkorrigeringar i underhållsläge](#to-install-and-verify-maintenance-mode-hotfixes) att installera underhållsläge uppdateringar.

## <a name="install-update-4-as-a-hotfix"></a>Installera uppdatering 4 som en snabbkorrigering
Den rekommenderade metoden för att installera uppdatering 4 är via Azure portal.

Använd den här proceduren om du inte gatewaykontroll när du försöker installera uppdateringar via Azure portal. Det går inte att kontrollen som du har en gateway som tilldelats till en icke-DATA 0-nätverksgränssnittet och din enhet kör en programvaruversion före uppdatering 1.

Programvaruversioner som kan uppgraderas med snabbkorrigeringar-metoden är:

* Uppdatera 0.1, 0.2, 0.3
* Uppdatera 1, 1.1, 1.2
* Uppdatera 2, 2.1, 2.2
* Uppdatering 3, 3.1


Metoden snabbkorrigering omfattar följande tre steg:

1. Ladda ned snabbkorrigeringar från Microsoft Update-katalogen.
2. Installera och verifiera i normalläge.
3. Installera och verifiera snabbkorrigeringen i underhållsläge.

#### <a name="download-updates-for-your-device"></a>Ladda ned uppdateringar för din enhet

Du måste hämta och installera följande snabbkorrigeringar i föreskrivna ordningen och föreslagna mappar:

| Beställa | kB | Beskrivning | Uppdateringstyp | Installationstid |Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Uppdatering av programvara |Normal <br></br>STÖRNINGSFRI |~ 25 minuter |FirstOrderUpdate|
| 2A. |KB4011841 <br> KB4011842 |LSI-drivrutinen och uppdateringar av inbyggd programvara <br> USM firmware-uppdatering (version 3.38) |Normal <br></br>STÖRNINGSFRI |~ 3 timmar <br> (inkluderar 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |OS-säkerhetspaketet uppdateringar <br> Ladda ned Windows Server 2012 R2 |Normal <br></br>STÖRNINGSFRI |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Paket för OS-uppdateringar <br> Ladda ned Windows Server 2012 R2 |Normal <br></br>STÖRNINGSFRI |- |SecondOrderUpdate|

Du kan också behöva installera uppdateringar av disk av inbyggd programvara på alla uppdateringar som visas i föregående tabeller. Du kan kontrollera om du behöver firmwareuppdateringar disk genom att köra den `Get-HcsFirmwareVersion` cmdlet. Om du kör dessa versioner av inbyggd programvara: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N002`, `0106`, behöver du inte installera uppdateringarna.

| Beställa | kB | Beskrivning | Uppdateringstyp | Installationstid | Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Inbyggda programvaran för disken |Underhåll <br></br>Disruptive |~ 30 minuter | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Den här proceduren behöver bara utföras en gång för att tillämpa uppdatering 4. Du kan använda Azure-portalen för att tillämpa efterföljande uppdateringar.
> * Om du uppdaterar från uppdatering 3 eller 3.1, är den totala installationstid nära 4 timmar.
> * Innan du använder den här proceduren för att installera uppdateringen kan du kontrollera att båda styrenheterna är online och alla maskinvarukomponenter är felfria.

Utför följande steg för att ladda ned och installera snabbkorrigerarna.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Nästa steg
Läs mer om den [uppdatering 4 versionen](storsimple-update4-release-notes.md).

