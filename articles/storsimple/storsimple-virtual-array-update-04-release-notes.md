---
title: StorSimple virtuell matris uppdatering 0,4 viktig information | Microsoft Docs
description: Beskriver viktiga öppna problem och lösningar för den virtuella StorSimple-matrisen som kör uppdatering 0,4.
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
ms.openlocfilehash: cc2b025b7f3e28954c7f95409ffab03e5cbcf13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927763"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>StorSimple virtuell matris uppdatering 0,4 viktig information

## <a name="overview"></a>Översikt

I följande versionsinformation identifiera kritiska öppna problem och löst problem för Microsoft Azure StorSimple virtuell matris uppdateringar.

Viktig information uppdateras kontinuerligt, och allteftersom allvarliga problem som kräver en lösning upptäcks, läggs de. Granska noggrant uppgifterna i viktig information innan du distribuerar din virtuella StorSimple-matris.

Uppdatera 0,4 motsvarar programvaruversionen **10.0.10289.0**.

> [!NOTE]
> Uppdateringar kan störande och starta om enheten. Om i/o som pågår, ådrar enheten driftstopp.


## <a name="whats-new-in-the-update-04"></a>Vad är nytt i uppdateringen 0,4
Uppdatering 0,4 är i första hand en buggfix version som tillsammans med några förbättringar. I den här versionen har flera buggar som ledde till ett fel vid säkerhetskopiering i den tidigare versionen åtgärdats. De huvudsakliga förbättringar och korrigeringarna är följande:

- **Säkerhetskopiera prestandaförbättringar** -den här versionen har gjort flera viktiga förbättringar för att förbättra prestandan för säkerhetskopiering. Därför finns säkerhetskopieringar som omfattar ett stort antal filer i en betydande minskning av tid att slutföra, för fullständiga och inkrementella säkerhetskopior.

- **Förbättrad återställningsprestanda** -den här versionen innehåller förbättringar som förbättrar återställningsprestanda när du använder många filer. Med 2-4 miljoner filer, rekommenderar vi att du etablera en virtuell matris med 16 GB RAM-MINNET finns förbättringar. När du använder mindre än 2 miljoner filer, fortsätter minimikravet för den virtuella datorn att vara 8 GB RAM-minne.

- **Förbättringar av supportpaket** -förbättringarna omfattar loggning i statistik för disk, CPU, minne, nätverk och molntjänster i stöd paketet därigenom förbättra processen att diagnostisera/felsökning av problem med enheter.

- **Gränsen lokalt Fäst iSCSI-volymer till 200 GB** -för lokalt fästa volymer, rekommenderar vi att du begränsar till en iSCSI-200 GB-volym på din virtuella StorSimple-matrisen. Lokala reservationen för nivåindelade volymer fortsätter att vara 10% av etablerade volymens storlek men högst 200 GB. 

- **Backup-relaterade felkorrigeringar** – i tidigare versioner av programvaran, det fanns problem relaterade till säkerhetskopieringar som skulle orsaka fel vid säkerhetskopiering. Dessa programfel har åtgärdats i den här versionen.


## <a name="issues-fixed-in-the-update-04"></a>Problem som åtgärdas i uppdateringen 0,4

Följande tabell innehåller en översikt över problem som åtgärdas i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |Prestanda vid säkerhetskopiering|I tidigare versioner tar säkerhetskopieringar som inbegriper många filer lång tid att slutföra (i dagar). I den här versionen finns både fullständiga och inkrementella säkerhetskopior en betydande minskning av tid för slutförande. |
| 2 |Support-paket|Disk, CPU, minne, nätverk och molnet statistik är nu inloggad till Support loggar gör supportpaket mycket effektivt vid felsökning av problem med enheter.|
| 3 |Säkerhetskopiering |I tidigare versioner, kan långvariga säkerhetskopieringar resultera i en matar utrymme på enheten som ledde till ett fel vid säkerhetskopiering. Det här problemet åtgärdas i den här versionen genom att tillåta mer än 5 säkerhetskopieringar till kön i taget.|
| 4 |iSCSI | I tidigare versioner kunde lokala reservationen för nivåindelade eller lokalt fästa volymer 10% av etablerade volymens storlek. I den här versionen lokala reservationen för iSCSI-volymer (lokalt Fäst eller nivåer) är begränsad till 10% med högst upp till 200 GB (för nivåindelade volymer som är större än 2 TB) vilket frigöra mer utrymme på den lokala disken. Vi rekommenderar att lokalt fästa volymer i den här versionen är begränsad till 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Kända problem i uppdateringen 0,4

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
| **15.** |Filserver |Symboliska länkar stöds inte. | |
| **16.** |Filserver |Filer som skyddas av Windows Krypterande filsystem (EFS) när kopieras över eller på virtuella StorSimple-matris fil servern resulterar i en konfiguration som inte stöds.  | |

## <a name="next-step"></a>Nästa steg
[Installera uppdatering 0,4](storsimple-virtual-array-install-update-04.md) på din virtuella StorSimple-matrisen.

## <a name="references"></a>Referenser
Letar du efter en äldre version anteckning? Gå till: 

* [StorSimple virtuell matris Update 0,3 viktig information](storsimple-ova-update-03-release-notes.md)
* [StorSimple virtuell matris uppdatering 0.1 och 0,2 viktig information](storsimple-ova-update-01-release-notes.md)
* [StorSimple virtuell matris allmän tillgänglighet viktig information](storsimple-ova-pp-release-notes.md)

