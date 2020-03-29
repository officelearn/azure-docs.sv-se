---
title: Viktig information om StorSimple Virtual Array Update 0.4| Microsoft-dokument
description: Beskriver kritiska öppna problem och lösningar för StorSimple Virtual Array som kör Uppdatering 0.4.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60334802"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>Viktig information om StorSimple Virtual Array Update 0.4

## <a name="overview"></a>Översikt

I följande viktig information identifieras de kritiska öppna problemen och de lösta problemen för Microsoft Azure StorSimple Virtual Array-uppdateringar.

Viktig information uppdateras kontinuerligt och när kritiska problem som kräver en lösning upptäcks läggs de till. Innan du distribuerar den virtuella storsimple-matrisen bör du noggrant granska informationen i viktig information.

Uppdatering 0.4 motsvarar programvaruversionen **10.0.10289.0**.

> [!NOTE]
> Uppdateringarna är störande och startar om enheten. Om I/O pågår ådrar sig enheten driftstopp.


## <a name="whats-new-in-the-update-04"></a>Nyheter i uppdateringen 0.4
Uppdatering 0,4 är främst en bug-fix bygga tillsammans med några förbättringar. I den här versionen har flera fel som resulterar i säkerhetskopieringsfel i den tidigare versionen åtgärdats. De viktigaste förbättringarna och buggfixarna är följande:

- **Prestandaförbättringar för säkerhetskopiering** – Den här versionen har gjort flera viktiga förbättringar för att förbättra säkerhetskopieringsprestandan. Som ett resultat, säkerhetskopior som involverar ett stort antal filer ser en betydande minskning av tiden för att slutföra, för fullständig och inkrementell säkerhetskopior.

- **Förbättrad återställningsprestanda** – Den här versionen innehåller förbättringar som avsevärt förbättrar återställningsprestandan när du använder ett stort antal filer. Om du använder 2 - 4 miljoner filer rekommenderar vi att du etablerar en virtuell matris med 16 GB RAM för att se förbättringarna. När du använder mindre än 2 miljoner filer fortsätter minimikravet för den virtuella datorn att vara 8 GB RAM.When using less than 2 million files, the minimum requirement for the virtual machine continues to be 8 GB RAM.

- **Förbättringar av supportpaketet** - Förbättringarna inkluderar loggning i statistiken för disk, CPU, minne, nätverk och moln i supportpaketet och därigenom förbättra processen för att diagnostisera / felsöka enhetsproblem.

- **Begränsa lokalt fästa iSCSI-volymer till 200 GB** - För lokalt fästa volymer rekommenderar vi att du begränsar till en 200 GB iSCSI-volym på din StorSimple Virtual Array. Den lokala reservationen för differentierade volymer fortsätter att vara 10 % av den avvägda volymstorleken, men är begränsad till 200 GB. 

- **Säkerhetskopieringsrelaterade buggfixar** - I tidigare versioner av programvara fanns det problem relaterade till säkerhetskopior som skulle orsaka säkerhetskopieringsfel. Dessa buggar har åtgärdats i den här versionen.


## <a name="issues-fixed-in-the-update-04"></a>Problem som åtgärdats i uppdateringen 0.4

I följande tabell finns en sammanfattning av problem som åtgärdats i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |Prestanda för säkerhetskopiering|I de tidigare versionerna skulle säkerhetskopiorna med ett stort antal filer ta lång tid att slutföra (i ordningen på dagarna). I den här versionen ser både fullständiga och inkrementella säkerhetskopior en betydande minskning av tiden till färdigställande. |
| 2 |Supportpaket|Disk-, CPU-, minnes-, nätverks- och molnstatistik loggas nu in i supportloggarna, vilket gör supportpaketen mycket effektiva när det gäller att felsöka eventuella enhetsproblem.|
| 3 |Säkerhetskopiering |I tidigare versioner kan tidskrävande säkerhetskopior resultera i en utrymmeskris på enheten som resulterar i säkerhetskopieringsfel. Det här felet åtgärdas i den här versionen genom att inte tillåta mer än 5 säkerhetskopior att köa samtidigt.|
| 4 |iSCSI | I tidigare versioner var den lokala reservationen för nivåindelade eller lokalt fästa volymer 10 % av den etablerade volymstorleken. I den här versionen är den lokala reservationen för alla iSCSI-volymer (lokalt fästa eller nivåindelade) begränsad till 10 % med högst 200 GB (för nivåindelade volymer som är större än 2 TB) vilket frigör mer utrymme på den lokala disken. Vi rekommenderar att de lokalt fästa volymerna i den här versionen begränsas till 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Kända problem i uppdateringen 0.4

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
| **8.** |Begagnad kapacitet för aktier |Du kan se förbrukning när det inte finns några data om resursen. Detta beror på att den använda kapaciteten för resurser innehåller metadata. | |
| **9.** |Haveriberedskap |Du kan bara utföra haveriberedskapen för en filserver till samma domän som källenheten. Haveriberedskap till en målenhet i en annan domän stöds inte i den här versionen. |Detta implementeras i en senare version. |
| **10.** |Azure PowerShell |De virtuella StorSimple-enheterna kan inte hanteras via Azure PowerShell i den här versionen. |All hantering av de virtuella enheterna bör göras via den klassiska Azure-portalen och det lokala webbgränssnittet. |
| **11.** |Lösenordsändring |Den virtuella matrisenhetskonsolen accepterar bara indata i en-US-tangentbordsformat. | |
| **12.** |CHAP |CHAP-autentiseringsuppgifter som en gång har skapats kan inte tas bort. Om du ändrar CHAP-autentiseringsuppgifterna måste du dessutom koppla från volymerna och sedan ansluta dem för att ändringen ska börja gälla. |Det här problemet åtgärdas i en senare version. |
| **13.** |iSCSI-server |Den "Använd lagring" som visas för en iSCSI-volym kan vara annorlunda i StorSimple Manager-tjänsten och iSCSI-värden. |ISCSI-värden har filsystemvyn.<br></br>Enheten ser de block som allokerats när volymen var maximal storlek. |
| **14.** |Filserver |Om en fil i en mapp har en associerad alternativ dataström (ADS) säkerhetskopieras eller återställs ADS inte via haveriberedskap, klon och återställning på objektnivå. | |
| **15.** |Filserver |Symboliska länkar stöds inte. | |
| **16.** |Filserver |Filer som skyddas av Windows Encrypting File System (EFS) när de kopieras över eller lagras på StorSimple Virtual Array-filservern resulterar i en konfiguration som inte stöds.  | |

## <a name="next-step"></a>Nästa steg
[Installera uppdatering 0.4](storsimple-virtual-array-install-update-04.md) på den virtuella storsimple-matrisen.

## <a name="references"></a>Referenser
Letar du efter en äldre release anteckning? Gå till: 

* [StorSimple Virtual Array Update 0.3 Viktig information](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 och 0.2 Viktig information](storsimple-ova-update-01-release-notes.md)
* [Viktig information om storsimple virtuell matris allmän tillgänglighet](storsimple-ova-pp-release-notes.md)

