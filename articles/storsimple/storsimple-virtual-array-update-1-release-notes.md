---
title: Viktig information om StorSimple Virtual Array Update 1.0
description: Beskriver kritiska öppna problem och lösningar för StorSimple Virtual Array som kör Uppdatering 1.0.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 48dec3a87ab540af224ae4ac59dd37cee7c9d0ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271345"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>Viktig information om StorSimple Virtual Array Update 1.0

## <a name="overview"></a>Översikt

I följande viktig information identifieras de kritiska öppna problemen och de lösta problemen för Microsoft Azure StorSimple Virtual Array-uppdateringar.

Viktig information uppdateras kontinuerligt och när kritiska problem som kräver en lösning upptäcks läggs de till. Innan du distribuerar den virtuella storsimple-matrisen bör du noggrant granska informationen i viktig information.

Uppdatering 1.0 motsvarar programvaruversionen **10.0.10296.0**.

> [!IMPORTANT]
> - Uppdateringarna är störande och startar om enheten. Om I/O pågår ådrar sig enheten driftstopp. Detaljerade instruktioner om hur du installerar uppdateringen finns i [Installera uppdatering 1.0](storsimple-virtual-array-install-update-1.md).
>
> - Uppdatering 1 är endast tillgänglig för dig via Azure-portalen om din enhet kör Uppdatering 0.6.

## <a name="whats-new-in-update-10"></a>Nyheter i uppdatering 1.0

**Uppdatering 1.0 innehåller ändringar relaterade till autentisering av StorSimple Device Manager-tjänsten och bör distribueras tidigast.** Den här uppdateringen innehåller följande förbättringar och buggfixar:

 - **Användning av Azure Active Directory (AAD) för att autentisera med Tjänsten StorSimple Device Manager** – Från uppdatering 1.0 och framåt används Azure Active Directory för att autentisera med Tjänsten StorSimple Device Manager. Den gamla autentiseringsmekanismen kommer att vara inaktuell i december 2017. Alla användare måste inkludera de nya autentiserings-URL:erna i sina brandväggsregler. Mer information finns i url:er för autentisering som anges i [nätverkskraven för den virtuella storsimple-matrisen](storsimple-ova-system-requirements.md).
 
    Om autentiserings-URL:en inte ingår i brandväggsreglerna visas en kritisk avisering om att deras StorSimple-enhet inte kunde autentisera med tjänsten. Om användarna ser den här aviseringen måste de inkludera den nya autentiserings-URL:en. Mer information finns i [StorSimples nätverksaviseringar](storsimple-virtual-array-manage-alerts.md).

 - **Prestandaförbättring** - Flera buggfixar gjordes för att förbättra hastigheten för molnläsningar, nivåindelningar och nivå-outs. Därför har både säkerhetskopierings- och återställningsprestanda förbättrats för iSCSI- och filserverenheter.

 - **Skräpinsamling förbättring** - Den här versionen har buggfixar som förbättrar prestandan för skräpinsamling cykel när enheten och lagringskontot är i två avlägsna regioner.

 - **Loggningsförbättring** - Den här versionen innehåller förbättringar av loggning i samband med skräpinsamling och I/O-sökväg.


## <a name="issues-fixed-in-update-10"></a>Problem som åtgärdats i uppdatering 1.0

I följande tabell finns en sammanfattning av problem som åtgärdats i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |AAD-baserad autentisering| Den här versionen innehåller ändringar som gör att AAD kan autentisera med StorSimple Device Manager.|
| 2 |Skräpinsamling| Det här problemet rapporterades på en kundplats där enhets- och lagringskontona finns i olika regioner och kunden rapporterade intermittenta nätverksfel vilket påverkade faktureringen. I den här versionen åtgärdades det här problemet. |
| 3 |Prestanda| Den här versionen innehåller ändringar som resulterar i återställning/molnläsning/nivå i/nivå ut prestandaförbättring.|
| 4 |Uppdatering| Det uppstod ett problem med uppdateringen i den tidigare versionen som resulterade i säkerhetskopieringsfel på en kundwebbplats. Det här problemet åtgärdas i den här versionen.|

## <a name="known-issues-in-update-10"></a>Kända problem i uppdatering 1.0

Följande tabell innehåller en sammanfattning av kända problem för StorSimple Virtual Array och innehåller de problem som har släppts från tidigare versioner.

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Uppdateringar |De virtuella matriser som skapas i förhandsversionen kan inte uppdateras till en version med allmän tillgänglighet som stöds. |Dessa virtuella matriser måste ha misslyckats för den allmänna tillgänglighetsutgåvan med hjälp av ett DR-arbetsflöde (Disaster Recovery). |
| **2.** |Etablerad datadisk |När du har etablerat en datadisk med en viss angiven storlek och skapat motsvarande StorSimple Virtual Array får du inte expandera eller förminska datadisken. Försöker göra resulterar i en förlust av alla data i de lokala nivåerna på enheten. | |
| **3.** |Grupprincip |När en enhet är domänansluten kan en grupprincip påverka enhetsåtgärden negativt om du tillämpar en grupprincip. |Kontrollera att den virtuella matrisen finns i en egen organisationsenhet (OU) för Active Directory och att inga grupprincipobjekt (GPO) tillämpas på den. |
| **4.** |Lokalt webbgränssnitt |Om förbättrade säkerhetsfunktioner är aktiverade i Internet Explorer (IE ESC) kanske vissa lokala webbgränssnittssidor, till exempel Felsökning eller Underhåll, inte fungerar som de ska. Knappar på dessa sidor kanske inte heller fungerar. |Inaktivera förbättrade säkerhetsfunktioner i Internet Explorer. |
| **5.** |Lokalt webbgränssnitt |I en virtuell Hyper-V-dator visas nätverksgränssnitten i webbgränssnittet som 10 Gbit/s-gränssnitt. |Detta är en återspegling av Hyper-V. Hyper-V visar alltid 10 Gbit/s för virtuella nätverkskort. |
| **6.** |Differentierade volymer eller andelar |Byte intervall låsning för program som fungerar med StorSimple nivåindelade volymer stöds inte. Om byteintervalllåsning är aktiverat fungerar inte StorSimple-nivåindelning. |Rekommenderade åtgärder inkluderar: <br></br>Inaktivera låsning av byteintervall i programlogiken.<br></br>Välj att placera data för det här programmet i lokalt fästa volymer i motsats till nivåindelade volymer.<br></br>*Varning*: När du använder lokalt fästa volymer och byte intervall låsning är aktiverad, lokalt fäst volym kan vara online redan innan återställningen är klar. I sådana fall, om en återställning pågår, måste du vänta på att återställningen ska slutföras. |
| **7.** |Nivåindelada aktier |Om du arbetar med stora filer kan det leda till långsam nivå ut. |När du arbetar med stora filer rekommenderar vi att den största filen är mindre än 3 % av resursstorleken. |
| **8.** |Begagnad kapacitet för aktier |Du kan se förbrukning när det inte finns några data om resursen. Den här förbrukningen beror på att den använda kapaciteten för resurser innehåller metadata. | |
| **9.** |Haveriberedskap |Du kan bara utföra haveriberedskapen för en filserver till samma domän som källenheten. Haveriberedskap till en målenhet i en annan domän stöds inte i den här versionen. |Detta implementeras i en senare version. Mer information finns i [Redundans och haveriberedskap för den virtuella storsimple-matrisen](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |StorSimple-virtuella matriser kan inte hanteras via Azure PowerShell i den här versionen. |All hantering av de virtuella enheterna bör göras via Azure-portalen och det lokala webbgränssnittet. |
| **11.** |Lösenordsändring |Den virtuella matrisenhetskonsolen accepterar bara indata i en-us-tangentbordsformat. | |
| **12.** |CHAP |CHAP-autentiseringsuppgifter som en gång har skapats kan inte tas bort. Om du ändrar CHAP-autentiseringsuppgifterna måste du dessutom koppla från volymerna och sedan ansluta dem för att ändringen ska börja gälla. |Det här problemet åtgärdas i en senare version. |
| **13.** |iSCSI-server |Den "Använd lagring" som visas för en iSCSI-volym kan vara annorlunda i Tjänsten StorSimple Device Manager och iSCSI-värden. |ISCSI-värden har filsystemvyn.<br></br>Enheten ser de block som allokerats när volymen var maximal storlek. |
| **14.** |Filserver |Om en fil i en mapp har en associerad alternativ dataström (ADS) säkerhetskopieras eller återställs ADS inte via haveriberedskap, klon och återställning på objektnivå. | |
| **15.** |Filserver |Symboliska länkar stöds inte. | |
| **16.** |Filserver |Filer som skyddas av Windows Encrypting File System (EFS) när de kopieras över eller lagras på StorSimple Virtual Array-filservern resulterar i en konfiguration som inte stöds.  | |
| **17.** |Uppdateringar |Om felkod visas: 2359302 (hex 0x240006) när du försöker installera en snabbkorrigering via det lokala användargränssnittet innebär det att snabbkorrigeringen redan är installerad på enheten.   | |
| **18.** |Uppdateringar |Om du använder det lokala webbgränssnittet för att installera uppdatering 1 på den virtuella matrisen måste du se till att du kör Uppdatering 0.6. Om du kör en version som är lägre än uppdatering 0.6 måste du installera uppdatering 0.6 först och sedan installera uppdatering 1. Om du installerar uppdatering 1.0 direkt från en version före uppdatering 0.6 kommer du att missa vissa uppdateringar och övervakningsdiagrammen fungerar inte.   | |


## <a name="next-steps"></a>Nästa steg
[Installera uppdatering 1.0](storsimple-virtual-array-install-update-1.md) på den virtuella storsimple-matrisen.

## <a name="references"></a>Referenser
Letar du efter en äldre release anteckning? Gå till:
*  [StorSimple Virtual Array Update 0.6 Viktig information](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 Viktig information](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 Viktig information](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Viktig information](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 och 0.2 Viktig information](storsimple-ova-update-01-release-notes.md)
* [Viktig information om storsimple virtuell matris allmän tillgänglighet](storsimple-ova-pp-release-notes.md)
