---
title: Installera uppdatering 4 på StorSimple 8000-serien | Microsoft-dokument
description: I artikeln beskrivs hur du installerar Uppdatering 4 i StorSimple 8000 Series 4 på storsimple 8000-serien.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60860516"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Installera uppdatering 4 på din StorSimple-enhet

## <a name="overview"></a>Översikt

Den här självstudien förklarar hur du installerar uppdatering 4 på en StorSimple-enhet som kör en tidigare programvaruversion via Azure-portalen och använder snabbkorrigeringsmetoden. Snabbkorrigeringsmetoden används när en gateway konfigureras i ett annat nätverksgränssnitt än DATA 0 på StorSimple-enheten och du försöker uppdatera från en programvaruversion före uppdatering 1.

Uppdatering 4 omfattar enhetsprogramvara, USM firmware, LSI-drivrutin och inbyggd programvara, Storport och Spaceport, OS-säkerhetsuppdateringar och en mängd andra OS-uppdateringar.  Enhetsprogramvaran, USM-programvaran, Spaceport, Storport och andra OS-uppdateringar är icke-störande uppdateringar. De icke-störande eller regelbundna uppdateringarna kan tillämpas via Azure-portalen eller via snabbkorrigeringsmetoden. Uppdateringar av den inbyggda programvaran för disken är störande uppdateringar och kan endast tillämpas via snabbkorrigeringsmetoden med hjälp av enhetens Windows PowerShell-gränssnitt.

> [!IMPORTANT]
> * En uppsättning manuella och automatiska förkontroller görs före installationen för att fastställa enhetens hälsa när det gäller maskinvarutillstånd och nätverksanslutning. Dessa förhandskontroller utförs endast om du installerar uppdateringarna från Azure-portalen.
> * Vi rekommenderar att du installerar programvaran och andra regelbundna uppdateringar via Azure-portalen. Du bör bara gå till enhetens Windows PowerShell-gränssnitt (för att installera uppdateringar) om gatewaykontrollen för förhandsuppdatering misslyckas i portalen. Beroende på vilken version du uppdaterar från kan det ta 4 timmar (eller mer) att installera uppdateringarna. Uppdateringarna för underhållsläge måste också installeras via enhetens Windows PowerShell-gränssnitt. Eftersom uppdateringar av underhållsläge är störande uppdateringar resulterar dessa i en nedtid för enheten.
> * Om du kör den valfria StorSimple Snapshot Manager kontrollerar du att du har uppgraderat snapshot manager-versionen till uppdatering 4 innan du uppdaterar enheten.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Installera uppdatering 4 via Azure-portalen
Gör följande för att uppdatera enheten till [uppdatering 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Microsoft hämtar ytterligare diagnostikinformation från enheten. När vårt driftteam identifierar enheter som har problem är vi därför bättre rustade att samla in information från enheten och diagnostisera problem. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Kontrollera att enheten kör **StorSimple 8000 Series Update 4 (6.3.9600.17820)**. Det **senast uppdaterade datumet** bör också ändras.

* Du kommer nu att se att uppdateringarna av underhållsläget är tillgängliga (det här meddelandet kan fortsätta att visas i upp till 24 timmar efter att du har installerat uppdateringarna). Uppdateringar av underhållsläge är störande uppdateringar som leder till driftstopp för enheten och kan endast tillämpas via enhetens Windows PowerShell-gränssnitt.

* Hämta uppdateringarna för underhållsläge med hjälp av stegen i [för att hämta snabbkorrigeringar](#to-download-hotfixes) för att söka efter och hämta KB4011837, som installerar uppdateringar av diskinbyggda programvara (de andra uppdateringarna bör redan installeras nu). Följ stegen i [snabbkorrigeringarna för installation och kontroll av underhållsläge](#to-install-and-verify-maintenance-mode-hotfixes) för att installera uppdateringar av underhållsläge.

## <a name="install-update-4-as-a-hotfix"></a>Installera uppdatering 4 som en snabbkorrigering
Den rekommenderade metoden för att installera uppdatering 4 är via Azure-portalen.

Använd den här proceduren om du misslyckas med gatewaykontrollen när du försöker installera uppdateringarna via Azure-portalen. Kontrollen misslyckas eftersom du har en gateway tilldelad ett icke-DATA 0-nätverksgränssnitt och enheten kör en programvaruversion före uppdatering 1.

De programvaruversioner som kan uppgraderas med snabbkorrigeringsmetoden är:

* Uppdatering 0.1, 0.2, 0.3
* Uppdatering 1, 1.1, 1.2
* Uppdatering 2, 2.1, 2.2
* Uppdatering 3, 3.1


Snabbkorrigeringsmetoden omfattar följande tre steg:

1. Hämta snabbkorrigeringarna från Microsoft Update Catalog.
2. Installera och verifiera snabbkorrigeringarna i vanligt läge.
3. Installera och verifiera snabbkorrigeringen för underhållsläge.

#### <a name="download-updates-for-your-device"></a>Ladda ned uppdateringar för din enhet

Du måste hämta och installera följande snabbkorrigeringar i föreskriven ordning och de föreslagna mapparna:

| Beställa | KB | Beskrivning | Uppdateringstyp | Installationstid |Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Uppdatering av programvara |Normal <br></br>Icke-störande |~ 25 minuter |Förstaorderuppställningen|
| 2A. |KB4011841 <br> KB4011842 |LSI-drivrutin och uppdateringar av inbyggd programvara <br> USM firmware uppdatering (version 3.38) |Normal <br></br>Icke-störande |~ 3 timmar <br> (omfattar 2A. + 2B. + 2C.)|Andraorderuppbyggen|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Paketet för säkerhetsuppdateringar för operativsystem <br> Ladda ned Windows Server 2012 R2 |Normal <br></br>Icke-störande |- |Andraorderuppbyggen|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Paketet för OS-uppdateringar <br> Ladda ned Windows Server 2012 R2 |Normal <br></br>Icke-störande |- |Andraorderuppbyggen|

Du kan också behöva installera uppdateringar av den fasta disken ovanpå alla uppdateringar som visas i de föregående tabellerna. Du kan kontrollera om du behöver uppdateringar `Get-HcsFirmwareVersion` av den fasta disken genom att köra cmdleten. Om du kör dessa `XMGJ`firmwareversioner: `VR08` `N002`, `0106` `XGEG` `KZ50`, `F6C2`, , , , , behöver du inte installera dessa uppdateringar.

| Beställa | KB | Beskrivning | Uppdateringstyp | Installationstid | Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Inbyggd disk |Underhåll <br></br>Störande |~ 30 minuter | TredjeOrderuppbyggen |

<br></br>

> [!IMPORTANT]
> * Den här proceduren behöver endast utföras en gång för att installera uppdatering 4. Du kan använda Azure-portalen för att tillämpa efterföljande uppdateringar.
> * Om uppdatering från uppdatering 3 eller 3.1 uppdateras är den totala installationstiden nära 4 timmar.
> * Innan du använder den här proceduren för att installera uppdateringen kontrollerar du att både enhetskontrollanterna är online och att alla maskinvarukomponenter är felfria.

Utför följande steg för att hämta och installera snabbkorrigeringarna.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Nästa steg
Läs mer om [releasen Uppdatering 4](storsimple-update4-release-notes.md).

