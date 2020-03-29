---
title: StorSimple Virtual Array Update 0.5 viktig information| Microsoft-dokument
description: Beskriver kritiska öppna problem och lösningar för StorSimple Virtual Array som kör Uppdatering 0.5.
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
ms.date: 05/08/2017
ms.author: alkohli
ms.openlocfilehash: 385d9126d578250064659153f6f0f54eec696790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60870680"
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>Viktig information om StorSimple Virtual Array Update 0.5

## <a name="overview"></a>Översikt

I följande viktig information identifieras de kritiska öppna problemen och de lösta problemen för Microsoft Azure StorSimple Virtual Array-uppdateringar.

Viktig information uppdateras kontinuerligt och när kritiska problem som kräver en lösning upptäcks läggs de till. Innan du distribuerar den virtuella storsimple-matrisen bör du noggrant granska informationen i viktig information.

Uppdatering 0,5 motsvarar programvaruversionen **10.0.10290.0**.

> [!NOTE]
> Uppdateringarna är störande och startar om enheten. Om I/O pågår ådrar sig enheten driftstopp. Detaljerade instruktioner om hur du installerar uppdateringen finns i [Installera uppdatering 0.5](storsimple-virtual-array-install-update-05.md).


## <a name="whats-new-in-the-update-05"></a>Nyheter i uppdateringen 0.5
Uppdatering 0,5 är främst en bug-fix build. De viktigaste förbättringarna och buggfixarna är följande:

- **Förbättringar av återhämtningssäkerhet** för säkerhetskopiering – Den här versionen har korrigeringar som förbättrar återhämtningen för säkerhetskopiering. I de tidigare versionerna gjordes säkerhetskopieringar på nytt endast för vissa undantag. Den här versionen försöker alla undantag för säkerhetskopiering och gör säkerhetskopiorna mer motståndskraftiga.

- **Uppdateringar för övervakning av lagringsanvändning** – Från och med den 30 juni 2017 kommer lagringsanvändningsövervakningen för StorSimple Virtual Device Series att dras tillbaka. Detta gäller övervakningsdiagram på alla virtuella matriser som kör uppdatering 0.4 eller lägre. Den här uppdateringen innehåller de ändringar som krävs för att du ska kunna fortsätta använda övervakning av lagringsanvändning i Azure-portalen. Installera den här viktiga uppdateringen före den 30 juni 2017 för att fortsätta använda övervakningsfunktionen.


## <a name="issues-fixed-in-the-update-05"></a>Problem som åtgärdats i uppdateringen 0,5

I följande tabell finns en sammanfattning av problem som åtgärdats i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |Återhämtning för säkerhetskopiering| I de tidigare versionerna gjordes säkerhetskopieringar på nytt endast för vissa undantag. Den här versionen innehåller en korrigering för att göra säkerhetskopior mer motståndskraftiga genom att försöka igen alla undantag för säkerhetskopiering.|
| 2 |Övervakning| Övervakningen av lagringsanvändning för StorSimple Virtual Device Series kommer att vara inaktuell från och med den 30 juni 2017. Den här åtgärden påverkar övervakningsdiagrammen på StorSimple Device Manager-tjänsten som körs på StorSimple Virtual Arrays (1200-modellen). Den här versionen innehåller uppdateringar som gör det möjligt för användaren att fortsätta användningen av lagringsanvändningsövervakning på de virtuella matriserna efter den 30 juni 2017.|
| 3 |Filserver| I de tidigare versionerna kan en användare av misstag kopiera krypterade filer till den virtuella matrisen. Den här versionen innehåller en korrigering som inte tillåter kopiering av krypterade filer till virtuell matris. Om enheten har befintliga krypterade filer före uppdateringen fortsätter säkerhetskopiorna att misslyckas tills alla krypterade filer tas bort från systemet. |


## <a name="known-issues-in-the-update-05"></a>Kända problem i uppdateringen 0.5

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

## <a name="next-step"></a>Nästa steg
[Installera uppdatering 0,5](storsimple-virtual-array-install-update-05.md) på den virtuella storsimple-matrisen.

## <a name="references"></a>Referenser
Letar du efter en äldre release anteckning? Gå till:

* [StorSimple Virtual Array Update 0.4 Viktig information](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Viktig information](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 och 0.2 Viktig information](storsimple-ova-update-01-release-notes.md)
* [Viktig information om storsimple virtuell matris allmän tillgänglighet](storsimple-ova-pp-release-notes.md)

