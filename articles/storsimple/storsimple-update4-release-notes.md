---
title: StorSimple 8000 Series uppdatering 4 viktig information | Microsoft Docs
description: "Beskriver nya funktioner, problem och lösningar för StorSimple 8000 Series uppdatering 4."
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
ms.openlocfilehash: ce9a6493e80d65641ef5d797d89663bfaa5cd624
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>StorSimple 8000 Series uppdatering 4 viktig information
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Översikt

Följande information beskriver de nya funktionerna och identifiera kritiska öppna problem för StorSimple 8000 Series uppdatering 4. De innehåller också en lista över StorSimple-programuppdateringar som ingår i den här versionen. 

Update 4 kan tillämpas på alla StorSimple-enhet som kör versionen (GA) eller uppdatering 0.1 via uppdatering 3.1. Den enhet som är associerade med Update 4 är 6.3.9600.17820.

Granska informationen som finns i viktig information innan du distribuerar uppdateringen i din StorSimple-lösning.

> [!IMPORTANT]
> * Uppdatering 4 har enhetsprogrammet, över inbyggd programvara, LSI drivrutiner och inbyggd programvara, disk inbyggd programvara, Storport och Spaceport, säkerhet och andra OS uppdateringar. Det tar cirka 4 timmar att installera uppdateringen. Disk firmware-uppdatering är en störande uppdatering och resulterar i ett driftstopp för din enhet. Vi rekommenderar att du installerar uppdatering 4 om du vill att enheten alltid är uppdaterade. 
> * För nya versioner, kan du inte se uppdateringar omedelbart eftersom vi gör en stegvis distribution av uppdateringar. Vänta några dagar och sedan söka efter uppdateringar igen som dessa är snart tillgängligt.

## <a name="whats-new-in-update-4"></a>Vad är nytt i uppdatering 4

Följande viktiga förbättringar och felkorrigeringar har gjorts i uppdatering 4.

* **Smartare automated algoritmer för frigöring av utrymme** – i uppdatering 4, den automatiserade utrymmesåtertagning algoritmer har förbättrats för att justera utrymme frigöring cykler som baseras på förväntade återvunnet utrymme i molnet. 

* **Prestandaförbättringar för lokalt fästa volymer** – uppdatering 4 har bättre prestanda för lokalt fästa volymer i scenarier där hög datapåfyllning (data jämförbar volymens storlek).

* **Heatmap-baserad återställning** – i tidigare versioner, efter en katastrofåterställning (DR) data har återställts från molnet baserat på åtkomstmönster som resulterar i ett långsamt. 

    En ny funktion är implementerad i uppdatering 4 som spårar data som ofta används för att skapa en heatmap när enheten är i användning innan DR (mest används datasegment har hög värme som används mindre segment har låg termiska). Efter DR använder StorSimple heatmap automatiskt återställa och rehydrate data från molnet. 

    Alla återställningar är nu heatmap baserat återställningar. Mer information om hur du frågar och Avbryt heatmap baserad återställning och rehydration går du till [Windows PowerShell för StorSimple cmdlet-referens](https://technet.microsoft.com/library/dn688168.aspx).

* **StorSimple-diagnostik** – frigörs i uppdatering 4, StorSimple-diagnostik för att möjliggöra enkelt diagnostisera och felsökning av problem relaterade till komponenthälsa system, nätverk, prestanda och maskinvara. Det här verktyget körs via Windows PowerShell för StorSimple. Mer information finns på [felsökning med StorSimple diagnostikverktyget](storsimple-8000-diagnostics.md).

* **UI-baserade StorSimple Migreringsverktyget** -före den här versionen krävs för migrering av data från 5000 7000-serien användare att köra en del av arbetsflödet för migrering med hjälp av Azure PowerShell-gränssnittet. I den här versionen är görs en lätt att använda UI-baserade StorSimple Migreringsverktyget tillgänglig för stöd för att underlätta samma arbetsflödet för migrering. Det här verktyget kan också för konsolidering av recovery buckets. 

* **FIPS-relaterade ändringar** – den här versionen och senare, FIPS är aktiverad som standard på alla enheter för StorSimple 8000-serien för både Microsoft Azure Government och Azure offentliga molntjänster konton.

* **Uppdatera ändringarna i** – i den här versionen buggar som rör uppdatera fel har åtgärdats.

* **Avisering för diskfel** -en ny avisering som varnar användare för nära förestående diskfel har lagts till i den här versionen. Om du stöter på den här aviseringen ska du kontakta Microsoft Support för att leverera en ersättningsdisken. Mer information finns på [maskinvara meddelanden på din StorSimple-enhet](storsimple-manage-alerts.md#hardware-alerts).

* **Domänkontrollanten ersättning ändringar** -en cmdlet som används att fråga status för processen för att byta domänkontrollant läggs till i den här versionen. Mer information finns i [för att fråga status för ersättning av domänkontrollanten](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Problem som åtgärdas i uppdatering 4

Följande tabell innehåller en översikt över problem som har åtgärdats i uppdatering 4.    

| Nej | Funktion | Problem | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- |
| 1 |Redundans |I den tidigare versionen efter växling vid fel, uppstod ett problem relaterat till rensning observerats hos kunden. Det här problemet löses i den här versionen. |Ja |Ja |
| 2 |Lokalt fästa volymer |I den tidigare versionen uppstod ett problem till relaterade volymer kan skapas för lokalt fästa volymer som skulle resultera i fel vid skapande av volymen. Det här problemet var roten orsakade och fast i den här versionen. |Ja |Nej |
| 3 |Support-paket |Det fanns problem relaterade till stöd för paket som skulle resultera i ett System.OutOfMemory undantag eller andra fel som leder stöd paketet skapas i föregående versionen. De här felen korrigeras i den här versionen. |Ja |Ja |
| 4 |Övervakning |I tidigare version fästa det ett problem som rör övervakning diagram för lokalt volymer där förbrukning visades i EB. Det här problemet löses i den här versionen. |Ja |Ja |
| 5 |Migrering |Det fanns flera problem relaterade till tillförlitligheten för migrering från 5000 7000-serien 8000-serien enheter i föregående versionen. Dessa problem har åtgärdats i den här versionen. |Ja |Ja |
| 6 |Uppdatering |I tidigare versioner, om det uppstod ett fel för uppdateringen, domänkontrollanterna hamnar i återställningsläge och därför användaren gick inte att fortsätta med uppdateringen måste du kontakta Microsoft Support. <br> Det här beteendet har ändrats i den här versionen. Om användaren har ett update-fel när båda domänkontrollanterna kör samma version (Update 4), flyttas inte domänkontrollanterna till återställningsläge. Om användaren påträffar felet, rekommenderar vi att de vänta lite och försök sedan uppdateringen. Det nya försöket lyckas. Om det nya försöket misslyckas, bör de kontakta Microsoft Support. |Ja |Ja |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Kända problem i uppdatering 4 från tidigare versioner

Det finns inga nya kända problem i uppdatering 4. En lista över problem som överförs till uppdatering 4 från tidigare versioner, gå till [uppdatering 3 viktig information](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Serieansluten SCSI (SAS) domänkontrollant och firmware-uppdateringar i uppdatering 4

Den här versionen har SAS-styrenhet och LSI drivrutinen och firmware-uppdateringar. Mer information om hur du installerar uppdateringarna finns [installera uppdatering 4](storsimple-install-update-4.md) på StorSimple-enheten.

## <a name="virtual-device-updates-in-update-4"></a>Uppdatering av virtuella enheter i uppdatering 4

Den här uppdateringen kan inte användas till molnet StorSimple-enhet (kallas även den virtuella enheten). Nya virtuella enheter måste skapas. 

## <a name="next-step"></a>Nästa steg

Lär dig hur du [installera uppdatering 4](storsimple-install-update-4.md) på StorSimple-enheten.

