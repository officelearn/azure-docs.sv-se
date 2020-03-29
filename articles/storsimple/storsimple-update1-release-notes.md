---
title: StorSimple 8000-serien Uppdatering 1,2 viktig information | Microsoft-dokument
description: Beskriver de nya funktionerna, problemen och lösningarna för StorSimple 8000 Series Update 1.2.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60530999"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Uppdatera 1.2-viktig information för din StorSimple 8000-serieenhet

## <a name="overview"></a>Översikt
Följande viktig information beskriver de nya funktionerna och identifierar de kritiska öppna problemen för StorSimple 8000 Series Update 1.2. De innehåller också en lista över StorSimple programvara, drivrutin och disk firmware uppdateringar som ingår i denna utgåva. 

Uppdatering 1.2 kan tillämpas på alla StorSimple-enheter som kör Ga (Release), Uppdatering 0.1, Uppdatering 0.2 eller Programvara för uppdatering 0.3. Uppdatering 1.2 är inte tillgänglig om enheten kör Uppdatering 1 eller Uppdatering 1.1. Om din enhet kör Release (GA) kontaktar du [Microsoft Support](storsimple-contact-microsoft-support.md) för att hjälpa dig att installera den här uppdateringen.

I följande tabell visas de enhetsprogramversioner som motsvarar uppdateringarna 1, 1.1 och 1.2.

| Om du kör uppdatering ... | Detta är din enhetsprogramversion. |
| --- | --- |
| Uppdatering 1.2 |6.3.9600.17584 |
| Uppdatering 1.1 |6.3.9600.17521 |
| Uppdatering 1.0 |6.3.9600.17491 |

Läs informationen i viktig information innan du distribuerar uppdateringen i StorSimple-lösningen. Mer information finns i hur du [installerar uppdatering 1.2 på StorSimple-enheten](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * Det tar ungefär 5-10 timmar att installera den här uppdateringen (inklusive Windows-uppdateringar). 
> * Uppdatering 1.2 har uppdateringar av programvara, LSI-drivrutin och diskprogramvara. Om du vill installera följer du instruktionerna i [installation av uppdatering 1.2 på StorSimple-enheten](storsimple-install-update-1.md).
> * För nya versioner kanske du inte ser uppdateringar omedelbart eftersom vi gör en stegvis distribution av uppdateringarna. Sök efter uppdateringar om några dagar igen eftersom dessa kommer att bli tillgängliga snart.
> 
> 

## <a name="whats-new-in-update-12"></a>Nyheter i uppdatering 1.2
Dessa funktioner släpptes först med uppdatering 1 som gjordes tillgängliga för en begränsad uppsättning användare. Med uppdateringen 1.2-versionen skulle de flesta StorSimple-användare se följande nya funktioner och förbättringar:

* **Migrering från enheter i 5 000-7000-serien till 8 000-serien** – Den här versionen introducerar en ny migreringsfunktion som gör att storsimple 5000-7000-seriens apparatanvändare kan migrera sina data till en fysisk storsimple-8000-serie eller en virtuell installation. Migreringsfunktionen har två nyckelvärdeserbjudanden:                                                                  
  
  * **Kontinuitet i verksamheten**, genom att möjliggöra migrering av befintliga data på 5000-7000-seriens apparater till 8000-seriens apparater.
  * **Förbättrade funktionserbjudanden för 8000-seriens apparater,** till exempel effektiv centraliserad hantering av flera apparater via StorSimple Manager-tjänsten, bättre maskinvaruklass och uppdaterad firmware, virtuella apparater, datamobilitet och funktioner i den framtida färdplanen.
    
    Mer information om hur du migrerar en StorSimple 5000-7000-serie till en enhet i 8000-serien finns i [migreringsguiden.](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) 
* **Tillgänglighet i Azure Government Portal** – StorSimple är nu tillgängligt i Azure Government-portalen. Se hur [du distribuerar en StorSimple-enhet i Azure Government Portal](storsimple-deployment-walkthrough-gov.md).
* **Stöd för andra molntjänstleverantörer** – De andra molntjänstleverantörerna som stöds är Amazon S3, Amazon S3 med RRS, HP och OpenStack (beta).
* **Uppdatering till senaste lagrings-API:er** – Med den här versionen har StorSimple uppdaterats till de senaste Azure Storage-tjänst-API:erna. StorSimple 8000-seriens enheter som kör programversioner för uppdatering 1 (Release, 0.1, 0.2 och 0.3) använder versioner av Azure Storage Service API:er som är äldre än den 17 juli 2009. Som anges i det uppdaterade [meddelandet om borttagning av lagringstjänstversioner](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), senast den 1 augusti 2016, kommer dessa API:er att vara inaktuella. Det är absolut nödvändigt att du tillämpar StorSimple 8000 Series Update 1 före den 1 augusti 2016. Om du inte gör det slutar StorSimple-enheter att fungera korrekt.
* **Stöd för zon redundant lagring (ZRS)** – Med uppgraderingen till den senaste versionen av lagrings-API:erna stöder StorSimple 8000-serien Zon redundant lagring (ZRS) utöver lokalt redundant lagring (LRS) och geoupplösent lagring (GRS). Se den här [artikeln om Azure Storage redundansalternativ](../storage/common/storage-redundancy.md) för ZRS-information.
* **Förbättrad inledande distribution och uppdateringsupplevelse** – I den här versionen har installations- och uppdateringsprocesserna förbättrats. Installationen via installationsguiden förbättras så att den ger feedback till användaren om nätverkskonfigurationen och brandväggsinställningarna är felaktiga. Ytterligare diagnostiska cmdlets har tillhandahållits för att hjälpa dig med felsökning av nätverk av enheten. Mer information om de nya diagnostik-cmdlets som används för felsökning finns i artikeln för felsökning av [distributionen.](storsimple-troubleshoot-deployment.md)

## <a name="issues-fixed-in-update-12"></a>Problem som åtgärdats i uppdatering 1.2
I följande tabell finns en sammanfattning av problem som åtgärdats i uppdateringarna 1.2, 1.1 och 1.    

| Nej. | Funktion | Problem | Fast i uppdatering | Gäller för fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell för StorSimple |När en användare fjärranslutit till StorSimple-enheten med hjälp av Windows PowerShell för StorSimple och sedan startade installationsguiden inträffade en krasch så snart data 0 IP har matats in. Det här felet har nu åtgärdats i uppdatering 1. |Uppdatering 1 |Ja |Ja |
| 2 |Fabriksåterställning |I vissa fall, när du utförde en fabriksåterställning, fastnade StorSimple-enheten och visade det här meddelandet: **Återställ till fabrik pågår (fas 8)**. Detta hände om du tryckte på CTRL+C medan cmdleten pågick. Denna bugg är nu fast. |Uppdatering 1 |Ja |Inga |
| 3 |Fabriksåterställning |Efter en misslyckad fabriksåterställning med dubbla styrenheter tilläts du fortsätta med enhetsregistreringen. Detta resulterade i en systemkonfiguration som inte stöds. I uppdatering 1 visas ett felmeddelande och registreringen blockeras på en enhet som har en misslyckad fabriksåterställning. |Uppdatering 1 |Ja |Inga |
| 4 |Fabriksåterställning |I vissa fall har falska positiva aviseringar som inte stämmer överens tagits upp. Felaktiga aviseringar för felmatchning genereras inte längre på enheter som kör uppdatering 1. |Uppdatering 1 |Ja |Inga |
| 5 |Fabriksåterställning |Om en fabriksåterställning avbröts innan den slutfördes gick enheten in i återställningsläge och du kunde inte komma åt Windows PowerShell för StorSimple. Denna bugg är nu fast. |Uppdatering 1 |Ja |Inga |
| 6 |Haveriberedskap |Ett DR-fel (Disaster Recovery) åtgärdades där DR skulle misslyckas under identifieringen av säkerhetskopior på målenheten. |Uppdatering 1 |Ja |Ja |
| 7 |Övervakning av lysdioder |I vissa fall visade övervakningslysdioderna på apparatens baksida inte korrekt status. Den blå lysdioden var avstängd. DATA 0 och DATA 1 lysdioder blinkade även när dessa gränssnitt inte konfigurerades. Problemet har åtgärdats och övervakningslysdioder anger nu rätt status. |Uppdatering 1 |Ja |Inga |
| 8 |Övervakning av lysdioder |I vissa fall, efter applicering av uppdatering 1, den blå lampan på den aktiva styrenheten stängs av vilket gör det svårt att identifiera den aktiva styrenheten. Det här problemet har åtgärdats i den här korrigeringsversionen. |Uppdatering 1.2 |Ja |Inga |
| 9 |Nätverksgränssnitt |I tidigare versioner kan en StorSimple-enhet som konfigurerats med en icke-dirigerbar gateway kopplas från. I den här versionen har routningsmåttet för data 0 gjorts till det lägsta. Därför, även om andra nätverksgränssnitt är molnaktiverade, dirigeras all molntrafik från enheten via Data 0. |Uppdatering 1 |Ja |Ja |
| 10 |Säkerhetskopior |Ett fel i uppdatering 1 som orsakade säkerhetskopior att misslyckas efter 24 dagar har åtgärdats i patch release Update 1.1. |Uppdatering 1.1 |Ja |Ja |
| 11 |Säkerhetskopior |Ett fel i tidigare versioner resulterade i dåliga prestanda för ögonblicksbilder av molnet med låg förändringsfrekvens. Den här buggen har åtgärdats i den här korrigeringsversionen. |Uppdatering 1.2 |Ja |Ja |
| 12 |Uppdateringar |Ett fel i uppdatering 1 som rapporterade en misslyckad uppgradering och orsakade att styrenheterna gick in i återställningsläge har åtgärdats i den här korrigeringsversionen. |Uppdatering 1.2 |Ja |Ja |

## <a name="known-issues-in-update-12"></a>Kända problem i uppdatering 1.2
I följande tabell finns en sammanfattning av kända problem i den här versionen.

| Nej. | Funktion | Problem | Kommentarer/lösning | Gäller för fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- | --- |
| 1 |Diskkvorum |I sällsynta fall, om majoriteten av diskarna i EBOD-höljet för en 8600-enhet kopplas från, vilket resulterar i inget diskkvorum, kommer lagringspoolen att vara offline. Det kommer att förbli offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår kontaktar du Microsoft Support för nästa steg. |Ja |Inga |
| 2 |Felaktigt styrenhets-ID |När en handkontroll byts ut kan styrenheten 0 visas som controller 1. När avbildningen läses in från peer-noden kan styrenhetens ID visas från början som peer-styrenhetens ID. I sällsynta fall kan detta beteende också ses efter en omstart av systemet. |Ingen användaråtgärd krävs. Den här situationen löser sig när styrenhetens utbyte är klar. |Ja |Inga |
| 3 |Lagringskonton |Att använda lagringstjänsten för att ta bort lagringskontot är ett scenario som inte stöds. Detta leder till en situation där användardata inte kan hämtas. |Ja |Ja | |
| 4 |Redundans för enheten |Flera redundanser av en volymbehållare från samma källenhet till olika målenheter stöds inte. Enhetsundansväxling från en enda död enhet till flera enheter gör att volymbehållarna på den första misslyckade över enheten förlorar dataägarskap. Efter en sådan redundans visas dessa volymbehållare eller fungerar annorlunda när du visar dem i den klassiska Azure-portalen. | |Ja |Inga |
| 5 |Installation |Under StorSimple-adaptern för SharePoint-installation måste du ange en enhets-IP för att installationen ska slutföras. | |Ja |Inga |
| 6 |Webbproxy |Om webbproxykonfigurationen har HTTPS som angivet protokoll påverkas kommunikationen mellan enheter och tjänsten och enheten kopplas från. Supportpaket genereras också i processen och förbrukar betydande resurser på din enhet. |Kontrollera att webbproxy-URL:en har HTTP som angivet protokoll. Mer information finns i [Konfigurera en webbproxy för din enhet](storsimple-configure-web-proxy.md). |Ja |Inga |
| 7 |Webbproxy |Om du konfigurerar och aktiverar webbproxy på en registrerad enhet måste du starta om den aktiva handkontrollen på enheten. | |Ja |Inga |
| 8 |Hög molnfördröjning och hög I/O-arbetsbelastning |När din StorSimple-enhet stöter på en kombination av mycket höga molnfördyningar (sekunders ordning) och hög I/O-arbetsbelastning hamnar enhetsvolymerna i försämrat tillstånd och I/O kan misslyckas med ett "enhet som inte är redo"-fel. |Du måste starta om enhetskontrollanterna manuellt eller utföra en enhetsväxling för att återställa från den här situationen. |Ja |Inga |
| 9 |Azure PowerShell |När du använder StorSimple cmdlet **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** för att markera det första objektet så att du kan skapa ett nytt **VolumeContainer-objekt,** returnerar cmdlet alla objekt. |Radbryta cmdlet inom parentes enligt följande: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Ja |Ja |
| 10 |Migrering |När flera volymbehållare skickas för migrering är ETA för senaste säkerhetskopieringen endast korrekt för den första volymbehållaren. Dessutom startar parallell migrering efter att de första 4 säkerhetskopiorna i den första volymbehållaren har migrerats. |Vi rekommenderar att du migrerar en volymbehållare i taget. |Ja |Inga |
| 11 |Migrering |Efter återställningen läggs volymer inte till i säkerhetskopieringsprincipen eller den virtuella diskgruppen. |Du måste lägga till dessa volymer i en princip för säkerhetskopiering för att kunna skapa säkerhetskopior. |Ja |Ja |
| 12 |Migrering |När migreringen är klar får enheten i 5000/7000-serien inte komma åt de migrerade databehållarna. |Vi rekommenderar att du tar bort de migrerade databehållarna när migreringen är klar och har bekräftats. |Ja |Inga |
| 13 |Klon och DR |En StorSimple-enhet som kör Uppdatering 1 kan inte klona eller utföra haveriberedskap till en enhet som kör föruppdatering 1 programvara. |Du måste uppdatera målenheten till uppdatering 1 för att dessa åtgärder ska kunna |Ja |Ja |
| 14 |Migrering |Säkerhetskopiering av konfiguration för migrering kan misslyckas på en enhet i 5000-7000-serien när det finns volymgrupper utan associerade volymer. |Ta bort alla tomma volymgrupper utan associerade volymer och försök sedan igen med säkerhetskopieringen av konfigurationen. |Ja |Inga |

## <a name="physical-device-updates-in-update-12"></a>Uppdateringar av fysiska enheter i uppdatering 1.2
Om korrigeringsuppdatering 1.2 tillämpas på en fysisk enhet (som kör versioner före uppdatering 1) ändras programvaruversionen till 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Uppdateringar av styrenhet och inbyggd programvara i uppdatering 1.2
Den här versionen uppdaterar drivrutinen och den inbyggda programvaran för disken på enheten.

* Mer information om SAS-styrenhetsuppdateringen finns i [Uppdatering 1 för LSI SAS-styrenheter i Microsoft Azure StorSimple Appliance](https://support.microsoft.com/kb/3043005). 
* Mer information om uppdateringen av den inbyggda programvaran för disk finns i [Uppdatering 1 för disken för Microsoft Azure StorSimple Appliance](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Uppdateringar av virtuella enheter i uppdatering 1.2
Den här uppdateringen kan inte tillämpas på den virtuella enheten. Nya virtuella enheter måste skapas. 

## <a name="next-steps"></a>Nästa steg
* [Installera uppdatering 1.2 på enheten](storsimple-install-update-1.md).

