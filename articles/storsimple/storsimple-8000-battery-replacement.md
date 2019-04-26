---
title: Ersätt batteri på enhet i Microsoft Azure StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du tar bort, ersätta och underhålla modulen extrabatteri på StorSimple-enheten.
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
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 4ebf3f28d40e0461d140a3fe74fb940720f26db6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60418969"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Ersätt extrabatteriet modulen på StorSimple-enheten

## <a name="overview"></a>Översikt
Det primära hölje Power och kylning modulen (PCM) på din Microsoft Azure StorSimple-enhet har du ett extra batteri pack. Paketet innehåller power så att StorSimple-enheten kan spara data om det finns förlust av nätström till primära höljet. Detta batteri pack kallas den *extrabatteri modulen*. Extrabatteri-modul finns bara för primära höljet i din StorSimple-enhet (EBOD-höljet inte innehåller en extrabatteri-modul).

I den här självstudien beskrivs hur du:

* Ta bort modulen extrabatteri
* Installera en ny extrabatteri-modul
* Underhålla modulen extrabatteri

> [!IMPORTANT]
> Innan du tar bort och ersätter en extrabatteri-modul, granska säkerhetsinformation i den [introduktion till StorSimple komponenten maskinvaruersättning](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Ta bort modulen extrabatteri
Modulen extrabatteri för StorSimple-enheten är en-fältutbytbar enhet. Innan det installeras i PCM ska batterimodulen lagras i originalförpackningen. Utför följande steg för att ta bort reservbatteriet.

#### <a name="to-remove-the-backup-battery-module"></a>Ta bort modulen extrabatteri
1. Gå till bladet för din StorSimple Device Manager-tjänsten i Azure-portalen. Gå till **enheter** och välj sedan din enhet i listan över enheter. Gå till **övervakaren** > **hälsotillstånd för maskinvara**. Under **Shared Components**, titta på statusen för batteriet.
2. Identifiera PCM där batteriet misslyckades. Bild 1 visar baksidan av StorSimple-enheten.
   
    ![Serverdelen av enhetens primära Höljesmoduler](./media/storsimple-battery-replacement/IC740994.png)
   
    **Bild 1** baksidan av primära enhet som visar PCM och controller moduler
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontrollenhet 0 |
   | 4 |Kontrollenhet 1 |
   
    Efter nummer 3 i bild 2 visas övervakning indikatorn LEDDE i PCM 0 som motsvarar **batteri fel** bör tändas.
   
    ![Serverdelen av övervakningsindikatorer för övervakning av enheten PCM](./media/storsimple-battery-replacement/IC740992.png)
   
    **Bild 2** tillbaka of PCM som visar övervakningsindikatorer
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |AC-strömavbrott |
   | 2 |Fläkt fel |
   | 3 |Batteri fel |
   | 4 |PCM OK |
   | 5 |DC strömavbrott |
   | 6 |Batteri är felfria |
3. Om du vill ta bort PCM med en misslyckad batteri, följer du stegen i [ta bort en PCM](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm).
4. Med PCM som har tagits bort kan lyfta och rotera modulreferens batteri uppåt som anges i följande bild och hämta upp till ta bort batteriet.
   
    ![Ta bort batteri från PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Bild 3** tar bort batteriet från PCM
5. Placera modulen i-fältutbytbar enhet paketering.
6. Returnera defekta till Microsoft för rätt underhåll och hantering av.

## <a name="install-a-new-backup-battery-module"></a>Installera en ny extrabatteri-modul
Utför följande steg för att installera modulen ersättning batteri i PCM i det primära höljet för StorSimple-enheten.

#### <a name="to-install-the-battery-module"></a>Att installera batterimodulen
1. Placera modulen extrabatteri med rätt orientering i PCM.
2. Tryck ned modulreferens batteri hela vägen till enhet kopplingen.
3. Ersätt PCM: en i det primära höljet genom att följa riktlinjerna i [ersätta en ström och kylning modulen på StorSimple-enheten](storsimple-8000-power-cooling-module-replacement.md).
4. När ersättningen är klar går du till din enhet och gå sedan till **övervakaren** > **hälsotillstånd för maskinvara** i Azure-portalen. Kontrollera status för batteri att se till att installationen lyckades. Grön status anger att batteriet är felfri.

## <a name="maintain-the-backup-battery-module"></a>Underhålla modulen extrabatteri
I din StorSimple-enhet ger modulen extrabatteri kraften att kontrollanten när power går förlorade händelser. Det gör att StorSimple-enheten för att spara viktiga data innan du stänger av på ett kontrollerat sätt. Systemet kan hantera två på varandra följande går förlorade händelser med två fullständigt debiteras batterier i PCMs.

I Azure-portalen i **hälsotillstånd för maskinvara** under den **övervakaren** bladet anger om batteriet är fel eller närmar sig slutet av liv. Batteristatusen indikeras av **batteri i PCM 0** eller **batteri i PCM 1** under **Shared Components**. Det här bladet visar en **DEGRADERAD** tillstånd för det närmar sig slutet på sin livscykel, och **misslyckades** för end livscykel har nåtts.

> [!NOTE]
> Batteriet kan rapportera **misslyckades** när den behöver bara att debiteras.


Om den **DEGRADERAD** tillståndet visas, rekommenderar vi vilka följande åtgärder:

* Systemet kan ha uppstått strömavbrott senaste eller batterierna kan underhållsarbeten periodiska. Iaktta systemet i 12 timmar innan du fortsätter.
  
  * Om tillståndet är fortfarande **DEGRADERAD** efter 12 timmar kontinuerlig anslutning till AC sätter med domänkontrollanter och PCMs kör laddar sedan batteriet måste ersättas. . [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för en ersättning extrabatteri modul.
  * Om tillståndet blir OK efter 12 timmar, batteriet fungerar och behövs bara en avgift för underhåll.
* Om det inte har en associerad förlust av funktionalitet och PCM är påslagen och ansluten till nätström, måste batteriet ersättas. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) att ordna en ersättning extrabatteri modul.

> [!IMPORTANT]
> Avyttra misslyckade batteriet enligt nationella och regionala regler.

## <a name="next-steps"></a>Nästa steg
Läs mer om [StorSimple maskinvaruersättning komponenten](storsimple-8000-hardware-component-replacement.md).

