---
title: Installera uppdatering 5 på enhet i StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du installerar StorSimple 8000-serien uppdatering 5 på din enhet i StorSimple 8000-serien.
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
ms.openlocfilehash: d86e77ef0148c0fac3dfa31153364de153b094ef
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126757"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Installera uppdatering 5 på din StorSimple-enhet

## <a name="overview"></a>Översikt

Den här självstudien beskrivs hur du installerar uppdatering 5 på en StorSimple-enhet som kör en tidigare programvaruversion via Azure-portalen och använder metoden snabbkorrigering. Metoden snabbkorrigeringen används när du försöker installera uppdatering 5 på en enhet som kör Föruppdatering 3 versioner. Metoden snabbkorrigeringen används också när en gateway har konfigurerats i ett nätverksgränssnitt än DATA 0 av StorSimple-enhet och du försöker uppdatera från en 1 programvaruversion före uppdateringen.

Uppdatering 5 innehåller enhetsprogrammet Storport och Spaceport, OS-säkerhetsuppdateringar och OS-uppdateringar och uppdateringar av inbyggd programvara för disk.  Enhetsprogrammet, Spaceport, Storport, säkerhet och andra OS-uppdateringar är avbrottsfria uppdateringar. Utan avbrott eller regelbundna uppdateringar kan tillämpas via Azure portal eller via metoden snabbkorrigering. Disk-uppdateringar av inbyggd programvara är störande uppdateringar och tillämpas när enheten är i underhållsläge via metoden snabbkorrigeringen med hjälp av Windows PowerShell-gränssnittet på enheten.

> [!IMPORTANT]
> * Uppdatering 5 är en obligatorisk uppdatering och bör omedelbart installeras. Mer information finns i [uppdatering 5 viktig](storsimple-update5-release-notes.md).
> * En uppsättning manuella och automatiska kontroller är klar innan du installera fastställa hälsotillståndet för enheten när det gäller maskinvara tillstånd och nätverksanslutningen. Dessa kontroller utförs endast om du installerar uppdateringarna från Azure-portalen.
> * Vi rekommenderar starkt att du installerar uppdateringarna med snabbkorrigeringen metoden när du uppdaterar en enhet som kör tidigare versioner än uppdatering 3. Om du får problem, [logga ett supportärende](storsimple-8000-contact-microsoft-support.md).
> * Vi rekommenderar att du installerar programvaran och andra regelbundna uppdateringar via Azure portal. Du bör bara gå till Windows PowerShell-gränssnittet på enheten (för att installera uppdateringarna) om den före uppdateringen gateway inte i portalen. Beroende på vilken version som du uppdaterar från kan uppdateringarna kan ta 4 timmar (eller senare) att installera. Uppdateringarna i underhållsläge måste installeras via Windows PowerShell-gränssnittet på enheten. Eftersom uppdateringar av underhållsläge är störande uppdateringar, leda dessa nertid för din enhet.
> * Om kör valfritt StorSimple Snapshot Manager kan du se till att du har uppgraderat din Snapshot Manager-version till uppdatering 5 innan du uppdaterar enheten.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Installera uppdatering 5 via Azure portal
Utför följande steg om du vill uppdatera enheten till [uppdatering 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Microsoft tar emot ytterligare diagnostikinformation från enheten. Därför när våra driftsteamet identifierar enheter som har problem, är vi bättre utrustade för att samla in information från enheten och diagnostisera problem.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Kontrollera att enheten kör **StorSimple 8000-serien uppdatering 5 (6.3.9600.17845)**. Den **senast uppdaterad datum** ska ändras.

Du ser nu att uppdateringarna i underhållsläge är tillgängliga (det här meddelandet kan visas fortfarande i upp till 24 timmar efter installation av uppdateringar). Stegen för att installera uppdatering för underhållsläge beskrivs i nästa avsnitt.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Installera uppdatering 5 som en snabbkorrigering

Programvaruversioner som kan uppgraderas med snabbkorrigeringar-metoden är:

* Uppdatera 0.1, 0.2, 0.3
* Uppdatera 1, 1.1, 1.2
* Uppdatera 2, 2.1, 2.2
* Uppdatering 3, 3.1
* Uppdatering 4

> [!NOTE] 
> Den rekommenderade metoden för att installera uppdatering 5 är via Azure portal vid försök att uppdatera från Update 3 och senare. Vid uppdatering av en enhet som kör tidigare versioner än uppdatering 3 kan du använda den här proceduren. Du kan också använda den här proceduren om du inte gatewaykontroll när du försöker installera uppdateringar via Azure portal. Kontrollen misslyckas när du har en gateway som tilldelats till en icke-DATA 0-nätverksgränssnittet och din enhet kör en programvaruversion tidigare än uppdatering 1.

Metoden snabbkorrigering omfattar följande tre steg:

1. Ladda ned snabbkorrigeringar från Microsoft Update-katalogen.
2. Installera och verifiera i normalläge.
3. Installera och verifiera snabbkorrigeringen i underhållsläge.

#### <a name="download-updates-for-your-device"></a>Ladda ned uppdateringar för din enhet

Du måste hämta och installera följande snabbkorrigeringar i föreskrivna ordningen och föreslagna mappar:

| Beställa | kB | Beskrivning | Uppdateringstyp | Installationstid |Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Uppdatering av programvara<br> Ladda ned båda _HcsSoftwareUpdate.exe_ och _CisMSDAgent.exe_ |Normal <br></br>STÖRNINGSFRI |~ 25 minuter |FirstOrderUpdate|

Om du uppdaterar från en enhet som kör uppdatering 4, behöver du bara att installera de kumulativa uppdateringarna för operativsystem som andra orderuppdateringar.

| Beställa | kB | Beskrivning | Uppdateringstyp | Installationstid |Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS kumulativa uppdateringar paket <br> Hämta Windows Server 2012 R2-versionen |Normal <br></br>STÖRNINGSFRI |- |SecondOrderUpdate|

Om installation från en enhet som kör uppdatering 3 eller tidigare kan du installera följande förutom kumulativa uppdateringar.

| Beställa | kB | Beskrivning | Uppdateringstyp | Installationstid |Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI-drivrutinen och uppdateringar av inbyggd programvara <br> USM firmware-uppdatering (version 3.38) |Normal <br></br>STÖRNINGSFRI |~ 3 timmar <br> (inkluderar 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS-säkerhetspaketet uppdateringar <br> Hämta Windows Server 2012 R2-versionen |Normal <br></br>STÖRNINGSFRI |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Paket för OS-uppdateringar <br> Hämta Windows Server 2012 R2-versionen |Normal <br></br>STÖRNINGSFRI |- |SecondOrderUpdate|


Du kan också behöva installera uppdateringar av disk av inbyggd programvara på alla uppdateringar som visas i föregående tabeller. Du kan kontrollera om du behöver firmwareuppdateringar disk genom att köra den `Get-HcsFirmwareVersion` cmdlet. Om du kör dessa versioner av inbyggd programvara: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, behöver du inte installera uppdateringarna.

| Beställa | kB | Beskrivning | Uppdateringstyp | Installationstid | Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Inbyggda programvaran för disken |Underhåll <br></br>Disruptive |~ 30 minuter | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Om du uppdaterar från uppdatering 4, är den totala installationstid nära 4 timmar.
> * Innan du använder den här proceduren för att installera uppdateringen kan du kontrollera att båda styrenheterna är online och alla maskinvarukomponenter är felfria.

Utför följande steg för att ladda ned och installera snabbkorrigerarna.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Nästa steg
Läs mer om den [uppdatering 5 versionen](storsimple-update5-release-notes.md).

