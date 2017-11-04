---
title: StorSimple 8000 uppdatering 0,2 viktig information | Microsoft Docs
description: "Beskriver nya funktioner och korrigeringar, öppna problem och tillgängliga lösningar för januari 2015 Microsoft Azure StorSimple version (uppdatera 0,2)."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: d9684ae3-b38f-4678-9d70-e5dbc6b03350
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/16/2016
ms.author: v-sharos
ms.openlocfilehash: 2fc50f7faddb4b61ea5e7d328469fc3cc0eb6f3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-update-02-release-notes---january-2015"></a>StorSimple 8000 uppdatering 0,2 viktig information - januari 2015
## <a name="overview"></a>Översikt
I följande versionsinformation identifiera kritiska öppna problem för januari 2015-versionen av Microsoft Azure StorSimple. De innehåller också en lista över StorSimple programvara och inbyggd programvara uppdateringar som ingår i den här versionen. Det här är den andra utgåvan efter version StorSimple 8000-serien släpper gjordes allmänt tillgänglig i juli 2014.

Den här uppdateringen ändras inte den fysiska enhet programvaruversionen från oktober uppdateringen. Den fortsätter att vara version 6.3.9600.17312. Den bild som används av den virtuella enhet avbildningen har ändrats i den här versionen. Därför alla nya virtuella enheter skapas efter 20/1/2015 visas versionen som 6.3.9600.17361.  

Granska följande information i viktig information om januari 2015-uppdateringen.

> [!IMPORTANT]
> * Den här uppdateringen är inte tillgänglig via Windows Update och kan inte installeras som andra uppdateringar. Enheten får inte den här uppdateringen, även om du har installerat uppdateringar med hjälp av den klassiska Azure-portalen. Den här uppdateringen gäller bara för virtuella enheter som skapats efter 20 januari 2015. 
> * Januari-versionen av StorSimple innehåller inte några uppdateringar till den fysiska StorSimple-enheten. Du kan använda alla tillgängliga Windows-uppdateringar till den virtuella enheten, inklusive de senaste säkerhetskorrigeringar, men en ändring i version för den fysiska StorSimple-enheten visas inte.
> 
> 

## <a name="whats-new-in-the-january-release"></a>Vad är nytt i januari-versionen
Den här uppdateringen innehåller en lösning som är relaterade till volymer försätts i offlineläge på den virtuella enheten. (Se [problem som åtgärdas i den här versionen](#issues-fixed-in-the-january-release).)  

Uppdateringen innehåller inte nya funktioner eller funktionalitet.  

## <a name="issues-fixed-in-the-january-release"></a>Problem som åtgärdas i januari-versionen
I följande tabell beskrivs de problem som fastställdes i den här uppdateringen.

| Nej. | Funktion | Problem | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- |
| 1 |Volymer försätts i offlineläge |När hög molnet svarstiderna kvarstår i flera minuter, de virtuella enheten StorSimple-volymerna i offlineläge på värdarna. Den här snabbkorrigeringen ökar tröskelvärdet för fördröjningar i molnet, vilket minimerar situationer som skulle orsaka volymer att försättas i offlineläge på värdar. |Nej |Ja |

## <a name="known-issues-in-the-january-release"></a>Kända problem i januari-versionen
Följande tabell innehåller en översikt över kända problem i den här versionen.

| Nej. | Funktion | Problem | Kommentarer/lösning | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- | --- |
| 1 |Fabriksåterställning |I vissa fall, när du utför en fabriksåterställning StorSimple-enhet kan ha fastnat och visa det här meddelandet: **återställning till fabriksinställningarna pågår (fas 8).** Detta händer om du trycker på CTRL + C när cmdleten pågår. |Tryck inte på CTRL + C när du initierar en fabriksåterställning. Om du redan är i det här tillståndet kan kontakta Microsoft Support för nästa steg. |Ja |Nej |
| 2 |Disk kvorum |I sällsynta fall om merparten av diskar i EBOD höljet av en 8600-enhet är frånkopplad ledde till Ingen disk i kvorum kommer sedan lagringspoolen vara offline. Den förblir offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår, kontakta Microsoft Support för nästa steg. |Ja |Nej |
| 3 |Fel i moln ögonblicksbild |I sällsynta fall kan en ögonblicksbild i molnet kan misslyckas med fel **säkerhetskopiering maxgräns uppnåtts**. Det här inträffar om du överskrider 255 online kloner på samma enhet från samma ursprungliga volym som har tagits bort. | |Ja |Ja |
| 4 |Felaktig styrenhets-ID |När en domänkontrollant ersättning utförs kan styrenhet 0 visas som kontrollant 1. Under controller ersättning, när avbildningen har lästs in från peer-noden kan styrenhets-ID visas först som peer-styrenhet-ID.  I sällsynta fall kan problemet visas efter en omstart. |Ingen användaråtgärd krävs. Den här situationen ska lösas av sig självt när domänkontrollanten ersättningen är klar. |Ja |Nej |
| 5 |Enheten övervakning diagram |Övervakning diagrammen enheten fungerar inte när grundläggande eller NTLM-autentisering är aktiverat i proxyserverkonfiguration för enheten i StorSimple Manager-tjänsten. |Ändra webbproxykonfiguration för enheten registrerad med din StorSimple Manager-tjänsten så att autentiseringen är inställd på NONE. Om du vill göra detta måste köras i Windows PowerShell för StorSimple Set-HcsWebProxy cmdlet. |Ja |Ja |
| 6 |Lagringskonton |Använder Storage-tjänsten för att ta bort lagringskontot är ett scenario som inte stöds. Detta leder till en situation där användardata inte kan hämtas. | |Ja |Ja |
| 7 |Enheten växling vid fel |Flera redundans av en volymbehållare från samma källa enheten till olika målenheter stöds inte. |Växling från en inaktiv enhet till flera enheter gör volymbehållarna på den första redundansväxlats enhet förlorar dataägarskap. Efter en växling, ska behållarna volymen visas eller fungera annorlunda när de visas i den klassiska Azure-portalen. |Ja |Nej |
| 8 |Installation |Under StorSimple-kort för SharePoint-installation måste du ange en IP-adress för enheten för att slutföra installationen. | |Ja |Nej |
| 9 |Webbproxy |Om din webbproxykonfigurationen har det angivna protokollet HTTPS, enhet-till-tjänst-kommunikation kommer att påverkas och enheten ska försättas i offlineläge. Stöd för paket skapas också i process förbrukar betydande resurser på enheten. |Kontrollera att URL: en för web proxy har det angivna protokollet HTTP. Se mer information om hur du [konfigurera webbproxy för din enhet](storsimple-configure-web-proxy.md). |Ja |Nej |
| 10 |Webbproxy |Om du konfigurerar och aktiverar webbproxy på en registrerad enhet, måste du starta om den aktiva styrenheten på enheten. | |Ja |Nej |
| 11 |Hög molnet latens och hög i/o-arbetsbelastning |När din StorSimple-enhet påträffar en kombination av mycket hög molnet latens (ordning sekunder) och höga i/o-arbetsbelastning, enheten volymer som ingår i ett degraderat tillstånd och I/o kan misslyckas med felet ”enheten är inte klar”. |Du måste starta om styrenheterna eller utför en växling vid fel med enheten för att återställa från den här situationen manuellt. |Ja |Nej |

## <a name="physical-device-updates-in-the-january-release"></a>Fysisk enhetsuppdateringar i januari släpps
Den här uppdateringen innehåller inte några andra ändringar till StorSimple-enhet.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-january-release"></a>Serieansluten SCSI (SAS)-styrenhet och uppdateringar av inbyggd i januari
Den här versionen innehåller inte några uppdateringar till serieansluten SCSI (SAS)-styrenheten eller den inbyggda programvaran. Drivrutinsuppdateringen var i oktober 2014-versionen. 

## <a name="virtual-device-updates-in-the-january-release"></a>Uppdatering av virtuella enheter i januari släpper
Den här versionen innehåller en uppdaterad bild för den virtuella enheten. Alla virtuella enheter som skapats efter 20 januari 2015 visas programvaruversionen som 6.3.9600.17361.

