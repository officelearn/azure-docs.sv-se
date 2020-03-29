---
title: StorSimple 8000-serien Uppdatering 2.2 viktig information | Microsoft-dokument
description: Beskriver de nya funktionerna, problemen och lösningarna för StorSimple 8000 Series Update 2.2.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60531064"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>StorSimple 8000-serien Uppdatering 2.2 viktig information

## <a name="overview"></a>Översikt
Följande viktig information beskriver de nya funktionerna och identifierar de kritiska öppna problemen för StorSimple 8000 Series Update 2.2. De innehåller också en lista över De StorSimple-programuppdateringar som ingår i den här versionen.

Uppdatering 2.2 kan tillämpas på alla StorSimple-enheter som kör Release (GA) eller Uppdatering 0.1 till uppdatering 2.1. Enhetsversionen som är associerad med uppdatering 2.2 är 6.3.9600.17708.

Läs informationen i viktig information innan du distribuerar uppdateringen i StorSimple-lösningen.

> [!IMPORTANT]
> * Uppdatering 2.2 har endast programuppdateringar. Det tar cirka 1,5-2 timmar att installera den här uppdateringen. 
> * Om du kör Uppdatering 2.1 rekommenderar vi att du installerar uppdatering 2.2 så snart som möjligt.
> * För nya versioner kanske du inte ser uppdateringar omedelbart eftersom vi gör en stegvis distribution av uppdateringarna. Vänta några dagar och sök sedan efter uppdateringar igen eftersom dessa kommer att bli tillgängliga snart.
> 
> 

## <a name="whats-new-in-update-22"></a>Nyheter i uppdatering 2.2
Följande viktiga förbättringar har gjorts i uppdatering 2.2.

* **Automatisk utrymmesåterklamationsoptimering** – När data tas bort på tunt etablerade volymer måste de oanvända lagringsblocken återkn återanvändas. Den här versionen har förbättrat utrymme återvinningsprocessen från molnet vilket resulterar i oanvänt utrymme blir tillgängliga snabbare jämfört med tidigare versioner.
* **Förbättringar av ögonblicksbilder** – Uppdatering 2.2 har förbättrat tiden för att bearbeta en ögonblicksbild i molnet i vissa scenarier där stora volymer används och det finns minimal till ingen dataomsättning. Ett scenario som skulle dra nytta av den här förbättringen skulle vara arkivvolymerna.
* **Härdning av supportpaket insamling** - Det har skett förbättringar i hur supportpaketet samlas in och laddas upp i den här versionen. 
* **Förbättringar av uppdateringstillförlitligheten** – Den här versionen har buggfixar som resulterar i en förbättrad uppdateringstillförlitlighet.

## <a name="issues-fixed-in-update-22"></a>Problem som åtgärdats i uppdatering 2.2
Följande tabeller innehåller en sammanfattning av problem som åtgärdats i uppdateringarna 2.2 och 2.1.    

| Inga | Funktion | Problem | Gäller för fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- |
| 1 |Värdprestanda |I den tidigare versionen observerades prestandaproblem på värdsidan när en lokalt fäst volym skapades och under konverteringen av en nivåindelad volym till en lokalt fäst volym. Dessa problem åtgärdas i den här versionen vilket resulterar i en förbättring av värdprestanda under volymskapande och konverteringsprocedurer. |Ja |Inga |
| 2 |Lokalt fixerade volymer |I sällsynta fall skulle systemet krascha när du skapar en lokalt fäst volym. Det här felet har åtgärdats i den här versionen. |Ja |Inga |
| 3 |Nivåindelning |Det uppstod sporadiska krascher när metadata för StorSimple Cloud Appliances (8010 och 8020) nivåindelad till molnet. Det här problemet åtgärdas i den här versionen. |Inga |Ja |
| 4 |Skapa ögonblicksbilder |Det fanns problem relaterade till skapandet av inkrementella ögonblicksbilder i scenarier med stora volymer och minimal till ingen dataomsättning. Dessa problem åtgärdas i den här versionen. |Ja |Ja |
| 5 |Öppnar autentisering med autentisering |När du använder Openstack som molntjänstleverantör skulle användaren stöta på ett sällan fel relaterat till autentiseringen där JSON-parsern resulterade i en krasch. Det här felet är åtgärdat i den här versionen. |Ja |Inga |
| 6 |Kopia på värdsidan |I tidigare versioner av programvara sågs en ovanlig bugg relaterad till ODX-tidpunkten när data kopierades från en volym till en annan volym. Detta skulle resultera i en kontrollant redundans och systemet kan eventuellt gå in i återställningsläge. Det här felet är åtgärdat i den här versionen. |Ja |Inga |
| 7 |Instrumentering av Windows-hantering (WMI) |I tidigare versioner av programvaran fanns det flera instanser av\<webbproxyfel med undantaget "ManagementException> Provider load failure". Denna bugg tillskrevs en WMI-minnesläcka och är nu åtgärdad. |Ja |Inga |
| 8 |Uppdatering |I vissa sällsynta fall, i tidigare versioner av programvara, fick användaren en "CisPowershellHcsscripterror" när han försökte skanna eller installera uppdateringar. Det här problemet åtgärdas i den här versionen. |Ja |Ja |
| 9 |Supportpaket |I den här versionen har det skett förbättringar av hur supportpaketet samlas in och laddas upp. |Ja |Ja |

## <a name="known-issues-in-update-22"></a>Kända problem i uppdatering 2.2
I följande tabell finns en sammanfattning av kända problem i den här versionen.

| Nej. | Funktion | Problem | Kommentarer / lösning | Gäller för fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- | --- |
| 1 |Diskkvorum |I sällsynta fall, om majoriteten av diskarna i EBOD-höljet för en 8600-enhet kopplas från, vilket resulterar i inget diskkvorum, kommer lagringspoolen att gå offline. Det kommer att förbli offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår kontaktar du Microsoft Support för nästa steg. |Ja |Inga |
| 2 |Felaktigt styrenhets-ID |När en handkontroll byts ut kan styrenheten 0 visas som controller 1. När avbildningen läses in från peer-noden kan styrenhetens ID visas från början som peer-styrenhetens ID. I sällsynta fall kan detta beteende också ses efter en omstart av systemet. |Ingen användaråtgärd krävs. Den här situationen löser sig när styrenhetens utbyte är klar. |Ja |Inga |
| 3 |Lagringskonton |Att använda lagringstjänsten för att ta bort lagringskontot är ett scenario som inte stöds. Detta leder till en situation där användardata inte kan hämtas. | |Ja |Ja |
| 4 |Redundans för enheten |Flera redundanser av en volymbehållare från samma källenhet till olika målenheter stöds inte. Redundans från en enda död enhet till flera enheter gör att volymbehållarna på den första misslyckade över enheten förlorar dataägarskap. Efter en sådan redundans visas dessa volymbehållare eller fungerar annorlunda när du visar dem i den klassiska Azure-portalen. | |Ja |Inga |
| 5 |Installation |Under StorSimple-adaptern för SharePoint-installation måste du ange en enhets-IP för att installationen ska slutföras. | |Ja |Inga |
| 6 |Webbproxy |Om webbproxykonfigurationen har HTTPS som angivet protokoll påverkas kommunikationen mellan enheter och tjänsten och enheten kopplas från. Supportpaket genereras också i processen och förbrukar betydande resurser på din enhet. |Kontrollera att webbproxy-URL:en har HTTP som angivet protokoll. Mer information finns i [Konfigurera en webbproxy för din enhet](storsimple-configure-web-proxy.md). |Ja |Inga |
| 7 |Webbproxy |Om du konfigurerar och aktiverar webbproxy på en registrerad enhet måste du starta om den aktiva handkontrollen på enheten. | |Ja |Inga |
| 8 |Hög molnfördröjning och hög I/O-arbetsbelastning |När din StorSimple-enhet stöter på en kombination av mycket höga molnfördyningar (sekunders ordning) och hög I/O-arbetsbelastning hamnar enhetsvolymerna i försämrat tillstånd och I/O kan misslyckas med ett "enhet som inte är redo"-fel. |Du måste starta om enhetskontrollanterna manuellt eller utföra en enhetsväxling för att återställa från den här situationen. |Ja |Inga |
| 9 |Azure PowerShell |När du använder StorSimple cmdlet **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** för att markera det första objektet så att du kan skapa ett nytt **VolumeContainer-objekt,** returnerar cmdlet alla objekt. |Radbryta cmdlet inom parentes enligt följande: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Ja |Ja |
| 10 |Migrering |När flera volymbehållare skickas för migrering är ETA för senaste säkerhetskopieringen endast korrekt för den första volymbehållaren. Dessutom startar parallell migrering efter att de första 4 säkerhetskopiorna i den första volymbehållaren har migrerats. |Vi rekommenderar att du migrerar en volymbehållare i taget. |Ja |Inga |
| 11 |Migrering |Efter återställningen läggs volymer inte till i säkerhetskopieringsprincipen eller den virtuella diskgruppen. |Du måste lägga till dessa volymer i en princip för säkerhetskopiering för att kunna skapa säkerhetskopior. |Ja |Ja |
| 12 |Migrering |När migreringen är klar får enheten i 5000/7000-serien inte komma åt de migrerade databehållarna. |Vi rekommenderar att du tar bort de migrerade databehållarna när migreringen är klar och har bekräftats. |Ja |Inga |
| 13 |Klon och DR |En StorSimple-enhet som kör uppdatering 1 kan inte klona eller utföra haveriberedskap till en enhet som kör föruppdatering 1 programvara. |Du måste uppdatera målenheten till uppdatering 1 för att dessa åtgärder ska kunna |Ja |Ja |
| 14 |Migrering |Säkerhetskopiering av konfiguration för migrering kan misslyckas på en enhet i 5000-7000-serien när det finns volymgrupper utan associerade volymer. |Ta bort alla tomma volymgrupper utan associerade volymer och försök sedan igen med säkerhetskopieringen av konfigurationen. |Ja |Inga |
| 15 |Azure PowerShell-cmdlets och lokalt fästa volymer |Du kan inte skapa en lokalt fäst volym via Azure PowerShell-cmdlets. (Alla volymer som du skapar via Azure PowerShell kommer att nivåindelad.) |Använd alltid Tjänsten StorSimple Manager för att konfigurera lokalt fästa volymer. |Ja |Inga |
| 16 |Tillgängligt utrymme för lokalt fästa volymer |Om du tar bort en lokalt fäst volym kanske det tillgängliga utrymmet för nya volymer inte uppdateras omedelbart. StorSimple Manager-tjänsten uppdaterar det lokala utrymme som är tillgängligt ungefär varje timme. |Vänta en timme innan du försöker skapa den nya volymen. |Ja |Inga |
| 17 |Lokalt fixerade volymer |Återställningsjobbet visar den tillfälliga säkerhetskopieringen av ögonblicksbilder i säkerhetskopieringskatalogen, men bara under återställningsjobbets varaktighet. Dessutom exponeras en virtuell diskgrupp med prefix **tmpCollection** på sidan Principer för **säkerhetskopiering,** men bara under återställningsjobbets varaktighet. |Detta kan inträffa om återställningsjobbet bara har lokalt fästa volymer eller en blandning av lokalt fästa och nivåindelade volymer. Om återställningsjobbet endast innehåller nivåindelade volymer kommer det här beteendet inte att inträffa. Ingen åtgärd från användaren krävs. |Ja |Inga |
| 18 |Lokalt fixerade volymer |Om du avbryter ett återställningsjobb och en redertkontrollkontroll inträffar omedelbart efteråt visas **Återställningsjobbet Misslyckades** i stället för **Avbruten**. Om ett återställningsjobb misslyckas och en rederingskontroll från styrenheten inträffar omedelbart efteråt visas **avbrutet avbrutet återställningsjobbet** i stället för **Misslyckades**. |Detta kan inträffa om återställningsjobbet bara har lokalt fästa volymer eller en blandning av lokalt fästa och nivåindelade volymer. Om återställningsjobbet endast innehåller nivåindelade volymer kommer det här beteendet inte att inträffa. Ingen åtgärd från användaren krävs. |Ja |Inga |
| 19 |Lokalt fixerade volymer |Om du avbryter ett återställningsjobb eller om en återställning misslyckas och en redertupp om styrenheten inträffar visas ytterligare ett återställningsjobb på sidan **Jobb.** |Detta kan inträffa om återställningsjobbet bara har lokalt fästa volymer eller en blandning av lokalt fästa och nivåindelade volymer. Om återställningsjobbet endast innehåller nivåindelade volymer kommer det här beteendet inte att inträffa. Ingen åtgärd från användaren krävs. |Ja |Inga |
| 20 |Lokalt fixerade volymer |Om du försöker konvertera en nivåindelad volym (skapad och klonad med uppdatering 1.2 eller tidigare) till en lokalt fäst volym och enheten börjar få på utrymme eller om det finns ett molnutbrott kan klonerna skadas. |Det här problemet uppstår endast med volymer som har skapats och klonats med programvara före uppdatering 2.1. Detta bör vara ett sällan förekommande scenario. | | |
| 21 |Volymkonvertering |Uppdatera inte de akusterna som är kopplade till en volym medan en volymkonvertering pågår (nivåindelad till lokalt fäst eller vice versa). Uppdatering av acr kan leda till att data skadas. |Om det behövs uppdaterar du ACR:erna före volymkonverteringen och gör inga ytterligare ACR-uppdateringar medan konverteringen pågår. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Uppdateringar av styrenhet och inbyggd programvara i uppdatering 2.2
Den här versionen har endast programuppdateringar. Om du uppdaterar från en version före uppdatering 2 måste du dock installera uppdateringar av drivrutinen, Storport, Spaceport och (i vissa fall) diskuppdateringar på enheten.

Mer information om hur du installerar uppdateringarna av drivrutinen, Storport, Spaceport och disken finns i [installera uppdatering 2.2](storsimple-install-update-21.md) på StorSimple-enheten.

## <a name="virtual-device-updates-in-update-22"></a>Uppdateringar av virtuella enheter i uppdatering 2.2
Den här uppdateringen kan inte tillämpas på den virtuella enheten. Nya virtuella enheter måste skapas. 

## <a name="next-step"></a>Nästa steg
Läs om hur du [installerar uppdatering 2.2](storsimple-install-update-21.md) på Din StorSimple-enhet.

