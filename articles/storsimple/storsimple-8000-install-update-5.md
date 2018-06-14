---
title: Installera uppdatering 5 på StorSimple 8000-serieenhet | Microsoft Docs
description: Beskriver hur du installerar StorSimple 8000 Series uppdatering 5 på enheten StorSimple 8000-serien.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: d6e17c7609fd41b8f4457edda373f6882a1a9d2b
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2018
ms.locfileid: "28108728"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Installera uppdatering 5 på din StorSimple-enhet

## <a name="overview"></a>Översikt

Den här självstudiekursen beskriver hur du installerar uppdatering 5 på en StorSimple-enhet som kör en tidigare programvaruversion via Azure portal och använder metoden snabbkorrigering. Metoden snabbkorrigeringen används när du försöker installera uppdatering 5 på en enhet som kör före uppdateringen 3 versioner. Metoden snabbkorrigeringen används också när en gateway har konfigurerats på ett nätverksgränssnitt än DATA 0 av StorSimple-enhet och du försöker uppdatera från före uppdateringen 1 programvaruversionen.

Uppdatering 5 innehåller enhetsprogrammet Storport och Spaceport, OS säkerhetsuppdateringar och OS-uppdateringar och uppdateringar av inbyggd disk.  Enhetsprogrammet, Spaceport, Storport, säkerhet och andra uppdateringar av OS uppdateras utan avbrott. Utan avbrott eller regelbundna uppdateringar kan tillämpas via Azure-portalen eller via metoden snabbkorrigering. Programvara disk störande uppdateras och tillämpas när enheten är i underhållsläge via metoden snabbkorrigeringen med hjälp av Windows PowerShell-gränssnittet för enheten.

> [!IMPORTANT]
> * Uppdatering 5 är en obligatorisk uppdatering och ska installeras omedelbart. Mer information finns i [uppdatering 5 viktig information](storsimple-update5-release-notes.md).
> * En uppsättning manuella och automatiska före kontroller är klar innan du installera fastställa hälsotillståndet för enheten vad gäller maskinvara tillstånd och nätverksanslutningen. Kontrollerna före utförs endast om du installerar uppdateringarna från Azure-portalen.
> * Vi rekommenderar starkt att du installerar uppdateringar med snabbkorrigeringen metoden när du uppdaterar en enhet som kör tidigare versioner än uppdatering 3. Om det uppstår något fel [logga ett supportärende](storsimple-8000-contact-microsoft-support.md).
> * Vi rekommenderar att du installerar programmet och andra regelbundna uppdateringar via Azure portal. Du bör bara gå till Windows PowerShell-gränssnittet för enheten (för att installera uppdateringar) om den före uppdateringen gateway inte i portalen. Beroende på vilken version som du uppdaterar från uppdateringarna kan ta 4 timmar (eller senare) att installera. Underhåll läge uppdateringar måste installeras via Windows PowerShell-gränssnittet för enheten. Eftersom Underhåll läge uppdateringar är störande uppdateringar, medföra dessa nertid för din enhet.
> * Se till att du har uppgraderat din Snapshot Manager version till uppdatering 5 innan du uppdaterar enheten om kör valfria StorSimple Snapshot Manager.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Installera uppdatering 5 via Azure portal
Utför följande steg om du vill uppdatera enheten till [uppdatera 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Microsoft hämtar ytterligare diagnostikinformation från enheten. Därför när våra driftteamet identifierar enheter som har problem, är vi bättre utrustad för att samla in information från enheten och diagnostisera problem.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Kontrollera att enheten kör **StorSimple 8000 Series uppdatering 5 (6.3.9600.17845)**. Den **senast uppdaterad datum** ska ändras.

Du ser nu att Underhåll läge uppdateringar är tillgängliga (det här meddelandet kan fortsätta att visas för upp till 24 timmar efter installation av uppdateringar). Stegen för att installera Underhåll läge uppdatering beskrivs i nästa avsnitt.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Installera uppdatering 5 som en snabbkorrigering

Programvaruversioner som kan uppgraderas med metoden snabbkorrigeringen är:

* Uppdatera 0.1, 0,2, 0,3
* Uppdatera 1, 1.1, 1.2
* Uppdatera 2, 2.1, 2.2
* Uppdatering 3, 3.1
* Uppdatering 4

> [!NOTE] 
> Den rekommenderade metoden för att installera uppdatering 5 är via Azure portal när du försöker uppdatera från Update 3 och senare. När du uppdaterar en enhet som kör tidigare versioner än uppdatering 3, Använd den här proceduren. Du kan också använda den här proceduren om du inte gateway-kontroll vid försök att installera uppdateringar via Azure-portalen. Kontrollen misslyckas när du har en gateway som tilldelats till en icke-DATA 0-nätverksgränssnittet och din enhet kör en programvaruversion tidigare än uppdatering 1.

Metoden snabbkorrigeringen omfattar följande tre steg:

1. Hämta snabbkorrigeringar från Microsoft Update-katalogen.
2. Installera och kontrollera standardläget snabbkorrigeringar.
3. Installera och kontrollera Underhåll läge snabbkorrigeringen.

#### <a name="download-updates-for-your-device"></a>Hämta uppdateringar för din enhet

Du måste hämta och installera följande snabbkorrigeringar i föreskrivna ordning och de föreslagna mapparna:

| Ordning | kB | Beskrivning | Typ av uppdatering | Installationstid |Installera i mappen|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Programuppdatering<br> Ladda ned båda _HcsSoftwareUpdate.exe_ och _CisMSDAgent.exe_ |Vanliga <br></br>Icke-störande |~ 25 minuter |FirstOrderUpdate|

Om uppdatering från en enhet som kör uppdatering 4, behöver du bara installera OS kumulativa uppdateringar som andra uppdateringar.

| Ordning | kB | Beskrivning | Typ av uppdatering | Installationstid |Installera i mappen|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS kumulativa uppdateringar paketet <br> Versionen av Windows Server 2012 R2 |Vanliga <br></br>Icke-störande |- |SecondOrderUpdate|

Om du installerar från en enhet som kör uppdatering 3 eller tidigare, kan du installera följande förutom kumulativa uppdateringar.

| Ordning | kB | Beskrivning | Typ av uppdatering | Installationstid |Installera i mappen|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI drivrutiner och uppdateringar av inbyggd programvara <br> ÖVER firmware-uppdatering (version 3.38) |Vanliga <br></br>Icke-störande |~ 3 timmar <br> (inklusive 2A. + 2B. + 2 C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Uppdateringar av OS säkerhetspaketet <br> Versionen av Windows Server 2012 R2 |Vanliga <br></br>Icke-störande |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Paketet för OS-uppdateringar <br> Versionen av Windows Server 2012 R2 |Vanliga <br></br>Icke-störande |- |SecondOrderUpdate|


Du kan också behöva installera uppdateringar av inbyggd disk ovanpå alla uppdateringar som visas i föregående tabeller. Du kan kontrollera om du behöver disk firmware-uppdateringar genom att köra den `Get-HcsFirmwareVersion` cmdlet. Om du kör dessa versioner av inbyggd: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, behöver du inte installera uppdateringarna.

| Ordning | kB | Beskrivning | Typ av uppdatering | Installationstid | Installera i mappen|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Disk inbyggd programvara |Underhåll <br></br>Störande |~ 30 minuter | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Om uppdatering från uppdatering 4, är den totala installationstid nära 4 timmar.
> * Innan du använder den här proceduren för att installera uppdateringen kan du kontrollera att både styrenheterna är online och alla maskinvarukomponenter är felfria.

Utför följande steg för att ladda ned och installera snabbkorrigeringar.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Nästa steg
Lär dig mer om den [uppdatering 5 versionen](storsimple-update5-release-notes.md).

