---
title: StorSimple 8000-serien uppdatering 2.2 viktig information | Microsoft Docs
description: Beskriver de nya funktionerna, problem och lösningar för StorSimple 8000-serien uppdatering 2.2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 12d11cddf077d4d07732490255d44e89ddaf3217
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527123"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>Viktig information om StorSimple 8000-serien uppdatering 2.2

## <a name="overview"></a>Översikt
Följande viktiga information beskriver de nya funktionerna och identifiera kritiska öppna ärenden för StorSimple 8000-serien uppdatering 2.2. De innehåller också en lista över de StorSimple-programuppdateringar som ingår i denna version.

Uppdatering 2.2 kan tillämpas på alla StorSimple-enhet som kör versionen (GA) eller uppdatering 0.1 via uppdatering 2.1. Enhetsversion som är associerade med uppdatering 2.2 är 6.3.9600.17708.

Granska informationen i den viktiga informationen innan du distribuerar uppdateringen i din StorSimple-lösning.

> [!IMPORTANT]
> * Uppdatering 2.2 har endast programuppdateringar. Det tar cirka 1,5-2 timmar att installera uppdateringen. 
> * Om du kör uppdatering 2.1, rekommenderar vi att du tillämpar uppdatering 2.2 så snart som möjligt.
> * För nya versioner, kan du inte se uppdateringar direkt, eftersom vi gör en stegvis distribution av uppdateringar. Vänta några få dagar och sedan söker efter uppdateringar igen som dessa är snart tillgängligt.
> 
> 

## <a name="whats-new-in-update-22"></a>Vad är nytt i uppdatering 2.2
Följande viktiga förbättringar har gjorts i uppdatering 2.2.

* **Automatiserad optimering för frigöring av utrymme** – när data raderas på tunt allokerade volymer, oanvända storage block behovet av att vara frigöras. Den här versionen har förbättrat utrymme frigöring processen från molnet, vilket resulterar i det oanvända utrymmet som blir tillgängliga snabbare jämfört med tidigare versioner.
* **Ta en ögonblicksbild prestandaförbättringar** – uppdatering 2.2 har förbättrat tid att bearbeta en av ögonblicksbild av molndata i vissa scenarier där det är stora volymer som används och det finns minimalt att inga dataomsättning. Ett scenario som skulle ha nytta av den här förbättringen är arkivvolymer.
* **Härdning av supportpaket samla in** – det har förbättringar i hur supportpaketet är samlas in och laddades upp i den här versionen. 
* **Uppdatera förbättrad tillförlitlighet** – den här versionen har felkorrigeringar som resulterar i en förbättrad tillförlitlighet för uppdateringen.

## <a name="issues-fixed-in-update-22"></a>Problem som åtgärdas i uppdatering 2.2
Följande tabeller innehåller en översikt över problem som har korrigerats i uppdateringar 2.2 och 2.1.    

| Nej | Funktion | Problem | Gäller för fysisk enhet | Gäller för virtuella enheten |
| --- | --- | --- | --- | --- |
| 1 |Värdprestanda |I den tidigare versionen observerades värden sida prestandaproblem när du skapar en lokalt Fäst volym och under konverteringen av en nivåindelad volym till en lokalt Fäst volym. De här problemen åtgärdas i den här versionen därmed vilket resulterar i en förbättring av värdprestanda under volym skapandet och konvertering procedurer. |Ja |Nej |
| 2 |Lokalt fixerade volymer |I sällsynta fall kan skulle systemet krascha när du skapar en lokalt Fäst volym. Det här felet har åtgärdats i den här versionen. |Ja |Nej |
| 3 |lagringsnivåer |Det fanns sporadiska krascher när metadata för StorSimple Cloud Appliances (8010 och 8020) nivåer till molnet. Det här problemet löses i den här versionen. |Nej |Ja |
| 4 |Skapa en ögonblicksbild |Det uppstod problem relaterade till skapandet av inkrementella ögonblicksbilder i scenarier med stora volymer och minimal att inga dataomsättning. De här problemen åtgärdas i den här versionen. |Ja |Ja |
| 5 |Openstack-autentisering |När du använder Openstack som molntjänstleverantören kan skulle användaren stöter på ett ovanligt fel som rör autentiseringen där JSON-parsern resulterade i en krasch. Det här felet åtgärdas i den här versionen. |Ja |Nej |
| 6 |Värd-kopia |I tidigare versioner av programvaran påträffades ett ovanligt fel som rör ODX tidtagningen när du kopierar data från en volym till en annan volym. Detta resulterar i en kontrollenhetsredundans och systemet kan potentiellt styrenheterna i återställningsläge. Det här felet åtgärdas i den här versionen. |Ja |Nej |
| 7 |Windows Management Instrumentation (WMI) |I tidigare versioner av programvaran, det fanns flera instanser av web proxy misslyckades med undantaget ”\<ManagementException > providern kunde inte läsas in”. Den här buggen har tilldelats en WMI-minnesläcka och nu har lösts. |Ja |Nej |
| 8 |Uppdatering |I vissa sällsynta fall kan i tidigare versioner av programvara, emot ”CisPowershellHcsscripterror” av användaren att skanna eller installera uppdateringar. Det här problemet löses i den här versionen. |Ja |Ja |
| 9 |Supportpaket |I den här versionen har förbättringar i hur supportpaketet samlas in och laddat upp. |Ja |Ja |

## <a name="known-issues-in-update-22"></a>Kända problem i uppdatering 2.2
Följande tabell innehåller en sammanfattning av kända problem i den här versionen.

| Nej. | Funktion | Problem | Kommentarer / lösning | Gäller för fysisk enhet | Gäller för virtuella enheten |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk kvorum |I sällsynta fall om merparten av diskar i EBOD-höljet av en 8600-enhet är frånkopplade vilket resulterar i ingen disk kvorum kommer lagringspoolen går offline. Den förblir offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår, kontakta Microsoft Support angående nästa steg. |Ja |Nej |
| 2 |Felaktig controller-ID |När en domänkontrollant ersättning utförs kan kontrollenhet 0 visas som kontrollenhet 1. Under controller ersättning när avbildningen har lästs in från peer-noden kan controller-ID visas inledningsvis som kontrollanten peer-ID. I sällsynta fall kan ses detta även efter en omstart av systemet. |Ingen användaråtgärd krävs. Det här fallet kommer att åtgärdas automatiskt när domänkontrollanten ersättningen är klar. |Ja |Nej |
| 3 |Lagringskonton |Med Storage-tjänsten för att ta bort lagringskontot är ett scenario som inte stöds. Detta leder till en situation där användardata inte kan hämtas. | |Ja |Ja |
| 4 |Redundansväxling av enhet |Flera redundans av en volymbehållare från samma källenheten till olika målenheter stöds inte. Redundansväxling från en enskild döda enhet till flera enheter blir volymbehållare på den första redundansväxlas enheten förlora dataägarskap. Efter en redundansväxling, ska dessa volymbehållare visas eller fungerar annorlunda mot när du visar dem i den klassiska Azure-portalen. | |Ja |Nej |
| 5 |Installation |Under StorSimple Adapter för SharePoint-installation måste du ange en IP-adress för enheten för att installationen ska slutföras. | |Ja |Nej |
| 6 |Webbproxy |Om din webbproxykonfigurationen har HTTPS som det angivna protokollet, din enhet-till-tjänst-kommunikation kommer att påverkas och enheten försätts offline. Supportpaket genereras även i den processen och förbruka avsevärda resurser på din enhet. |Se till att Webbadressen till webbproxy har HTTP som det angivna protokollet. Mer information finns i [Konfigurera en webbproxy för din enhet](storsimple-configure-web-proxy.md). |Ja |Nej |
| 7 |Webbproxy |Om du konfigurerar och aktiverar webbproxy på en registrerad enhet, måste du starta om den aktiva kontrollenheten på din enhet. | |Ja |Nej |
| 8 |Hög molnet fördröjning och hög i/o-arbetsbelastning |När din StorSimple-enhet påträffar en kombination av mycket hög molnet fördröjning (efter några sekunder) och hög i/o-arbetsbelastning, går du till enhetens volymer i ett degraderat tillstånd och I/o kan misslyckas med ett ”enheten är inte klar”-fel. |Du behöver att manuellt starta om styrenheterna eller utföra en redundansväxling av enhet att återställa från den här situationen. |Ja |Nej |
| 9 |Azure PowerShell |När du använder cmdleten StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - först 1 - vänta** att välja det första objektet så att du kan skapa en ny **VolumeContainer** objekt, cmdleten returnerar alla objekt. |Innesluta cmdleten inom parentes på följande sätt: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - först 1 - vänta** |Ja |Ja |
| 10 |Migrering |När flera volymbehållare skickas för migrering, stämmer ETA för senaste säkerhetskopiering endast för den första volymbehållaren. Dessutom startar parallella migreringen efter de första 4 säkerhetskopiorna i första volymcontainern har migrerats. |Vi rekommenderar att du migrerar en volymbehållare i taget. |Ja |Nej |
| 11 |Migrering |Efter återställningen läggs inte volymer till principen för säkerhetskopiering eller gruppen virtuell disk. |Du behöver att lägga till dessa volymer i en princip för säkerhetskopiering för att skapa säkerhetskopior. |Ja |Ja |
| 12 |Migrering |När migreringen är klar måste 5000/7000-serien enheten inte att komma åt de migrerade data-behållarna. |Vi rekommenderar att du tar bort behållare för migrerade data när migreringen är klar och Skickat. |Ja |Nej |
| 13 |Klona och DR |En StorSimple-enhet som kör uppdatering 1 kan inte klona eller utföra haveriberedskap till en enhet som kör föruppdatering 1-programvara. |Behöver du uppdatera målenheten till uppdatering 1 för att tillåta dessa åtgärder |Ja |Ja |
| 14 |Migrering |Skapa säkerhetskopia för migrering kan misslyckas på en enhet i 5000-7000-serien när det finns volymgrupper med några associerade volymer. |Ta bort alla tomma volymgrupper med ingen tillhörande volymerna och försök sedan configuration-säkerhetskopiering. |Ja |Nej |
| 15 |Azure PowerShell-cmdlets och lokalt fixerade volymer |Du kan inte skapa en lokalt Fäst volym via Azure PowerShell-cmdlets. (Alla volymer som du skapar via Azure PowerShell kommer att vara nivåindelad.) |Använd alltid StorSimple Manager-tjänsten för att konfigurera lokalt fixerade volymer. |Ja |Nej |
| 16 |Tillgängliga utrymmet för lokalt fixerade volymer |Om du tar bort en lokalt Fäst volym kan det tillgängliga utrymmet för nya volymer inte uppdateras direkt. StorSimple Manager-tjänsten uppdaterar lokalt tillgängligt utrymme ungefär varje timme. |Vänta en timme innan du försöker skapa den nya volymen. |Ja |Nej |
| 17 |Lokalt fixerade volymer |Din återställningsjobbet exponerar den tillfälliga ögonblicksbildsäkerhetskopia i säkerhetskopieringskatalogen, men endast för varaktigheten för återställningsjobbet. Dessutom kan det visar att en virtuell disk-grupp med prefixet **tmpCollection** på den **principer för säkerhetskopiering** sidan, men endast för varaktigheten för återställningsjobbet. |Detta kan inträffa om din Återställningsjobbet har endast lokalt fixerade volymer eller en blandning av lokalt fixerade och nivåindelade volymer. Om återställningsjobbet innehåller endast nivåindelade volymer, utförs inte det här beteendet. Ingen åtgärd från användaren krävs. |Ja |Nej |
| 18 |Lokalt fixerade volymer |Om du avbryter ett återställningsjobb och en kontrollenhetsredundans sker omedelbart efteråt återställningsjobbet visas **misslyckades** i stället för **avbruten**. Om en återställningsjobbet misslyckas och det uppstår redundans controller omedelbart efteråt återställningsjobbet visas **avbruten** i stället för **misslyckades**. |Detta kan inträffa om din Återställningsjobbet har endast lokalt fixerade volymer eller en blandning av lokalt fixerade och nivåindelade volymer. Om återställningsjobbet innehåller endast nivåindelade volymer, utförs inte det här beteendet. Ingen åtgärd från användaren krävs. |Ja |Nej |
| 19 |Lokalt fixerade volymer |Om du avbryter ett återställningsjobb eller om en återställning misslyckas och sedan en kontrollenhetsredundans inträffar, en ytterligare återställningsjobbet visas på den **jobb** sidan. |Detta kan inträffa om din Återställningsjobbet har endast lokalt fixerade volymer eller en blandning av lokalt fixerade och nivåindelade volymer. Om återställningsjobbet innehåller endast nivåindelade volymer, utförs inte det här beteendet. Ingen åtgärd från användaren krävs. |Ja |Nej |
| 20 |Lokalt fixerade volymer |Om du försöker konvertera en nivåindelad volym (skapade och klonade med uppdatering 1.2 eller tidigare) till en lokalt Fäst volym och din enhet slut på utrymme eller om det sker ett avbrott i molnet, kan clone(s) skadas. |Det här problemet uppstår endast med volymer som har skapats och klonade med före uppdatering 2.1-programvara. Det bör vara ett ovanligt scenario. | | |
| 21 |Volymkonvertering |Uppdaterar inte åtkomstkontrollposter som är kopplade till en volym när en Volymkonvertering pågår (nivåindelade lokalt fixerade eller vice versa). Uppdaterar åtkomstkontrollposter kan resultera i skadade data. |Om det behövs, uppdatera åtkomstkontrollposter innan konverteringen volym och gör inte ytterligare ACR uppdateringar när konverteringen pågår. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Styrenhet och firmware-uppdateringar i uppdatering 2.2
Den här versionen har programvarubaserad uppdateringar. Men om du uppdaterar från en version före uppdatering 2, du måste installera drivrutinen Storport, Spaceport, och (i vissa fall) disk uppdateringar av inbyggd programvara på din enhet.

Läs mer om hur du installerar drivrutiner, Storport, Spaceport och uppdateringar av inbyggd programvara för disk [installera uppdatering 2.2](storsimple-install-update-21.md) på StorSimple-enheten.

## <a name="virtual-device-updates-in-update-22"></a>Uppdateringar av virtuell enhet i uppdatering 2.2
Den här uppdateringen kan inte tillämpas på den virtuella enheten. Nya virtuella enheter måste skapas. 

## <a name="next-step"></a>Nästa steg
Lär dig hur du [installera uppdatering 2.2](storsimple-install-update-21.md) på StorSimple-enheten.

