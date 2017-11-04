---
title: StorSimple 8000 Series uppdatering 2.2 viktig information | Microsoft Docs
description: "Beskriver nya funktioner, problem och lösningar för StorSimple 8000 Series uppdatering 2.2."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2016
ms.author: alkohli
ms.openlocfilehash: 3793df974175a5c41b83800f73748c52c8223572
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>StorSimple 8000 Series uppdatering 2.2 viktig information
## <a name="overview"></a>Översikt
Följande information beskriver de nya funktionerna och identifiera kritiska öppna problem för StorSimple 8000 Series uppdatering 2.2. De innehåller också en lista över StorSimple-programuppdateringar som ingår i den här versionen. 

Uppdatera 2.2 kan tillämpas på alla StorSimple-enhet som kör versionen (GA) eller uppdatering 0.1 via uppdatering 2.1. Enheten-version som är associerade med uppdatering 2.2 är 6.3.9600.17708.

Granska informationen som finns i viktig information innan du distribuerar uppdateringen i din StorSimple-lösning.

> [!IMPORTANT]
> * 2.2 har endast programuppdateringar. Det tar cirka 1,5-2 timmar att installera uppdateringen. 
> * Om du kör uppdatering 2.1, rekommenderar vi att du installerar uppdateringen 2.2 så snart som möjligt.
> * För nya versioner, kan du inte se uppdateringar omedelbart eftersom vi gör en stegvis distribution av uppdateringar. Vänta några dagar och sedan söka efter uppdateringar igen som dessa är snart tillgängligt.
> 
> 

## <a name="whats-new-in-update-22"></a>Vad är nytt i uppdateringen 2.2
Följande viktiga förbättringar har gjorts i uppdateringen 2.2.

* **Automatisk optimering för frigöring av utrymme** – när data tas bort på tunt allokerade volymer, oanvända lagring block behovet av att frigöras. Den här versionen har förbättrats utrymme frigöring processen från molnet, vilket resulterar i outnyttjat utrymme blir tillgängliga snabbare jämfört med tidigare versioner.
* **Ögonblicksbild prestandaförbättringar** – uppdatering 2.2 har förbättrats tid för att behandla ett moln som en ögonblicksbild i vissa scenarier där stora volymer som används och det finns minimalt att ingen dataomsättningen. Ett scenario som vill dra nytta av den här förbättringen är Arkiv-volymer.
* **Härdning av supportpaket samla in** – det har gjorts förbättringar i hur Support paketet samlas in och överföra i den här versionen. 
* **Uppdatera förbättrad tillförlitlighet** – den här versionen har felkorrigeringar som resulterar i en förbättrad tillförlitlighet för uppdateringen.

## <a name="issues-fixed-in-update-22"></a>Problem som åtgärdas i uppdateringen 2.2
Följande tabeller innehåller en översikt över problem som har korrigerats i uppdateringar 2.2 och 2.1.    

| Nej | Funktion | Problem | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- |
| 1 |Värd-prestanda |I den tidigare versionen observerades värden på klientsidan prestandaproblem under genereringen av en lokalt Fäst volym och under konverteringen av en nivåindelad volym till en lokalt Fäst volym. De här problemen har lösts i den här versionen vilket ledde till en förbättring av prestanda värden under konvertering och skapa procedurer för volymen. |Ja |Nej |
| 2 |Lokalt fästa volymer |I sällsynta fall kan skulle systemet krascha när du skapar en lokalt Fäst volym. Det här felet har korrigerats i den här versionen. |Ja |Nej |
| 3 |Skiktning |Det fanns sporadiska kraschar när metadata för StorSimple moln installationer (8010 och 8020) nivåer till molnet. Det här problemet löses i den här versionen. |Nej |Ja |
| 4 |Skapa en ögonblicksbild |Det uppstod problem relaterade till att skapa inkrementell ögonblicksbilder i scenarier med stora volymer och minimal att inga dataomsättningen. De här problemen har lösts i den här versionen. |Ja |Ja |
| 5 |Openstack autentisering |När du använder Openstack som molntjänstleverantören kan körs användaren i ett ovanligt fel som rör autentiseringen där JSON-parsern resulterade i en krasch. Det här felet är åtgärdat i den här versionen. |Ja |Nej |
| 6 |Kopiera värden på klientsidan |Ett ovanligt fel som rör ODX tidsinställning påträffades i tidigare versioner av programvaran när du kopierar data från en volym till en annan volym. Detta leder till en domänkontrollant och växling vid fel och systemet kan potentiellt gå i återställningsläge. Det här felet är åtgärdat i den här versionen. |Ja |Nej |
| 7 |Windows Management Instrumentation (WMI) |I tidigare versioner av programvaran har flera instanser av web proxy misslyckades med undantaget ”<ManagementException> providern kunde inte läsas in”. Det här programfelet har tilldelats en WMI-minnesläcka och nu har åtgärdats. |Ja |Nej |
| 8 |Uppdatering |I vissa sällsynta fall kan i tidigare versioner av programvaran, emot användaren ”CisPowershellHcsscripterror” när du försöker skanna eller installera uppdateringar. Det här problemet löses i den här versionen. |Ja |Ja |
| 9 |Support-paket |I den här versionen har till de stöd paketet samlas in och överföra förbättringar. |Ja |Ja |

## <a name="known-issues-in-update-22"></a>Kända problem i uppdatering 2.2
Följande tabell innehåller en översikt över kända problem i den här versionen.

| Nej. | Funktion | Problem | Kommentarer / lösning | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk kvorum |I sällsynta fall om merparten av diskar i EBOD höljet av en 8600-enhet är frånkopplad ledde till Ingen disk i kvorum kommer lagringspoolen går offline. Den förblir offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår, kontakta Microsoft Support för nästa steg. |Ja |Nej |
| 2 |Felaktig styrenhets-ID |När en domänkontrollant ersättning utförs kan styrenhet 0 visas som kontrollant 1. Under controller ersättning, när avbildningen har lästs in från peer-noden kan styrenhets-ID visas först som peer-styrenhet-ID. I sällsynta fall kan problemet visas efter en omstart. |Ingen användaråtgärd krävs. Den här situationen ska lösas av sig självt när domänkontrollanten ersättningen är klar. |Ja |Nej |
| 3 |Lagringskonton |Använder Storage-tjänsten för att ta bort lagringskontot är ett scenario som inte stöds. Detta leder till en situation där användardata inte kan hämtas. | |Ja |Ja |
| 4 |Enheten växling vid fel |Flera redundans av en volymbehållare från samma källa enheten till olika målenheter stöds inte. Växling från en inaktiv enhet till flera enheter gör volymbehållarna på den första redundansväxlats enhet förlorar dataägarskap. Efter en växling, ska behållarna volymen visas eller fungera annorlunda när de visas i den klassiska Azure-portalen. | |Ja |Nej |
| 5 |Installation |Under StorSimple-kort för SharePoint-installation måste du ange en IP-adress för enheten för att slutföra installationen. | |Ja |Nej |
| 6 |Webbproxy |Om din webbproxykonfigurationen har det angivna protokollet HTTPS, enhet-till-tjänst-kommunikation kommer att påverkas och enheten ska försättas i offlineläge. Stöd för paket skapas också i process förbrukar betydande resurser på enheten. |Kontrollera att URL: en för web proxy har det angivna protokollet HTTP. Mer information finns i [Konfigurera en webbproxy för din enhet](storsimple-configure-web-proxy.md). |Ja |Nej |
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

## <a name="controller-and-firmware-updates-in-update-22"></a>Domänkontrollanten och firmware-uppdateringar i uppdateringen 2.2
Den här versionen har endast är programvarubaserad uppdateringar. Men om du uppdaterar från en tidigare version än uppdatering 2, behöver du installera drivrutinen Storport, Spaceport, och (i vissa fall) disk uppdateringar av inbyggd programvara på din enhet.

Mer information om hur du installerar drivrutiner, Storport, Spaceport och uppdateringar av inbyggd disk finns [installera uppdateringen 2.2](storsimple-install-update-21.md) på StorSimple-enheten.

## <a name="virtual-device-updates-in-update-22"></a>Virtuell enhetsuppdateringar i uppdateringen 2.2
Den här uppdateringen kan inte tillämpas på den virtuella enheten. Nya virtuella enheter måste skapas. 

## <a name="next-step"></a>Nästa steg
Lär dig hur du [installera uppdateringen 2.2](storsimple-install-update-21.md) på StorSimple-enheten.

