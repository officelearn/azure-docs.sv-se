---
title: StorSimple 8000 uppdatering 0,3 viktig information | Microsoft Docs
description: "Beskriver nya funktioner och korrigeringar, öppna problem och tillgängliga lösningar för februari 2015 Microsoft Azure StorSimple version (uppdatering 0.3)."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: b01bfd04-f9f8-45f4-ade8-95ac2b979e6a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 87a0a59d0c980baa87aa5d7f5f0e8098884b5ec9
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="storsimple-8000-series-update-03-release-notes---february-2015"></a>StorSimple 8000 uppdatering 0,3 viktig information – februari 2015
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Översikt
I följande versionsinformation identifiera kritiska öppna problem för StorSimple 8000 uppdatering 0.3 ut i februari 2015. De innehåller också en lista över StorSimple programvara och inbyggd programvara uppdateringar som ingår i den här versionen. Detta är den tredje utgåvan efter version StorSimple 8000-serien släpper gjordes allmänt tillgänglig i juli 2014.

Den här uppdateringen ändras inte programvaruversionen enheten från januariuppdateringen. Den fortsätter att vara version 6.3.9600.17312. Du kan bekräfta att uppdateringen har installerats genom att kontrollera den **senaste uppdaterade** datum. Om datumet är 2/10/2015 eller senare, och sedan uppdateringen har installerats.  

Vi rekommenderar att du söka efter och installera tillgängliga uppdateringar direkt när du har installerat din StorSimple-enhet. Du kan också aktivera Automatiska uppdateringar för att hämta och installera viktiga uppdateringar från Microsoft så snart de blir tillgängliga. Mer information finns i [uppdatera din StorSimple-enhet](storsimple-update-device.md).  

Granska informationen som finns i viktig information innan du distribuerar uppdateringen i din StorSimple-lösning.  

> [!IMPORTANT]
> * Använda StorSimple Manager-tjänsten och inte Windows PowerShell för StorSimple för att installera uppdateringen februari.   
> * Det tar ungefär en timme att installera uppdateringen. Men om du installerar kumulativa uppdateringar i processen kan ta cirka 3 timmar för att slutföra.  
> * Februari-versionen av StorSimple innehåller inte några uppdateringar till den virtuella StorSimple-enheten. Du kan använda alla tillgängliga Windows-uppdateringar till den virtuella enheten, inklusive de senaste säkerhetskorrigeringar, men en ändring i versionen för den virtuella enheten visas inte.  
> 
> 

Kontrollera att följande krav är uppfyllda innan du uppdaterar din StorSimple-enhet.  

* Se till att båda styrenheter körs innan du söker efter uppdateringar. Sökningen misslyckas om antingen domänkontrollanten körs. Kontrollera att domänkontrollanterna är i felfritt tillstånd, gå till **maskinvarustatus** under den **Underhåll** sidan. Om det finns komponenter som **behöver åtgärdas**, kontakta Microsoft Support innan du fortsätter.
* Kontrollera att fasta IP-adresser för både styrenhet 0 och kontrollant 1 är dirigerbara och kan ansluta till Internet som används för att underhålla uppdateringarna till enheten. Du kan använda den [Test-Connection cmdlet](https://technet.microsoft.com/library/hh849808.aspx) att pinga en känd adress utanför nätverket, till exempel outlook.com, kontrollera att kontrollanten är ansluten till det externa nätverket.
* Kontrollera att portarna 80 och 443 är tillgängliga på din StorSimple-enhet för utgående kommunikation. Mer information finns i [nätverkskrav för din StorSimple-enhet](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
* Om enheten programvaruversionen är äldre än 6.3.9600.17312 (oktober 2014-uppdatering), inaktivera Data 2 och Data 3 portar, om aktiverad, innan du startar uppdateringen. Lämnar Data 2 och Data 3 portar aktiverad när du installerar uppdateringen orsaka styrenheten för enheten att i återställningsläge. Observera att när du inaktiverar nätverksgränssnitten associerade volymer försätts i offlineläge och I/o kommer att avbrytas under uppdateringen.  

## <a name="whats-new-in-the-february-release"></a>Vad är nytt i februari-versionen
Den här uppdateringen innehåller en korrigering för problemet uppstod på fabriksåterställa enheter som har uppgraderats från GA frisläppa till oktober 2014-versionen. Mer information finns i [problem som åtgärdas i den här versionen](#issues-fixed-in-the-february-release).   

Den här uppdateringen innehåller inte nya funktioner eller funktionalitet.  

## <a name="issues-fixed-in-the-february-release"></a>Problem som åtgärdas i februari-versionen
I följande tabell beskrivs de problem som fastställdes i den här uppdateringen.  

| Nej. | Funktion | Problem | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- |
| 1 |Fabriksåterställning |Försök att utföra en fabriksåterställning på en enhet som ursprungligen hade GA-version (version 6.3.9600.17215) installerat, men har uppdaterats till oktober versionen (version 6.3.9600.17312). Fabriksåterställa misslyckas och enheten blir instabil. |Ja |Nej |

## <a name="known-issues-in-the-february-release"></a>Kända problem i februari-versionen
Följande tabell innehåller en översikt över kända problem i den här versionen.

| Nej. | Funktion | Problem | Kommentarer/lösning | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- | --- |
| 1 |Fabriksåterställning |I vissa fall, när du utför en fabriksåterställning StorSimple-enhet kan ha fastnat och visa det här meddelandet: **återställning till fabriksinställningarna pågår (fas 8)**. Detta händer om du trycker på CTRL + C när cmdleten pågår. |Tryck inte på CTRL + C när du initierar en fabriksåterställning. Om du redan är i det här tillståndet kan kontakta Microsoft Support för nästa steg. |Ja |Nej |
| 2 |Disk kvorum |I sällsynta fall om merparten av diskar i EBOD höljet av en 8600device är frånkopplad ledde till Ingen disk i kvorum kommer sedan lagringspoolen vara offline. Den förblir offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår, kontakta Microsoft Support för nästa steg. |Ja |Nej |
| 3 |Fel i moln ögonblicksbild |I sällsynta fall kan en ögonblicksbild i molnet kan misslyckas med fel **säkerhetskopiering maxgräns uppnåtts**. Det här inträffar om du överskrider 255 online kloner på samma enhet från samma ursprungliga volym som har tagits bort. | |Ja |Ja |
| 4 |Felaktig styrenhets-ID |När en domänkontrollant ersättning utförs kan styrenhet 0 visas som kontrollant 1. Under controller ersättning, när avbildningen har lästs in från peer-noden kan styrenhets-ID visas först som peer-styrenhet-ID. I sällsynta fall kan problemet visas efter en omstart. |Ingen användaråtgärd krävs. Den här situationen ska lösas av sig självt när domänkontrollanten ersättningen är klar. |Ja |Nej |
| 5 |Enheten övervakning diagram |Övervakning diagrammen enheten fungerar inte när grundläggande eller NTLM-autentisering är aktiverat i proxyserverkonfiguration för enheten i StorSimple Manager-tjänsten. |Ändra webbproxykonfiguration för enheten registrerad med din StorSimple Manager-tjänsten så att autentiseringen är inställd på NONE. Om du vill göra detta måste köras i Windows PowerShell för StorSimple Set-HcsWebProxy cmdlet. |Ja |Ja |
| 6 |Lagringskonton |Använder Storage-tjänsten för att ta bort lagringskontot är ett scenario som inte stöds. Detta leder till en situation där användardata inte kan hämtas. | |Ja |Ja |
| 7 |Enheten växling vid fel |Flera redundans av en volymbehållare från samma källa enheten till olika målenheter stöds inte.    Växling från en inaktiv enhet till flera enheter gör volymbehållarna på den första redundansväxlats enhet förlorar dataägarskap. Efter en växling, ska behållarna volymen visas eller fungera annorlunda när de visas i den klassiska Azure-portalen. | |Ja |Nej |
| 8 |Installation |Under StorSimple-kort för SharePoint-installation måste du ange en IP-adress för enheten för att slutföra installationen. | |Ja |Nej |
| 9 |Webbproxy |Om din webbproxykonfigurationen har det angivna protokollet HTTPS, enhet-till-tjänst-kommunikation kommer att påverkas och enheten ska försättas i offlineläge. Stöd för paket skapas också i process förbrukar betydande resurser på enheten. |Kontrollera att URL: en för web proxy har det angivna protokollet HTTP. Mer information om hur du [konfigurera webbproxy för din enhet](storsimple-configure-web-proxy.md). |Ja |Nej |
| 10 |Webbproxy |Om du konfigurerar och aktiverar webbproxy på en registrerad enhet, måste du starta om den aktiva styrenheten på enheten. | |Ja |Nej |
| 11 |Hög molnet latens och hög i/o-arbetsbelastning |När din StorSimple-enhet påträffar en kombination av mycket hög molnet latens (ordning sekunder) och höga i/o-arbetsbelastning, enheten volymer som ingår i ett degraderat tillstånd och I/o kan misslyckas med felet ”enheten är inte klar”. |Du måste starta om styrenheterna eller utför en växling vid fel med enheten för att återställa från den här situationen manuellt. |Ja |Nej |

## <a name="physical-device-updates-in-the-february-release"></a>Fysisk enhetsuppdateringar i februari släpps
Åtgärdas factory Återställ problem som uppstod på enheter som har uppgraderats från GA frisläppa till oktober 2014-versionen. Det innehåller inte några andra uppdateringar till StorSimple-enhet.  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-february-release"></a>Serieansluten SCSI (SAS)-styrenhet och uppdateringar av inbyggd i februari
Den här versionen innehåller inte några uppdateringar till serieansluten SCSI (SAS)-styrenheten eller den inbyggda programvaran. Drivrutinsuppdateringen var i oktober 2014-versionen.  

## <a name="virtual-device-updates-in-the-february-release"></a>Uppdatering av virtuella enheter i februari släpper
Den här versionen innehåller inte några uppdateringar för den virtuella enheten. Uppdateringen ändrar inte programvaruversionen för en virtuell enhet.

