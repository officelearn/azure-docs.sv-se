---
title: Ersätt en diskenhet på en enhet i StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du ersätter en diskenhet på en primär StorSimple-enhet eller en EBOD bilaga.
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
ms.workload: TBD
ms.date: 8/25/2017
ms.author: alkohli
ms.openlocfilehash: 3d6ef22e4df36996d68194589f43ea0f57def22c
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673939"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Ersätt en diskenhet på din enhet i StorSimple 8000-serien

## <a name="overview"></a>Översikt
Den här självstudien beskrivs hur du kan ta bort och ersätter en felaktig eller misslyckade hårddisk på en Microsoft Azure StorSimple-enhet. Om du vill ersätta en diskenhet, måste du:

* Koppla ur antitamper låset
* Ta bort diskenheten
* Installera diskenheten ersättning

> [!IMPORTANT]
> Innan du tar bort och ersätter en diskenhet, granska säkerhetsinformation i [StorSimple maskinvaruersättning komponenten](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>Koppla ur antitamper låset
Den här proceduren förklarar hur antitamper lås på din StorSimple-enhet kan aktiverad eller inaktiverad när du ersätter diskenheterna. Antitamper låsen monteras i enheten operatör hanterar och de kan nås via en liten öppning i avsnittet spärr i referensen. Enheter levereras med låsen inställd låsta.

#### <a name="to-unlock-the-antitamper-lock"></a>Att låsa upp antitamper låset
1. Infoga noggrant på Lock (en ”tamperproof” T10 för som tillhandahålls av Microsoft) till öppning i referensen och till en socket. 
   
   Om antitamper låset har aktiverats kan är den röda indikatorn synlig i öppning.
  
    ![Låst enhet](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Bild 1** anti förvanskningstålig Lås engagerade
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |Indikatorn öppning |
   | 2 |Antitamper Lås |
2. Rotera nyckeln i en moturs riktning tills den röda indikatorn inte visas i öppning ovan nyckeln.
3. Ta bort nyckeln.
   
    ![Upplåst diskenhet](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Bild 2** olåst diskenhet
4. Diskenheten kan nu tas bort.

Följ stegen i omvänd att engagera låset.

## <a name="remove-the-disk-drive"></a>Ta bort diskenheten
StorSimple-enheten har stöd för en RAID 10-liknande blanksteg lagringskonfiguration. Detta innebär att den kan fungera normalt med en felaktig disk, Solid State-hårddisk (SSD), eller hårddisk enhet (HDD).

> [!IMPORTANT]
> * Om systemet har fler än en felaktig, ta inte bort mer än en SSD eller HDD från systemet när som helst i tid. Detta kan resultera i dataförlust.
> * Se till att du placerar en ersättning SSD i ett uttag som tidigare har innehållit en SSD. På samma sätt kan placera en ersättning HDD i ett uttag som tidigare har innehållit en Hårddisk.
> * I Azure-portalen fack numreras från 0 – 11. Därför om portalen visar att en disk i fack 2 har misslyckats på enheten, leta efter den skadade disken i den tredje platsen från längst ned till vänster.
> 
> 

Enheter kan tas bort och ersättas medan systemet körs.

#### <a name="to-remove-a-drive"></a>Ta bort en enhet
1. För att identifiera den skadade disken i Azure-portalen går du till din enhet **Inställningar > hälsotillstånd för maskinvara**. Eftersom en disk kan misslyckas i primära höljet och/eller i ett EBOD-hölje (om du använder en modell 8600), titta på statusen för diskarna under **delade komponenter** och under **delade EBOD-komponenter**. En felaktig disk i antingen hölje visas med röd status.
2. Hitta enheter först i primära höljet eller EBOD-höljet. 
3. Om disken är olåst, kan du gå vidare till nästa steg. Om disken är låst, låser du upp den genom att följa anvisningarna i [kopplas ur antitamper låset](#disengage-the-antitamper-lock).
4. Tryck på svart spärr i modulen enhet operatör och dra i handtaget för enhet-operatör ut och in från fram på datorn.
   
    ![Släpper diskenheten referens](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Bild 3** lanserar enhet-referens
5. När enheten operatör referensen utökas fullständigt, dra enhet operatör utanför chassit. 
   
    ![Glidande disk av diskenhet](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Bild 4** glidande diskenheten från vilken operatör

## <a name="install-the-replacement-disk-drive"></a>Installera diskenheten ersättning
När en enhet har misslyckats i din StorSimple-enhet och du har tagit bort det, följer du proceduren för att ersätta den med en ny enhet.

#### <a name="to-insert-a-drive"></a>Att infoga en enhet
1. Kontrollera att enheten operatör referensen utökas helt, enligt följande bild.
   
    ![Diskenhet med referensen utökade](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Bild 5** enhet med referensen utökade
2. Skjut enhet operatör hela vägen i chassit.
   
    ![Glidande disk i enhet operatör](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Bild 6** glidande enhet-operatör i chassit
3. Stäng enheten operatör referensen när du fortsätter att skicka enhet-operatör till chassin, tills enheten operatör handtaget fästs på en låst plats med vilken enhet operatör som infogas.
4. Använd på LOCK som angavs av Microsoft (tamperproof Torx för) för att skydda den operatör referensen till rätt plats genom att aktivera Lås skruven ett kvarts varv medsols.
5. Kontrollera att ersättningen lyckades och att enheten är i drift. Tillgång till Azure portal och gå till **Enhetsinställningar** > **hälsotillstånd för maskinvara**. Under **delade komponenter** eller **delade EBOD-komponenter**, status för enheten vara grön, som anger att den är felfri.

   
   > [!NOTE]
   > Det kan ta flera timmar för diskstatus att Stäng grön efter ersättningen.
  
## <a name="next-steps"></a>Nästa steg
Läs mer om [StorSimple maskinvaruersättning komponenten](storsimple-8000-hardware-component-replacement.md).

