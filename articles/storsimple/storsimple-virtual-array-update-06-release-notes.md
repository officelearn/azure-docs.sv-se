---
title: Viktig information om StorSimple Virtual Array Update 0.6| Microsoft-dokument
description: Beskriver kritiska öppna problem och lösningar för StorSimple Virtual Array som kör Uppdatering 0.6.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60870714"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>Viktig information om StorSimple Virtual Array Update 0.6

## <a name="overview"></a>Översikt

I följande viktig information identifieras de kritiska öppna problemen och de lösta problemen för Microsoft Azure StorSimple Virtual Array-uppdateringar.

Viktig information uppdateras kontinuerligt och när kritiska problem som kräver en lösning upptäcks läggs de till. Innan du distribuerar den virtuella storsimple-matrisen bör du noggrant granska informationen i viktig information.

Uppdatering 0.6 motsvarar programvaruversionen **10.0.10293.0**.

> [!IMPORTANT]
> - Uppdateringarna är störande och startar om enheten. Om I/O pågår ådrar sig enheten driftstopp. Detaljerade instruktioner om hur du installerar uppdateringen finns i [Installera uppdatering 0.6](storsimple-virtual-array-install-update-06.md).
>
> - Vi rekommenderar starkt att du installerar uppdatering 0.6 omedelbart eftersom den innehåller viktiga säkerhetskorrigeringar.


## <a name="whats-new-in-the-update-06"></a>Nyheter i uppdateringen 0.6
Uppdatering 0.6 är en viktig uppdatering och bör distribueras omedelbart. Den hÃ¤r uppdateringen innehÃ¥r fÃ¶1 ã¥s: 

- **Windows Security-korrigeringar** - Den här versionen har **Windows-kritiska säkerhetskorrigeringar**. Läs följande säkerhetsuppdateringar om du vill ha mer information om säkerhetsproblemen och de tillhörande korrigeringarna:
    - [Kvalitetsuppdatering för säkerhet i december 2016 för Windows 8.1 och Windows Server 2012 R2](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [Kvalitetsuppdatering för säkerhet i mars 2017 för Windows 8.1 och Windows Server 2012 R2](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [9 maj 2017 –KB4019213 (uppdatering endast för säkerhet)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Återställningskorrigering** - I tidigare versioner fanns det ett fel som skulle förhindra att återställningen slutförs. Det här felet har åtgärdats i den här versionen.


## <a name="issues-fixed-in-the-update-06"></a>Problem som åtgärdats i uppdateringen 0.6

I följande tabell finns en sammanfattning av problem som åtgärdats i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |Säkerhet| Den här versionen innehåller viktiga Windows-säkerhetsuppdateringar. Vi föreslår att du installerar den här uppdateringen omedelbart.|
| 2 |Återställ| Under en återställning fanns det ett rastillstånd som skulle förhindra att återställningsjobbet slutförs. Buggfixet åtgärdar detta rastillstånd.|


## <a name="known-issues-in-the-update-06"></a>Kända problem i uppdateringen 0.6

Följande tabell innehåller en sammanfattning av kända problem för StorSimple Virtual Array och innehåller de problem som har släppts från tidigare versioner.

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Uppdateringar |De virtuella enheter som skapas i förhandsversionen kan inte uppdateras till en version med allmän tillgänglighet som stöds. |Dessa virtuella enheter måste ha misslyckats för den allmänna tillgänglighetsutgåvan med hjälp av ett DR-arbetsflöde (Disaster Recovery). |
| **2.** |Etablerad datadisk |När du har etablerat en datadisk med en viss angiven storlek och skapat motsvarande StorSimple-virtuell enhet får du inte expandera eller förminska datadisken. Försöker göra resulterar i en förlust av alla data i de lokala nivåerna på enheten. | |
| **3.** |Grupprincip |När en enhet är domänansluten kan en grupprincip påverka enhetsåtgärden negativt om du tillämpar en grupprincip. |Kontrollera att den virtuella matrisen finns i en egen organisationsenhet (OU) för Active Directory och att inga grupprincipobjekt (GPO) tillämpas på den. |
| **4.** |Lokalt webbgränssnitt |Om förbättrade säkerhetsfunktioner är aktiverade i Internet Explorer (IE ESC) kanske vissa lokala webbgränssnittssidor, till exempel Felsökning eller Underhåll, inte fungerar som de ska. Knappar på dessa sidor kanske inte heller fungerar. |Inaktivera förbättrade säkerhetsfunktioner i Internet Explorer. |
| **5.** |Lokalt webbgränssnitt |I en virtuell Hyper-V-dator visas nätverksgränssnitten i webbgränssnittet som 10 Gbit/s-gränssnitt. |Detta är en återspegling av Hyper-V. Hyper-V visar alltid 10 Gbit/s för virtuella nätverkskort. |
| **6.** |Differentierade volymer eller andelar |Byte intervall låsning för program som fungerar med StorSimple nivåindelade volymer stöds inte. Om byteintervalllåsning är aktiverat fungerar inte StorSimple-nivåindelning. |Rekommenderade åtgärder inkluderar: <br></br>Inaktivera låsning av byteintervall i programlogiken.<br></br>Välj att placera data för det här programmet i lokalt fästa volymer i motsats till nivåindelade volymer.<br></br>*Varning*: När du använder lokalt fästa volymer och byte intervall låsning är aktiverad, lokalt fäst volym kan vara online redan innan återställningen är klar. I sådana fall, om en återställning pågår, måste du vänta på att återställningen ska slutföras. |
| **7.** |Nivåindelada aktier |Om du arbetar med stora filer kan det leda till långsam nivå ut. |När du arbetar med stora filer rekommenderar vi att den största filen är mindre än 3 % av resursstorleken. |
| **8.** |Begagnad kapacitet för aktier |Du kan se förbrukning när det inte finns några data om resursen. Den här förbrukningen beror på att den använda kapaciteten för resurser innehåller metadata. | |
| **9.** |Haveriberedskap |Du kan bara utföra haveriberedskapen för en filserver till samma domän som källenheten. Haveriberedskap till en målenhet i en annan domän stöds inte i den här versionen. |Detta implementeras i en senare version. Mer information finns i [Redundans och haveriberedskap för den virtuella storsimple-matrisen](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |De virtuella StorSimple-enheterna kan inte hanteras via Azure PowerShell i den här versionen. |All hantering av de virtuella enheterna bör göras via Azure-portalen och det lokala webbgränssnittet. |
| **11.** |Lösenordsändring |Den virtuella matrisenhetskonsolen accepterar bara indata i en-us-tangentbordsformat. | |
| **12.** |CHAP |CHAP-autentiseringsuppgifter som en gång har skapats kan inte tas bort. Om du ändrar CHAP-autentiseringsuppgifterna måste du dessutom koppla från volymerna och sedan ansluta dem för att ändringen ska börja gälla. |Det här problemet åtgärdas i en senare version. |
| **13.** |iSCSI-server |Den "Använd lagring" som visas för en iSCSI-volym kan vara annorlunda i Tjänsten StorSimple Device Manager och iSCSI-värden. |ISCSI-värden har filsystemvyn.<br></br>Enheten ser de block som allokerats när volymen var maximal storlek. |
| **14.** |Filserver |Om en fil i en mapp har en associerad alternativ dataström (ADS) säkerhetskopieras eller återställs ADS inte via haveriberedskap, klon och återställning på objektnivå. | |
| **15.** |Filserver |Symboliska länkar stöds inte. | |
| **16.** |Filserver |Filer som skyddas av Windows Encrypting File System (EFS) när de kopieras över eller lagras på StorSimple Virtual Array-filservern resulterar i en konfiguration som inte stöds.  | |
| **17.** |Uppdateringar |Om felkod visas: 2359302 (hex 0x240006) när du försöker installera en snabbkorrigering via det lokala användargränssnittet innebär det att snabbkorrigeringen redan är installerad på enheten.   | |

## <a name="next-step"></a>Nästa steg
[Installera uppdatering 0.6](storsimple-virtual-array-install-update-06.md) på den virtuella storsimple-matrisen.

## <a name="references"></a>Referenser
Letar du efter en äldre release anteckning? Gå till:

* [StorSimple Virtual Array Update 0.5 Viktig information](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 Viktig information](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Viktig information](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 och 0.2 Viktig information](storsimple-ova-update-01-release-notes.md)
* [Viktig information om storsimple virtuell matris allmän tillgänglighet](storsimple-ova-pp-release-notes.md)

