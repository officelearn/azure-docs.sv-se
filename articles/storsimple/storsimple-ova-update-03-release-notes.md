---
title: StorSimple virtuell matris uppdateringar viktig information | Microsoft Docs
description: "Beskriver viktiga öppna problem och lösningar för den virtuella StorSimple-matrisen som kör uppdatering 0.3."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: b197651a-3c40-4185-b23d-4c8f22cfa8f4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2016
ms.author: alkohli
ms.openlocfilehash: fe9d4f6b232e9abcf1fe9fc5657044b6c72fedb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>StorSimple virtuell matris uppdatering 0,3 viktig information
## <a name="overview"></a>Översikt
I följande versionsinformation identifiera kritiska öppna problem och löst problem för Microsoft Azure StorSimple virtuell matris uppdateringar.

Viktig information uppdateras kontinuerligt, och allteftersom allvarliga problem som kräver en lösning upptäcks, läggs de. Granska noggrant uppgifterna i viktig information innan du distribuerar din virtuella StorSimple-matris.

Uppdatering 0.3 motsvarar programvaruversionen **10.0.10288.0**.

> [!NOTE]
> Uppdateringar kan störande och starta om enheten. Om i/o som pågår, ådrar enheten driftstopp.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Vad är nytt i uppdatering 0.3
Uppdatering 0.3 är i första hand en buggfix version. I den här versionen har flera buggar som ledde till ett fel vid säkerhetskopiering i den tidigare versionen åtgärdats.

## <a name="issues-fixed-in-the-update-03"></a>Problem som åtgärdas i uppdatering 0.3
Följande tabell innehåller en översikt över problem som åtgärdas i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |Säkerhetskopior |Ett problem påträffades i den tidigare versionen där säkerhetskopiorna skulle misslyckas med att slutföra för en filresurs. Om det här problemet uppstod säkerhetskopieringsjobbet skulle misslyckas och en kritisk varning har aktiverats på StorSimple Manager-tjänsten för att meddela användaren. Det här problemet påverkade inte data på resurser eller åtkomst till data. Den grundläggande orsaken har identifierats och åtgärdats i den här versionen. <br></br> Korrigering gäller inte retroaktivt för resurser som redan ser det här problemet. Kunder som ser det här problemet bör du först tillämpa uppdatering 0.3 sedan kontakta Microsoft-supporten om du vill utföra en fullständig säkerhetskopiering för att åtgärda problemet. I stället för att kontakta Microsoft Support kan kunder även återställa till en ny resurs från en felfri säkerhetskopia för de berörda resurserna. |
| 2 |iSCSI |Ett problem påträffades i den tidigare versionen där volymerna försvinner när du kopierar data till en volym på den virtuella StorSimple-matrisen. Det här problemet åtgärdades i den här versionen. <br></br> Korrigeringar gälla bara på nyskapad volymer. Korrigeringar gäller inte retroaktivt för volymer som redan ser det här problemet. Kunder bör ta de berörda volymerna online via den klassiska Azure-portalen, utföra en säkerhetskopiering av dessa volymer och Återställ sedan dessa volymer på nya volymer. |

## <a name="known-issues-in-the-update-03"></a>Kända problem i uppdatering 0.3
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
| **8.** |Används kapacitet för resurser |Du kan se dela förbrukning när det finns inga data för resursen. Det beror på att använda kapacitet för resurser som innehåller metadata. | |
| **9.** |Haveriberedskap |Du kan bara utföra katastrofåterställning för en filserver i samma domän som källan. Återställning till en målenhet i en annan domän stöds inte i den här versionen. |Detta är implementerad i en senare version. |
| **10.** |Azure PowerShell |Virtuella StorSimple-enheter kan inte hanteras via Azure PowerShell i den här versionen. |Alla hanteringen av virtuella enheter som ska utföras via den klassiska Azure-portalen och lokala webbgränssnittet. |
| **11.** |Ändra lösenordet |Virtuella matris klientenhetskonsolen accepterar endast indata i en-US tangentbord format. | |
| **12.** |CHAP |CHAP autentiseringsuppgifter när skapat tas inte bort. Dessutom, om du ändrar CHAP autentiseringsuppgifterna behöver du ta volymerna offline och tar dem online för att ändringarna ska börja gälla. |Det här problemet åtgärdas i en senare version. |
| **13.** |iSCSI-server |Den 'används för lagring, visas för en iSCSI-volym kan skilja sig i StorSimple Manager-tjänsten och iSCSI-värden. |ISCSI-värden har vyn filsystem.<br></br>Enheten ser block allokerade när volymen på den maximala storleken. |
| **14.** |Filserver |Om en fil i en mapp har en alternativ Data dataström (ADS) som är associerade med den kan säkerhetskopieras eller återställs via katastrofåterställning och klona objektet återställning ANNONSER de inte. | |

## <a name="next-step"></a>Nästa steg
[Installera uppdatering 0.3](storsimple-ova-install-update-01.md) på din virtuella StorSimple-matrisen.

## <a name="references"></a>Referenser
Letar du efter en äldre version anteckning? Gå till: 

* [StorSimple virtuell matris uppdatering 0.1 och 0,2 viktig information](storsimple-ova-update-01-release-notes.md)
* [StorSimple virtuell matris allmän tillgänglighet viktig information](storsimple-ova-pp-release-notes.md)

