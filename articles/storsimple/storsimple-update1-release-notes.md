---
title: StorSimple 8000-serien uppdatering 1.2 viktig information | Microsoft Docs
description: Beskriver de nya funktionerna, problem och lösningar för StorSimple 8000-serien uppdatering 1.2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6c9aae87-6f77-44b8-b7fa-ebbdc9d8517c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 11138857e33eec0f854ddb61956ea24c858c49a5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60530999"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Uppdatera 1,2 viktig information för din enhet i StorSimple 8000-serien

## <a name="overview"></a>Översikt
Följande viktiga information beskriver de nya funktionerna och identifiera kritiska öppna ärenden för StorSimple 8000-serien uppdatering 1.2. De innehåller också en lista över StorSimple-programvara, drivrutinen och disk firmware-uppdateringar som ingår i den här versionen. 

Uppdatering 1.2 kan tillämpas på alla StorSimple-enhet som kör versionen (GA), uppdatering 0.1, uppdatering 0.2 eller uppdatering 0.3 programvara. Uppdatering 1.2 är inte tillgängligt om enheten kör uppdatering 1 eller uppdatering 1.1. Om enheten kör versionen (GA), [kontakta Microsoft Support](storsimple-contact-microsoft-support.md) kan hjälpa dig med att installera uppdateringen.

I följande tabell visas enheten programvaruversioner som motsvarar uppdateringar 1, 1.1 och 1.2.

| Om du kör uppdatering... | Det här är enhetens programvaruversion. |
| --- | --- |
| Uppdatering 1.2 |6.3.9600.17584 |
| Uppdatera 1.1 |6.3.9600.17521 |
| Version 1.0 |6.3.9600.17491 |

Granska informationen i den viktiga informationen innan du distribuerar uppdateringen i din StorSimple-lösning. Läs mer om hur du [installera uppdatering 1.2 på StorSimple-enheten](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * Det tar cirka 5 – 10 timmar att installera den här uppdateringen (inklusive Windows uppdaterar). 
> * Uppdatering 1.2 har programvara, LSI-drivrutinen och uppdateringar av inbyggd programvara för disk. Om du vill installera, följ instruktionerna i [installera uppdatering 1.2 på StorSimple-enheten](storsimple-install-update-1.md).
> * För nya versioner, kan du inte se uppdateringar direkt, eftersom vi gör en stegvis distribution av uppdateringar. Söker efter uppdateringar på några dagar igen som dessa är snart tillgängligt.
> 
> 

## <a name="whats-new-in-update-12"></a>Vad är nytt i uppdatering 1.2
De här funktionerna släpptes först med uppdatering 1 som har gjorts tillgängliga för en begränsad uppsättning användare. De flesta av StorSimple-användare skulle uppdatering 1.2-versionen finns följande nya funktioner och förbättringar:

* **Migrering från 5000 – 7000-serien 8000-serien enheter** – den här versionen finns en ny funktion för migrering som användarna i StorSimple 5000-7000 serien installation kan migrera sina data till en fysisk installation för StorSimple 8000-serien eller en virtuell installationen. Funktionen migrering har två viktiga värderingsförslag:                                                                  
  
  * **Affärskontinuitet**, genom att aktivera migrering av befintliga data på 5000 – 7000-serien installationer till 8000-serien.
  * **Förbättrad funktionserbjudanden för 8000-serien installationer**, till exempel effektiv centraliserad hantering av flera installationer via StorSimple Manager-tjänsten, bättre maskinvaruklass och uppdaterade inbyggd programvara, virtuella enheter, data mobility. och nya funktioner i framtida översikten.
    
    Referera till den [Migreringsguide](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) för information om hur du migrerar en virtuell StorSimple 5000-7000-serien till en enhet för 8000-serien. 
* **Tillgänglighet i Azure Government Portal** – StorSimple är nu tillgängligt i Azure Government-portalen. Se hur du [distribuera en StorSimple-enhet i Azure Government Portal](storsimple-deployment-walkthrough-gov.md).
* **Stöd för andra molntjänstleverantörer** – de andra molntjänstleverantörer som stöds är Amazon S3, Amazon S3 med RRS, HP och OpenStack (beta).
* **Uppdatering av API: erna med senaste Storage** – med den här versionen StorSimple har uppdaterats till den senaste Azure Storage-tjänsten-API: er. Enheter i StorSimple 8000-serien som kör Föruppdatering 1 programvaruversioner (versionen 0.1, 0.2 och 0.3) använder versioner av Azure Storage Service API: erna äldre än den 17 juli 2009. Enligt informationen i den uppdaterade [tillkännagivande om borttagning av Storage-tjänstversioner](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), med 1 augusti 2016 kommer dessa API: er upphör att gälla. Det är viktigt att du tillämpar StorSimple 8000-serien uppdatering 1 före den 1 augusti 2016. Om du inte göra det, slutar StorSimple-enheter att fungera korrekt.
* **Stöd för Zonredundant lagring (ZRS)** – med uppgraderingen till den senaste versionen av Storage-API: er, StorSimple 8000-serien har stöd för Zonredundant lagring (ZRS) förutom lokalt Redundant lagring (LRS) och Geo-redundant lagring (GRS ). Referera till denna [artikel om redundansalternativ för Azure Storage](../storage/common/storage-redundancy.md) ZRS-information.
* **Förbättrad första upplevelse för distribution och uppdatering** – i den här versionen, installation och uppdatering processer har förbättrats. Installationen via installationsguiden förbättrats för att ge feedback till användaren om nätverkskonfigurationen och brandväggsinställningar är felaktiga. Ytterligare diagnostik cmdlets har angetts som hjälper dig med felsökning av nätverk för enheten. Se den [felsökningsartikeln distribution](storsimple-troubleshoot-deployment.md) för mer information om de nya diagnostiska cmdletar som används för felsökning.

## <a name="issues-fixed-in-update-12"></a>Problem som åtgärdas i uppdatering 1.2
Följande tabell innehåller en översikt över problem som har korrigerats i uppdateringar 1.2, 1.1 och 1.    

| Nej. | Funktion | Problem | Korrigeras i uppdateringen | Gäller för fysisk enhet | Gäller för virtuella enheten |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell för StorSimple |När en användare få fjärråtkomst till StorSimple-enheten med hjälp av Windows PowerShell för StorSimple och sedan startas installationsguiden, inträffade en krasch så snart som Data 0 IP har indata. Den här buggen löses nu i uppdatering 1. |Uppdatering 1 |Ja |Ja |
| 2 |Fabriksåterställning |I vissa fall, när du har utfört en fabriksåterställning StorSimple-enheten blev fastnat och visas det här meddelandet: **Återställ till factory pågår (fas 8)**. Det har inträffat om du har tryckt CTRL + C när cmdleten pågick. Den här buggen löses nu. |Uppdatering 1 |Ja |Nej |
| 3 |Fabriksåterställning |När en felaktig dubbel styrenhet fabriksåterställning, tilläts att fortsätta med enhetsregistrering. Detta resulterade i en konfiguration som inte stöds. Ett felmeddelande visas i uppdatering 1 och registreringen är blockerad på en enhet som har en misslyckad fabriksåterställning. |Uppdatering 1 |Ja |Nej |
| 4 |Fabriksåterställning |I vissa fall kan skapats falskt positivt matchningsfel aviseringar. Felaktig matchning av datatyp aviseringar genereras inte längre på enheter som kör uppdatering 1. |Uppdatering 1 |Ja |Nej |
| 5 |Fabriksåterställning |Om en fabriksåterställning avbröts innan den är slutförd, enheten i återställningsläge och tillåter inte dig att komma åt Windows PowerShell för StorSimple. Den här buggen löses nu. |Uppdatering 1 |Ja |Nej |
| 6 |Haveriberedskap |En disaster recovery (DR) bugg har åtgärdats där DR misslyckas under identifieringen av säkerhetskopior på målenheten. |Uppdatering 1 |Ja |Ja |
| 7 |Övervaka led: ar |I vissa fall övervakning led: ar på baksidan installationen inte visa rätt status. Blå LED har inaktiverats. DATA 0 och DATA 1 led: ar blinkande även när dessa gränssnitt har inte konfigurerats. Problemet har åtgärdats och övervaknings-led: ar indikerar nu rätt status. |Uppdatering 1 |Ja |Nej |
| 8 |Övervaka led: ar |I vissa fall, efter att ha tillämpat uppdatering 1 blå ljus på den aktiva kontrollenheten avstängd därmed blir det svårt att identifiera den aktiva kontrollenheten. Det här problemet har åtgärdats i den här patch-versionen. |Uppdatering 1.2 |Ja |Nej |
| 9 |Nätverksgränssnitt |I tidigare versioner av går en StorSimple-enhet som har konfigurerats med en icke-dirigerbara gateway offline. I den här versionen routning mått för Data 0 har gjort den lägsta; därför även om andra nätverksgränssnitt är moln-aktiverat kan kommer alla molntrafiken från enheten att dirigeras via Data 0. |Uppdatering 1 |Ja |Ja |
| 10 |Säkerhetskopior |En bugg i uppdatering 1 som gjorde att säkerhetskopieringen ska utföras efter 24 dagar har åtgärdats i patch-version 1.1 för uppdatering. |Uppdatera 1.1 |Ja |Ja |
| 11 |Säkerhetskopior |Ett fel i tidigare versioner resulterade i sämre prestanda för ögonblicksbilder av molnet med lite ändringar. Det här felet har åtgärdats i den här patch-versionen. |Uppdatering 1.2 |Ja |Ja |
| 12 |Uppdateringar |En bugg i uppdatering 1 som rapporterats av en misslyckad uppgradering och orsakade styrenheterna i återställningsläge, har åtgärdats i den här patch-versionen. |Uppdatering 1.2 |Ja |Ja |

## <a name="known-issues-in-update-12"></a>Kända problem i uppdatering 1.2
Följande tabell innehåller en sammanfattning av kända problem i den här versionen.

| Nej. | Funktion | Problem | Kommentarer/lösning | Gäller för fysisk enhet | Gäller för virtuella enheten |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk kvorum |I sällsynta fall om merparten av diskar i EBOD-höljet av en 8600-enhet är frånkopplad, vilket resulterar i ingen disk kvorum vara sedan lagringspoolen offline. Den förblir offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår, kontakta Microsoft Support angående nästa steg. |Ja |Nej |
| 2 |Felaktig controller-ID |När en domänkontrollant ersättning utförs kan kontrollenhet 0 visas som kontrollenhet 1. Under controller ersättning när avbildningen har lästs in från peer-noden kan controller-ID visas inledningsvis som kontrollanten peer-ID. I sällsynta fall kan ses detta även efter en omstart av systemet. |Ingen användaråtgärd krävs. Det här fallet kommer att åtgärdas automatiskt när domänkontrollanten ersättningen är klar. |Ja |Nej |
| 3 |Lagringskonton |Med Storage-tjänsten för att ta bort lagringskontot är ett scenario som inte stöds. Detta leder till en situation där användardata inte kan hämtas. |Ja |Ja | |
| 4 |Redundansväxling av enhet |Flera redundans av en volymbehållare från samma källenheten till olika målenheter stöds inte. Redundansväxling av enhet från en enda döda enhet till flera enheter blir volymbehållare på den första redundansväxlas enheten förlora dataägarskap. Efter en redundansväxling, ska dessa volymbehållare visas eller fungerar annorlunda mot när du visar dem i den klassiska Azure-portalen. | |Ja |Nej |
| 5 |Installation |Under StorSimple Adapter för SharePoint-installation måste du ange en IP-adress för enheten för att installationen ska slutföras. | |Ja |Nej |
| 6 |Webbproxy |Om din webbproxykonfigurationen har HTTPS som det angivna protokollet, din enhet-till-tjänst-kommunikation kommer att påverkas och enheten försätts offline. Supportpaket genereras även i den processen och förbruka avsevärda resurser på din enhet. |Se till att Webbadressen till webbproxy har HTTP som det angivna protokollet. Mer information finns i [Konfigurera en webbproxy för din enhet](storsimple-configure-web-proxy.md). |Ja |Nej |
| 7 |Webbproxy |Om du konfigurerar och aktiverar webbproxy på en registrerad enhet, måste du starta om den aktiva kontrollenheten på din enhet. | |Ja |Nej |
| 8 |Hög molnet fördröjning och hög i/o-arbetsbelastning |När din StorSimple-enhet påträffar en kombination av mycket hög molnet fördröjning (efter några sekunder) och hög i/o-arbetsbelastning, går du till enhetens volymer i ett degraderat tillstånd och I/o kan misslyckas med ett ”enheten är inte klar”-fel. |Du behöver att manuellt starta om styrenheterna eller utföra en redundansväxling av enhet att återställa från den här situationen. |Ja |Nej |
| 9 |Azure PowerShell |När du använder cmdleten StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - först 1 - vänta** att välja det första objektet så att du kan skapa en ny **VolumeContainer** objekt, cmdleten returnerar alla objekt. |Innesluta cmdleten inom parentes på följande sätt: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - först 1 - vänta** |Ja |Ja |
| 10 |Migrering |När flera volymbehållare skickas för migrering, stämmer ETA för senaste säkerhetskopiering endast för den första volymbehållaren. Dessutom startar parallella migreringen efter de första 4 säkerhetskopiorna i första volymcontainern har migrerats. |Vi rekommenderar att du migrerar en volymbehållare i taget. |Ja |Nej |
| 11 |Migrering |Efter återställningen läggs inte volymer till principen för säkerhetskopiering eller gruppen virtuell disk. |Du behöver att lägga till dessa volymer i en princip för säkerhetskopiering för att skapa säkerhetskopior. |Ja |Ja |
| 12 |Migrering |När migreringen är klar måste 5000/7000-serien enheten inte att komma åt de migrerade data-behållarna. |Vi rekommenderar att du tar bort behållare för migrerade data när migreringen är klar och Skickat. |Ja |Nej |
| 13 |Klona och DR |En StorSimple-enhet som kör uppdatering 1 kan inte klona eller utföra Haveriberedskap till en enhet som kör föruppdatering 1-programvara. |Behöver du uppdatera målenheten till uppdatering 1 för att tillåta dessa åtgärder |Ja |Ja |
| 14 |Migrering |Skapa säkerhetskopia för migrering kan misslyckas på en enhet i 5000-7000-serien när det finns volymgrupper med några associerade volymer. |Ta bort alla tomma volymgrupper med ingen tillhörande volymerna och försök sedan configuration-säkerhetskopiering. |Ja |Nej |

## <a name="physical-device-updates-in-update-12"></a>Uppdateringar av fysisk enhet i uppdatering 1.2
Om patch uppdatera 1.2 tillämpas på en fysisk enhet (som kör tidigare versioner än uppdatering 1), programvaruversionen ändras till 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Styrenhet och firmware-uppdateringar i uppdatering 1.2
Den här versionen uppdaterar drivrutinen och den inbyggda programvaran för disken på din enhet.

* Läs mer om SAS-kontrollenhetsuppdatering [uppdatering 1 för LSI SAS-enheter i Microsoft Azure StorSimple-installation](https://support.microsoft.com/kb/3043005). 
* Mer information om uppdateringen av disk-inbyggd programvara finns i [inbyggda programvaran för disken uppdatering 1 för Microsoft Azure StorSimple-enhet för](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Uppdateringar av virtuell enhet i uppdatering 1.2
Den här uppdateringen kan inte tillämpas på den virtuella enheten. Nya virtuella enheter måste skapas. 

## <a name="next-steps"></a>Nästa steg
* [Installera uppdatering 1.2 på din enhet](storsimple-install-update-1.md).

