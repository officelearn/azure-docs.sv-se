---
title: StorSimple virtuell matris uppdatering 0,4 viktig information | Microsoft Docs
description: Beskriver viktiga öppna problem och lösningar för StorSimple Virtual Array som kör uppdatering 0.4.
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
ms.date: 04/05/2017
ms.author: alkohli
ms.openlocfilehash: 06a3469507631d032535bce62b01d964e99dc603
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54319409"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>StorSimple virtuell matris uppdatering 0,4 viktig information

## <a name="overview"></a>Översikt

Följande viktiga information identifiera kritiska öppna ärenden och löst problem för Microsoft Azure StorSimple Virtual Array-uppdateringar.

Den viktiga informationen uppdateras kontinuerligt och allteftersom allvarliga problem som kräver en lösning upptäcks, läggs de. Innan du distribuerar din StorSimple Virtual Array noga igenom uppgifterna i viktig information.

Uppdatering 0.4 motsvarar programvaruversionen **10.0.10289.0**.

> [!NOTE]
> Uppdateringarna är störande och starta om enheten. Om i/o som pågår, sker enheten.


## <a name="whats-new-in-the-update-04"></a>Vad är nytt i uppdatering 0.4
Uppdatering 0.4 är främst en felkorrigering version som tillsammans med några förbättringar. I den här versionen har flera buggar, vilket resulterar i fel vid säkerhetskopiering i den tidigare versionen åtgärdats. Den huvudsakliga förbättringar och korrigeringar av fel är följande:

- **Säkerhetskopiera prestandaförbättringar** -den här versionen har gjort flera förbättringar för att förbättra säkerhetskopieringsprestanda. Därför finns säkerhetskopieringar som omfattar ett stort antal filer i en betydande minskning i tid att slutföra för fullständiga och inkrementella säkerhetskopior.

- **Förbättrad återställningsprestanda** -den här versionen innehåller förbättringar som förbättrar återställningsprestanda när du använder många filer. Om använder 2 – 4 miljoner filer, rekommenderar vi att du etablerar en virtuell matris med 16 GB RAM för att se på förbättringar. När du använder mindre än 2 miljoner filer är fortsätter minimikravet för den virtuella datorn att vara 8 GB RAM-minne.

- **Förbättringar av supportpaket** -förbättringarna innefattar loggning i statistik för disk, processor, minne, nätverk och molnet till supportpaket därmed förbättra processen för att diagnostisera/felsökning av problem med enheter.

- **Gränsen för lokalt fixerade iSCSI-volymer till 200 GB** -för lokalt fästa volymer, rekommenderar vi att du begränsar till en iSCSI-200 GB-volym på StorSimple Virtual Array. Lokal reservation för nivåindelade volymer fortsätter att vara 10% av den installerade volymstorleken men är högst 200 GB. 

- **Backup-relaterade felkorrigeringar** – i tidigare versioner av programvara, det fanns problem som rör säkerhetskopieringar som skulle leda till Säkerhetskopieringsfel. De här felen har åtgärdats i den här versionen.


## <a name="issues-fixed-in-the-update-04"></a>Problem som åtgärdas i uppdatering 0.4

Följande tabell innehåller en översikt över problem som åtgärdas i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |Prestanda vid säkerhetskopiering|I tidigare versioner, skulle de säkerhetskopior som involverar stort antal filer ta lång tid att slutföra (i dagar). I den här versionen finns både fullständiga och inkrementella säkerhetskopior i en betydande minskning av tid för slutförande. |
| 2 |Supportpaket|Disk, processor, minne, nätverk och molnet statistik är nu inloggad till Support-loggarna effektivt felsöka eventuella problem med att göra paket som stöd.|
| 3 |Backup |I tidigare versioner kan tidskrävande säkerhetskopieringar resultera i ett utrymme bearbeta på enheten, vilket resulterar i fel vid säkerhetskopiering. Den här buggen behandlas i den här versionen genom att tillåta mer än 5 säkerhetskopior i kö i taget.|
| 4 |iSCSI | Lokal reservation för nivåindelade eller lokalt fixerade volymer har 10% av den installerade volymstorleken i tidigare versioner. I den här versionen lokalt reserverat utrymme för alla iSCSI-volymer (lokalt Fäst eller nivåer) är begränsad till 10% med högst upp till 200 GB (för nivåindelade volymer som är större än 2 TB) därmed frigöra mer utrymme på den lokala disken. Vi rekommenderar att lokalt fixerade volymer i den här versionen är begränsad till 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Kända problem i uppdatering 0.4

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
| **15.** |Filserver |Symboliska länkar stöds inte. | |
| **16.** |Filserver |Filer som skyddas av Windows Krypterande filsystem (EFS) när kopieras över eller lagras på StorSimple Virtual Array file server resultatet i en konfiguration som inte stöds.  | |

## <a name="next-step"></a>Nästa steg
[Installera uppdatering 0.4](storsimple-virtual-array-install-update-04.md) på StorSimple Virtual Array.

## <a name="references"></a>Referenser
Letar du efter en äldre dokumentet? Gå till: 

* [StorSimple Virtual Array Update 0.3 viktig information](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array uppdatering 0.1 och 0,2 viktig information](storsimple-ova-update-01-release-notes.md)
* [StorSimple virtuell matris allmän tillgänglighet viktig information](storsimple-ova-pp-release-notes.md)

