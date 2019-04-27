---
title: StorSimple virtuell matris uppdateringar viktig information | Microsoft Docs
description: Beskriver viktiga öppna problem och lösningar för StorSimple Virtual Array som kör uppdatering 0.2 och 0.1.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629296"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Viktig information StorSimple Virtual Array uppdatering 0.2 och 0.1
## <a name="overview"></a>Översikt
Följande viktiga information identifiera kritiska öppna ärenden och löst problem för Microsoft Azure StorSimple Virtual Array-uppdateringar. (Microsoft Azure StorSimple Virtual Array är även känd som den virtuella enheten StorSimple lokala eller den virtuella StorSimple-enheten.) 

Den viktiga informationen uppdateras kontinuerligt och allteftersom allvarliga problem som kräver en lösning upptäcks, läggs de. Innan du distribuerar din virtuella StorSimple-enhet måste du noga igenom uppgifterna i viktig information.

Uppdatering 0.2 motsvarar programvaruversionen **10.0.10280.0**; Uppdatering 0.1 är version **10.0.10279.0**. I avsnitten nedan listas ändringarna för varje uppdatering. 

> [!NOTE]
> Uppdateringarna är störande och startar om enheten. Om i/o som pågår, debiteras enheten driftstopp.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problem som åtgärdas i uppdatering 0.2
Uppdatering 0.2 innehåller alla ändringar från uppdatering 0.1 förutom korrigeringen som beskrivs i följande tabell:

| Funktion | Problem |
| --- | --- |
| Uppdateringar |I den senaste versionen kan identifieras uppdateringar inte automatiskt i den klassiska Azure-portalen, så var du tvungen att använda det lokala Webbgränssnittet för att installera uppdateringar. Det här problemet löses i den här versionen. När du har installerat uppdatering 0.2, kan du installera framtida uppdateringar med hjälp av den klassiska Azure-portalen. |

## <a name="whats-new-in-the-update-01"></a>Vad är nytt i uppdatering 0.1
Uppdatering 0.1 innehåller följande felkorrigeringar och förbättringar. 

* **Förbättrad flexibilitet för molnet avbrott**: Den här versionen har flera felkorrigeringar kring haveriberedskap, säkerhetskopiering, återställning och lagringsnivåer i händelse av ett avbrott för anslutning av molnet. 
* **Förbättrad återställningsprestanda**: Den här versionen har felkorrigeringar som har avsevärt minska tidsåtgången för återställningsjobb.
* **Automatiserad optimering för frigöring av utrymme**: När data tas bort på tunt allokerade volymer, måste de oanvända storage blocken återtas. Den här versionen har förbättrat utrymme frigöring processen från molnet, vilket resulterar i det oanvända utrymmet som blir tillgängliga snabbare jämfört med tidigare versioner.
* **Den nya virtuella diskavbildningar**: Nya virtuella Hårddisken, VHDX och VMDK är nu tillgängliga via den klassiska Azure-portalen. Du kan hämta dessa bilder för att etablera nya uppdatering 0.1-enheter.
* **Förbättra Jobbstatus i portalen**: Jobbstatus rapportering i portalen var inte detaljerade i den tidigare versionen av programvaran. Det här problemet är löst i den här versionen.
* **Domän join upplevelse**: Felkorrigeringar som rör domänanslutning och byta namn på enheten.

## <a name="issues-fixed-in-the-update-01"></a>Problem som åtgärdas i uppdatering 0.1
Följande tabell innehåller en översikt över problem som åtgärdas i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |VMDK |I vissa versioner av VMware har OS-disken används som sparse orsakar aviseringar och störa normal drift. Detta har åtgärdats i den här versionen. |
| 2 |iSCSI-servern |I den senaste versionen, har användaren måste ange en gateway för varje aktiverade nätverksgränssnitt på den virtuella StorSimple-enheten. Det här beteendet ändras i den här versionen så att användaren måste konfigurera minst en gateway för alla aktiverade nätverksgränssnitt. |
| 3 |Supportpaket |I den tidigare versionen av programvaran Supportloggar paketet misslyckades när paketet storlekar var större än 1 GB. Det här problemet löses i den här versionen. |
| 4 |Molnåtkomst |I den senaste versionen om StorSimple Virtual Array inte har nätverksanslutning och startades om skulle lokala Användargränssnittet ha problem med nätverksanslutningen. Det här problemet åtgärdas i den här versionen. |
| 5 |Övervakning av diagram |I den tidigare versionen, efter en redundansväxling av enhet, visas molnet kapacitet användning diagrammen felaktiga värden i den klassiska Azure-portalen. Detta åtgärdas i den aktuella versionen. |

## <a name="known-issues-in-the-update-01"></a>Kända problem i uppdatering 0.1
I följande tabell innehåller en översikt över kända problem för StorSimple Virtual Array och omfattar problem versionen anges från tidigare versioner. **Problem-versionen som anges i den här versionen är markerade med en asterisk. Nästan alla problem i den här listan har överföras från GA-versionen av StorSimple Virtual Array.**

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Uppdateringar |De virtuella enheter som skapats i förhandsversionen kan inte uppdateras till en version som stöds är allmänt tillgängligt. |Dessa virtuella enheter måste flyttas över för den allmänt tillgängliga versionen med hjälp av ett arbetsflöde för disaster recovery (DR). |
| **2.** |Etablerade datadisk |När du har etablerat en datadisk med en viss angivna storlek och skapat den motsvarande virtuella StorSimple-enheten, du måste inte öka eller minska datadisken. Försök att göra detta leder till förlust av alla data på de lokala nivåerna för enheten. | |
| **3.** |Grupprincip |När en enhet är ansluten till domänen, kan tillämpa en grupprincip påverkas negativt enheten. |Kontrollera att din virtuella matris är i sin egen organisationsenhet (OU) för Active Directory och inga grupprincipobjekt (GPO) tillämpas. |
| **4.** |Lokala webbgränssnittet |Om utökad säkerhet har aktiverats i Internet Explorer (IE ESC), vissa lokala webbsidor Användargränssnittet, till exempel felsökning eller underhåll kanske inte fungerar korrekt. Knapparna på dessa sidor kan också inte fungerar. |Stäng av funktioner för förbättrad säkerhet i Internet Explorer. |
| **5.** |Lokala webbgränssnittet |I en Hyper-V virtuell dator, nätverksgränssnitt i webb-UI visas som 10 Gbit/s-gränssnitt. |Detta är en avbildning av Hyper-V. Hyper-V visar alltid 10 Gbit/s för virtuella nätverkskort. |
| **6.** |Nivåindelade volymer eller resurser |Byte-intervallet låsning för program som fungerar med StorSimple nivåindelade volymer inte stöds. Om låsning av byte-intervallet är aktiverad, fungerar inte StorSimple lagringsnivåer. |Rekommenderade åtgärder är: <br></br>Inaktivera byteintervall låsning i programlogiken.<br></br>Välja att placera data för det här programmet i lokalt fixerade volymer till skillnad från nivåindelade volymer.<br></br>*Villkor för*: Om med hjälp av lokalt fixerade volymer och låsning byte-intervallet är aktiverad, Tänk på att lokalt fixerad volym kan vara online och med innan återställningen är slutförd. I sådana fall om en återställning pågår måste sedan du vänta tills återställningen har slutförts. |
| **7.** |Nivåindelade filresurser |Arbeta med stora filer kan orsaka långsam ut nivå. |När du arbetar med stora filer, rekommenderar vi att den största filen är mindre än 3 procent av resursstorleken. |
| **8.** |Kapacitet för resurser som används av |Du kan se dela förbrukning i avsaknad av alla data på resursen. Det beror på att Använd kapacitet för filresurser innehåller metadata. | |
| **9.** |Haveriberedskap |Du kan bara utföra haveriberedskap för en server till samma domän som källenheten. Haveriberedskap till en målenhet i en annan domän stöds inte i den här versionen. |Detta kommer att implementeras i en senare version. |
| **10.** |Azure PowerShell |StorSimple-enheter kan inte hanteras via Azure PowerShell i den här versionen. |All hantering av virtuella enheter bör göras via den klassiska Azure-portalen och det lokala webbgränssnittet. |
| **11.** |Lösenordsändring |Virtuell matris klientenhetskonsolen accepterar endast indata i en-US tangentbord format. | |
| **12.** |CHAP |CHAP-autentiseringsuppgifter som är skapade tas inte bort. Om du ändrar CHAP-autentiseringsuppgifter, behöver du dessutom att frånkoppla volymerna och anpassa dem online för att ändringen ska börja gälla. |Dessa kommer att åtgärdas i en senare version. |
| **13.** |iSCSI-servern |Den ”används storage” visas för en iSCSI-volymen kan skilja sig i StorSimple Manager-tjänsten och iSCSI-värden. |ISCSI-värden har vyn filsystem.<br></br>Enheten ser de block som tilldelas när volymen har vid den maximala storleken. |
| **14.** |Filen server * |Om en fil i en mapp har en annan Data Stream (ADS) som är associerade med det, är ANNONSER inte säkerhetskopieras eller återställs via haveriberedskap, klona och återställning på objektnivå. | |

## <a name="next-step"></a>Nästa steg
[Installera uppdateringar](storsimple-ova-install-update-01.md) på StorSimple Virtual Array.

