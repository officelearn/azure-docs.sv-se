---
title: StorSimple virtuell matris uppdateringar viktig information | Microsoft Docs
description: Beskriver viktiga öppna problem och lösningar för StorSimple Virtual Array som kör uppdatering 0.3.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: b197651a-3c40-4185-b23d-4c8f22cfa8f4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2016
ms.author: alkohli
ms.openlocfilehash: 635b5f4edf5d403c569b4957540fc105997b3e8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629279"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>StorSimple virtuell matris uppdatering 0.3 viktig information
## <a name="overview"></a>Översikt
Följande viktiga information identifiera kritiska öppna ärenden och löst problem för Microsoft Azure StorSimple Virtual Array-uppdateringar.

Den viktiga informationen uppdateras kontinuerligt och allteftersom allvarliga problem som kräver en lösning upptäcks, läggs de. Innan du distribuerar din StorSimple Virtual Array noga igenom uppgifterna i viktig information.

Uppdatering 0.3 motsvarar programvaruversionen **10.0.10288.0**.

> [!NOTE]
> Uppdateringarna är störande och starta om enheten. Om i/o som pågår, sker enheten.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Vad är nytt i uppdatering 0.3
Uppdatering 0.3 är främst en felkorrigering version. I den här versionen har flera buggar, vilket resulterar i fel vid säkerhetskopiering i den tidigare versionen åtgärdats.

## <a name="issues-fixed-in-the-update-03"></a>Problem som åtgärdas i uppdatering 0.3
Följande tabell innehåller en översikt över problem som åtgärdas i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |Säkerhetskopior |Ett problem har synts den tidigare versionen där säkerhetskopiorna skulle misslyckas för en filresurs. Om det här problemet uppstod säkerhetskopieringsjobbet skulle misslyckas och en kritisk varning har angetts på StorSimple Manager-tjänsten för att meddela användaren. Det här problemet påverkas inte data på den filresurser eller åtkomst till data. Den grundläggande orsaken har identifierats och åtgärdats i den här versionen. <br></br> Korrigeringen gäller inte retroaktivt för resurser som redan ser det här problemet. Kunder som ser det här problemet bör du först installera uppdatering 0.3 sedan kontakta Microsoft Support om du vill utföra en fullständig säkerhetskopiering för att åtgärda problemet. I stället för att kontakta Microsoft Support-kan kunder även återställa till en ny filresurs från en felfri säkerhetskopia för berörda resurser. |
| 2 |iSCSI |Ett problem har synts den tidigare versionen där volymerna som försvinner när du kopierar data till en volym på StorSimple Virtual Array. Det här problemet har åtgärdats i den här versionen. <br></br> Om korrigeringarna gälla bara på nyligen skapade volymer. Om korrigeringarna gäller inte retroaktivt för volymer som redan ser det här problemet. Kunder bör ta de berörda volymerna online via den klassiska Azure-portalen, utföra en säkerhetskopiering av dessa volymer och Återställ sedan dessa volymer till nya volymer. |

## <a name="known-issues-in-the-update-03"></a>Kända problem i uppdatering 0.3
I följande tabell innehåller en översikt över kända problem för StorSimple Virtual Array och omfattar problem versionen anges från tidigare versioner. 

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Uppdateringar |De virtuella enheter som skapats i förhandsversionen kan inte uppdateras till en version som stöds är allmänt tillgängligt. |Dessa virtuella enheter måste flyttas över för den allmänt tillgängliga versionen med hjälp av ett arbetsflöde för disaster recovery (DR). |
| **2.** |Etablerade datadisk |När du har etablerat en datadisk med en viss angivna storlek och skapat den motsvarande virtuella StorSimple-enheten, du måste inte öka eller minska datadisken. Om du försöker göra leder till förlust av alla data på de lokala nivåerna för enheten. | |
| **3.** |Grupprincip |När en enhet är ansluten till domänen, kan tillämpa en grupprincip påverkas negativt enheten. |Kontrollera att din virtuella matris är i sin egen organisationsenhet (OU) för Active Directory och inga grupprincipobjekt (GPO) tillämpas. |
| **4.** |Lokala webbgränssnittet |Om utökad säkerhet har aktiverats i Internet Explorer (IE ESC), vissa lokala webbsidor Användargränssnittet, till exempel felsökning eller underhåll kanske inte fungerar korrekt. Knapparna på dessa sidor kan också inte fungerar. |Stäng av funktioner för förbättrad säkerhet i Internet Explorer. |
| **5.** |Lokala webbgränssnittet |I en Hyper-V virtuell dator, nätverksgränssnitt i webb-UI visas som 10 Gbit/s-gränssnitt. |Detta är en avbildning av Hyper-V. Hyper-V visar alltid 10 Gbit/s för virtuella nätverkskort. |
| **6.** |Nivåindelade volymer eller resurser |Byte-intervallet låsning för program som fungerar med StorSimple nivåindelade volymer inte stöds. Om låsning av byte-intervallet är aktiverad, fungerar inte StorSimple lagringsnivåer. |Rekommenderade åtgärder är: <br></br>Inaktivera byteintervall låsning i programlogiken.<br></br>Välja att placera data för det här programmet i lokalt fixerade volymer till skillnad från nivåindelade volymer.<br></br>*Villkor för*: När med hjälp av lokalt fixerade volymer och låsning byte-intervallet är aktiverad, kan lokalt fixerad volym vara online, även innan återställningen är klar. I sådana fall om en återställning pågår måste sedan du vänta tills återställningen har slutförts. |
| **7.** |Nivåindelade filresurser |Arbeta med stora filer kan orsaka långsam ut nivå. |När du arbetar med stora filer, rekommenderar vi att den största filen är mindre än 3 procent av resursstorleken. |
| **8.** |Kapacitet för resurser som används av |Du kan se dela förbrukning när det finns inga data i resursen. Det beror på att Använd kapacitet för filresurser innehåller metadata. | |
| **9.** |Haveriberedskap |Du kan bara utföra haveriberedskap för en server till samma domän som källenheten. Haveriberedskap till en målenhet i en annan domän stöds inte i den här versionen. |Detta är implementerat i en senare version. |
| **10.** |Azure PowerShell |StorSimple-enheter kan inte hanteras via Azure PowerShell i den här versionen. |All hantering av virtuella enheter bör göras via den klassiska Azure-portalen och det lokala webbgränssnittet. |
| **11.** |Lösenordsändring |Virtuell matris klientenhetskonsolen accepterar endast indata i en-US tangentbord format. | |
| **12.** |CHAP |CHAP-autentiseringsuppgifter som är skapade tas inte bort. Om du ändrar CHAP-autentiseringsuppgifter måste du även att frånkoppla volymerna och anpassa dem online för att ändringen ska börja gälla. |Det här problemet korrigeras i en senare version. |
| **13.** |iSCSI-servern |Den ”används storage” visas för en iSCSI-volymen kan skilja sig i StorSimple Manager-tjänsten och iSCSI-värden. |ISCSI-värden har vyn filsystem.<br></br>Enheten ser de block som tilldelas när volymen har vid den maximala storleken. |
| **14.** |Filserver |Om en fil i en mapp har en annan Data Stream (ADS) som är associerade med det, är ANNONSER inte säkerhetskopieras eller återställs via haveriberedskap, klona och återställning på objektnivå. | |

## <a name="next-step"></a>Nästa steg
[Installera uppdatering 0.3](storsimple-ova-install-update-01.md) på StorSimple Virtual Array.

## <a name="references"></a>Referenser
Letar du efter en äldre dokumentet? Gå till: 

* [StorSimple Virtual Array uppdatering 0.1 och 0,2 viktig information](storsimple-ova-update-01-release-notes.md)
* [StorSimple virtuell matris allmän tillgänglighet viktig information](storsimple-ova-pp-release-notes.md)

