---
title: StorSimple virtuell matris uppdatering 0,6 viktig information | Microsoft Docs
description: "Beskriver viktiga öppna problem och lösningar för den virtuella StorSimple-matrisen som kör uppdatering 0,6."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: af202cf652300ee7897eb2dede33f38058fc2837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>StorSimple virtuell matris uppdatering 0,6 viktig information

## <a name="overview"></a>Översikt

I följande versionsinformation identifiera kritiska öppna problem och löst problem för Microsoft Azure StorSimple virtuell matris uppdateringar.

Viktig information uppdateras kontinuerligt, och allteftersom allvarliga problem som kräver en lösning upptäcks, läggs de. Granska noggrant uppgifterna i viktig information innan du distribuerar din virtuella StorSimple-matris.

Uppdatera 0,6 motsvarar programvaruversionen **10.0.10293.0**.

> [!IMPORTANT]
> - Uppdateringar kan störande och starta om enheten. Om i/o som pågår, ådrar enheten driftstopp. Detaljerade anvisningar om hur du installerar uppdateringen, gå till [uppdatering 0,6](storsimple-virtual-array-install-update-06.md).
>
> - Vi rekommenderar starkt att du installerar uppdateringen 0,6 omedelbart eftersom den innehåller viktiga säkerhetskorrigeringar.


## <a name="whats-new-in-the-update-06"></a>Vad är nytt i uppdateringen 0,6
Uppdateringen 0,6 är en viktig uppdatering och bör distribueras direkt. Den här uppdateringen innehåller följande korrigeringar: 

- **Windows säkerhetskorrigeringar** -den här versionen har **Windows kritiska säkerhetskorrigeringar**. Granska följande säkerhetsuppdateringar för mer information om säkerhetsproblem och associerade korrigeringar:
    - [December 2016 säkerhet endast kvalitet uppdatering för Windows 8.1 och Windows Server 2012 R2](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [Mars 2017 säkerhet endast kvalitet uppdatering för Windows 8.1 och Windows Server 2012 R2](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [9 maj 2017 – KB4019213 (endast säkerhet uppdatering)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Återställa korrigera** -det uppstod ett fel som kan förhindra att återställningen slutförs i tidigare versioner. Det här felet har korrigerats i den här versionen.


## <a name="issues-fixed-in-the-update-06"></a>Problem som åtgärdas i uppdateringen 0,6

Följande tabell innehåller en översikt över problem som åtgärdas i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |Säkerhet| Den här versionen innehåller viktiga uppdateringar för Windows-säkerhet. Vi rekommenderar att du installerar uppdateringen omedelbart.|
| 2 |Återställ| Det uppstod ett konkurrenstillstånd som förhindrar att återställningsjobbet Slutför under en återställning. Buggfix löser den här konkurrenstillstånd.|


## <a name="known-issues-in-the-update-06"></a>Kända problem i uppdateringen 0,6

Följande tabell innehåller en översikt över kända problem för den virtuella StorSimple-matrisen och omfattar problem versionen anges från tidigare versioner.

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Uppdateringar |Virtuella enheter skapas i förhandsversionen kan inte uppdateras till en allmän tillgänglighet-version som stöds. |Dessa virtuella enheter måste flyttas över för allmän tillgänglighet versionen med hjälp av ett arbetsflöde för disaster recovery (DR). |
| **2.** |Etablerade datadisk |När du har etablerat en datadisk med en viss angiven storlek och skapa den virtuella enheten StorSimple motsvarande du måste inte öka eller minska datadisken. Om du försöker göra leder till förlust av alla data i de lokala nivåerna för enheten. | |
| **3.** |Grupprincip |När en enhet är ansluten till domänen kan kan tillämpa en grupprincip påverkas negativt enhet. |Kontrollera att din virtuella matris är i sin egen organisationsenhet (OU) för Active Directory och inga grupprincipobjekt (GPO) tillämpas. |
| **4.** |Lokala webbgränssnittet |Om Förbättrad säkerhetsfunktioner är aktiverade i Internet Explorer (IE ESC), vissa lokala webbsidor Användargränssnittet, till exempel felsökning eller underhåll kanske inte fungerar korrekt. Knapparna på dessa sidor fungerar kanske inte heller. |Inaktivera Förbättrad säkerhetsfunktioner i Internet Explorer. |
| **5.** |Lokala webbgränssnittet |I en Hyper-V virtuell dator nätverksgränssnitt på den webbplatsen Användargränssnittet visas som 10 Gbit/s-gränssnitt. |Detta är en avbildning av Hyper-V. Hyper-V visas alltid 10 Gbit/s för virtuella nätverkskort. |
| **6.** |Nivåindelade volymer eller resurser |Byteintervall låsning för program som fungerar med StorSimple nivåindelade volymer inte stöds. Om låsning byte-intervallet är aktiverad, fungerar StorSimple skiktning inte. |Rekommenderade åtgärder är: <br></br>Inaktivera låsning i applogiken byte-intervallet.<br></br>Välj att placera data för det här programmet i lokalt fästa volymer och nivåindelade volymer.<br></br>*Begränsning*: när med hjälp av lokalt fästa volymer och låsa byte-intervallet är aktiverat, lokalt Fäst volym kan det vara online innan återställningen är slutförd. I sådana fall om en återställning pågår måste sedan du vänta tills återställningen har slutförts. |
| **7.** |Nivåindelad resurser |Arbeta med stora filer kan resultera i långsamma nivån. |När du arbetar med stora filer, rekommenderar vi att den största filen är mindre än 3% av storleken på filresursen. |
| **8.** |Används kapacitet för resurser |Du kan se dela förbrukning när det finns inga data för resursen. Denna förbrukning beror Använd kapacitet för resurser som innehåller metadata. | |
| **9.** |Haveriberedskap |Du kan bara utföra katastrofåterställning för en filserver i samma domän som källan. Återställning till en målenhet i en annan domän stöds inte i den här versionen. |Detta är implementerad i en senare version. Mer information finns på [redundans och disaster recovery för din virtuella StorSimple-matris](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Virtuella StorSimple-enheter kan inte hanteras via Azure PowerShell i den här versionen. |Alla hanteringen av virtuella enheter som ska utföras via Azure-portalen och lokala webbgränssnittet. |
| **11.** |Ändra lösenordet |Virtuella matris klientenhetskonsolen endast accepterar indata i en-us tangentbord format. | |
| **12.** |CHAP |CHAP autentiseringsuppgifter när skapat tas inte bort. Dessutom, om du ändrar CHAP autentiseringsuppgifterna behöver du ta volymerna offline och tar dem online för att ändringarna ska börja gälla. |Det här problemet åtgärdas i en senare version. |
| **13.** |iSCSI-server |Den 'används för lagring, visas för en iSCSI-volym kan skilja sig i Enhetshanteraren för StorSimple-tjänsten och iSCSI-värden. |ISCSI-värden har vyn filsystem.<br></br>Enheten ser block allokerade när volymen på den maximala storleken. |
| **14.** |Filserver |Om en fil i en mapp har en alternativ Data dataström (ADS) som är associerade med den kan säkerhetskopieras eller återställs via katastrofåterställning och klona objektet återställning ANNONSER de inte. | |
| **15.** |Filserver |Symboliska länkar stöds inte. | |
| **16.** |Filserver |Filer som skyddas av Windows Krypterande filsystem (EFS) när kopieras över eller på virtuella StorSimple-matris fil servern resulterar i en konfiguration som inte stöds.  | |
| **17.** |Uppdateringar |Om du ser fel kod: 2359302 (hex 0x240006) när du försöker installera en snabbkorrigering i användargränssnittet för lokala sedan detta innebär att snabbkorrigeringen är installerad på enheten.   | |

## <a name="next-step"></a>Nästa steg
[Installera uppdatering 0,6](storsimple-virtual-array-install-update-06.md) på din virtuella StorSimple-matrisen.

## <a name="references"></a>Referenser
Letar du efter en äldre version anteckning? Gå till:

* [StorSimple virtuell matris Update 0,5 viktig information](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple virtuell matris Update 0,4 viktig information](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple virtuell matris Update 0,3 viktig information](storsimple-ova-update-03-release-notes.md)
* [StorSimple virtuell matris uppdatering 0.1 och 0,2 viktig information](storsimple-ova-update-01-release-notes.md)
* [StorSimple virtuell matris allmän tillgänglighet viktig information](storsimple-ova-pp-release-notes.md)

