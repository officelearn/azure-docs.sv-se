---
title: StorSimple virtuell matris uppdatering 1.0 viktig information | Microsoft Docs
description: Beskriver viktiga öppna problem och lösningar för StorSimple Virtual Array som kör version 1.0.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: fdf37a8360ec69017458fabee2a9e16aa2c160aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789679"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>Viktig information om StorSimple Virtual Array version 1.0

## <a name="overview"></a>Översikt

Följande viktiga information identifiera kritiska öppna ärenden och löst problem för Microsoft Azure StorSimple Virtual Array-uppdateringar.

Den viktiga informationen uppdateras kontinuerligt och allteftersom allvarliga problem som kräver en lösning upptäcks, läggs de. Innan du distribuerar din StorSimple Virtual Array noga igenom uppgifterna i viktig information.

Version 1.0 motsvarar programvaruversionen **10.0.10296.0**.

> [!IMPORTANT]
> - Uppdateringarna är störande och starta om enheten. Om i/o som pågår, sker enheten. Detaljerade anvisningar om hur du installerar uppdateringen, går du till [installera uppdatering 1.0](storsimple-virtual-array-install-update-1.md).
>
> - Uppdatering 1 är bara tillgängliga för dig via Azure portal om enheten kör uppdatering 0.6.

## <a name="whats-new-in-update-10"></a>Vad är nytt i version 1.0

**Version 1.0 innehåller ändringar för autentisering av StorSimple Device Manager-tjänsten och bör distribueras i din så snart som möjligt.** Den här uppdateringen innehåller följande förbättringar och felkorrigeringar:

 - **Användning av Azure Active Directory (AAD) att autentisera med StorSimple Device Manager-tjänsten** – från version 1.0 och senare, Azure Active Directory används för att autentisera med StorSimple Device Manager-tjänsten. Gamla autentiseringsmekanismen upphör att gälla från och med December 2017. Alla användare måste ange de nya URL: er för autentisering på sina brandväggsregler. Mer information går du till autentisering URL: er i den [nätverkskrav för StorSimple Virtual Array](storsimple-ova-system-requirements.md).
 
    Om URL: en för autentisering inte är inkluderat i brandväggsreglerna, ser användarna en kritisk varning som deras StorSimple-enheten inte kunde autentisera med tjänsten. Om användarna ser den här aviseringen, som de behöver inkludera den nya URL: en för autentisering. Mer information går du till [StorSimple nätverk aviseringar](storsimple-virtual-array-manage-alerts.md).

 - **Förbättring av prestanda** -flera felkorrigeringar har utförts för att förbättra hastigheterna för molnet läsningar, molnläsningar och nivåskalning uppåt. Prestanda för både säkerhetskopiering och återställning har därför bättre för iSCSI och filen server-enheter.

 - **Skräpinsamling samling improvement** -den här versionen har felkorrigeringar som förbättrar prestandan för cykel för insamling av skräpinsamling när enheten och storage-konto finns i två avlägsna regioner.

 - **Loggning improvement** -den här versionen innehåller förbättringar för loggning som rör skräpinsamling och i/o-sökväg.


## <a name="issues-fixed-in-update-10"></a>Problem som åtgärdas i version 1.0

Följande tabell innehåller en översikt över problem som åtgärdas i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |AAD-baserad autentisering| Den här versionen innehåller ändringar som gör att AAD för att autentisera med StorSimple Device Manager.|
| 2 |Skräpinsamling| Det här problemet har rapporterats på kundens plats där enheten och storage-konton finns i olika regioner och kunden rapporteras tillfälliga nätverksfel därmed påverka faktureringen. Det här problemet har åtgärdats i den här versionen. |
| 3 |Prestanda| Den här versionen innehåller ändringar som kan leda till återställning/läsningar/molnnivån i / nivå ut förbättring av prestanda.|
| 4 |Uppdatering| Det uppstod ett problem med uppdateringen i den tidigare versionen som resulterade i fel vid säkerhetskopiering på kundens plats. Det här problemet löses i den här versionen.|

## <a name="known-issues-in-update-10"></a>Kända problem i version 1.0

I följande tabell innehåller en översikt över kända problem för StorSimple Virtual Array och omfattar problem versionen anges från tidigare versioner.

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Uppdateringar |De virtuella matriser som skapats i förhandsversionen kan inte uppdateras till en version som stöds är allmänt tillgängligt. |Dessa virtuella matriser måste flyttas över för den allmänt tillgängliga versionen med hjälp av ett arbetsflöde för disaster recovery (DR). |
| **2.** |Etablerade datadisk |När du har etablerat en datadisk med en viss angivna storlek och skapat motsvarande StorSimple Virtual Array, du måste inte öka eller minska datadisken. Om du försöker göra leder till förlust av alla data på de lokala nivåerna för enheten. | |
| **3.** |Grupprincip |När en enhet är ansluten till domänen, kan tillämpa en grupprincip påverkas negativt enheten. |Kontrollera att din virtuella matris är i sin egen organisationsenhet (OU) för Active Directory och inga grupprincipobjekt (GPO) tillämpas. |
| **4.** |Lokala webbgränssnittet |Om utökad säkerhet har aktiverats i Internet Explorer (IE ESC), vissa lokala webbsidor Användargränssnittet, till exempel felsökning eller underhåll kanske inte fungerar korrekt. Knapparna på dessa sidor kan också inte fungerar. |Stäng av funktioner för förbättrad säkerhet i Internet Explorer. |
| **5.** |Lokala webbgränssnittet |I en Hyper-V virtuell dator, nätverksgränssnitt i webb-UI visas som 10 Gbit/s-gränssnitt. |Detta är en avbildning av Hyper-V. Hyper-V visar alltid 10 Gbit/s för virtuella nätverkskort. |
| **6.** |Nivåindelade volymer eller resurser |Byte-intervallet låsning för program som fungerar med StorSimple nivåindelade volymer inte stöds. Om låsning av byte-intervallet är aktiverad, fungerar inte StorSimple lagringsnivåer. |Rekommenderade åtgärder är: <br></br>Inaktivera byteintervall låsning i programlogiken.<br></br>Välja att placera data för det här programmet i lokalt fixerade volymer till skillnad från nivåindelade volymer.<br></br>*Villkor för*: När med hjälp av lokalt fixerade volymer och låsning byte-intervallet är aktiverad, kan lokalt fixerad volym vara online, även innan återställningen är klar. I sådana fall om en återställning pågår måste sedan du vänta tills återställningen har slutförts. |
| **7.** |Nivåindelade filresurser |Arbeta med stora filer kan orsaka långsam ut nivå. |När du arbetar med stora filer, rekommenderar vi att den största filen är mindre än 3 procent av resursstorleken. |
| **8.** |Kapacitet för resurser som används av |Du kan se dela förbrukning när det finns inga data i resursen. Den här förbrukning beror på Använd kapacitet för filresurser innehåller metadata. | |
| **9.** |Haveriberedskap |Du kan bara utföra haveriberedskap för en server till samma domän som källenheten. Haveriberedskap till en målenhet i en annan domän stöds inte i den här versionen. |Detta är implementerat i en senare version. Mer information går du till [redundans och återställning vid återställning av StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |StorSimple Virtual Array kan inte hanteras via Azure PowerShell i den här versionen. |All hantering av virtuella enheter bör göras via Azure-portalen och det lokala webbgränssnittet. |
| **11.** |Lösenordsändring |Virtuell matris klientenhetskonsolen endast godkänner indata i en-us tangentbord format. | |
| **12.** |CHAP |CHAP-autentiseringsuppgifter som är skapade tas inte bort. Om du ändrar CHAP-autentiseringsuppgifter måste du även att frånkoppla volymerna och anpassa dem online för att ändringen ska börja gälla. |Det här problemet korrigeras i en senare version. |
| **13.** |iSCSI-servern |Den ”används storage” visas för en iSCSI-volymen kan skilja sig i StorSimple Device Manager-tjänsten och iSCSI-värden. |ISCSI-värden har vyn filsystem.<br></br>Enheten ser de block som tilldelas när volymen har vid den maximala storleken. |
| **14.** |Filserver |Om en fil i en mapp har en annan Data Stream (ADS) som är associerade med det, är ANNONSER inte säkerhetskopieras eller återställs via haveriberedskap, klona och återställning på objektnivå. | |
| **15.** |Filserver |Symboliska länkar stöds inte. | |
| **16.** |Filserver |Filer som skyddas av Windows Krypterande filsystem (EFS) när kopieras över eller lagras på StorSimple Virtual Array file server resultatet i en konfiguration som inte stöds.  | |
| **17.** |Uppdateringar |Om du ser fel kod: 2359302 (hex 0x240006) när du försöker installera en snabbkorrigering via lokala Användargränssnittet sedan detta innebär att snabbkorrigeringen har redan installerats på enheten.   | |
| **18.** |Uppdateringar |Om du använder det lokala webbgränssnittet för att installera uppdatering 1 på din virtuella matris, måste du kontrollera att du kör uppdatering 0.6. Om du kör en version lägre än uppdatering 0.6, måste du installera uppdatering 0.6 först och sedan tillämpar uppdatering 1. Om du installerar direkt version 1.0 från en 0,6 version före uppdateringen, sedan du missar några uppdateringar och Övervakningsdiagrammen fungerar inte.   | |


## <a name="next-steps"></a>Nästa steg
[Installera version 1.0](storsimple-virtual-array-install-update-1.md) på StorSimple Virtual Array.

## <a name="references"></a>Referenser
Letar du efter en äldre dokumentet? Gå till:
*  [StorSimple Virtual Array Update 0,6 viktig information](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0,5 viktig information](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0,4 viktig information](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 viktig information](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array uppdatering 0.1 och 0,2 viktig information](storsimple-ova-update-01-release-notes.md)
* [StorSimple virtuell matris allmän tillgänglighet viktig information](storsimple-ova-pp-release-notes.md)
