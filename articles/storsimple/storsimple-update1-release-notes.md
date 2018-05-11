---
title: StorSimple 8000 Series uppdatering 1.2 viktig information | Microsoft Docs
description: Beskriver nya funktioner, problem och lösningar för StorSimple 8000 Series uppdatering 1.2.
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
ms.openlocfilehash: 67603c04cf7b5f45517d16620e5b79241aaae94d
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Uppdatera 1.2 viktig information för enheten StorSimple 8000-serien

## <a name="overview"></a>Översikt
Följande information beskriver de nya funktionerna och identifiera kritiska öppna problem för StorSimple 8000 Series uppdatering 1.2. De innehåller också en lista över StorSimple-programvara, drivrutiner och uppdateringar av inbyggd disk i den här versionen. 

Uppdatera 1.2 kan tillämpas på alla StorSimple-enhet som kör versionen (GA), uppdatering 0.1, uppdatering 0,2 eller uppdatering 0.3 programvara. Uppdatering 1.2 är inte tillgängligt om enheten kör uppdatering 1 eller uppdatering 1.1. Om enheten kör versionen (GA), [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md) att hjälpa dig med att installera uppdateringen.

I följande tabell visas enheten programvaruversioner som motsvarar uppdatering 1, 1.1 och 1.2.

| Om du kör uppdatering... | Det här är din enhet programvaruversionen. |
| --- | --- |
| Uppdatera 1.2 |6.3.9600.17584 |
| Uppdatera 1.1 |6.3.9600.17521 |
| Uppdatering 1.0 |6.3.9600.17491 |

Granska informationen som finns i viktig information innan du distribuerar uppdateringen i din StorSimple-lösning. Mer information finns i så här [installera uppdateringen 1.2 på din StorSimple-enhet](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * Det tar cirka 5-10 timmar att installera uppdateringen (inklusive uppdateringar för Windows). 
> * 1.2 har programvara, LSI drivrutinen och uppdateringar av inbyggd disk. För att installera, följ instruktionerna i [installera uppdateringen 1.2 på din StorSimple-enhet](storsimple-install-update-1.md).
> * För nya versioner, kan du inte se uppdateringar omedelbart eftersom vi gör en stegvis distribution av uppdateringar. Söker efter uppdateringar igen några dagar som dessa är snart tillgängligt.
> 
> 

## <a name="whats-new-in-update-12"></a>Vad är nytt i Update 1.2
Dessa funktioner publicerades först med uppdatering 1 som har gjorts tillgängliga för en begränsad uppsättning användare. De flesta användarna StorSimple skulle med Update 1.2-versionen finns följande nya funktioner och förbättringar:

* **Migrering från 5000 7000-serien 8000-serien enheter** – den här versionen finns en ny funktion för migrering som användarna i StorSimple 5000 7000-serien installation kan migrera sina data till en fysisk installation för StorSimple 8000-serien eller en virtuell installation. Funktionen för hårdlänksmigrering har två viktiga värderingsförslag:                                                                  
  
  * **Kontinuitet för företag**, genom att aktivera migrering av befintliga data på 5000 7000-serien installationer 8000-serien-enheterna.
  * **Förbättrad funktionen erbjudanden av utrustning i 8000-serien**, till exempel effektiv centraliserad hantering av flera installationer via StorSimple Manager-tjänsten, bättre maskinvaruklass och uppdatera inbyggd programvara, virtuella installationer, data mobility och funktioner i framtida Översikt.
    
    Referera till den [Migreringsguide](http://www.microsoft.com/download/details.aspx?id=47322) för information om hur du migrerar en virtuell StorSimple 5000 7000-serien till en serieenhet i 8000-. 
* **Tillgänglighet i Azure Portal för myndigheter** – StorSimple är nu tillgängligt i Azure Government-portalen. Se hur du [distribuera en virtuell StorSimple-enhet i Azure Portal för myndigheter](storsimple-deployment-walkthrough-gov.md).
* **Stöd för andra molntjänstleverantörer** – andra molntjänstleverantörer som stöds är Amazon S3, Amazon S3 med RRS, HP och OpenStack (beta).
* **Uppdatera till senaste lagring API: er** – med den här versionen, StorSimple har uppdaterats till den senaste Azure Storage service API: er. StorSimple 8000-serien enheter som körs före uppdatering 1 programvaruversioner (versionen 0,1 och 0,2 0,3) använder versioner av Azure Storage Service API: erna äldre än 17 juli 2009. Enligt informationen i den uppdaterade [meddelande om borttagning av Storage-tjänstversioner](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), genom att 1 augusti 2016 dessa API: er att bli inaktuell. Det är viktigt att du tillämpar StorSimple 8000 Series uppdatering 1 före den 1 augusti 2016. Om du inte gör stoppa StorSimple-enheter fungerar korrekt.
* **Stöd för zonen Redundant lagring (ZRS)** – med uppgraderingen till den senaste versionen av API: er för lagring, StorSimple 8000-serien stöder zonen Redundant lagring (ZRS) förutom lokalt Redundant lagring (LRS) och Geo-redundant lagring (GRS ). Referera till den här [artikel på Azure Storage redundansalternativ](../storage/common/storage-redundancy.md) ZRS information.
* **Förbättrad inledande distributionen och uppdatera experience** – i den här versionen, installation och uppdatera processer har förbättrats. Installationen via installationsguiden har förbättrats för att ge feedback till användaren om nätverkskonfigurationen och brandväggsinställningar är felaktig. Ytterligare diagnostiska cmdlets har angetts som hjälper dig med felsökning av nätverk för enheten. Finns det [distribution artikeln Felsöka](storsimple-troubleshoot-deployment.md) för mer information om nya diagnostiska cmdlets som används för felsökning.

## <a name="issues-fixed-in-update-12"></a>Problem som åtgärdas i uppdateringen 1.2
Följande tabell innehåller en översikt över problem som har korrigerats i uppdateringar 1.2, 1.1 och 1.    

| Nej. | Funktion | Problem | Korrigeras i uppdateringen | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell för StorSimple |När en användare fjärransluta till StorSimple-enhet med hjälp av Windows PowerShell för StorSimple och sedan startas installationsguiden, uppstod en krasch så snart Data 0 IP har indata. Det här programfelet är nu fast i uppdatering 1. |Uppdatering 1 |Ja |Ja |
| 2 |Fabriksåterställning |I vissa fall, när du har utfört en fabriksåterställning StorSimple-enhet blivit fastnat och visas det här meddelandet: **återställning till fabriksinställningarna pågår (fas 8)**. Detta har hänt om trycks ned CTRL + C när cmdleten pågick. Det här problemet löses nu. |Uppdatering 1 |Ja |Nej |
| 3 |Fabriksåterställning |Efter en misslyckad dubbla domänkontrollant fabrik återställts tilläts att fortsätta med enhetsregistrering. Detta resulterade i en konfiguration som inte stöds. Ett felmeddelande visas i uppdatering 1 och registrering är blockerad på en enhet som har ett felaktigt fabriksåterställa. |Uppdatering 1 |Ja |Nej |
| 4 |Fabriksåterställning |I vissa fall höjdes falska positiva matchningsfel aviseringar. Felaktig matchning aviseringar genereras inte längre på enheter som kör uppdatering 1. |Uppdatering 1 |Ja |Nej |
| 5 |Fabriksåterställning |Om en fabriksåterställning avbröts innan slutförande enheten i återställningsläge och tillåter inte att du kan komma åt Windows PowerShell för StorSimple. Det här problemet löses nu. |Uppdatering 1 |Ja |Nej |
| 6 |Haveriberedskap |Ett programfel för disaster recovery (DR) åtgärdades där DR misslyckas under identifieringen av säkerhetskopieringar på målenheten. |Uppdatering 1 |Ja |Ja |
| 7 |Övervaka indikatorer |I vissa fall övervakning indikatorer på baksidan installation anger inte längre rätt status. Den blå Indikatorn har varit avstängd. DATA 0 och DATA 1 led blinkande även när dessa gränssnitt har inte konfigurerats. Problemet har åtgärdats och övervakning indikatorer visar nu rätt status. |Uppdatering 1 |Ja |Nej |
| 8 |Övervaka indikatorer |I vissa fall när uppdatering 1 blå ljust på den aktiva styrenheten avstängd vilket gör det svårt att identifiera den aktiva styrenheten. Det här problemet har korrigerats i den här versionen av korrigering. |Uppdatera 1.2 |Ja |Nej |
| 9 |Nätverksgränssnitt |I tidigare versioner av går en virtuell StorSimple-enhet som har konfigurerats med en icke-dirigerbara gateway offline. I den här versionen routning mått för Data 0 har gjorts de lägsta; även om andra nätverksgränssnitt moln-aktiverat, dirigeras därför molnet trafik från enheten via Data 0. |Uppdatering 1 |Ja |Ja |
| 10 |Säkerhetskopior |Ett fel i uppdatering 1 som orsakade säkerhetskopieringar misslyckas efter 24 dagar har korrigerats i utgåvan av korrigering uppdatering 1.1. |Uppdatera 1.1 |Ja |Ja |
| 11 |Säkerhetskopior |Ett fel i tidigare versioner resulterade i sämre prestanda för molnögonblicksbilder med låg ändra priser. Det här felet har korrigerats i den här versionen av korrigering. |Uppdatera 1.2 |Ja |Ja |
| 12 |Uppdateringar |Ett fel i uppdatering 1 som rapporterats av en misslyckad uppgradering och gjorde domänkontrollanter i återställningsläge, har korrigerats i den här versionen av korrigering. |Uppdatera 1.2 |Ja |Ja |

## <a name="known-issues-in-update-12"></a>Kända problem i Update 1.2
Följande tabell innehåller en översikt över kända problem i den här versionen.

| Nej. | Funktion | Problem | Kommentarer/lösning | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk kvorum |I sällsynta fall om merparten av diskar i EBOD höljet av en 8600-enhet är frånkopplad ledde till Ingen disk i kvorum kommer sedan lagringspoolen vara offline. Den förblir offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår, kontakta Microsoft Support för nästa steg. |Ja |Nej |
| 2 |Felaktig styrenhets-ID |När en domänkontrollant ersättning utförs kan styrenhet 0 visas som kontrollant 1. Under controller ersättning, när avbildningen har lästs in från peer-noden kan styrenhets-ID visas först som peer-styrenhet-ID. I sällsynta fall kan problemet visas efter en omstart. |Ingen användaråtgärd krävs. Den här situationen ska lösas av sig självt när domänkontrollanten ersättningen är klar. |Ja |Nej |
| 3 |Lagringskonton |Använder Storage-tjänsten för att ta bort lagringskontot är ett scenario som inte stöds. Detta leder till en situation där användardata inte kan hämtas. |Ja |Ja | |
| 4 |Enheten växling vid fel |Flera redundans av en volymbehållare från samma källa enheten till olika målenheter stöds inte. Enheten växling från en inaktiv enhet till flera enheter gör volymbehållarna på den första redundansväxlats enhet förlorar dataägarskap. Efter en växling, ska behållarna volymen visas eller fungera annorlunda när de visas i den klassiska Azure-portalen. | |Ja |Nej |
| 5 |Installation |Under StorSimple-kort för SharePoint-installation måste du ange en IP-adress för enheten för att slutföra installationen. | |Ja |Nej |
| 6 |Webbproxy |Om din webbproxykonfigurationen har det angivna protokollet HTTPS, enhet-till-tjänst-kommunikation kommer att påverkas och enheten ska försättas i offlineläge. Stöd för paket skapas också i process förbrukar betydande resurser på enheten. |Kontrollera att URL: en för web proxy har det angivna protokollet HTTP. Mer information finns i [Konfigurera en webbproxy för din enhet](storsimple-configure-web-proxy.md). |Ja |Nej |
| 7 |Webbproxy |Om du konfigurerar och aktiverar webbproxy på en registrerad enhet, måste du starta om den aktiva styrenheten på enheten. | |Ja |Nej |
| 8 |Hög molnet latens och hög i/o-arbetsbelastning |När din StorSimple-enhet påträffar en kombination av mycket hög molnet latens (ordning sekunder) och höga i/o-arbetsbelastning, enheten volymer som ingår i ett degraderat tillstånd och I/o kan misslyckas med felet ”enheten är inte klar”. |Du måste starta om styrenheterna eller utför en växling vid fel med enheten för att återställa från den här situationen manuellt. |Ja |Nej |
| 9 |Azure PowerShell |När du använder cmdleten StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - först 1 - vänta** du väljer det första objektet så att du kan skapa en ny **VolumeContainer** objektet, cmdlet returnerar alla objekt. |Omsluta cmdlet inom parentes på följande sätt: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - första 1 - vänta** |Ja |Ja |
| 10 |Migrering |När flera volymbehållare skickas för migrering är ETA för senaste säkerhetskopian exakt endast för den första volymbehållaren. Dessutom startas parallella migrering efter de första 4 säkerhetskopiorna i första volymbehållare migreras. |Vi rekommenderar att du migrerar en volymbehållare i taget. |Ja |Nej |
| 11 |Migrering |Efter återställningen läggs inte volymer till principen för säkerhetskopiering eller gruppen virtuell disk. |Du behöver lägga till dessa volymer till en princip för säkerhetskopiering för att skapa säkerhetskopior. |Ja |Ja |
| 12 |Migrering |När migreringen är klar, måste enheten 5000/7000-serien inte komma åt behållarna migrerade data. |Vi rekommenderar att du tar bort migrerade databehållare när migreringen har slutförts och bekräftats. |Ja |Nej |
| 13 |Klona och Katastrofåterställning |En StorSimple-enhet som kör uppdatering 1 kan inte klona eller utföra en återställning till en enhet med före uppdateringen 1 programvara. |Du behöver uppdatera målenheten uppdatering 1 för att tillåta dessa åtgärder |Ja |Ja |
| 14 |Migrering |Konfigurationssäkerhetskopia för migrering kan misslyckas på en serieenhet för 5000 7000-när volymen grupper med inga associerade volymer. |Ta bort alla tomma volym grupper med inga associerade volymer och försök sedan säkerhetskopian av konfigurationen. |Ja |Nej |

## <a name="physical-device-updates-in-update-12"></a>Fysisk enhetsuppdateringar i Update 1.2
Om korrigering uppdatera 1.2 tillämpas på en fysisk enhet (som kör tidigare versioner än uppdatering 1), programvaruversionen ändras till 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Domänkontrollanten och firmware-uppdateringar i Update 1.2
Den här versionen uppdaterar drivrutinen och disk inbyggd programvara på din enhet.

* Mer information om SAS-styrenhet uppdateringen finns [uppdatering 1 för LSI SAS-enheter i Microsoft Azure StorSimple-enhet](https://support.microsoft.com/kb/3043005). 
* Läs mer om disken firmware-uppdatering [Disk firmware uppdatering 1 för Microsoft Azure StorSimple-enhet](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Virtuell enhetsuppdateringar i Update 1.2
Den här uppdateringen kan inte tillämpas på den virtuella enheten. Nya virtuella enheter måste skapas. 

## <a name="next-steps"></a>Nästa steg
* [Installera uppdatering 1.2 på din enhet](storsimple-install-update-1.md).

