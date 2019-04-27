---
title: StorSimple virtuell matris uppdatering 0,6 viktig information | Microsoft Docs
description: Beskriver viktiga öppna problem och lösningar för StorSimple Virtual Array som kör uppdatering 0.6.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: e984531feced2d61332e4c399848c12cd245a34a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870714"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>StorSimple virtuell matris uppdatering 0,6 viktig information

## <a name="overview"></a>Översikt

Följande viktiga information identifiera kritiska öppna ärenden och löst problem för Microsoft Azure StorSimple Virtual Array-uppdateringar.

Den viktiga informationen uppdateras kontinuerligt och allteftersom allvarliga problem som kräver en lösning upptäcks, läggs de. Innan du distribuerar din StorSimple Virtual Array noga igenom uppgifterna i viktig information.

Uppdatering 0.6 motsvarar programvaruversionen **10.0.10293.0**.

> [!IMPORTANT]
> - Uppdateringarna är störande och starta om enheten. Om i/o som pågår, sker enheten. Detaljerade anvisningar om hur du installerar uppdateringen, går du till [installera uppdatering 0.6](storsimple-virtual-array-install-update-06.md).
>
> - Vi rekommenderar starkt att du installerar uppdatering 0.6 omedelbart eftersom den innehåller viktiga säkerhetskorrigeringar.


## <a name="whats-new-in-the-update-06"></a>Vad är nytt i uppdatering 0.6
Uppdatering 0.6 är en viktig uppdatering och bör distribueras direkt. Den här uppdateringen innehåller följande: 

- **Windows säkerhetskorrigeringar** -den här versionen har **Windows kritiska säkerhetskorrigeringar**. Granska följande säkerhetsuppdateringar för mer information om säkerhetsproblem och associerade korrigeringar:
    - [December 2016 Security endast kvalitet uppdatering för Windows 8.1 och Windows Server 2012 R2](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [Mars 2017 Security endast kvalitet uppdatering för Windows 8.1 och Windows Server 2012 R2](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [Den 9 maj 2017 – KB4019213 (endast Security uppdatering)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Återställa korrigering** -det uppstod ett fel som förhindrar att återställningen inte slutföra i tidigare versioner. Det här felet har åtgärdats i den här versionen.


## <a name="issues-fixed-in-the-update-06"></a>Problem som åtgärdas i uppdatering 0.6

Följande tabell innehåller en översikt över problem som åtgärdas i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |Säkerhet| Den här versionen innehåller kritiska säkerhetsuppdateringar för Windows. Vi rekommenderar att du installerar uppdateringen omedelbart.|
| 2 |Återställ| Det uppstod ett konkurrenstillstånd som kan förhindra att återställningsjobbet från att slutföras under en återställning. Felkorrigering löser den här konkurrenstillstånd.|


## <a name="known-issues-in-the-update-06"></a>Kända problem i uppdatering 0.6

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
| **8.** |Kapacitet för resurser som används av |Du kan se dela förbrukning när det finns inga data i resursen. Den här förbrukning beror på Använd kapacitet för filresurser innehåller metadata. | |
| **9.** |Haveriberedskap |Du kan bara utföra haveriberedskap för en server till samma domän som källenheten. Haveriberedskap till en målenhet i en annan domän stöds inte i den här versionen. |Detta är implementerat i en senare version. Mer information går du till [redundans och återställning vid återställning av StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |StorSimple-enheter kan inte hanteras via Azure PowerShell i den här versionen. |All hantering av virtuella enheter bör göras via Azure-portalen och det lokala webbgränssnittet. |
| **11.** |Lösenordsändring |Virtuell matris klientenhetskonsolen endast godkänner indata i en-us tangentbord format. | |
| **12.** |CHAP |CHAP-autentiseringsuppgifter som är skapade tas inte bort. Om du ändrar CHAP-autentiseringsuppgifter måste du även att frånkoppla volymerna och anpassa dem online för att ändringen ska börja gälla. |Det här problemet korrigeras i en senare version. |
| **13.** |iSCSI-servern |Den ”används storage” visas för en iSCSI-volymen kan skilja sig i StorSimple Device Manager-tjänsten och iSCSI-värden. |ISCSI-värden har vyn filsystem.<br></br>Enheten ser de block som tilldelas när volymen har vid den maximala storleken. |
| **14.** |Filserver |Om en fil i en mapp har en annan Data Stream (ADS) som är associerade med det, är ANNONSER inte säkerhetskopieras eller återställs via haveriberedskap, klona och återställning på objektnivå. | |
| **15.** |Filserver |Symboliska länkar stöds inte. | |
| **16.** |Filserver |Filer som skyddas av Windows Krypterande filsystem (EFS) när kopieras över eller lagras på StorSimple Virtual Array file server resultatet i en konfiguration som inte stöds.  | |
| **17.** |Uppdateringar |Om du ser fel kod: 2359302 (hex 0x240006) när du försöker installera en snabbkorrigering via lokala Användargränssnittet sedan detta innebär att snabbkorrigeringen har redan installerats på enheten.   | |

## <a name="next-step"></a>Nästa steg
[Installera uppdatering 0.6](storsimple-virtual-array-install-update-06.md) på StorSimple Virtual Array.

## <a name="references"></a>Referenser
Letar du efter en äldre dokumentet? Gå till:

* [StorSimple Virtual Array Update 0,5 viktig information](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0,4 viktig information](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 viktig information](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array uppdatering 0.1 och 0,2 viktig information](storsimple-ova-update-01-release-notes.md)
* [StorSimple virtuell matris allmän tillgänglighet viktig information](storsimple-ova-pp-release-notes.md)

