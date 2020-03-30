---
title: StorSimple 8000 Series Update 4 viktig information | Microsoft-dokument
description: Beskriver de nya funktionerna, problemen och lösningarna för Uppdatering 4 i StorSimple 8000 Series.
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
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: ef95ca7b9f94690b607e37fbf5d9378c2f2bcfda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254591"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>Viktig information om StorSimple 8000-serien Update 4

## <a name="overview"></a>Översikt

Följande viktig information beskriver de nya funktionerna och identifierar de kritiska öppna problemen för StorSimple 8000 Series Update 4. De innehåller också en lista över De StorSimple-programuppdateringar som ingår i den här versionen. 

Uppdatering 4 kan tillämpas på alla StorSimple-enheter som kör Release (GA) eller Uppdatering 0.1 till uppdatering 3.1. Enhetsversionen som är associerad med uppdatering 4 är 6.3.9600.17820.

Läs informationen i viktig information innan du distribuerar uppdateringen i StorSimple-lösningen.

> [!IMPORTANT]
> * Uppdatering 4 har enhetsprogramvara, USM firmware, LSI-drivrutin och firmware, disk firmware, Storport och Spaceport, säkerhet och andra OS-uppdateringar. Det tar cirka 4 timmar att installera den här uppdateringen. Uppdatering av den inbyggda programvaran för disk är en störande uppdatering och resulterar i en stilleståndstid för enheten. Vi rekommenderar att du installerar Uppdatering 4 för att hålla enheten uppdaterad. 
> * För nya versioner kanske du inte ser uppdateringar omedelbart eftersom vi gör en stegvis distribution av uppdateringarna. Vänta några dagar och sök sedan efter uppdateringar igen eftersom dessa kommer att bli tillgängliga snart.

## <a name="whats-new-in-update-4"></a>Nyheter i uppdatering 4

Följande viktiga förbättringar och buggfixar har gjorts i uppdatering 4.

* **Smartare automatiserade utrymme återvinning algoritmer** - I uppdatering 4, de automatiserade utrymme återvinning algoritmer förbättras för att justera utrymme återvinning cykler baserat på den förväntade återvunna utrymme som finns i molnet. 

* **Prestandaförbättringar för lokalt fästa volymer** – Uppdatering 4 har förbättrat prestanda för lokalt fästa volymer i scenarier med hög datainmatning (data som är jämförbara med volymstorlek).

* **Heatmap-baserad återställning** – I de tidigare versionerna, efter en haveriberedskap (DR), återställdes data från molnet baserat på åtkomstmönstren som resulterade i en långsam prestanda. 

    En ny funktion implementeras i uppdatering 4 som spårar data som ofta används för att skapa en värmekarta när enheten används före DR (De flesta använda datasegment har hög värme medan mindre använda segment har låg värme). Efter DR använder StorSimple heatmap för att automatiskt återställa och rehydrera data från molnet. 

    Alla återställningar är nu heatmap baserade återställningar. Mer information om hur du frågar och avbryter heatmap-baserade återställnings- och rehydreringsjobb finns i [Windows PowerShell för StorSimple cmdlet-referens](https://technet.microsoft.com/library/dn688168.aspx).

* **StorSimple Diagnostics tool** – I uppdatering 4 släpps ett StorSimple Diagnostics-verktyg för att möjliggöra enkel diagnos och felsökning av problem relaterade till system-, nätverks-, prestanda- och maskinvarukomponenthälsa. Det här verktyget körs via Windows PowerShell för StorSimple. Mer information finns [i felsÃ¶k med storsimplediagnostikverktyget](storsimple-8000-diagnostics.md).

* **UI-baserade StorSimple Migration tool** - Före den här versionen krävde migrering av data från 5000-7000-serien att användarna skulle köra en del av migreringsarbetsflödet med Azure PowerShell-gränssnittet. I den här versionen görs ett lättanvänt gränssnittsbaserat StorSimple-migreringsverktyg tillgängligt för support för att underlätta samma migreringsarbetsflöde. Detta verktyg skulle också möjliggöra konsolidering av återställningsgrupper. 

* **FIPS-relaterade ändringar** – Den här versionen och framåt, FIPS är aktiverat som standard på alla StorSimple 8000-serieenheter för både Microsoft Azure Government och Azure offentliga molnkonton.

* **Uppdateringsändringar** - I den här versionen har fel relaterade till uppdateringsfel åtgärdats.

* **Varning för diskfel** - En ny avisering som varnar användaren om förestående diskfel läggs till i den här versionen. Om du stöter på den här varningen kontaktar du Microsoft Support för att skicka en ersättningsdisk. Mer information finns i [maskinvaruvarningar på StorSimple-enheten](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Ersättningsändringar för styrenhet** - En cmdlet som gör att användaren kan fråga status för ersättningsprocessen för styrenheten läggs till i den här versionen. Mer information finns i [cmdleten för att fråga om ersättningsstatus för styrenheten](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Problem som åtgärdats i uppdatering 4

I följande tabell finns en sammanfattning av problem som åtgärdats i uppdatering 4.    

| Inga | Funktion | Problem | Gäller för fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- |
| 1 |Redundans |I den tidigare versionen, efter redundansen, uppstod ett problem som gällde rensning på kundens webbplats. Det här problemet åtgärdas i den här versionen. |Ja |Ja |
| 2 |Lokalt fixerade volymer |I den tidigare versionen uppstod ett problem med relaterad volymgenerering för lokalt fästa volymer som skulle resultera i fel på volymgenerering. Det här problemet orsakades av root och åtgärdades i den här versionen. |Ja |Inga |
| 3 |Supportpaket |I tidigare version fanns det problem relaterade till supportpaket som skulle resultera i ett System.OutOfMemory-undantag eller andra fel som resulterade i ett fel i skapandet av supportpaket. Dessa buggar är fasta i den här versionen. |Ja |Ja |
| 4 |Övervakning |I föregående utgåva finns ett problem som rör övervakningsdiagram för lokalt fästa volymer där förbrukningen visades i EB. Det här felet är löst i den här versionen. |Ja |Ja |
| 5 |Migrering |I tidigare versioner fanns det flera problem relaterade till tillförlitligheten i migreringen från 5000-7000-serien till 8000-serien enheter. Dessa problem har åtgärdats i den här versionen. |Ja |Ja |
| 6 |Uppdatering |I tidigare versioner, om det uppstod ett uppdateringsfel, skulle styrenheterna gå in i återställningsläge och därför kunde användaren inte fortsätta med uppdateringen och skulle behöva kontakta Microsoft Support. <br> Detta har ändrats i den här versionen. Om användaren har ett uppdateringsfel efter att båda styrenheterna har samma version (uppdatering 4) går styrenheterna inte i återställningsläge. Om användaren stöter på det här felet rekommenderar vi att de väntar lite och sedan försöker igen uppdateringen. Det nya försöket kan lyckas. Om återförsöket misslyckas bör de kontakta Microsoft Support. |Ja |Ja |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Kända problem i uppdatering 4 från tidigare versioner

Det finns inga nya kända problem i uppdatering 4. En lista över problem som överförts till uppdatering 4 från tidigare versioner finns i [viktig information om uppdatering 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Seriell-bifogade SCSI(SAS) styrenhet och uppdateringar av inbyggd programvara i uppdatering 4

Den här versionen har SAS-styrenhet och LSI-drivrutins- och firmwareuppdateringar. Mer information om hur du installerar dessa uppdateringar finns i [installera uppdatering 4](storsimple-install-update-4.md) på Din StorSimple-enhet.

## <a name="virtual-device-updates-in-update-4"></a>Uppdateringar av virtuella enheter i uppdatering 4

Den här uppdateringen kan inte tillämpas på StorSimple Cloud Appliance (kallas även den virtuella enheten). Nya virtuella enheter måste skapas. 

## <a name="next-step"></a>Nästa steg

Läs om hur du [installerar uppdatering 4](storsimple-install-update-4.md) på din StorSimple-enhet.

