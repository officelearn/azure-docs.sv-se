---
title: "StorSimple 8000 släpper version viktig information | Microsoft Docs"
description: "Beskriver nya funktioner, öppna problem och tillgängliga lösningar för juli 2014 Microsoft Azure StorSimple version."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 12f1796e-37c3-42b4-b997-a84fc1950c20
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: e6e087ad61ee54fef3552895c00a478fa51e2072
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="storsimple-8000-series-release-version-release-notes---july-2014"></a>StorSimple 8000-serien släpper Version viktig information - juli 2014

> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Översikt

Följ viktig information identifiera kritiska öppna problem för StorSimple 8000-serien juli 2014 allmän tillgänglighet (GA) versionen av Microsoft Azure StorSimple. Den här versionen motsvarar programvaruversion 6.3.9600.17215.

Om inget annat anges här viktiga informationen gäller för alla modeller av StorSimple-enhet. Viktig information uppdateras kontinuerligt; allteftersom allvarliga problem som kräver en lösning upptäcks, läggs de till. Tänk på följande innan du distribuerar Microsoft Azure StorSimple-lösningen.

## <a name="known-issues-in-this-release"></a>Kända problem i den här versionen

Följande tabell innehåller en översikt över kända problem i den här versionen.  

| Nej. | Funktion | Problem | Kommentarer/lösning | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- | --- |
| 1 |Fabriksåterställning |I vissa fall, när du utför en fabriksåterställning StorSimple-enhet kan ha fastnat och visa det här meddelandet: **återställning till fabriksinställningarna pågår (fas 8)**. Detta händer om du trycker på CTRL + C när cmdleten pågår. |Tryck inte på CTRL + C när du initierar en fabriksåterställning. Om du redan är i det här tillståndet kan du kontakta Microsofts Support för nästa steg. |Ja |Nej |
| 2 |Disk kvorum |I sällsynta fall om merparten av diskar i EBOD höljet av en 8600-enhet är frånkopplad ledde till Ingen disk i kvorum tas sedan lagringspoolen offline. Den förblir offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår, kontakta Microsoft Support för nästa steg. |Ja |Nej |
| 3 |Fel i moln ögonblicksbild |I sällsynta fall kan en ögonblicksbild i molnet kan misslyckas med fel **säkerhetskopiering maxgräns uppnåtts**. Det här inträffar om du överskrider 255 online kloner på samma enhet från samma ursprungliga volym som har tagits bort. | |Ja |Ja |
| 4 |Felaktig styrenhets-ID |När en domänkontrollant ersättning utförs kan styrenhet 0 visas som kontrollant 1. Under controller ersättning, när avbildningen har lästs in från peer-noden kan styrenhets-ID visas först som peer-styrenhet-ID. I sällsynta fall kan problemet visas efter en omstart. |Ingen användaråtgärd krävs. Den här situationen ska lösas av sig självt när domänkontrollanten ersättningen är klar. |Ja |Nej |
| 5 |Enheten övervakning diagram |Övervakning diagrammen enheten fungerar inte när grundläggande eller NTLM-autentisering är aktiverat i proxyserverkonfiguration för enheten i StorSimple Manager-tjänsten. |Ändra webbproxykonfiguration för enheten registrerad med din StorSimple Manager-tjänsten så att autentiseringen är inställd på NONE. Om du vill göra det, kör du Windows PowerShell för StorSimple Set-HcsWebProxy cmdlet. |Ja |Ja |
| 6 |Lagringskonton |Använder Storage-tjänsten för att ta bort lagringskontot är ett scenario som inte stöds. Detta leder till en situation där användardata inte kan hämtas. | |Ja |Ja |
| 7 |Återställning efter fel |En återställning efter fel inom 24 timmar för katastrofåterställning (DR) stöds inte. | |Ja |Nej |
| 8 |Enheten växling vid fel |Flera redundans av en volymbehållare från samma källa enheten till olika målenheter stöds inte. Redundans från en enda döda enhet till flera enheter gör volymbehållarna på den första redundansväxlats enhet förlorar dataägarskap. Efter en växling, ska behållarna volymen visas eller fungera annorlunda när de visas i den klassiska Azure-portalen. | |Ja |Nej |
| 9 |Installation |Under StorSimple-kort för SharePoint-installation måste du ange en IP-adress för enheten för att slutföra installationen. | |Ja |Nej |
| 10 |Nätverksgränssnitt |Nätverksgränssnitten DATA 2 och DATA 3 har växlats i programvaran. |Kontakta Microsoft Support om du måste konfigurera dessa gränssnitt. |Ja |Nej |

