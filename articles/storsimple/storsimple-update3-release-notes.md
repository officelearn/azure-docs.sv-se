---
title: StorSimple 8000 Series uppdatering 3 viktig information | Microsoft Docs
description: "Beskriver nya funktioner, problem och lösningar för StorSimple 8000 Series uppdatering 3."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c22e3859fad55a8632d62aaf5f656081d6d662bd
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Uppdatera 3 viktig information för enheten StorSimple 8000-serien
> [!NOTE]
> Den klassiska portalen för StorSimple är inaktuell. Dina StorSimple-enhetshanterare flyttas automatiskt till nya Azure Portal enligt utfasningsschemat. Du kommer att få ett e-postmeddelande och ett portalmeddelande om flytten. Det här dokumentet kommer också att dras tillbaka snart. Om du har frågor om flytten kan du läsa [Vanliga frågor och svar: Flytta till Azure Portal](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Översikt
Följande information beskriver de nya funktionerna och identifiera kritiska öppna problem för StorSimple 8000 Series uppdatering 3. De innehåller också en lista över StorSimple-programuppdateringar som ingår i den här versionen. 

Uppdatering 3 kan tillämpas på alla StorSimple-enhet som kör versionen (GA) eller uppdatering 0.1 via uppdatering 2.2. Den enhet som är associerade med uppdatering 3 är 6.3.9600.17759.

Granska informationen som finns i viktig information innan du distribuerar uppdateringen i din StorSimple-lösning.

> [!IMPORTANT]
> * Update 3 har enhetsprogrammet, LSI drivrutiner och inbyggd programvara och Storport och Spaceport uppdateras. Det tar cirka 1,5-2 timmar att installera uppdateringen. 
> * För nya versioner, kan du inte se uppdateringar omedelbart eftersom vi gör en stegvis distribution av uppdateringar. Vänta några dagar och sedan söka efter uppdateringar igen som dessa är snart tillgängligt.
> 
> 

## <a name="whats-new-in-update-3"></a>Vad är nytt i uppdatering 3
Följande viktiga förbättringar och felkorrigeringar har gjorts i uppdatering 3.

* **Automatisk frigöring adressutrymmesändringarna** – starta uppdatering 3 utrymme återvinning av algoritmer som körs på vänteläge styrenheten för systemet som resulterar i snabbare körning. Mer information om vilka portar som krävs för att arbeta med utrymmesåtertagning avser den [StorSimple nätverkskrav](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Prestandaförbättringar** – uppdatering 3 har bättre prestanda för Läs-och skrivbehörighet till molnet.
* **Förbättringar av migreringsrelaterade** – i den här versionen flera programfel korrigeringar och förbättringar som gjordes för funktionen för migrering från 5000/7000-serien enheter till enheter i 8000-serien. Mer information om hur du använder funktionen migreringen går du till [migrering från enheten 5000/7000-serien 8000-serien enheten](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Övervaka relaterade korrigeringar** – i den här versionen buggar som rör övervakning diagram, service-instrumentpanelen, och enheten instrumentpanelen har lösts.

## <a name="issues-fixed-in-update-3"></a>Problem som åtgärdas i uppdatering 3
Följande tabeller innehåller en översikt över problem som har åtgärdats i uppdatering 3.    

| Nej | Funktion | Problem | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- |
| 1 |Värden på klientsidan datamigrering |I den tidigare versionen StorSimple moln installation försätts i offlineläge under en värd på klientsidan datamigreringen. Det här problemet löses i den här versionen. |Nej |Ja |
| 2 |Lokalt fästa volymer |Det fanns problem relaterade till i/o-fel, volym Konverteringsfel och datapath fel för lokalt fästa volymer i den tidigare versionen. De här problemen var roten orsakade och fast i den här versionen. |Ja |Nej |
| 3 |Övervakning |Det fanns flera problem som rör rapportering enheter och övervaka samt enheten instrumentpanelen diagram där felaktig information visades för lokalt fästa volymer. De här problemen har lösts i den här versionen. |Ja |Nej |
| 4 |Tunga skrivningar i/o |När du använder StorSimple för arbetsbelastningar som involverar tunga skrivningar körs användaren i ett ovanligt fel där arbetsminne har som nivåer i molnet. Det här felet är åtgärdat i den här versionen. |Ja |Ja |
| 5 |Backup |I vissa sällsynta fall kan i tidigare versioner av programvaran, när användaren har gjort en säkerhetskopia av en fjärransluten klon klickar de skulle stöter på Molnfel och åtgärden skulle fel ut. I den här versionen är problemet är löst och åtgärden slutförs. |Ja |Ja |
| 6 |Princip för säkerhetskopiering |I vissa sällsynta fall i tidigare versioner av programvaran, det uppstod ett fel som rör borttagningen av princip för säkerhetskopiering. Det här problemet löses i den här versionen. |Ja |Ja |

## <a name="known-issues-in-update-3"></a>Kända problem i uppdatering 3
Följande tabell innehåller en översikt över kända problem i den här versionen.

| Nej. | Funktion | Problem | Kommentarer / lösning | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk kvorum |I sällsynta fall om merparten av diskar i EBOD höljet av en 8600-enhet är frånkopplad ledde till Ingen disk i kvorum kommer lagringspoolen går offline. Den förblir offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår, kontakta Microsoft Support för nästa steg. |Ja |Nej |
| 2 |Felaktig styrenhets-ID |När en domänkontrollant ersättning utförs kan styrenhet 0 visas som kontrollant 1. Under controller ersättning, när avbildningen har lästs in från peer-noden kan styrenhets-ID visas först som peer-styrenhet-ID. I sällsynta fall kan problemet visas efter en omstart. |Ingen användaråtgärd krävs. Den här situationen ska lösas av sig självt när domänkontrollanten ersättningen är klar. |Ja |Nej |
| 3 |Lagringskonton |Använder Storage-tjänsten för att ta bort lagringskontot är ett scenario som inte stöds. Detta leder till en situation där användardata inte kan hämtas. | |Ja |Ja |
| 4 |Enheten växling vid fel |Flera redundans av en volymbehållare från samma källa enheten till olika målenheter stöds inte. Växling från en inaktiv enhet till flera enheter gör volymbehållarna på den första redundansväxlats enhet förlorar dataägarskap. Efter en växling, ska behållarna volymen visas eller fungera annorlunda när de visas i den klassiska Azure-portalen. | |Ja |Nej |
| 5 |Installation |Under StorSimple-kort för SharePoint-installation måste du ange en IP-adress för enheten för att slutföra installationen. | |Ja |Nej |
| 6 |Webbproxy |Om din webbproxykonfigurationen har det angivna protokollet HTTPS, enhet-till-tjänst-kommunikation kommer att påverkas och enheten ska försättas i offlineläge. Stöd för paket skapas också i process förbrukar betydande resurser på enheten. |Kontrollera att URL: en för web proxy har det angivna protokollet HTTP. Mer information finns i [Konfigurera en webbproxy för din enhet](storsimple-8000-configure-web-proxy.md). |Ja |Nej |
| 7 |Webbproxy |Om du konfigurerar och aktiverar webbproxy på en registrerad enhet, måste du starta om den aktiva styrenheten på enheten. | |Ja |Nej |
| 8 |Hög molnet latens och hög i/o-arbetsbelastning |När din StorSimple-enhet påträffar en kombination av mycket hög molnet latens (ordning sekunder) och höga i/o-arbetsbelastning, enheten volymer som ingår i ett degraderat tillstånd och I/o kan misslyckas med felet ”enheten är inte klar”. |Du måste starta om styrenheterna eller utför en växling vid fel med enheten för att återställa från den här situationen manuellt. |Ja |Nej |
| 9 |Azure PowerShell |När du använder cmdleten StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - först 1 - vänta** du väljer det första objektet så att du kan skapa en ny **VolumeContainer** objekt, returnerar cmdleten alla objekt. |Omsluta cmdlet inom parentes på följande sätt: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - först 1 - vänta** |Ja |Ja |
| 10 |Migrering |När flera volymbehållare skickas för migrering är ETA för senaste säkerhetskopian exakt endast för den första volymbehållaren. Dessutom startas parallella migrering efter de första 4 säkerhetskopiorna i första volymbehållare migreras. |Vi rekommenderar att du migrerar en volymbehållare i taget. |Ja |Nej |
| 11 |Migrering |Efter återställningen läggs inte volymer till principen för säkerhetskopiering eller gruppen virtuell disk. |Du behöver lägga till dessa volymer till en princip för säkerhetskopiering för att skapa säkerhetskopior. |Ja |Ja |
| 12 |Migrering |När migreringen är klar, måste enheten 5000/7000-serien inte komma åt behållarna migrerade data. |Vi rekommenderar att du tar bort migrerade databehållare när migreringen har slutförts och bekräftats. |Ja |Nej |
| 13 |Klona och Katastrofåterställning |En StorSimple-enhet som kör uppdatering 1 kan inte klona eller utföra en återställning till en enhet med före uppdateringen 1 programvara. |Du behöver uppdatera målenheten uppdatering 1 för att tillåta dessa åtgärder |Ja |Ja |
| 14 |Migrering |Konfigurationssäkerhetskopia för migrering kan misslyckas på en serieenhet för 5000 7000-när volymen grupper med inga associerade volymer. |Ta bort alla tomma volym grupper med inga associerade volymer och försök sedan säkerhetskopian av konfigurationen. |Ja |Nej |
| 15 |Azure PowerShell-cmdlets och lokalt fästa volymer |Du kan inte skapa en lokalt Fäst volym via Azure PowerShell-cmdlets. (Alla volymer som du skapar via Azure PowerShell kommer att skikt.) |Använd alltid StorSimple Manager-tjänsten för att konfigurera lokalt fästa volymer. |Ja |Nej |
| 16 |Diskutrymme för lokalt fästa volymer |Om du tar bort en lokalt Fäst volym kan det tillgängliga utrymmet för nya volymer inte uppdateras omedelbart. StorSimple Manager-tjänsten uppdaterar lokalt tillgängligt utrymme cirka varje timme. |Vänta tills en timme innan du försöker skapa den nya volymen. |Ja |Nej |
| 17 |Lokalt fästa volymer |Din återställningsjobbet exponerar tillfälliga ögonblicksbilden säkerhetskopieringen i katalogen för säkerhetskopiering, men bara för återställningsjobbet varaktighet. Dessutom det visar att en virtuell diskgrupp med prefixet **tmpCollection** på den **Säkerhetskopieringsprinciper** sidan, men endast för återställningsjobbet varaktighet. |Detta kan inträffa om din Återställningsjobbet har bara lokalt Fäst volymer eller en blandning av lokalt Fäst och nivåindelade volymer. Om återställningsjobbet innehåller endast nivåindelade volymer, sker inte det här beteendet. Inga användaråtgärder krävs. |Ja |Nej |
| 18 |Lokalt fästa volymer |Om du avbryter en återställningsjobbet och det uppstår redundans controller omedelbart efteråt återställningsjobbet visas **misslyckades** i stället för **avbruten**. Om en återställningsjobbet misslyckas och det uppstår redundans controller omedelbart efteråt återställningsjobbet visas **avbruten** i stället för **misslyckades**. |Detta kan inträffa om din Återställningsjobbet har bara lokalt Fäst volymer eller en blandning av lokalt Fäst och nivåindelade volymer. Om återställningsjobbet innehåller endast nivåindelade volymer, sker inte det här beteendet. Inga användaråtgärder krävs. |Ja |Nej |
| 19 |Lokalt fästa volymer |Om du avbryter en återställningsjobbet eller om en återställning misslyckas och sedan en domänkontrollant växling vid fel, en ytterligare återställningsjobbet visas på den **jobb** sidan. |Detta kan inträffa om din Återställningsjobbet har bara lokalt Fäst volymer eller en blandning av lokalt Fäst och nivåindelade volymer. Om återställningsjobbet innehåller endast nivåindelade volymer, sker inte det här beteendet. Inga användaråtgärder krävs. |Ja |Nej |
| 20 |Lokalt fästa volymer |Om du försöker konvertera en nivåindelad volym (skapats och klonade med Update 1.2 eller tidigare) till en lokalt Fäst volym och enheten få slut på utrymme eller om ett avbrott i molnet, kan sedan clone(s) vara skadad. |Det här problemet uppstår bara med volymer som har skapats och klonade med före uppdateringen 2.1 programvara. Detta bör vara ett ovanligt scenario. | | |
| 21 |Volymkonvertering |Uppdatera inte ACRs kopplade till en volym när en Volymkonvertering pågår (nivåer att lokalt Fäst eller vice versa). Uppdaterar ACRs kan resultera i skadade data. |Om det behövs, uppdatera ACRs innan konverteringen volym och gör inte ytterligare uppdateringar ACR medan konverteringen pågår. | | |
| 22 |Uppdateringar |När du använder uppdatering 3 den **Underhåll** sida i den klassiska Azure portalen visas följande meddelande rör uppdatering 2 – ”StorSimple 8000-serien uppdatering 2 innehåller möjligheten för Microsoft att samla in logginformation från din enhet när vi identifiera potentiella problem”. Detta vilseledande eftersom det anger att enheten ska uppdateras till uppdatering 2. När enheten är succeesfully uppdateras till uppdatering 3, försvinner meddelandet. |Problemet korrigeras i en framtida version. |Ja |Nej |

## <a name="controller-and-firmware-updates-in-update-3"></a>Domänkontrollanten och firmware-uppdateringar i uppdatering 3
Den här versionen har LSI drivrutinen och firmware-uppdateringar. Mer information om hur du installerar LSI drivrutinen och uppdateringar av inbyggd finns [installera uppdatering 3](storsimple-install-update-3.md) på StorSimple-enheten.

## <a name="virtual-device-updates-in-update-3"></a>Uppdatering av virtuella enheter i uppdatering 3
Den här uppdateringen kan inte användas till molnet StorSimple-enhet (kallas även den virtuella enheten). Nya virtuella enheter måste skapas. 

## <a name="next-step"></a>Nästa steg
Lär dig hur du [installera uppdatering 3](storsimple-install-update-3.md) på StorSimple-enheten.

