---
title: StorSimple 8000 Series Update 3 viktig information | Microsoft Docs
description: Beskriver de nya funktionerna, problem och lösningar för StorSimple 8000 Series Update 3.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d18feba4ded3dfccb8f774112a7dc8d42b12f1d5
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488341"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Uppdatera 3 viktig information för din enhet i StorSimple 8000-serien

## <a name="overview"></a>Översikt
Följande viktiga information beskriver de nya funktionerna och identifiera kritiska öppna ärenden för StorSimple 8000 Series Update 3. De innehåller också en lista över de StorSimple-programuppdateringar som ingår i denna version. 

Uppdatering 3 kan tillämpas på alla StorSimple-enhet som kör versionen (GA) eller uppdatering 0.1 via uppdatering 2.2. Enhetsversion som är associerade med uppdatering 3 är 6.3.9600.17759.

Granska informationen i den viktiga informationen innan du distribuerar uppdateringen i din StorSimple-lösning.

> [!IMPORTANT]
> * Uppdatering 3 har enhetsprogrammet, LSI drivrutiner och inbyggd programvara och Storport och Spaceport uppdateras. Det tar cirka 1,5-2 timmar att installera uppdateringen. 
> * För nya versioner, kan du inte se uppdateringar direkt, eftersom vi gör en stegvis distribution av uppdateringar. Vänta några få dagar och sedan söker efter uppdateringar igen som dessa är snart tillgängligt.
> 
> 

## <a name="whats-new-in-update-3"></a>Vad är nytt i uppdatering 3
Följande viktiga förbättringar och felkorrigeringar har gjorts i Update 3.

* **Automatiserad ändringar för frigöring av utrymme** – startar Update 3 kan algoritmerna för frigöring av utrymme som körs på kontrollenheten i vänteläge i systemet, vilket ger snabbare körning. Mer information om de portar som krävs för att arbeta med frigöring av utrymme finns i den [StorSimple nätverkskraven](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Prestandaförbättringar** – uppdatering 3 har bättre prestanda för Läs-och skrivbehörighet till molnet.
* **Förbättringar för migrering** – i den här versionen har flera felkorrigeringar och förbättringar har utförts för funktionen migrering från 5000/7000-serieenheter till enheter i 8000-serien. Mer information om hur du använder funktionen migreringen går du till [migrering från 5000/7000-serieenhet till enheten i 8000-serien](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Övervakning av relaterade korrigeringar** – i den här versionen buggar som rör övervakning diagram, instrumentpanelen för tjänsten och instrumentpanelen har lösts.

## <a name="issues-fixed-in-update-3"></a>Problem som åtgärdas i Update 3
Följande tabeller innehåller en översikt över problem som har korrigerats i Update 3.    

| Nej | Funktion | Problem | Gäller för fysisk enhet | Gäller för virtuella enheten |
| --- | --- | --- | --- | --- |
| 1 |Värd-sida datamigrering |I den tidigare versionen StorSimple Cloud Appliance går offline under en migrering av data för värd-sida. Det här problemet löses i den här versionen. |Nej |Ja |
| 2 |Lokalt fixerade volymer |I den föregående versionen kan det uppstod problem som rör i/o-fel, volym Konverteringsfel och datapath fel för lokalt fästa volymer. De här problemen har orsakades av rotcertifikatutfärdare och åtgärdas i den här versionen. |Ja |Nej |
| 3 |Övervakning |Det fanns flera problem som rör rapportering enheter och övervakning samt instrumentpanelsdiagram för enheten där felaktig information visades för lokalt fästa volymer. De här problemen åtgärdas i den här versionen. |Ja |Nej |
| 4 |Tung skrivningar i/o |När du använder StorSimple för arbetsbelastningar som involverar tung skrivningar kan skulle användaren stöter på ett ovanligt fel där arbetsminne har som nivåer i molnet. Det här felet åtgärdas i den här versionen. |Ja |Ja |
| 5 |Backup |I vissa sällsynta fall kan i tidigare versioner av programvara, när användaren har gjort en säkerhetskopia av en fjärransluten klon klickar de skulle stöter på Molnfel och åtgärden skulle ett fel. I den här versionen är problemet är löst och åtgärden slutförs. |Ja |Ja |
| 6 |Princip för säkerhetskopiering |I vissa sällsynta fall i tidigare versioner av programvara, det uppstod ett fel som rör borttagningen av säkerhetskopieringsprincipen. Det här problemet löses i den här versionen. |Ja |Ja |

## <a name="known-issues-in-update-3"></a>Kända problem i uppdatering 3
Följande tabell innehåller en sammanfattning av kända problem i den här versionen.

| Nej. | Funktion | Problem | Kommentarer / lösning | Gäller för fysisk enhet | Gäller för virtuella enheten |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk kvorum |I sällsynta fall om merparten av diskar i EBOD-höljet av en 8600-enhet är frånkopplade vilket resulterar i ingen disk kvorum kommer lagringspoolen går offline. Den förblir offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår, kontakta Microsoft Support angående nästa steg. |Ja |Nej |
| 2 |Felaktig controller-ID |När en domänkontrollant ersättning utförs kan kontrollenhet 0 visas som kontrollenhet 1. Under controller ersättning när avbildningen har lästs in från peer-noden kan controller-ID visas inledningsvis som kontrollanten peer-ID. I sällsynta fall kan ses detta även efter en omstart av systemet. |Ingen användaråtgärd krävs. Det här fallet kommer att åtgärdas automatiskt när domänkontrollanten ersättningen är klar. |Ja |Nej |
| 3 |Lagringskonton |Med Storage-tjänsten för att ta bort lagringskontot är ett scenario som inte stöds. Detta leder till en situation där användardata inte kan hämtas. | |Ja |Ja |
| 4 |Redundansväxling av enhet |Flera redundans av en volymbehållare från samma källenheten till olika målenheter stöds inte. Redundansväxling från en enskild döda enhet till flera enheter blir volymbehållare på den första redundansväxlas enheten förlora dataägarskap. Efter en redundansväxling, ska dessa volymbehållare visas eller fungerar annorlunda mot när du visar dem i den klassiska Azure-portalen. | |Ja |Nej |
| 5 |Installation |Under StorSimple Adapter för SharePoint-installation måste du ange en IP-adress för enheten för att installationen ska slutföras. | |Ja |Nej |
| 6 |Webbproxy |Om din webbproxykonfigurationen har HTTPS som det angivna protokollet, din enhet-till-tjänst-kommunikation kommer att påverkas och enheten försätts offline. Supportpaket genereras även i den processen och förbruka avsevärda resurser på din enhet. |Se till att Webbadressen till webbproxy har HTTP som det angivna protokollet. Mer information finns i [Konfigurera en webbproxy för din enhet](storsimple-8000-configure-web-proxy.md). |Ja |Nej |
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
| 22 |Uppdateringar |När du tillämpar Update 3 kan det **Underhåll** sidan i den klassiska Azure-portalen visar följande meddelande rör uppdatering 2 – ”StorSimple 8000-serien uppdatering 2 omfattar möjligheten för Microsoft att proaktivt samla in logg information från din enhet när vi identifierar potentiella problem ”. Detta vilseledande eftersom det anger att enheten ska uppdateras till uppdatering 2. När enheten har uppdaterats till uppdatering 3, försvinner meddelandet. |Det här problemet korrigeras i en framtida version. |Ja |Nej |

## <a name="controller-and-firmware-updates-in-update-3"></a>Styrenhet och firmware-uppdateringar i Update 3
Den här versionen har LSI-drivrutinen och firmware-uppdateringar. Läs mer om hur du installerar LSI-drivrutinen och uppdateringar av inbyggd programvara, [installera uppdatering 3](storsimple-install-update-3.md) på StorSimple-enheten.

## <a name="virtual-device-updates-in-update-3"></a>Uppdatering av virtuella enheter i Update 3
Den här uppdateringen kan inte tillämpas på StorSimple-Molninstallationen (även känt som den virtuella enheten). Nya virtuella enheter måste skapas. 

## <a name="next-step"></a>Nästa steg
Lär dig hur du [installera uppdatering 3](storsimple-install-update-3.md) på StorSimple-enheten.

