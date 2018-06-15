---
title: Ersätta en enhet på en enhet för StorSimple 8000-serien | Microsoft Docs
description: Förklarar hur du ersätter en enhet på en primär StorSimple-enhet eller en EBOD bilaga.
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
ms.date: 073/2017
ms.author: alkohli
ms.openlocfilehash: a8616eb51b177a9447a7c466c9d934b9139afedf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874894"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Ersätta en diskenhet på enheten StorSimple 8000-serien

## <a name="overview"></a>Översikt
Den här självstudiekursen beskrivs hur du kan ta bort och ersätter en felaktig eller misslyckade hårddisk på en Microsoft Azure StorSimple-enhet. Om du vill ersätta en enhet, måste du:

* Koppla ur antitamper låset
* Ta bort enheten
* Installera diskenhet ersättning

> [!IMPORTANT]
> Innan du tar bort och ersätter en diskenhet granska säkerhetsinformation i [ersättning av StorSimple maskinvara komponenten](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>Koppla ur antitamper låset
Här beskrivs hur antitamper lås på din StorSimple-enhet kan vara aktiverad eller inaktiverad när du ersätter diskenheter. Antitamper Lås monteras i enheten operatör hanterar och de kan nås via en liten öppning i avsnittet spärren i referensen. Enheter anges med lås som den låsta position.

#### <a name="to-unlock-the-antitamper-lock"></a>Att låsa upp antitamper låset
1. Infoga noggrant på Lock (en ”tamperproof” T10 för som tillhandahålls av Microsoft) till öppning i referensen och till en socket. 
   
   Röd indikator är synlig i öppning om antitamper lock är aktiverat.
  
    ![Låst enhet](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Bild 1** anti manipulationsindikerande Lås används
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |Öppning av indikator |
   | 2 |Antitamper Lås |
2. Rotera nyckeln i en moturs riktning tills den röda indikatorn inte visas i öppning ovanför nyckeln.
3. Ta bort nyckeln.
   
    ![Olåsta diskenhet](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Bild 2** olåst diskenhet
4. Diskettenheten kan nu tas bort.

Följ stegen i omvänd att låset.

## <a name="remove-the-disk-drive"></a>Ta bort enheten
Din StorSimple-enhet stöder en RAID 10-liknande blanksteg lagringskonfiguration. Detta innebär att den fungerar normalt med en skadad disk SSD-enhet (SSD) eller hårddisk enhet (HDD).

> [!IMPORTANT]
> * Om systemet har mer än en felande disken, ta inte bort mer än en SSD och HDD från systemet när som helst i tid. Detta kan resultera i förlust av data.
> * Se till att du placerar en ersättning SSD i en plats som tidigare har innehållit en SSD. Placera en annan Hårddisk på samma sätt i en plats som tidigare har innehållit en Hårddisk.
> * I Azure-portalen fack numreras från 0 – 11. Därför om portalen visar att en disk i uttag 2 misslyckades på enheten, leta efter den skadade disken på tredje plats från den övre vänstra.
> 
> 

Enheter kan tas bort och ersätts när systemet körs.

#### <a name="to-remove-a-drive"></a>Ta bort en enhet
1. Gå till din enhet för att identifiera den felande disken i Azure-portalen **Inställningar > maskinvara hälsa**. Eftersom en disk kan misslyckas i primära höljet och/eller i en EBOD hölje (om du använder en 8600-modellen), se status för diskarna under **delade komponenter** och under **EBOD delade komponenter**. En disk som misslyckats i antingen hölje visas med röd status.
2. Hitta enheter först i primära höljet eller EBOD höljet. 
3. Om disken är olåst vidare till nästa steg. Om disken är låst kan låsa upp genom att följa proceduren i [kopplas ur antitamper låset](#disengage-the-antitamper-lock).
4. Tryck på svart låset på enheten operatör modulen och dra enhet operatör referensen ut och framifrån chassit.
   
    ![Frigöra referensen diskenhet](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Bild 3** frigöra referensen för enhet
5. När enheten operatör referensen utökas fullständigt, dra en operatör enhet utanför chassit. 
   
    ![Glidande disk diskenhet](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Bild 4** glidande diskenhet utanför en operatör

## <a name="install-the-replacement-disk-drive"></a>Installera diskenhet ersättning
När en enhet har misslyckats i din StorSimple-enhet och har tagits bort, följer du proceduren för att ersätta den med en ny enhet.

#### <a name="to-insert-a-drive"></a>Infoga en enhet
1. Se till att enheten operatör referensen utökas helt, enligt följande bild.
   
    ![Enhet med referensen utökad](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Bild 5** enheten med referensen utökad
2. Skjut enhet operatör ända i chassit.
   
    ![Glidande disk i enhet operatör](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Bild 6** glidande enhet operatör i chassit
3. Stäng enheten operatör referensen med en enhet operatör som infogas när fortsätter att skicka en enhet operatör till chassit, tills enheten operatör referensen fästs i låst läge.
4. Använd på LOCK som angavs av Microsoft (tamperproof Torx för) operatör referensen till rätt plats genom att aktivera Lås skruven Stäng för ett kvartal medurs.
5. Kontrollera att ersättningen lyckades och att enheten är i drift. Åtkomst till Azure-portalen och gå till **Enhetsinställningar** > **maskinvara hälsa**. Under **delade komponenter** eller **EBOD delade komponenter**, status för enheten ska vara grön, som anger att den är felfri.

   
   > [!NOTE]
   > Det kan ta flera timmar diskstatusen blir grönt efter ersättningen.
  
## <a name="next-steps"></a>Nästa steg
Lär dig mer om [ersättning av StorSimple maskinvara komponenten](storsimple-8000-hardware-component-replacement.md).

