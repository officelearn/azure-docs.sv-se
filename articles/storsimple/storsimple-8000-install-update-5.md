---
title: Installera uppdatering 5 på StorSimple 8000-serien | Microsoft-dokument
description: I artikeln beskrivs hur du installerar Uppdatering 5 i StorSimple 8000 Series 5 på storsimple 8000-serien.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267890"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Installera uppdatering 5 på din StorSimple-enhet

## <a name="overview"></a>Översikt

Den här självstudien förklarar hur du installerar uppdatering 5 på en StorSimple-enhet som kör en tidigare programvaruversion via Azure-portalen och använder snabbkorrigeringsmetoden. Snabbkorrigeringsmetoden används när du försöker installera uppdatering 5 på en enhet som kör versioner före uppdatering 3. Snabbkorrigeringsmetoden används också när en gateway konfigureras i ett annat nätverksgränssnitt än DATA 0 på StorSimple-enheten och du försöker uppdatera från en programvaruversion före uppdatering 1.

Uppdatering 5 omfattar enhetsprogram, Storport och Spaceport, OS-säkerhetsuppdateringar och OS-uppdateringar samt uppdateringar av inbyggd programvara för disk.  Enhetsprogramvaran, Spaceport, Storport, säkerhet och andra OS-uppdateringar är icke-störande uppdateringar. De icke-störande eller regelbundna uppdateringarna kan tillämpas via Azure-portalen eller via snabbkorrigeringsmetoden. Uppdateringar av den inbyggda programvaran för disken är störande uppdateringar och tillämpas när enheten är i underhållsläge via snabbkorrigeringsmetoden med hjälp av enhetens Windows PowerShell-gränssnitt.

> [!IMPORTANT]
> * Uppdatering 5 är en obligatorisk uppdatering och bör installeras omedelbart. Mer information finns i [Viktig information för uppdatering 5](storsimple-update5-release-notes.md).
> * En uppsättning manuella och automatiska förkontroller görs före installationen för att fastställa enhetens hälsa när det gäller maskinvarutillstånd och nätverksanslutning. Dessa förhandskontroller utförs endast om du installerar uppdateringarna från Azure-portalen.
> * Vi rekommenderar starkt att du installerar uppdateringarna med snabbkorrigeringsmetod när du uppdaterar en enhet som kör versioner före uppdatering 3. Om du stöter på några problem [loggar du en supportbiljett](storsimple-8000-contact-microsoft-support.md).
> * Vi rekommenderar att du installerar programvaran och andra regelbundna uppdateringar via Azure-portalen. Du bör bara gå till enhetens Windows PowerShell-gränssnitt (för att installera uppdateringar) om gatewaykontrollen för förhandsuppdatering misslyckas i portalen. Beroende på vilken version du uppdaterar från kan det ta 4 timmar (eller mer) att installera uppdateringarna. Uppdateringarna för underhållsläge måste installeras via enhetens Windows PowerShell-gränssnitt. Eftersom uppdateringar av underhållsläge är störande uppdateringar resulterar dessa i en nedtid för enheten.
> * Om du kör den valfria StorSimple Snapshot Manager kontrollerar du att du har uppgraderat snapshot manager-versionen till uppdatering 5 innan du uppdaterar enheten.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Installera uppdatering 5 via Azure-portalen
Gör följande för att uppdatera enheten till [uppdatering 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Microsoft hämtar ytterligare diagnostikinformation från enheten. När vårt driftteam identifierar enheter som har problem är vi därför bättre rustade att samla in information från enheten och diagnostisera problem.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Kontrollera att enheten kör **StorSimple 8000 Series Update 5 (6.3.9600.17845)**. **Det senast uppdaterade datumet** ska ändras.

Du kommer nu att se att uppdateringarna av underhållsläget är tillgängliga (det här meddelandet kan fortsätta att visas i upp till 24 timmar efter att du har installerat uppdateringarna). Stegen för att installera uppdateringen av underhållsläge beskrivs i nästa avsnitt.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Installera uppdatering 5 som en snabbkorrigering

De programvaruversioner som kan uppgraderas med snabbkorrigeringsmetoden är:

* Uppdatering 0.1, 0.2, 0.3
* Uppdatering 1, 1.1, 1.2
* Uppdatering 2, 2.1, 2.2
* Uppdatering 3, 3.1
* Uppdatering 4

> [!NOTE] 
> Den rekommenderade metoden för att installera uppdatering 5 är via Azure-portalen när du försöker uppdatera från uppdatering 3 och senare version. När du uppdaterar en enhet som kör versioner före uppdatering 3 använder du den här proceduren. Du kan också använda den här proceduren om du misslyckas med gatewaykontrollen när du försöker installera uppdateringarna via Azure-portalen. Kontrollen misslyckas när du har tilldelat en gateway till ett icke-DATA 0-nätverksgränssnitt och enheten kör en programvaruversion tidigare än uppdatering 1.

Snabbkorrigeringsmetoden omfattar följande tre steg:

1. Hämta snabbkorrigeringarna från Microsoft Update Catalog.
2. Installera och verifiera snabbkorrigeringarna i vanligt läge.
3. Installera och verifiera snabbkorrigeringen för underhållsläge.

#### <a name="download-updates-for-your-device"></a>Ladda ned uppdateringar för din enhet

Du måste hämta och installera följande snabbkorrigeringar i föreskriven ordning och de föreslagna mapparna:

| Beställa | KB | Beskrivning | Uppdateringstyp | Installationstid |Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Uppdatering av programvara<br> Ladda ner både _HcsSoftwareUpdate.exe_ och _CisMSDAgent.exe_ |Normal <br></br>Icke-störande |~ 25 minuter |Förstaorderuppställningen|

Om du uppdaterar från en enhet som kör Uppdatering 4 behöver du bara installera de kumulativa uppdateringarna för operativsystemet som andra orderuppdateringar.

| Beställa | KB | Beskrivning | Uppdateringstyp | Installationstid |Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |Paketet för kumulativa uppdateringar av os <br> Ladda ned Windows Server 2012 R2-version |Normal <br></br>Icke-störande |- |Andraorderuppbyggen|

Om du installerar från en enhet som kör uppdatering 3 eller tidigare installerar du följande utöver de kumulativa uppdateringarna.

| Beställa | KB | Beskrivning | Uppdateringstyp | Installationstid |Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI-drivrutin och uppdateringar av inbyggd programvara <br> USM firmware uppdatering (version 3.38) |Normal <br></br>Icke-störande |~ 3 timmar <br> (omfattar 2A. + 2B. + 2C.)|Andraorderuppbyggen|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Paketet för säkerhetsuppdateringar för operativsystem <br> Ladda ned Windows Server 2012 R2-version |Normal <br></br>Icke-störande |- |Andraorderuppbyggen|
| 2d. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Paketet för OS-uppdateringar <br> Ladda ned Windows Server 2012 R2-version |Normal <br></br>Icke-störande |- |Andraorderuppbyggen|


Du kan också behöva installera uppdateringar av den fasta disken ovanpå alla uppdateringar som visas i de föregående tabellerna. Du kan kontrollera om du behöver uppdateringar `Get-HcsFirmwareVersion` av den fasta disken genom att köra cmdleten. Om du kör dessa `XMGJ`firmwareversioner: `VR08` `N003`, `0107` `XGEG` `KZ50`, `F6C2`, , , , , behöver du inte installera dessa uppdateringar.

| Beställa | KB | Beskrivning | Uppdateringstyp | Installationstid | Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Inbyggd disk |Underhåll <br></br>Störande |~ 30 minuter | TredjeOrderuppbyggen |

<br></br>

> [!IMPORTANT]
> * Om uppdatering från uppdatering 4 uppdateras den totala installationstiden nästan 4 timmar.
> * Innan du använder den här proceduren för att installera uppdateringen kontrollerar du att både enhetskontrollanterna är online och att alla maskinvarukomponenter är felfria.

Utför följande steg för att hämta och installera snabbkorrigeringarna.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Nästa steg
Läs mer om [releasen Uppdatering 5](storsimple-update5-release-notes.md).

