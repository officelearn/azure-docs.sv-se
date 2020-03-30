---
title: Viktig information om StorSimple Virtual Array Updates| Microsoft-dokument
description: Beskriver kritiska öppna problem och lösningar för StorSimple Virtual Array som kör Uppdatering 0.2 och 0.1.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: aad60024187ca180c002f119f4b975e8f69796e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60629296"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Viktig information om StorSimple Virtual Array Update 0.2 och 0.1
## <a name="overview"></a>Översikt
I följande viktig information identifieras de kritiska öppna problemen och de lösta problemen för Microsoft Azure StorSimple Virtual Array-uppdateringar. (Microsoft Azure StorSimple Virtual Array kallas även den lokala storsimpleenheten eller den virtuella StorSimple-enheten.) 

Viktig information uppdateras kontinuerligt och när kritiska problem som kräver en lösning upptäcks läggs de till. Innan du distribuerar den virtuella StorSimple-enheten bör du noggrant granska informationen i viktig information.

Uppdatering 0.2 motsvarar programvaruversionen **10.0.10280.0**; Uppdatering 0.1 är version **10.0.10279.0**. I avsnitten nedan visas ändringarna för varje uppdatering. 

> [!NOTE]
> Uppdateringarna är störande och startar om enheten. Om I/O pågår kommer enheten att drabbas av driftstopp.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problem som åtgärdats i uppdateringen 0.2
Uppdatering 0.2 innehåller alla ändringar från uppdatering 0.1 utöver den korrigering som beskrivs i följande tabell:

| Funktion | Problem |
| --- | --- |
| Uppdateringar |I den senaste versionen upptäcktes inte uppdateringar automatiskt i den klassiska Azure-portalen, så du var tvungen att använda det lokala webbgränssnittet för att installera uppdateringar. Det här problemet åtgärdas i den här versionen. När du har installerat Uppdatering 0.2 kan du installera framtida uppdateringar med den klassiska Azure-portalen. |

## <a name="whats-new-in-the-update-01"></a>Nyheter i uppdateringen 0.1
Uppdatering 0.1 innehåller följande buggfixar och förbättringar. 

* **Förbättrad återhämtning för molnavbrott:** Den här versionen har flera buggfixar runt haveriberedskap, säkerhetskopiering, återställning och nivåindelning i händelse av ett avbrott i molnanslutningen. 
* **Förbättrad återställningsprestanda:** Den här versionen har buggfixar som avsevärt har minskat slutförandetiden för återställningsjobben.
* **Automatisk utrymme återvinning optimering:** När data tas bort på tunt etablerade volymer, de oanvända lagringsblock måste återkvitas. Den här versionen har förbättrat utrymme återvinningsprocessen från molnet vilket resulterar i oanvänt utrymme blir tillgängliga snabbare jämfört med tidigare versioner.
* **Nya virtuella diskavbildningar:** Ny VIRTUELLD, VHDX och VMDK är nu tillgängliga via den klassiska Azure-portalen. Du kan hämta dessa avbildningar för att etablera nya Update 0.1-enheter.
* **Förbättra noggrannheten för jobb status i portalen:** I den tidigare versionen av programvaran, jobbstatus rapportering i portalen var inte detaljerad. Det här problemet är löst i den här versionen.
* **Domänkopplingsupplevelse:** Buggfixar relaterade till domänanslutning och namnbyte av enheten.

## <a name="issues-fixed-in-the-update-01"></a>Problem som åtgärdats i uppdatering 0.1
I följande tabell finns en sammanfattning av problem som åtgärdats i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |Vmdk |I vissa VMware-versioner sågs OS-disken som glesa orsakar varningar och stör normala operationer. Detta åtgärdades i den här versionen. |
| 2 |iSCSI-server |I den senaste versionen var användaren tvungen att ange en gateway för varje aktiverat nätverksgränssnitt för din StorSimple virtuella enhet. Det här problemet ändras i den här versionen så att användaren måste konfigurera minst en gateway för alla aktiverade nätverksgränssnitt. |
| 3 |Supportpaket |I den tidigare versionen av programvaran misslyckades supportpaketets samling när paketstorlekarna var större än 1 GB. Det här problemet åtgärdas i den här versionen. |
| 4 |Molnåtkomst |Om StorSimple Virtual Array inte hade nätverksanslutning och startades om i den senaste versionen skulle det lokala användargränssnittet ha anslutningsproblem. Det här problemet är åtgärdat i den här versionen. |
| 5 |Övervakningsdiagram |I den tidigare versionen, efter en enhets redundans, visade molnkapacitetsutnyttjandediagrammen felaktiga värden i den klassiska Azure-portalen. Detta åtgärdas i den aktuella versionen. |

## <a name="known-issues-in-the-update-01"></a>Kända problem i uppdateringen 0.1
Följande tabell innehåller en sammanfattning av kända problem för StorSimple Virtual Array och innehåller de problem som har släppts från tidigare versioner. **De problem som anges i den här versionen är markerade med en asterisk. Nästan alla problem i den här listan har förts över från GA-versionen av StorSimple Virtual Array.**

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Uppdateringar |De virtuella enheter som skapas i förhandsversionen kan inte uppdateras till en version med allmän tillgänglighet som stöds. |Dessa virtuella enheter måste ha misslyckats för den allmänna tillgänglighetsutgåvan med hjälp av ett DR-arbetsflöde (Disaster Recovery). |
| **2.** |Etablerad datadisk |När du har etablerat en datadisk med en viss angiven storlek och skapat motsvarande StorSimple-virtuell enhet får du inte expandera eller förminska datadisken. Om du försöker göra det går alla data i enhetens lokala nivåer. | |
| **3.** |Grupprincip |När en enhet är domänansluten kan en grupprincip påverka enhetsåtgärden negativt om du tillämpar en grupprincip. |Kontrollera att den virtuella matrisen finns i en egen organisationsenhet (OU) för Active Directory och att inga grupprincipobjekt (GPO) tillämpas på den. |
| **4.** |Lokalt webbgränssnitt |Om förbättrade säkerhetsfunktioner är aktiverade i Internet Explorer (IE ESC) kanske vissa lokala webbgränssnittssidor, till exempel Felsökning eller Underhåll, inte fungerar som de ska. Knappar på dessa sidor kanske inte heller fungerar. |Inaktivera förbättrade säkerhetsfunktioner i Internet Explorer. |
| **5.** |Lokalt webbgränssnitt |I en virtuell Hyper-V-dator visas nätverksgränssnitten i webbgränssnittet som 10 Gbit/s-gränssnitt. |Detta är en återspegling av Hyper-V. Hyper-V visar alltid 10 Gbit/s för virtuella nätverkskort. |
| **6.** |Differentierade volymer eller andelar |Byte intervall låsning för program som fungerar med StorSimple nivåindelade volymer stöds inte. Om byteintervalllåsning är aktiverat fungerar inte StorSimple-nivåindelning. |Rekommenderade åtgärder inkluderar: <br></br>Inaktivera låsning av byteintervall i programlogiken.<br></br>Välj att placera data för det här programmet i lokalt fästa volymer i motsats till nivåindelade volymer.<br></br>*Varning*: Om det är aktiverat att använda lokalt fästa volymer och byteintervalllåsning bör du vara medveten om att den lokalt fästa volymen kan vara online redan innan återställningen är klar. I sådana fall, om en återställning pågår, måste du vänta på att återställningen ska slutföras. |
| **7.** |Nivåindelada aktier |Om du arbetar med stora filer kan det leda till långsam nivå ut. |När du arbetar med stora filer rekommenderar vi att den största filen är mindre än 3 % av resursstorleken. |
| **8.** |Begagnad kapacitet för aktier |Du kan se förbrukning i avsaknad av data på resursen. Detta beror på att den använda kapaciteten för resurser innehåller metadata. | |
| **9.** |Haveriberedskap |Du kan bara utföra haveriberedskapen för en filserver till samma domän som källenheten. Haveriberedskap till en målenhet i en annan domän stöds inte i den här versionen. |Detta kommer att implementeras i en senare utgåva. |
| **10.** |Azure PowerShell |De virtuella StorSimple-enheterna kan inte hanteras via Azure PowerShell i den här versionen. |All hantering av de virtuella enheterna bör göras via den klassiska Azure-portalen och det lokala webbgränssnittet. |
| **11.** |Lösenordsändring |Den virtuella matrisenhetskonsolen accepterar bara indata i en-US-tangentbordsformat. | |
| **12.** |CHAP |CHAP-autentiseringsuppgifter som en gång har skapats kan inte tas bort. Dessutom, om du ändrar CHAP-autentiseringsuppgifterna, måste du ta volymerna offline och sedan ansluta dem för att ändringen ska börja gälla. |Dessa kommer att tas upp i en senare utgåva. |
| **13.** |iSCSI-server |Den "Använd lagring" som visas för en iSCSI-volym kan vara annorlunda i StorSimple Manager-tjänsten och iSCSI-värden. |ISCSI-värden har filsystemvyn.<br></br>Enheten ser de block som allokerats när volymen var maximal storlek. |
| **14.** |Filserver* |Om en fil i en mapp har en associerad alternativ dataström (ADS) säkerhetskopieras eller återställs ADS inte via haveriberedskap, klon och återställning på objektnivå. | |

## <a name="next-step"></a>Nästa steg
[Installera uppdateringar](storsimple-ova-install-update-01.md) på den virtuella storsimple-matrisen.

