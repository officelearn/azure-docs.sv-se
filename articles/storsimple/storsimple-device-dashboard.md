---
title: "Instrumentpanel för StorSimple Manager-enhet | Microsoft Docs"
description: "Beskriver StorSimple Manager service enheten instrumentpanelen och hur du använder den för att visa storage-mätvärden och anslutna initierare och hitta serienummer och IQN."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 6c213969-a385-461f-b698-78ef5b8a79cc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d2209790b54f5dd18a413f2e08ddc6ffd6aef662
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-device-dashboard-in-storsimple-manager-service"></a>Använd instrumentpanelen enhet i StorSimple Manager-tjänsten  
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Om du vill visa versionen av den här artikeln för den nya Azure portalen, gå till [använda instrumentpanelen enhet i StorSimple Manager-tjänsten ](storsimple-8000-device-dashboard.md). Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Översikt
Instrumentpanelen för StorSimple Manager-enhet ger dig en översikt över information för en specifik StorSimple-enhet, till skillnad från instrumentpanelen för tjänsten, som ger information om alla enheter som ingår i Microsoft Azure StorSimple-lösningen.

![Instrumentpanelssidan för enhet](./media/storsimple-device-dashboard/StorSimple_DeviceDashbaord1M.png)

Instrumentpanelen innehåller följande information:

* **Diagramområde** – du kan se relevant storage-mätvärden i diagramområdet överst på instrumentpanelen. Du kan visa egenskaperna för totalt antal primära lagring (mängden data som skrivs av värdar på enheten) och totala molnlagring som används av din enhet under en viss tidsperiod i det här diagrammet.
  
     I den här kontexten *primära lagringsplatsen* refererar till den totala mängden data som skrivs av värden, och kan delas upp på volymtyp: *primära nivåer lagring* innehåller både lokalt lagrade data och data nivåer att molnet; *primära lokalt Fäst lagring* innehåller bara data som lagras lokalt. *Molnlagring*, å andra sidan är ett mått på den totala mängden data som lagras i molnet. Detta inkluderar nivåindelade data och säkerhetskopieringar. Observera att data som lagras i molnet är deduplicerad och komprimerade, medan primära lagringsplatsen anger hur mycket lagringsutrymme som används innan data är deduplicerad och komprimeras. (Du kan jämföra dessa två tal för att få en uppfattning om hastigheten med komprimering.) För både primär och lagringsutrymmet i molnet, de belopp som visas baseras på frekvensen för spårning av som du konfigurerar. Till exempel om du väljer en frekvens som en vecka visas sedan diagrammet data för varje dag i föregående vecka.
  
     Du kan konfigurera diagrammet på följande sätt:
  
  * Om du vill visa mängden molnlagring som används över tid, Välj den **MOLN lagring används** alternativet. Om du vill se det totala lagringsutrymmet som har skrivits av värden, Välj den **primära nivåer lagring används** och **primära LOKALT FÄST lagring används** alternativ. Båda alternativen är markerade i bild; därför visar diagrammet lagring både till molnet och primära lagringsplatsen. Observera att alla primära lagringsutrymme som används innan du installerar uppdatering 2 representeras av den **primära nivåer lagring används** rad.
  * Använd den nedrullningsbara menyn i övre högra hörnet i diagrammet för att ange en 1 vecka, 1-månads, 3-månaders eller 1 års tidsperiod. Observera att diagrammet på den högsta nivån uppdateras bara en gång per dag och därför motsvarar föregående dag summor.
    
    Mer information finns i [använda StorSimple Manager-tjänsten för att övervaka din StorSimple-enhet](storsimple-monitor-device.md).
* **Användningsöversikten** – i den **användningsöversikten** området visas mängden primära lagringsutrymme som används, mängden lagringsutrymme som etablerade och den maximala lagringskapaciteten för din enhet. Genom att jämföra dessa värden för användning med maximal mängd lagringsutrymme som är tillgängligt, visas en överblick över om du behöver skaffa extra lagringsutrymme. Observera att den här översikten uppdateras var 15: e minut och på grund av skillnader i uppdateringsfrekvens kan visa olika nummer än de som visas i diagramområdet ovan, som uppdateras dagligen. Mer information finns i [använda StorSimple Manager-tjänsten för att övervaka din StorSimple-enhet](storsimple-monitor-device.md).
* **Aviseringar** – **aviseringar** innehåller en översikt över aviseringar för din enhet. Aviseringar är grupperade efter allvarlighetsgrad och ett antal tillhandahålls av antalet aviseringar på varje allvarlighetsgrad. Om du klickar på allvarlighetsgraden öppnas vyn omfattas av aviseringsfliken att visa endast aviseringar med den allvarlighetsgraden för den här enheten.
* **Jobb** – **jobb** området visas resultatet av den senaste jobb aktiviteten. Detta kan garantera att systemet fungerar som förväntat eller det att du vet att du behöver vidta åtgärder. Klicka för att visa mer information om senast slutförda jobb **jobben har slutförts under de senaste 24 timmarna**.
* Den **snabböversikten** till höger på instrumentpanelen innehåller användbar information som enhetsmodell serienummer, status, beskrivning och antal volymer.

Du kan också konfigurera redundans och visa anslutna initierare från instrumentpanelen för enheten.

Vanliga uppgifter som kan utföras på den här sidan är:

* Visa anslutna initierare
* Hitta enhetens serienummer
* Hitta enheten mål IQN

## <a name="view-connected-initiators"></a>Visa anslutna initierare
Du kan visa iSCSI-initierare som är anslutna till din enhet genom att klicka på den **visa anslutna initierare** länken i den **snabböversikten** område i instrumentpanelen i enheten. Den här sidan innehåller en tabell lista över initierare som har anslutit till din enhet. För varje initierare kan du se:

* ISCSI kvalificerade namn (IQN) för den anslutna initieraren.
* Namnet på åtkomstkontrollpost (ACR) som gör att anslutna initieraren.
* IP-adressen för den anslutna initieraren.
* Nätverksgränssnitt som initieraren är ansluten till på lagringsenheten. De kan variera från DATA 0 till DATA 5.
* Alla volymer som anslutna initieraren tillåts att komma åt enligt den aktuella ACR-konfigurationen.

Om du ser oväntat initierare i listan eller ser inte de förväntade granska din ACR-konfiguration. Högst 512 initierare kan ansluta till din enhet.

## <a name="find-the-device-serial-number"></a>Hitta enhetens serienummer
Du kan behöva enhetens serienummer när du konfigurerar Microsoft Multipath I/O (MPIO) på enheten. Utför följande steg för att hitta enhetens serienummer.

#### <a name="to-find-the-device-serial-number"></a>Du hittar enhetens serienummer
1. Gå till **enheter** > **instrumentpanelen**.
2. Leta upp i den högra rutan i instrumentpanelen i **snabböversikten** område.
3. Bläddra nedåt och leta upp serienumret.

## <a name="find-the-device-target-iqn"></a>Hitta enheten mål IQN
Du kanske måste enheten målet IQN när du konfigurerar CHAP Challenge Handshake Authentication Protocol () på din StorSimple-enhet. Utför följande steg för att hitta enheten mål IQN.

### <a name="to-find-the-device-target-iqn"></a>Att hitta enheten målet IQN
1. Gå till **enheter** > **instrumentpanelen**.
2. Leta upp i den högra rutan i instrumentpanelen i **snabböversikten** område.
3. Bläddra nedåt och hitta målet IQN.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om den [StorSimple Manager service instrumentpanelen](storsimple-service-dashboard.md).
* Lär dig mer om [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

