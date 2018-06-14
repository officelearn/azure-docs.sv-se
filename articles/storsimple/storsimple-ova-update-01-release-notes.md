---
title: StorSimple virtuell matris uppdateringar viktig information | Microsoft Docs
description: Beskriver viktiga öppna problem och lösningar för den virtuella StorSimple-matrisen som kör uppdatering 0,2 och 0,1.
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
ms.openlocfilehash: c4ccde9635b3874864baa9d4d262ff5ddcf2a425
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
ms.locfileid: "23875902"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>StorSimple virtuell matris uppdatera 0,2 och 0,1 viktig information
## <a name="overview"></a>Översikt
I följande versionsinformation identifiera kritiska öppna problem och löst problem för Microsoft Azure StorSimple virtuell matris uppdateringar. (Microsoft Azure StorSimple virtuell matrisen är även känd som den virtuella enheten StorSimple lokalt eller den virtuella enheten StorSimple.) 

Viktig information uppdateras kontinuerligt, och allteftersom allvarliga problem som kräver en lösning upptäcks, läggs de. Granska noggrant uppgifterna i viktig information innan du distribuerar din virtuella StorSimple-enhet.

Uppdatera 0,2 motsvarar programvaruversionen **10.0.10280.0**; Uppdatering 0.1 är version **10.0.10279.0**. I avsnitten nedan listas ändringarna för varje uppdatering. 

> [!NOTE]
> Uppdateringar kan störande och startar om enheten. Om i/o som pågår, orsakar enheten driftstopp.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problem som åtgärdas i uppdateringen 0,2
0,2 ingår alla ändringar från uppdatering 0.1 förutom korrigeringen som beskrivs i följande tabell:

| Funktion | Problem |
| --- | --- |
| Uppdateringar |I den senaste versionen identifieras uppdateringar inte automatiskt i den klassiska Azure-portalen så var du tvungen att använda lokala Webbgränssnittet för att installera uppdateringar. Det här problemet löses i den här versionen. När du har installerat uppdateringen 0,2 kan du installera framtida uppdateringar med hjälp av den klassiska Azure-portalen. |

## <a name="whats-new-in-the-update-01"></a>Vad är nytt i uppdatering 0.1
Uppdatering 0.1 innehåller följande felkorrigeringar och förbättringar. 

* **Förbättrad flexibilitet för molnet avbrott**: den här versionen har flera felkorrigeringar runt katastrofåterställning, säkerhetskopiering, återställning och skiktning vid ett avbrott för anslutning av molnet. 
* **Bättre återställningsprestanda**: den här versionen har felkorrigeringar har avsevärt minska slutförandetiden av återställningsjobb.
* **Automatisk optimering för frigöring av utrymme**: när data tas bort på tunt allokerade volymer oanvända lagring-Adressblock måste vara frigöras. Den här versionen har förbättrats utrymme frigöring processen från molnet, vilket resulterar i outnyttjat utrymme blir tillgängliga snabbare jämfört med tidigare versioner.
* **Den nya virtuella diskbilder**: nya VHD och VHDX VMDK är nu tillgängliga via den klassiska Azure-portalen. Du kan hämta dessa avbildningar för att etablera nya uppdatering 0.1 enheter.
* **Förbättra riktighet Jobbstatus i portalen**: I den tidigare versionen av programvaran jobbstatus rapportering i portalen inte detaljerade. Det här problemet är löst i den här versionen.
* **Domän koppling upplevelse**: felkorrigeringar rör domänanslutning och ändra namn på enheten.

## <a name="issues-fixed-in-the-update-01"></a>Problem som åtgärdas i uppdatering 0.1
Följande tabell innehåller en översikt över problem som åtgärdas i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |VMDK |I vissa versioner av VMware påträffades OS-disk som sparse orsakar aviseringar och störa normal drift. Detta åtgärdades i den här versionen. |
| 2 |iSCSI-server |I den senaste versionen var användaren måste ange en gateway för varje aktiverat nätverksgränssnittet för din virtuella StorSimple-enhet. Denna funktion har ändrats i den här versionen så att användaren måste konfigurera minst en gateway för alla aktiverade nätverkskort. |
| 3 |Support-paket |I den tidigare versionen av programvaran stöd paketet collection misslyckades när paketet storlekar var större än 1 GB. Det här problemet löses i den här versionen. |
| 4 |Molnåtkomst |I den senaste versionen om den virtuella StorSimple-matrisen hade inte ansluten till nätverket och startades om skulle lokala Användargränssnittet ha problem med nätverksanslutningen. Det här problemet åtgärdas i den här versionen. |
| 5 |Övervakning av diagram |I den tidigare versionen efter en växling på enheten, visas molnet kapacitet användning diagrammen felaktiga värden i den klassiska Azure-portalen. Detta är fast i den aktuella versionen. |

## <a name="known-issues-in-the-update-01"></a>Kända problem i uppdatering 0.1
Följande tabell innehåller en översikt över kända problem för den virtuella StorSimple-matrisen och omfattar problem versionen anges från tidigare versioner. **Problem-versionen som anges i den här versionen är markerade med en asterisk. Nästan alla problem i den här listan har överförts från GA-versionen av virtuella StorSimple-matris.**

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Uppdateringar |Virtuella enheter skapas i förhandsversionen kan inte uppdateras till en allmän tillgänglighet-version som stöds. |Dessa virtuella enheter måste flyttas över för allmän tillgänglighet versionen med hjälp av ett arbetsflöde för disaster recovery (DR). |
| **2.** |Etablerade datadisk |När du har etablerat en datadisk med en viss angiven storlek och skapa den virtuella enheten StorSimple motsvarande du måste inte öka eller minska datadisken. Försök att göra det leder till förlust av alla data i de lokala nivåerna för enheten. | |
| **3.** |Grupprincip |När en enhet är ansluten till domänen kan kan tillämpa en grupprincip påverkas negativt enhet. |Kontrollera att din virtuella matris är i sin egen organisationsenhet (OU) för Active Directory och inga grupprincipobjekt (GPO) tillämpas. |
| **4.** |Lokala webbgränssnittet |Om Förbättrad säkerhetsfunktioner är aktiverade i Internet Explorer (IE ESC), vissa lokala webbsidor Användargränssnittet, till exempel felsökning eller underhåll kanske inte fungerar korrekt. Knapparna på dessa sidor fungerar kanske inte heller. |Inaktivera Förbättrad säkerhetsfunktioner i Internet Explorer. |
| **5.** |Lokala webbgränssnittet |I en Hyper-V virtuell dator nätverksgränssnitt på den webbplatsen Användargränssnittet visas som 10 Gbit/s-gränssnitt. |Detta är en avbildning av Hyper-V. Hyper-V visas alltid 10 Gbit/s för virtuella nätverkskort. |
| **6.** |Nivåindelade volymer eller resurser |Byteintervall låsning för program som fungerar med StorSimple nivåindelade volymer inte stöds. Om låsning byte-intervallet är aktiverad, fungerar StorSimple skiktning inte. |Rekommenderade åtgärder är: <br></br>Inaktivera låsning i applogiken byte-intervallet.<br></br>Välj att placera data för det här programmet i lokalt fästa volymer och nivåindelade volymer.<br></br>*Begränsning*: om med hjälp av lokalt fästa volymer och låsa byte-intervallet är aktiverad, Tänk på att lokalt Fäst volym kan vara online innan återställningen är slutförd. I sådana fall om en återställning pågår måste sedan du vänta tills återställningen har slutförts. |
| **7.** |Nivåindelad resurser |Arbeta med stora filer kan resultera i långsamma nivån. |När du arbetar med stora filer, rekommenderar vi att den största filen är mindre än 3% av storleken på filresursen. |
| **8.** |Används kapacitet för resurser |Du kan se dela förbrukning i frånvaron av alla data på resursen. Det beror på att använda kapacitet för resurser som innehåller metadata. | |
| **9.** |Haveriberedskap |Du kan bara utföra katastrofåterställning för en filserver i samma domän som källan. Återställning till en målenhet i en annan domän stöds inte i den här versionen. |Detta kommer att genomföras i en senare version. |
| **10.** |Azure PowerShell |Virtuella StorSimple-enheter kan inte hanteras via Azure PowerShell i den här versionen. |Alla hanteringen av virtuella enheter som ska utföras via den klassiska Azure-portalen och lokala webbgränssnittet. |
| **11.** |Ändra lösenordet |Virtuella matris klientenhetskonsolen accepterar endast indata i en-US tangentbord format. | |
| **12.** |CHAP |CHAP autentiseringsuppgifter när skapat tas inte bort. Dessutom, om du ändrar CHAP-autentiseringsuppgifter måste volymerna offline och sedan tar dem online för att ändringarna ska börja gälla. |Dessa kommer att åtgärdas i en senare version. |
| **13.** |iSCSI-server |Den 'används för lagring, visas för en iSCSI-volym kan skilja sig i StorSimple Manager-tjänsten och iSCSI-värden. |ISCSI-värden har vyn filsystem.<br></br>Enheten ser block allokerade när volymen på den maximala storleken. |
| **14.** |Filen server * |Om en fil i en mapp har en alternativ Data dataström (ADS) som är associerade med den kan säkerhetskopieras eller återställs via katastrofåterställning och klona objektet återställning ANNONSER de inte. | |

## <a name="next-step"></a>Nästa steg
[Installera uppdateringar](storsimple-ova-install-update-01.md) på din virtuella StorSimple-matrisen.

