---
title: "Installera uppdatering 3 på din StorSimple-enhet | Microsoft Docs"
description: "Beskriver hur du installerar StorSimple 8000 Series uppdatering 3 på enheten StorSimple 8000-serien."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: c6c4634d-4f3a-4bc4-b307-a22bf18664e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2b99b9cd52dd28f7f62b5d8d5ffe32339a67f82a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="install-update-3-on-your-storsimple-8000-series-device"></a>Installera uppdatering 3 på enheten StorSimple 8000-serien

> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Översikt

Den här självstudiekursen beskriver hur du installerar uppdatering 3 på en StorSimple-enhet som kör en tidigare programvaruversion via den klassiska Azure-portalen och använder metoden snabbkorrigering. Metoden snabbkorrigeringen används när en gateway har konfigurerats på ett nätverksgränssnitt än DATA 0 av StorSimple-enhet och du försöker uppdatera från före uppdateringen 1 programvaruversionen.

Uppdatering 3 innehåller enhetsprogrammet, LSI drivrutiner och inbyggd programvara, Storport och Spaceport uppdateras. Du måste också använda iSCSI, WMI, och i vissa fall disk uppdateringar av inbyggd om uppdaterar från uppdatering 2 eller en tidigare version. Den enhetsprogrammet, WMI, iSCSI, LSI drivrutin, Spaceport och Storport korrigeringar uppdateras utan avbrott. Dessa uppdateringar kan tillämpas via den klassiska Azure-portalen. Programvara disk störande uppdateringar och kan endast användas via Windows PowerShell-gränssnittet för enheten.

> [!IMPORTANT]
> * En uppsättning manuella och automatiska före kontroller är klar innan du installera fastställa hälsotillståndet för enheten vad gäller maskinvara tillstånd och nätverksanslutningen. Kontrollerna före utförs endast om du installerar uppdateringarna från den klassiska Azure-portalen.
> * Vi rekommenderar att du installerar uppdateringar för programvara eller drivrutiner via den klassiska Azure-portalen. Gå till Windows PowerShell-gränssnittet för enheten (för att installera uppdateringar) endast om den före uppdateringen gateway inte i portalen. Beroende på vilken version som du uppdaterar från kan uppdateringarna ta 1.5 2,5 timmar att installera. Underhåll läge uppdateringar måste installeras via Windows PowerShell-gränssnittet för enheten. Eftersom Underhåll läge uppdateringar är störande uppdateringar, upplevelser enheten driftstopp.
> * Se till att du har uppgraderat din Snapshot Manager version till uppdatering 2 innan du uppdaterar enheten om kör valfria StorSimple Snapshot Manager.

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-3-via-the-azure-classic-portal"></a>Installera uppdatering 3 via den klassiska Azure-portalen
Utför följande steg om du vill uppdatera enheten till [Update 3](storsimple-update3-release-notes.md).

> [!NOTE]
> Om du kopplar uppdatering 2 eller senare (inklusive uppdatering 2.1), kommer Microsoft att kunna dra ytterligare diagnostikinformation från enheten. Den här informationen hjälper till att identifiera StorSimple-enheter som har problem och aids diagnostisera problem. Genom att acceptera uppdatering 2 eller senare, kan vi har stöd för detta proaktiv.


[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

Kontrollera att enheten kör **StorSimple 8000 Series uppdatering 3 (6.3.9600.17759)**. Den **senast uppdaterad datum** ändras. 
   - Om du uppdaterar från en tidigare version än uppdatering 2, ser du att Underhåll läge uppdateringar är tillgängliga. Det här meddelandet kan fortsätta att visas för upp till 24 timmar efter installation av uppdateringar.
     Underhåll läge uppdateringar är störande uppdateringar som leda till enheten driftstopp. Dessa uppdateringar kan endast användas via Windows PowerShell-gränssnittet på enheten. I vissa fall när du kör Update 1.2, disk-firmware kanske redan är uppdaterad och du behöver inte installera de uppdateringar som Underhåll läge.
   - Om du uppdaterar från uppdatering 2 eller senare, bör enheten nu uppdaterade. Du kan hoppa över nästa steg.

Hämta uppdateringar för underhåll-läge med hjälp av stegen i [att hämta snabbkorrigeringar](#to-download-hotfixes) att söka efter och hämta KB3121899, vilka installerar disk firmware-uppdateringar (andra uppdateringar ska vara installerad nu). Följ stegen i [installera och underhåll läge snabbkorrigeringar](#to-install-and-verify-maintenance-mode-hotfixes) för att installera underhållsläge uppdateringar. 

## <a name="install-update-3-as-a-hotfix"></a>Installera uppdatering 3 som en snabbkorrigering
Använd följande procedur om du inte gateway-kontroll vid försök att installera uppdateringar via den klassiska Azure-portalen. Det går inte att kontrollen som du har en gateway som tilldelats till en icke-DATA 0-nätverksgränssnittet och din enhet med en programvaruversion före uppdatering 1.

Programvaruversioner som kan uppgraderas med metoden snabbkorrigeringen är:

* Uppdatera 0.1, 0,2, 0,3
* Uppdatera 1, 1.1, 1.2
* Uppdatera 2, 2.1, 2.2 

> [!IMPORTANT]
> * Om enheten kör versionen (GA) version, kontakta [Microsoft-supporten](storsimple-contact-microsoft-support.md) som hjälper dig med uppdateringen.
> 
> 

Metoden snabbkorrigeringen omfattar följande tre steg:

1. Hämta snabbkorrigeringar från Microsoft Update-katalogen.
2. Installera och kontrollera standardläget snabbkorrigeringar.
3. Installera och kontrollera snabbkorrigeringen Underhåll läge (endast vid uppdatering från före uppdateringen 2 programvara).

#### <a name="download-updates-for-your-device"></a>Hämta uppdateringar för din enhet
**Om enheten kör uppdatering 2.1 eller 2.2**, måste du hämta och installera följande snabbkorrigeringar i den föreskrivna ordningen:

| Ordning | kB | Beskrivning | Typ av uppdatering | Installationstid |
| --- | --- | --- | --- | --- |
| 1. |KB3186843 |Programuppdaterings &#42; |Vanliga <br></br>Icke-störande |~ 45 minuter |
| 2. |KB3186859 |LSI drivrutiner och inbyggd programvara |Vanliga <br></br>Icke-störande |~ 20 minuter |
| 3. |KB3121261 |Storport och Spaceport korrigering </br> Windows Server 2012 R2 |Vanliga <br></br>Icke-störande |~ 20 minuter |

&#42;  *Observera att programuppdateringen består av två binärfiler: enheten programuppdateringen inleds med `all-hcsmdssoftwareupdate` och Cis och Mds-agenten som inleds med `all-cismdsagentupdatebundle`. Programuppdateringen enheten måste vara installerad före Cis och Mds-agenten. Du måste också starta om den aktiva styrenheten via den `Restart-HcsController` cmdlet när du har installerat agenten Cis och Mds uppdatera (och innan de återstående uppdateringar).* 

**Om enheten kör uppdatering 0.1, 0,2, 0.3, 1.0, 1.1, 1.2 eller 2.0**, du måste hämta och installera följande snabbkorrigeringar förutom programvara, LSI drivrutinen och firmware-uppdateringar (visas i tabellen ovan), i den föreskrivna ordningen:

| Ordning | kB | Beskrivning | Typ av uppdatering | Installationstid |
| --- | --- | --- | --- | --- |
| 4. |KB3146621 |iSCSI-paket |Vanliga <br></br>Icke-störande |~ 20 minuter |
| 5. |KB3103616 |WMI-paket |Vanliga <br></br>Icke-störande |~ 12 minuter |

<br></br>

**Om enheten körs versioner 0,2, 0.3, 1.0, 1.1 och 1.2**, du kan också behöva installera uppdateringar av inbyggd disk ovanpå alla uppdateringar som visas i föregående tabeller. Du kan kontrollera om du behöver disk firmware-uppdateringar genom att köra den `Get-HcsFirmwareVersion` cmdlet. Om du kör dessa versioner av inbyggd: `XMGG`, `XGEG`, `KZ50`, `F6C2`, `VR08`, behöver du inte installera uppdateringarna.

| Ordning | kB | Beskrivning | Typ av uppdatering | Installationstid |
| --- | --- | --- | --- | --- |
| 6. |KB3121899 |Disk inbyggd programvara |Underhåll <br></br>Störande |~ 30 minuter |

<br></br>

> [!IMPORTANT]
> * Den här proceduren behöver bara utföras en gång om du vill använda uppdatering 3. Du kan använda den klassiska Azure-portalen för att tillämpa efterföljande uppdateringar.
> * Om uppdatering från uppdateringen 2.2 är den totala installationstid nära 1.1 timmar.
> * Innan du använder den här proceduren för att installera uppdateringen kan du kontrollera att både styrenheterna är online och alla maskinvarukomponenter är felfria.
> 
> 

Utför följande steg för att ladda ned och installera snabbkorrigeringar.

[!INCLUDE [storsimple-install-update3-hotfix](../../includes/storsimple-install-update3-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Nästa steg
Lär dig mer om den [uppdatering 3 versionen](storsimple-update3-release-notes.md).

