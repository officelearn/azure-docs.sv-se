---
title: Viktig information om StorSimple 8000-serien Update 3
description: Beskriver de nya funktionerna, problemen och lösningarna för Uppdatering 3 i StorSimple 8000 Series.
author: alkohli
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b61caecd67881eb08c82ea0c26522c63c3e8396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254617"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Uppdatera 3 viktig information för din StorSimple 8000-serieenhet

## <a name="overview"></a>Översikt
Följande viktig information beskriver de nya funktionerna och identifierar de kritiska öppna problemen för StorSimple 8000 Series Update 3. De innehåller också en lista över De StorSimple-programuppdateringar som ingår i den här versionen. 

Uppdatering 3 kan tillämpas på alla StorSimple-enheter som kör Release (GA) eller Uppdatering 0.1 till uppdatering 2.2. Enhetsversionen som är associerad med uppdatering 3 är 6.3.9600.17759.

Läs informationen i viktig information innan du distribuerar uppdateringen i StorSimple-lösningen.

> [!IMPORTANT]
> * Uppdatering 3 har enhetsprogram, LSI-drivrutin och inbyggd programvara samt Storport- och Spaceport-uppdateringar. Det tar cirka 1,5-2 timmar att installera den här uppdateringen. 
> * För nya versioner kanske du inte ser uppdateringar omedelbart eftersom vi gör en stegvis distribution av uppdateringarna. Vänta några dagar och sök sedan efter uppdateringar igen eftersom dessa kommer att bli tillgängliga snart.
> 
> 

## <a name="whats-new-in-update-3"></a>Nyheter i uppdatering 3
Följande viktiga förbättringar och buggfixar har gjorts i uppdatering 3.

* **Automatiserad utrymme återvinning förändringar** - Starta uppdatering 3, utrymme återvinning algoritmer körs på standby-styrenheten i systemet vilket resulterar i snabbare körning. Mer information om de portar som krävs för att arbeta med utrymmesåterklamation finns i [lagringsnätverkskraven](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Prestandaförbättringar** – Uppdatering 3 har förbättrat läs- och skrivprestanda till molnet.
* **Migreringsrelaterade förbättringar** – I den här versionen gjordes flera buggfixar och förbättringar för migreringsfunktionen från enheter i 5000/7000-serien till enheter i 8000-serien. Mer information om hur du använder migreringsfunktionen finns i [Migreringsenheten från enheten i 5000/7000-serien till 8 000-serien](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Övervakningsrelaterade korrigeringar** - I den här versionen åtgärdades fel relaterade till övervakningsdiagram, instrumentpanelen för tjänsten och instrumentpanelen för enheten.

## <a name="issues-fixed-in-update-3"></a>Problem som åtgärdats i uppdatering 3
Följande tabeller innehåller en sammanfattning av problem som åtgärdats i uppdatering 3.    

| Inga | Funktion | Problem | Gäller för fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- |
| 1 |Datamigrering på värdsidan |I den tidigare versionen gick StorSimple Cloud Appliance offline under en datamigrering på värdsidan. Det här problemet åtgärdas i den här versionen. |Inga |Ja |
| 2 |Lokalt fixerade volymer |I den tidigare versionen uppstod problem relaterade till I/O-fel, volymkonverteringsfel och datapath-fel för lokalt fästa volymer. Dessa problem orsakades av root-caused och åtgärdades i den här versionen. |Ja |Inga |
| 3 |Övervakning |Det fanns flera problem relaterade till rapporteringsenheter och övervakning samt instrumentpanelsdiagram där felaktig information visades för lokalt fästa volymer. Dessa problem åtgärdas i den här versionen. |Ja |Inga |
| 4 |Tunga skriver I/O |När du använder StorSimple för arbetsbelastningar som involverar tunga skrivningar, skulle användaren stöta på en ovanlig bugg där arbetsminnet var nivåindelade i molnet. Det här felet är åtgärdat i den här versionen. |Ja |Ja |
| 5 |Säkerhetskopiering |I vissa sällsynta fall, i tidigare versioner av programvara, när användaren tog en säkerhetskopia av en fjärrklon, skulle de stöta på molnfel och åtgärden skulle fel. I den här versionen är problemet åtgärdat och åtgärden slutförs. |Ja |Ja |
| 6 |Princip för säkerhetskopiering |I vissa sällsynta fall, i tidigare versioner av programvara, fanns det en bugg i samband med radering av säkerhetskopieringsprincip. Det här problemet åtgärdas i den här versionen. |Ja |Ja |

## <a name="known-issues-in-update-3"></a>Kända problem i uppdatering 3
I följande tabell finns en sammanfattning av kända problem i den här versionen.

| Nej. | Funktion | Problem | Kommentarer / lösning | Gäller för fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- | --- |
| 1 |Diskkvorum |I sällsynta fall, om majoriteten av diskarna i EBOD-höljet för en 8600-enhet kopplas från, vilket resulterar i inget diskkvorum, kommer lagringspoolen att gå offline. Det kommer att förbli offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår kontaktar du Microsoft Support för nästa steg. |Ja |Inga |
| 2 |Felaktigt styrenhets-ID |När en handkontroll byts ut kan styrenheten 0 visas som controller 1. När avbildningen läses in från peer-noden kan styrenhetens ID visas från början som peer-styrenhetens ID. I sällsynta fall kan detta beteende också ses efter en omstart av systemet. |Ingen användaråtgärd krävs. Den här situationen löser sig när styrenhetens utbyte är klar. |Ja |Inga |
| 3 |Lagringskonton |Att använda lagringstjänsten för att ta bort lagringskontot är ett scenario som inte stöds. Detta leder till en situation där användardata inte kan hämtas. | |Ja |Ja |
| 4 |Redundans för enheten |Flera redundanser av en volymbehållare från samma källenhet till olika målenheter stöds inte. Redundans från en enda död enhet till flera enheter gör att volymbehållarna på den första misslyckade över enheten förlorar dataägarskap. Efter en sådan redundans visas dessa volymbehållare eller fungerar annorlunda när du visar dem i den klassiska Azure-portalen. | |Ja |Inga |
| 5 |Installation |Under StorSimple-adaptern för SharePoint-installation måste du ange en enhets-IP för att installationen ska slutföras. | |Ja |Inga |
| 6 |Webbproxy |Om webbproxykonfigurationen har HTTPS som angivet protokoll påverkas kommunikationen mellan enheter och tjänsten och enheten kopplas från. Supportpaket genereras också i processen och förbrukar betydande resurser på din enhet. |Kontrollera att webbproxy-URL:en har HTTP som angivet protokoll. Mer information finns i [Konfigurera en webbproxy för din enhet](storsimple-8000-configure-web-proxy.md). |Ja |Inga |
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
| 22 |Uppdateringar |När du installerar uppdatering 3 visar **sidan Underhåll** i den klassiska Azure-portalen följande meddelande relaterat till uppdatering 2 - "StorSimple 8000-serien Uppdatering 2 innehåller möjligheten för Microsoft att proaktivt samla in logginformation från din enhet när vi upptäcker potentiella problem". Detta är missvisande eftersom det indikerar att enheten uppdateras till uppdatering 2. När enheten har uppdaterats till uppdatering 3 försvinner det här meddelandet. |Detta kommer att åtgärdas i en framtida version. |Ja |Inga |

## <a name="controller-and-firmware-updates-in-update-3"></a>Uppdateringar av styrenhet och inbyggd programvara i uppdatering 3
Den här versionen har LSI-drivrutin och uppdateringar av inbyggd programvara. Mer information om hur du installerar LSI-drivrutinen och uppdateringar av inbyggd programvara finns i [installera uppdatering 3](storsimple-install-update-3.md) på Din StorSimple-enhet.

## <a name="virtual-device-updates-in-update-3"></a>Uppdateringar av virtuella enheter i uppdatering 3
Den här uppdateringen kan inte tillämpas på StorSimple Cloud Appliance (kallas även den virtuella enheten). Nya virtuella enheter måste skapas. 

## <a name="next-step"></a>Nästa steg
Läs om hur du [installerar uppdatering 3](storsimple-install-update-3.md) på din StorSimple-enhet.

