---
title: StorSimple virtuell matris uppdatering 1.0 viktig information | Microsoft Docs
description: Beskriver viktiga öppna problem och lösningar för den virtuella StorSimple-matrisen som kör uppdatering 1.0.
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
ms.openlocfilehash: 777718c4893f1edab3613be5dc941e2716d4c972
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
ms.locfileid: "24012108"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>StorSimple virtuell matris uppdatering 1.0 viktig information

## <a name="overview"></a>Översikt

I följande versionsinformation identifiera kritiska öppna problem och löst problem för Microsoft Azure StorSimple virtuell matris uppdateringar.

Viktig information uppdateras kontinuerligt, och allteftersom allvarliga problem som kräver en lösning upptäcks, läggs de. Granska noggrant uppgifterna i viktig information innan du distribuerar din virtuella StorSimple-matris.

Uppdatering 1.0 motsvarar programvaruversionen **10.0.10296.0**.

> [!IMPORTANT]
> - Uppdateringar kan störande och starta om enheten. Om i/o som pågår, ådrar enheten driftstopp. Detaljerade anvisningar om hur du installerar uppdateringen, gå till [installera uppdatering 1.0](storsimple-virtual-array-install-update-1.md).
>
> - Uppdatering 1 är bara tillgängliga via Azure portal om enheten kör uppdatering 0,6.

## <a name="whats-new-in-update-10"></a>Vad är nytt i uppdatering 1.0

**Uppdatering 1.0 innehåller ändringar som rör verifiering av StorSimple enheten Manager-tjänsten och bör distribueras på din tidigaste.** Den här uppdateringen innehåller följande förbättringar och felkorrigeringar:

 - **Användning av Azure Active Directory (AAD) att autentisera med StorSimple Enhetshanteraren tjänsten** – från uppdatering 1.0 och senare, Azure Active Directory används för att autentisera med tjänsten StorSimple Enhetshanteraren. Den gamla autentiseringsmekanismen att bli inaktuell med December 2017. Alla användare måste inkludera de nya URL: er för autentisering i sina brandväggsregler. Mer information finns på autentisering webbadresserna i den [nätverkskrav för din virtuella StorSimple-matris](storsimple-ova-system-requirements.md).
 
    Om URL: en för autentisering inte ingår i brandväggsreglerna, ser användarna en kritisk varning som sin StorSimple-enhet inte kunde autentisera med tjänsten. Om användarna kan se den här aviseringen, behöver de ta den nya URL för autentisering. Mer information finns på [StorSimple nätverk aviseringar](storsimple-virtual-array-manage-alerts.md).

 - **Prestandaförbättring** -flera felkorrigeringar har gjort för att förbättra hastigheterna för molnet läsning, nivå-moduler och nivå-timeout. Därför kan har prestandan för både säkerhetskopiering och återställning förbättrats för iSCSI- och server-enheter.

 - **Skräp samling improvement** -den här versionen har felkorrigeringar som förbättrar prestandan för cykel för insamling av skräp när enheten och storage-kontot är i två avlägsna regioner.

 - **Loggning improvement** -den här versionen innehåller förbättringar av loggning som rör skräpinsamling och i/o-sökväg.


## <a name="issues-fixed-in-update-10"></a>Problem som åtgärdas i uppdatering 1.0

Följande tabell innehåller en översikt över problem som åtgärdas i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |AAD-baserad autentisering| Den här versionen innehåller ändringar som gör att AAD att autentisera med StorSimple Enhetshanteraren.|
| 2 |Skräpinsamling| Det här problemet har rapporterats på kundens plats där enheten och lagring finns i olika regioner och kunden rapporterade tillfälliga nätverksfel därmed påverka faktureringen. Det här problemet åtgärdades i den här versionen. |
| 3 |Prestanda| Den här versionen innehåller ändringar som kan leda till återställning eller ett moln läsningar/nivå i / tjänstnivån ut förbättring av prestanda.|
| 4 |Uppdatering| Det uppstod ett problem med uppdateringen i den tidigare versionen som resulterade i fel vid säkerhetskopiering på kundens plats. Det här problemet löses i den här versionen.|

## <a name="known-issues-in-update-10"></a>Kända problem i uppdatering 1.0

Följande tabell innehåller en översikt över kända problem för den virtuella StorSimple-matrisen och omfattar problem versionen anges från tidigare versioner.

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Uppdateringar |De virtuella matriser som skapats i förhandsversionen kan inte uppdateras till en allmän tillgänglighet-version som stöds. |Dessa virtuella matriser måste flyttas över för allmän tillgänglighet versionen med hjälp av ett arbetsflöde för disaster recovery (DR). |
| **2.** |Etablerade datadisk |När du har etablerat en datadisk med en viss angiven storlek och skapa den motsvarande virtuella StorSimple-matrisen du måste inte öka eller minska datadisken. Om du försöker göra leder till förlust av alla data i de lokala nivåerna för enheten. | |
| **3.** |Grupprincip |När en enhet är ansluten till domänen kan kan tillämpa en grupprincip påverkas negativt enhet. |Kontrollera att din virtuella matris är i sin egen organisationsenhet (OU) för Active Directory och inga grupprincipobjekt (GPO) tillämpas. |
| **4.** |Lokala webbgränssnittet |Om Förbättrad säkerhetsfunktioner är aktiverade i Internet Explorer (IE ESC), vissa lokala webbsidor Användargränssnittet, till exempel felsökning eller underhåll kanske inte fungerar korrekt. Knapparna på dessa sidor fungerar kanske inte heller. |Inaktivera Förbättrad säkerhetsfunktioner i Internet Explorer. |
| **5.** |Lokala webbgränssnittet |I en Hyper-V virtuell dator nätverksgränssnitt på den webbplatsen Användargränssnittet visas som 10 Gbit/s-gränssnitt. |Detta är en avbildning av Hyper-V. Hyper-V visas alltid 10 Gbit/s för virtuella nätverkskort. |
| **6.** |Nivåindelade volymer eller resurser |Byteintervall låsning för program som fungerar med StorSimple nivåindelade volymer inte stöds. Om låsning byte-intervallet är aktiverad, fungerar StorSimple skiktning inte. |Rekommenderade åtgärder är: <br></br>Inaktivera låsning i applogiken byte-intervallet.<br></br>Välj att placera data för det här programmet i lokalt fästa volymer och nivåindelade volymer.<br></br>*Begränsning*: när med hjälp av lokalt fästa volymer och låsa byte-intervallet är aktiverat, lokalt Fäst volym kan det vara online innan återställningen är slutförd. I sådana fall om en återställning pågår måste sedan du vänta tills återställningen har slutförts. |
| **7.** |Nivåindelad resurser |Arbeta med stora filer kan resultera i långsamma nivån. |När du arbetar med stora filer, rekommenderar vi att den största filen är mindre än 3% av storleken på filresursen. |
| **8.** |Används kapacitet för resurser |Du kan se dela förbrukning när det finns inga data för resursen. Denna förbrukning beror Använd kapacitet för resurser som innehåller metadata. | |
| **9.** |Haveriberedskap |Du kan bara utföra katastrofåterställning för en filserver i samma domän som källan. Återställning till en målenhet i en annan domän stöds inte i den här versionen. |Detta är implementerad i en senare version. Mer information finns på [redundans och disaster recovery för din virtuella StorSimple-matris](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Den virtuella StorSimple-matriser kan inte hanteras via Azure PowerShell i den här versionen. |Alla hanteringen av virtuella enheter som ska utföras via Azure-portalen och lokala webbgränssnittet. |
| **11.** |Ändra lösenordet |Virtuella matris klientenhetskonsolen endast accepterar indata i en-us tangentbord format. | |
| **12.** |CHAP |CHAP autentiseringsuppgifter när skapat tas inte bort. Dessutom, om du ändrar CHAP autentiseringsuppgifterna behöver du ta volymerna offline och tar dem online för att ändringarna ska börja gälla. |Det här problemet åtgärdas i en senare version. |
| **13.** |iSCSI-server |Den 'används för lagring, visas för en iSCSI-volym kan skilja sig i Enhetshanteraren för StorSimple-tjänsten och iSCSI-värden. |ISCSI-värden har vyn filsystem.<br></br>Enheten ser block allokerade när volymen på den maximala storleken. |
| **14.** |Filserver |Om en fil i en mapp har en alternativ Data dataström (ADS) som är associerade med den kan säkerhetskopieras eller återställs via katastrofåterställning och klona objektet återställning ANNONSER de inte. | |
| **15.** |Filserver |Symboliska länkar stöds inte. | |
| **16.** |Filserver |Filer som skyddas av Windows Krypterande filsystem (EFS) när kopieras över eller på virtuella StorSimple-matris fil servern resulterar i en konfiguration som inte stöds.  | |
| **17.** |Uppdateringar |Om du ser fel kod: 2359302 (hex 0x240006) när du försöker installera en snabbkorrigering i användargränssnittet för lokala sedan detta innebär att snabbkorrigeringen är installerad på enheten.   | |
| **18.** |Uppdateringar |Om du använder lokala webbgränssnittet för att installera uppdatering 1 på din virtuella matris, måste du kontrollera att du kör uppdatering 0,6. Om du kör en version lägre än uppdatera 0,6, måste du först installera uppdateringen 0,6 och tillämpa sedan uppdatering 1. Om du installerar direkt uppdatering 1.0 från en före uppdateringen 0,6 version, sedan du missar vissa uppdateringar och övervakning diagram fungerar inte.   | |


## <a name="next-steps"></a>Nästa steg
[Installera uppdatering 1.0](storsimple-virtual-array-install-update-1.md) på din virtuella StorSimple-matrisen.

## <a name="references"></a>Referenser
Letar du efter en äldre version anteckning? Gå till:
*  [StorSimple virtuell matris Update 0,6 viktig information](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple virtuell matris Update 0,5 viktig information](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple virtuell matris Update 0,4 viktig information](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple virtuell matris Update 0,3 viktig information](storsimple-ova-update-03-release-notes.md)
* [StorSimple virtuell matris uppdatering 0.1 och 0,2 viktig information](storsimple-ova-update-01-release-notes.md)
* [StorSimple virtuell matris allmän tillgänglighet viktig information](storsimple-ova-pp-release-notes.md)
