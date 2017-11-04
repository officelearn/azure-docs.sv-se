---
title: StorSimple 8000 uppdatering 0.1 viktig information | Microsoft Docs
description: "Beskriver nya funktioner och korrigeringar, öppna problem och tillgängliga lösningar för oktober 2014 Microsoft Azure StorSimple version (uppdatering 0.1)."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: fd35e3c3-4770-460c-999d-f72ab7053a20
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: 4dfd3973593a94adfc15a6e15d69c697e13998af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-update-01-release-notes--october-2014"></a>StorSimple 8000 uppdatering 0.1 viktig information – oktober 2014
## <a name="overview"></a>Översikt
I följande versionsinformation identifiera kritiska öppna problem för StorSimple 8000 uppdatering 0.1 ut i oktober 2014. De innehåller också en lista över StorSimple programvara och inbyggd programvara uppdateringar som ingår i den här versionen. Det här är den första versionen när StorSimple 8000-serien släpper version gjordes allmänt tillgänglig i juli 2014 och motsvarar programvaruversion 6.3.9600.17312.  

Vi rekommenderar att du söka efter och installera tillgängliga uppdateringar omedelbart efter installation av enheten. Du kan också aktivera Automatiska uppdateringar för att hämta och installera viktiga uppdateringar från Microsoft så snart de blir tillgängliga. Mer information finns i så här [uppdatera din StorSimple-enhet](storsimple-update-device.md).  

Granska informationen som finns i viktig information innan du kan distribuera uppdateringar i din StorSimple-lösning.  

> [!IMPORTANT]
> * Använda StorSimple Manager-tjänsten och inte Windows PowerShell för StorSimple oktober uppdateringarna ska installeras.  
> * Uppdateringarna tar normalt ca 3 timmar för att slutföra.  
> * Oktober-versionen av StorSimple innehåller inte några uppdateringar till den virtuella StorSimple-enheten. Du kan använda alla tillgängliga Windows-uppdateringar, inklusive de senaste säkerhetskorrigeringar, men en ändring i versionen för den virtuella enheten visas inte.  
> 
> 

Kontrollera att följande krav är uppfyllda innan du uppdaterar din StorSimple-enhet.  

* Se till att båda styrenheter körs innan du söker efter uppdateringar. Sökningen misslyckas om antingen domänkontrollanten körs. Kontrollera att domänkontrollanterna är i felfritt tillstånd, gå till **maskinvarustatus** under den **Underhåll** sidan. Om det finns komponenter som **behöver åtgärdas**, kontakta Microsoft Support innan du fortsätter.  
* Kontrollera att fasta IP-adresser för både styrenhet 0 och 1 är dirigerbara och kan ansluta till Internet som används för att underhålla uppdateringarna till enheten. Du kan använda den [Test-Connection cmdlet](https://technet.microsoft.com/library/hh849808.aspx) att pinga en känd adress utanför nätverket, till exempel outlook.com, kontrollera att kontrollanten är ansluten till det externa nätverket.  
* Se till att de utgående portarna som krävs är tillgängliga på din StorSimple-enhet för utgående kommunikation. Mer information finns i [nätverkskrav för din StorSimple-enhet](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).  
* Om enheten programvaruversionen är äldre än 6.3.9600.17312 (oktober 2014-uppdatering), inaktivera Data 2 och Data 3 portar, om aktiverad, innan du startar uppdateringen. Om du lämnar Data 2 och Data 3 portar aktiverad när du kan uppdatera, kan det orsaka styrenheten för enheten att i återställningsläge. Observera att när du inaktiverar nätverksgränssnitten associerade volymer försätts i offlineläge och läsning/skrivning kommer att avbrytas under uppdateringen.  

## <a name="whats-new-in-the-october-release"></a>Vad är nytt i oktober-versionen
Den här uppdateringen innehåller följande förbättringar:

* Du kan nu använda StorSimple Manager-tjänsten Användargränssnittet för att hantera din styrenheter. Hanteringen åtgärderna är bland annat starta om avslutning, eller aktivera en domänkontrollant. Mer information finns på [hantera StorSimple-styrenheter](storsimple-manage-device-controller.md).  
* Du kan schemalägga WAN bandbreddsallokering enligt kombinationer av dag veckan och tid på dagen. På så sätt kan du se bättre användning av WAN-bandbredden vid låg belastning. Mallar för olika bandbredd tillåts för olika volymbehållare. Mer information finns på [hantera dina StorSimple bandbredd mallar](storsimple-manage-bandwidth-templates.md).  
* Du kan konfigurera e-postaviseringar proaktivt meddela administratörer och andra befintliga eller eventuellt kommande problem. Mer information finns på [konfigurera aviseringsinställningar](storsimple-manage-alerts.md#configure-alert-settings).  

## <a name="issues-fixed-in-the-october-release"></a>Problem som åtgärdas i oktober-versionen
Följande tabell innehåller en översikt över problem som har lösts i den här uppdateringen.  

| Nej. | Funktion | Problem | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- |
| 1 |Nätverksgränssnitt |I den tidigare versionen har växlats nätverksgränssnitten DATA 2 och DATA 3 i programvaran. Problemet har åtgärdats i den här uppdateringen. Ta bort inställningarna och inaktivera dessa nätverksgränssnitt innan du installerar uppdateringen. Efter installationen av uppdateringen behöver du konfigurera om dessa gränssnitt. |Ja |Nej |
| 2 |Support-paket |I den tidigare versionen om du har kört Windows PowerShell **Export HcsSupportPackage** loggar för att hämta Baseboard Management Controller (BMC), åtgärden misslyckades med följande varning: ”åtgärden har slutförts på den här domänkontrollanten, men misslyckades på peer-styrenheten på grund av följande fel. Kontrollera om peer-datorn är felfri och om den aktuella noden kan ansluta till peer-datorn ”. Det här problemet löses nu. |Ja |Nej |
| 3 |Enheten växling vid fel |I den tidigare versionen fanns det en risk för data om en **identifiera säkerhetskopiering** jobb som misslyckades under en växling vid fel för enheten. Det här problemet löses nu. |Ja |Nej |
| 4 |Enheten växling vid fel |Säkerhetskopieringar var synliga i den tidigare versionen efter en växling på enheten, men associerade volymbehållaren fanns inte på målenheten. Det här problemet löses nu. |Ja |Nej |
| 5 |Enheten växling vid fel |I den tidigare versionen uppstod ett fel i uppräkningen säkerhetskopieringar i molnet under registret-återställningen som potentiellt kan leda till inkonsekventa data om det fanns molnet anslutningsproblem. |Ja |Nej |
| 6 |Firmware-uppdatering |I den tidigare versionen enhetens inbyggda programvara Uppdateringsjobbet misslyckades och visas ett fel som anges cmdlets inte var går att känna igen och att uppdateringen misslyckades som ett resultat. Har gått sedan domänkontrollanten i återställningsläge. Det här problemet löses nu. |Ja |Nej |
| 7 |Installation |Fel som beror på enheten inte som har avbildats korrekt under installationen har nu korrigerats. |Ja |Nej |
| 8 |Fabriksåterställning |Du kan nu om du vill hoppa över kontrollen av inbyggd programvara för fabriksåterställning. Detta är en förändring från den tidigare versionen. |Ja |Nej |
| 9 |Fabriksåterställning |I den tidigare versionen när en cmdlet för återställning av fabriken kördes har inbyggd programvara version kontroller gjorts endast för vissa komponenter. Ytterligare inbyggd programvara kontrollerar har gjorts efter den första omstarten i processen, vilket kan leda till återställningen ska misslyckas. Den här snabbkorrigeringen säkerställer att alla inbyggda programvaran görs en kontroll när fabriksåterställa cmdlet körs och före det första systemet att starta om. |Ja |Nej |
| 10 |Storage-konto viktiga rotation |Den **Invoke-HcsmServiceDataEncryptionKeyChange** cmdlet som används för att rotera lagringskontonycklarna nu uppmanar användaren att ange krypteringsnyckeln för tjänstdata. Detta är en förändring från den tidigare versionen som krypteringsnyckel för tjänstdata skickades som en infogad-parameter. |Ja |Nej |
| 11 |Återställning efter fel inom 24 timmar |Vid katastrofåterställning utfördes Rensa på på källenheten inte korrekt, vilket ledde återställning misslyckas. Problemet har åtgärdats i den här versionen. |Ja |Nej |

## <a name="known-issues-in-the-october-release"></a>Kända problem i oktober-versionen
Följande tabell innehåller en översikt över kända problem i den här versionen.

| Nej. | Funktion | Problem | Kommentarer/lösning | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- | --- |
| 1 |Fabriksåterställning |I vissa fall, när du utför en fabriksåterställning StorSimple-enhet kan ha fastnat och visa det här meddelandet: **återställning till fabriksinställningarna pågår (fas 8)**. Detta händer om du trycker på CTRL + C när cmdleten pågår. |Tryck inte på CTRL + C när du initierar en fabriksåterställning. Om du redan är i det här tillståndet kan kontakta Microsoft Support för nästa steg. |Ja |Nej |
| 2 |Fabriksåterställning |Gör inte fabriksåterställning en StorSimple-enhet som har uppdaterats från GA till oktober 2014 versionen. |Den här åtgärden fungerar endast om en korrigering är installerad. Kontakta Microsoft Support för att få korrigeringsfilen krävs. |Ja |Nej |
| 3 |Disk kvorum |I sällsynta fall om merparten av diskar i EBOD höljet av en 8600-enhet är frånkopplad ledde till Ingen disk i kvorum kommer sedan lagringspoolen vara offline. Den förblir offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår, kontakta Microsoft Support för nästa steg. |Ja |Nej |
| 4 |Fel i moln ögonblicksbild |I sällsynta fall kan en ögonblicksbild i molnet kan misslyckas med fel **säkerhetskopiering maxgräns uppnåtts**. Det här inträffar om du överskrider 255 online kloner på samma enhet från samma ursprungliga volym som har tagits bort. | |Ja |Ja |
| 5 |Felaktig styrenhets-ID |När en domänkontrollant ersättning utförs kan styrenhet 0 visas som kontrollant 1. Under controller ersättning, när avbildningen har lästs in från peer-noden kan styrenhets-ID visas först som peer-styrenhet-ID. I sällsynta fall kan problemet visas efter en omstart. |Ingen användaråtgärd krävs. Den här situationen ska lösas av sig självt när domänkontrollanten ersättningen är klar. |Ja |Nej |
| 6 |Enheten övervakning diagram |Övervakning diagrammen enheten fungerar inte när grundläggande eller NTLM-autentisering är aktiverat i proxyserverkonfiguration för enheten i StorSimple Manager-tjänsten. |Ändra webbproxykonfiguration för enheten registrerad med din StorSimple Manager-tjänsten så att autentiseringen är inställd på NONE. Om du vill göra detta måste köras i Windows PowerShell för StorSimple Set-HcsWebProxy cmdlet. |Ja |Ja |
| 7 |Lagringskonton |Använder Storage-tjänsten för att ta bort lagringskontot är ett scenario som inte stöds. Detta leder till en situation där användardata inte kan hämtas. | |Ja |Ja |
| 8 |Enheten växling vid fel |Flera redundans av en volymbehållare från samma källa enheten till olika målenheter stöds inte. |Växling från en inaktiv enhet till flera enheter gör volymbehållarna på den första redundansväxlats enhet förlorar dataägarskap. Efter en växling, ska behållarna volymen visas eller fungera annorlunda när de visas i den klassiska Azure-portalen. |Ja |Nej |
| 9 |Installation |Under StorSimple-kort för SharePoint-installation måste du ange en IP-adress för enheten för att slutföra installationen. | |Ja |Nej |
| 10 |Webbproxy |Om din webbproxykonfigurationen har det angivna protokollet HTTPS, enhet-till-tjänst-kommunikation kommer att påverkas och enheten ska försättas i offlineläge. Stöd för paket skapas också i process förbrukar betydande resurser på enheten. |Kontrollera att URL: en för web proxy har det angivna protokollet HTTP. Mer information om hur du [konfigurera webbproxy för din enhet](storsimple-configure-web-proxy.md). |Ja |Nej |
| 11 |Webbproxy |Om du konfigurerar och aktiverar webbproxy på en registrerad enhet, måste du starta om den aktiva styrenheten på enheten. | |Ja |Nej |
| 12 |Hög molnet latens och hög i/o-arbetsbelastning |När din StorSimple-enhet påträffar en kombination av mycket hög molnet latens (ordning sekunder) och höga i/o-arbetsbelastning, enheten volymer som ingår i ett degraderat tillstånd och I/o kan misslyckas med felet ”enheten är inte klar”. |Du måste starta om styrenheterna eller utför en växling vid fel med enheten för att återställa från den här situationen manuellt. |Ja |Nej |

## <a name="physical-device-updates-in-the-october-release"></a>Fysisk enhetsuppdateringar i oktober släpps
När uppdateringarna tillämpas på en fysisk enhet ändras programvaruversionen till 6.3.9600.17312. Om inget annat anges här viktiga informationen gäller för alla modeller av StorSimple-enhet. Mer information om de här uppdateringarna finns [oktober 2014 fysisk installation programuppdateringen för Microsoft Azure StorSimple-enhet](http://support.microsoft.com/kb/2986997).  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-october-release"></a>Serieansluten SCSI (SAS)-styrenhet och uppdateringar av inbyggd i oktober
Den här versionen uppdaterar drivrutinen och den inbyggda programvaran på SAS-styrenheten för den fysiska enheten. Mer information om SAS-styrenhet uppdateringen finns [uppdatering under oktober 2014 för LSI SAS-enheter i Microsoft Azure StorSimple-enhet](http://support.microsoft.com/kb/2987020).   

Den här versionen gäller även en kumulativ firmware-uppdatering som hanterar tillförlitlighetsproblem med maskinvarukomponenterna som enheten. Läs mer om firmware-uppdatering [oktober 2014 firmware-uppdatering för Microsoft Azure StorSimple-enhet](http://support.microsoft.com/kb/2987015).  

## <a name="virtual-device-updates-in-the-october-release"></a>Uppdatering av virtuella enheter i oktober släpper
Den här versionen innehåller inte några uppdateringar för den virtuella enheten. Uppdateringen ändrar inte programvaruversionen för en virtuell enhet.

