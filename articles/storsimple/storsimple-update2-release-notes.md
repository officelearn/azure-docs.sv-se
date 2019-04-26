---
title: StorSimple 8000 Series Update 2 viktig information | Microsoft Docs
description: Beskriver de nya funktionerna, problem och lösningar för StorSimple 8000 Series Update 2.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: f23a507ab631be553613e22cafa037291548a8aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60530856"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>Viktig information om StorSimple 8000 Series Update 2

## <a name="overview"></a>Översikt
Följande viktiga information beskriver de nya funktionerna och identifiera kritiska öppna ärenden för StorSimple 8000 Series Update 2. De innehåller också en lista över StorSimple-programvara, drivrutiner och disk firmware-uppdateringar som ingår i den här versionen. 

Uppdatering 2 kan tillämpas på alla StorSimple-enhet som kör versionen (GA) eller uppdatering 0.1 via uppdatering 1.2. Enhetsversion som är associerade med uppdatering 2 är 6.3.9600.17673.

Granska informationen i den viktiga informationen innan du distribuerar uppdateringen i din StorSimple-lösning.

> [!IMPORTANT]
> * Det tar cirka 4 – 7 timmar att installera den här uppdateringen (inklusive Windows-uppdateringar). 
> * Uppdatering 2 har programvara, LSI-drivrutiner och uppdateringar av inbyggd programvara för SSD.
> * För nya versioner, kan du inte se uppdateringar direkt, eftersom vi gör en stegvis distribution av uppdateringar. Vänta några få dagar och sedan söker efter uppdateringar igen som dessa är snart tillgängligt.
> 
> 

## <a name="whats-new-in-update-2"></a>Vad är nytt i uppdatering 2
Uppdateringen 2 innehåller följande nya funktioner.

* **Lokalt fixerade volymer** – i tidigare versioner av StorSimple 8000-serien har datablock nivåer i molnet baserat på användning. Det gick inte att garantera att block skulle förblir på lokala. I uppdatering 2 när du skapar en volym, kan du ange en volym som inte att vara nivåindelad lokalt fixerade och primära data från volymen till molnet. Ögonblicksbilder av lokalt fixerade volymer kommer fortfarande att kopieras till molnet för säkerhetskopiering så att molnet kan användas för mobilitet och haveriberedskap dataåterställningsändamål. Dessutom kan du ändra volymtyp (det vill säga convert nivåindelade volymer till lokalt fixerade volymer och konvertera lokalt fixerade volymer till nivåer). 
* **StorSimple virtuell enhet förbättringar** – tidigare StorSimple 8000-serien placeras den virtuella enheten som en disaster recovery eller utveckling/test-lösning. Det uppstod endast en modell för virtuell enhet (modell 1100). Uppdatering 2 innehåller två virtuella enhetsmodeller: 
  
  * 8010 (kallades tidigare 1100) –; ingen ändring har en kapacitet på 30 TB och använder Azure standardlagring.
  * 8020 – har en kapacitet på 64 TB och använder Azure Premium storage för bättre prestanda.
    
    Det finns en enda virtuell Hårddisk för båda virtuella enhetsmodeller (8010/8020). När du startar den virtuella enheten identifierar parametrarna plattform och tillämpar den korrekta versionen.
* **Förbättringar av nätverk** – uppdatering 2 innehåller följande nätverk förbättringar:
  
  * Flera nätverkskort kan aktiveras för molnet så att växling vid fel kan inträffa om ett nätverkskort inte.
  * Routning förbättringar med fast mått för molnet aktiverat block.
  * Online återförsök för misslyckade resurser före redundans.
  * Nya aviseringar för inträffar.
* **Uppdaterar förbättringar** – i Uppdatera 1.2 och tidigare, StorSimple 8000-serien har uppdaterats via två kanaler: Windows Update för klustring, iSCSI, och så vidare och Microsoft Update för binärfiler och inbyggd programvara.
    Uppdatering 2 använder Microsoft Update för alla uppdateringspaket. Detta ska leda till mindre tid uppdatering eller gör redundans. 
* **Uppdateringar av inbyggd programvara** – med följande firmware uppdateringar ingår:
  
  * LSI: lsi_sas2.sys Product Version 2.00.72.10
  * Endast SSD (inga HDD-uppdateringar): XMGG, XGEG, KZ50, F6C2 och VR08
* **Proaktiv Support** – uppdatering 2 hjälper Microsoft att hämta ytterligare diagnostikinformation från enheten. När våra driftsteamet identifierar enheter som har problem, är vi bättre utrustade för att samla in information från enheten och diagnostisera problem. **Genom att acceptera uppdatering 2, kan vi har stöd för detta proaktiv**.    

## <a name="issues-fixed-in-update-2"></a>Problem som åtgärdas i uppdatering 2
Följande tabeller innehåller en översikt över problem som har korrigerats i uppdateringar 2.    

| Nej. | Funktion | Problem | Gäller för fysisk enhet | Gäller för virtuella enheten |
| --- | --- | --- | --- | --- |
| 1 |Nätverksgränssnitt |Efter en uppgradering till uppdatering 1 rapporterade StorSimple Manager-tjänsten att portarna fil2 och Data3 misslyckades på en domänkontrollant. Det här problemet har åtgärdats. |Ja |Nej |
| 2 |Uppdateringar |Efter en uppgradering till uppdatering 1 uppstod ljudligt alarm aviseringar i den klassiska Azure-portalen på flera enheter. Det här problemet har åtgärdats. |Ja |Nej |
| 3 |Openstack-autentisering |När du använder Openstack som din molntjänstleverantör, kan ett felmeddelande att din cloud autentisering sträng var för långt. Problemet har åtgärdats. |Ja |Nej |

## <a name="known-issues-in-update-2"></a>Kända problem i uppdatering 2
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
| 20 |Lokalt fixerade volymer |Om du försöker konvertera en nivåindelad volym (skapade och klonade med uppdatering 1.2 eller tidigare) till en lokalt Fäst volym och din enhet slut på utrymme eller om det sker ett avbrott i molnet, kan clone(s) skadas. |Det här problemet uppstår endast med volymer som har skapats och klonade med före uppdatering 2 programvara. Det bör vara ett ovanligt scenario. | | |
| 21 |Volymkonvertering |Uppdaterar inte åtkomstkontrollposter som är kopplade till en volym när en Volymkonvertering pågår (nivåindelade lokalt fixerade eller vice versa). Uppdaterar åtkomstkontrollposter kan resultera i skadade data. |Om det behövs, uppdatera åtkomstkontrollposter innan konverteringen volym och gör inte ytterligare ACR uppdateringar när konverteringen pågår. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Styrenhet och firmware-uppdateringar i uppdatering 2
Den här versionen uppdaterar drivrutinen och den inbyggda programvaran för disken på din enhet.

* Mer information om den inbyggda programvaran LSI uppdatera, finns i Microsoft Knowledge base-artikeln 3121900. 
* Mer information om den inbyggda programvaran för disken uppdatera finns i Microsoft Knowledge base-artikeln 3121899.

## <a name="virtual-device-updates-in-update-2"></a>Uppdatering av virtuella enheter i uppdatering 2
Den här uppdateringen kan inte tillämpas på den virtuella enheten. Nya virtuella enheter måste skapas. 

## <a name="next-step"></a>Nästa steg
Lär dig hur du [installera uppdatering 2](storsimple-install-update-2.md) på StorSimple-enheten.

