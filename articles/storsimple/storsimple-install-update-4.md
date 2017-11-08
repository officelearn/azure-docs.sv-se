---
title: "Installera uppdatering 4 på din StorSimple-enhet | Microsoft Docs"
description: "Beskriver hur du installerar StorSimple 8000 Series uppdatering 4 på enheten StorSimple 8000-serien."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 8a771040c650dd05fc7b523931202a67693d4842
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="install-update-4-on-your-storsimple-device"></a>Installera uppdatering 4 på din StorSimple-enhet
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Om du vill visa versionen av den här artikeln för den nya Azure portalen, gå till [installera uppdatering 4 på StorSimple-enheten](storsimple-8000-install-update-4.md). Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Översikt

Den här självstudiekursen beskriver hur du installerar uppdatering 4 på en StorSimple-enhet som kör en tidigare programvaruversion via den klassiska Azure-portalen och använder metoden snabbkorrigering. Metoden snabbkorrigeringen används när en gateway har konfigurerats på ett nätverksgränssnitt än DATA 0 av StorSimple-enhet och du försöker uppdatera från före uppdateringen 1 programvaruversionen.

Uppdatering 4 innehåller enhetsprogrammet, över inbyggd programvara, LSI drivrutiner och inbyggd programvara, Storport och Spaceport, OS säkerhetsuppdateringar och en mängd andra OS-uppdateringar.  Enhetsprogrammet över inbyggd programvara, Spaceport, Storport och andra uppdateringar av OS uppdateras utan avbrott. Utan avbrott eller regelbundna uppdateringar kan tillämpas via den klassiska Azure-portalen eller via metoden snabbkorrigering. Programvara disk störande uppdateringar och kan endast användas via metoden snabbkorrigeringen med hjälp av Windows PowerShell-gränssnittet för enheten. 

> [!IMPORTANT]
> * En uppsättning manuella och automatiska före kontroller är klar innan du installera fastställa hälsotillståndet för enheten vad gäller maskinvara tillstånd och nätverksanslutningen. Kontrollerna före utförs endast om du installerar uppdateringarna från den klassiska Azure-portalen.
> * Vi rekommenderar att du installerar programmet och andra regelbundna uppdateringar via den klassiska Azure-portalen. Du bör bara gå till Windows PowerShell-gränssnittet för enheten (för att installera uppdateringar) om den före uppdateringen gateway inte i portalen. Beroende på vilken version som du uppdaterar från uppdateringarna kan ta 4 timmar (eller senare) att installera. Underhåll läge uppdateringar måste också installeras via Windows PowerShell-gränssnittet för enheten. Eftersom Underhåll läge uppdateringar är störande uppdateringar, kommer dessa nertid för din enhet.
> * Se till att du har uppgraderat din Snapshot Manager version till Update 4 innan du uppdaterar enheten om kör valfria StorSimple Snapshot Manager.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-classic-portal"></a>Installera uppdatering 4 via den klassiska Azure-portalen
Utför följande steg om du vill uppdatera enheten till [uppdatering 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Om du kopplar uppdatering 2 eller senare (inklusive uppdatering 2.1), kommer Microsoft att kunna dra ytterligare diagnostikinformation från enheten. Därför när våra driftteamet identifierar enheter som har problem, är vi bättre utrustad för att samla in information från enheten och diagnostisera problem. Genom att acceptera uppdatering 2 eller senare, kan vi har stöd för detta proaktiv. 

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

Kontrollera att enheten kör **StorSimple 8000 Series uppdatering 4 (6.3.9600.17820)**. Den **senast uppdaterad datum** bör också ändras. 

* Du ser nu att Underhåll läge uppdateringar är tillgängliga (det här meddelandet kan fortsätta att visas för upp till 24 timmar efter installation av uppdateringar). Underhåll läge uppdateringar är störande uppdateringar som leda till enheten driftstopp och kan endast användas via Windows PowerShell-gränssnittet på enheten.
 
* Hämta uppdateringar för underhåll-läge med hjälp av stegen i [att hämta snabbkorrigeringar](#to-download-hotfixes) att söka efter och hämta KB4011837, vilka installerar disk firmware-uppdateringar (andra uppdateringar ska vara installerad nu). Följ stegen i [installera och underhåll läge snabbkorrigeringar](#to-install-and-verify-maintenance-mode-hotfixes) för att installera underhållsläge uppdateringar. 

## <a name="install-update-4-as-a-hotfix"></a>Installera uppdatering 4 som en snabbkorrigering
Den rekommenderade metoden för att installera uppdatering 4 är via den klassiska Azure-portalen.

Använd följande procedur om du inte gateway-kontroll vid försök att installera uppdateringar via den klassiska Azure-portalen. Det går inte att kontrollen som du har en gateway som tilldelats till en icke-DATA 0-nätverksgränssnittet och din enhet med en programvaruversion före uppdatering 1.

Programvaruversioner som kan uppgraderas med metoden snabbkorrigeringen är:

* Uppdatera 0.1, 0,2, 0,3
* Uppdatera 1, 1.1, 1.2
* Uppdatera 2, 2.1, 2.2
* Uppdatering 3, 3.1 


Metoden snabbkorrigeringen omfattar följande tre steg:

1. Hämta snabbkorrigeringar från Microsoft Update-katalogen.
2. Installera och kontrollera standardläget snabbkorrigeringar.
3. Installera och kontrollera Underhåll läge snabbkorrigeringen.

#### <a name="download-updates-for-your-device"></a>Hämta uppdateringar för din enhet

Du måste hämta och installera följande snabbkorrigeringar i föreskrivna ordning och de föreslagna mapparna:

| Ordning | kB | Beskrivning | Typ av uppdatering | Installationstid |Installera i mappen|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 <br> (2-filer) |Programuppdateringen för enhet <br> TIS/MDS agentuppdatering |Vanliga <br></br>Icke-störande |~ 25 minuter |FirstOrderUpdate <br> _Installera enheten programuppdatering före uppdateringen av TIS/MDS-agent_|
| 2A. |KB4011841 <br> KB4011842 |LSI drivrutiner och uppdateringar av inbyggd programvara <br> ÖVER firmware-uppdatering (version 3.38) |Vanliga <br></br>Icke-störande |~ 3 timmar <br> (inklusive 2A. + 2B. + 2 C.)|SecondOrderUpdate|
| 2B. |KB3139398 KB3108381 <br> KB3205400 KB3142030 <br> KB3197873 KB3192392  <br> KB3153704 KB3174644 <br> KB3139914  |Uppdateringar av OS säkerhetspaketet |Vanliga <br></br>Icke-störande |- |SecondOrderUpdate|
| 2C. |KB3210083 KB3103616 <br> KB3146621 KB3121261 <br> KB3123538 |Paketet för OS-uppdateringar |Vanliga <br></br>Icke-störande |- |SecondOrderUpdate|

Du kan också behöva installera uppdateringar av inbyggd disk ovanpå alla uppdateringar som visas i föregående tabeller. Du kan kontrollera om du behöver disk firmware-uppdateringar genom att köra den `Get-HcsFirmwareVersion` cmdlet. Om du kör dessa versioner av inbyggd: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N002`, `0106`, behöver du inte installera uppdateringarna.

| Ordning | kB | Beskrivning | Typ av uppdatering | Installationstid | Installera i mappen|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4011837 |Disk inbyggd programvara |Underhåll <br></br>Störande |~ 30 minuter | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Den här proceduren behöver bara utföras en gång om du vill använda uppdatering 4. Du kan använda den klassiska Azure-portalen för att tillämpa efterföljande uppdateringar.
> * Om uppdatering från uppdatering 3 eller 3.1, är den totala installationstid nära 4 timmar.
> * Innan du använder den här proceduren för att installera uppdateringen kan du kontrollera att både styrenheterna är online och alla maskinvarukomponenter är felfria.

Utför följande steg för att ladda ned och installera snabbkorrigeringar.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Nästa steg
Lär dig mer om den [uppdatering 4 versionen](storsimple-update4-release-notes.md).

