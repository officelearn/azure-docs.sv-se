---
title: StorSimple 8000 Series Update 2 viktig information | Microsoft-dokument
description: Beskriver de nya funktionerna, problemen och lösningarna för Uppdatering 2 i StorSimple 8000 Series.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: twooley
ms.openlocfilehash: 4e57fffd2f74ae1b14f51537c92299607f193ad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934064"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>Viktig information om StorSimple 8000 Series Update 2

## <a name="overview"></a>Översikt
Följande viktig information beskriver de nya funktionerna och identifierar de kritiska öppna problemen för StorSimple 8000 Series Update 2. De innehåller också en lista över StorSimple-program-, drivrutins- och diskprogramvara uppdateringar som ingår i den här versionen. 

Uppdatering 2 kan tillämpas på alla StorSimple-enheter som kör Release (GA) eller Uppdatering 0.1 till uppdatering 1.2. Enhetsversionen som är associerad med uppdatering 2 är 6.3.9600.17673.

Läs informationen i viktig information innan du distribuerar uppdateringen i StorSimple-lösningen.

> [!IMPORTANT]
> * Det tar ungefär 4-7 timmar att installera den här uppdateringen (inklusive Windows-uppdateringar). 
> * Uppdatering 2 har uppdateringar av programvara, LSI-drivrutin och SSD-firmware.
> * För nya versioner kanske du inte ser uppdateringar omedelbart eftersom vi gör en stegvis distribution av uppdateringarna. Vänta några dagar och sök sedan efter uppdateringar igen eftersom dessa kommer att bli tillgängliga snart.
> 
> 

## <a name="whats-new-in-update-2"></a>Nyheter i uppdatering 2
Uppdatering 2 introducerar följande nya funktioner.

* **Lokalt fästa volymer** – I tidigare versioner av StorSimple 8000-serien har datablock nivåats till molnet baserat på användning. Det fanns inget sätt att garantera att blocken skulle stanna på lokal. I Uppdatering 2, när du skapar en volym, kan du ange en volym som lokalt fäst, och primära data från den volymen kommer inte att nivåindelas till molnet. Ögonblicksbilder av lokalt fästa volymer kopieras fortfarande till molnet för säkerhetskopiering så att molnet kan användas för datamobilitet och katastrofåterställning. Dessutom kan du ändra volymtypen (det vill säga konvertera nivåindelada volymer till lokalt fästa volymer och konvertera lokalt fästa volymer till nivåindelad). 
* **StorSimple virtuella enhetsförbättringar** – Tidigare placerade StorSimple 8000-serien den virtuella enheten som en katastrofåterställning eller utvecklings-/testlösning. Det fanns bara en modell av virtuell enhet (modell 1100). Uppdatering 2 introducerar två virtuella enhetsmodeller: 
  
  * 8010 (tidigare kallad 1100) - Ingen förändring; har en kapacitet på 30 TB och använder Azure-standardlagring.
  * 8020 – Har en kapacitet på 64 TB och använder Azure Premium-lagring för bättre prestanda.
    
    Det finns en enda virtuell hårddisk för båda virtuella enhetsmodeller (8010/8020). När du startar den virtuella enheten upptäcks plattformsparametrarna och korrekt modellversion används.
* **Nätverksförbättringar** – Uppdatering 2 innehåller följande nätverksförbättringar:
  
  * Flera nätverkskort kan aktiveras för molnet så att redundans kan inträffa om ett nätverkskort misslyckas.
  * Routningsförbättringar, med fasta mått för molnaktiverade block.
  * Onlineförsök på misslyckade resurser före en redundans.
  * Nya aviseringar för tjänstfel.
* **Uppdateringsförbättringar** – I uppdatering 1.2 och tidigare uppdaterades StorSimple 8000-serien via två kanaler: Windows Update för klustring, iSCSI och så vidare och Microsoft Update för binärfiler och inbyggd programvara.
    I uppdatering 2 används Microsoft Update för alla uppdateringspaket. Detta bör leda till mindre tid patching eller göra redundans. 
* Uppdateringar av den **inbyggda** programvaran – Följande uppdateringar av den inbyggda programvaran ingår:
  
  * LSI: lsi_sas2.sys Produkt version 2.00.72.10
  * Endast SSD (inga HDD-uppdateringar): XMGG, XGEG, KZ50, F6C2 och VR08
* **Proaktiv support** – Uppdatering 2 gör det möjligt för Microsoft att hämta ytterligare diagnostikinformation från enheten. När vårt driftteam identifierar enheter som har problem är vi bättre rustade att samla in information från enheten och diagnostisera problem. **Genom att acceptera uppdatering 2 tillåter du oss att tillhandahålla detta proaktiva stöd.**    

## <a name="issues-fixed-in-update-2"></a>Problem som åtgärdats i uppdatering 2
Följande tabeller innehåller en sammanfattning av problem som åtgärdats i uppdateringar 2.    

| Nej. | Funktion | Problem | Gäller för fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- |
| 1 |Nätverksgränssnitt |Efter en uppgradering till uppdatering 1 rapporterade StorSimple Manager-tjänsten att Data2- och Data3-portarna misslyckades på en styrenhet. Det här problemet har åtgärdats. |Ja |Inga |
| 2 |Uppdateringar |Efter en uppgradering till uppdatering 1 inträffade hörbara larmaviseringar i den klassiska Azure-portalen på flera enheter. Det här problemet har åtgärdats. |Ja |Inga |
| 3 |Öppnar autentisering med autentisering |När du använder Openstack som molntjänstleverantör kan du få ett felmeddelande om att molnautentiseringssträngen var för lång. Problemet har åtgärdats. |Ja |Inga |

## <a name="known-issues-in-update-2"></a>Kända problem i uppdatering 2
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
| 20 |Lokalt fixerade volymer |Om du försöker konvertera en nivåindelad volym (skapad och klonad med uppdatering 1.2 eller tidigare) till en lokalt fäst volym och enheten börjar få på utrymme eller om det finns ett molnutbrott kan klonerna skadas. |Det här problemet uppstår endast med volymer som har skapats och klonats med programvara före uppdatering 2. Detta bör vara ett sällan förekommande scenario. | | |
| 21 |Volymkonvertering |Uppdatera inte de akusterna som är kopplade till en volym medan en volymkonvertering pågår (nivåindelad till lokalt fäst eller vice versa). Uppdatering av acr kan leda till att data skadas. |Om det behövs uppdaterar du ACR:erna före volymkonverteringen och gör inga ytterligare ACR-uppdateringar medan konverteringen pågår. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Uppdateringar av styrenhet och inbyggd programvara i uppdatering 2
Den här versionen uppdaterar drivrutinen och den inbyggda programvaran för disken på enheten.

* Mer information om uppdateringen av LSI-firmware finns i artikel 3121900 i Microsoft Knowledge base. 
* Mer information om uppdateringen av den fasta disken finns i artikel 3121899 i Microsoft Knowledge base.

## <a name="virtual-device-updates-in-update-2"></a>Uppdateringar av virtuella enheter i uppdatering 2
Den här uppdateringen kan inte tillämpas på den virtuella enheten. Nya virtuella enheter måste skapas. 

## <a name="next-step"></a>Nästa steg
Läs om hur du [installerar uppdatering 2](storsimple-install-update-2.md) på din StorSimple-enhet.

