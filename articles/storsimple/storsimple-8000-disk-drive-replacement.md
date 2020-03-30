---
title: Byt ut en diskenhet på en StorSimple 8000-serieenhet | Microsoft-dokument
description: I artikeln beskrivs hur du byter ut en diskenhet i en StorSimple-primärhölje eller ett EBOD-hölje.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267916"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Ersätta en diskenhet på en enhet i StorSimple 8000-serien

## <a name="overview"></a>Översikt
I den här självstudien beskrivs hur du kan ta bort och ersätta en felaktig eller misslyckad hårddisk på en Microsoft Azure StorSimple-enhet. Om du vill byta ut en diskenhet måste du:

* Koppla bort antitamperlåset
* Ta bort diskenheten
* Installera ersättningsdisken

> [!IMPORTANT]
> Innan du tar bort och byter ut en diskenhet bör du läsa säkerhetsinformationen i [StorSimples maskinvarukomponentersättning](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>Koppla bort antitamperlåset
Den här proceduren förklarar hur antitamperlåsen på StorSimple-enheten kan aktiveras eller kopplas från när du byter ut hårddiskarna. Antitamperlåsen är monterade i drivhållarens handtag, och de nås genom en liten öppning i spärrdelen av handtaget. Enheterna levereras med låsen inställda på låst läge.

#### <a name="to-unlock-the-antitamper-lock"></a>Så här låser du upp antitamperlåset
1. Sätt försiktigt in låsnyckeln (en "manipuleringssäker" T10-skruvmejsel som Microsoft tillhandahöll) i öppningen i handtaget och i uttaget. 
   
   Om antitamperlåset är aktiverat syns den röda indikatorn i bländaren.
  
    ![Låst diskenhet](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Bild 1** Anti-tamper lås inkopplad
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |Bländare för indikator |
   | 2 |Antitamper lås |
2. Vrid tangenten moturs tills den röda indikatorn inte syns i bländaren ovanför tangenten.
3. Ta bort nyckeln.
   
    ![Olåst diskenhet](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Bild 2** Olåst diskenhet
4. Diskenheten kan nu tas bort.

Följ stegen bakåt för att aktivera låset.

## <a name="remove-the-disk-drive"></a>Ta bort diskenheten
Din StorSimple-enhet har stöd för en RAID 10-liknande konfiguration av lagringsutrymmen. Detta innebär att det kan fungera normalt med en misslyckad disk, solid state-enhet (SSD) eller hårddisk (HDD).

> [!IMPORTANT]
> * Om ditt system har mer än en misslyckad disk ska du inte ta bort mer än en SSD- eller hårddisk från systemet vid någon tidpunkt. Om du gör det kan data gå förlorade.
> * Se till att du placerar en ersättningSD i en plats som tidigare innehöll en SSD. På samma sätt placera en ersättnings hdd i en kortplats som tidigare innehöll en hårddisk.
> * I Azure-portalen numreras slots från 0 – 11. Om portalen visar att en disk i kortplats 2 har misslyckats, på enheten, leta efter den misslyckade disken i den tredje platsen från det övre vänstra.
> 
> 

Enheter kan tas bort och bytas ut medan systemet är i drift.

#### <a name="to-remove-a-drive"></a>Så här tar du bort en enhet
1. Om du vill identifiera den felaktiga disken går du till **enhetens inställningar > maskinvaruhälsa**i Azure-portalen . Eftersom en disk kan misslyckas i den primära höljet och/eller i en EBOD-hölje (om du använder en 8600-modell) kan du titta på diskarnas status under **Delade komponenter** och under **delade EBOD-komponenter**. En misslyckad disk i någon av höljena visas med röd status.
2. Placera enheterna framför den primära höljet eller EBOD-höljet. 
3. Om disken är upplåst går du vidare till nästa steg. Om disken är låst låser du upp den genom att följa proceduren i [Koppla bort antitamperlåset](#disengage-the-antitamper-lock).
4. Tryck på den svarta spärren på drivhållaremodulen och dra ut drivhållarens handtag och bort från chassits framsida.
   
    ![Släppa diskenhetshandtag](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Bild 3** Släppa drivhandtaget
5. När drivhållarens handtag är helt utsträckt skjuter du ut drivhållaren ur chassit. 
   
    ![Glidande disk av diskenhet](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Bild 4** Att skjuta ut diskenheten ur transportören

## <a name="install-the-replacement-disk-drive"></a>Installera ersättningsdisken
När en enhet har misslyckats i StorSimple-enheten och du har tagit bort den följer du den här proceduren för att ersätta den med en ny enhet.

#### <a name="to-insert-a-drive"></a>Så här infogar du en enhet
1. Se till att drivhållarehandtaget är helt utsträckt, som visas i följande bild.
   
    ![Diskenhet med utökat handtag](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Bild 5** Kör med handtag förlängt
2. Skjut in drivhållaren hela vägen in i chassit.
   
    ![Glida disk i diskenhetshållare](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Bild 6**  Skjuta in drivhållaren i chassit
3. När drivhållaren är isatt stänger du drivhållarens handtag samtidigt som du fortsätter att trycka in drivhållaren i chassit, tills drivhållarens handtag snäpper fast i låst läge.
4. Använd låsnyckeln som tillhandahölls av Microsoft (manipuleringssäker Torx skruvmejsel) för att säkra bärhandtaget på plats genom att vrida låsskruven en kvarts sväng medurs.
5. Kontrollera att ersättningen lyckades och att enheten är i drift. Öppna Azure-portalen och navigera till **maskinvaruhälsa för enhetsinställningar** > **Hardware health**. Under **Delade komponenter** eller delade **EBOD-komponenter**ska enhetsstatusen vara grön, vilket indikerar att den är felfri.

   
   > [!NOTE]
   > Det kan ta flera timmar innan diskstatusen blir grön efter att du har ersatt den.
  
## <a name="next-steps"></a>Nästa steg
Läs mer om byte av [Maskinvarukomponent i StorSimple](storsimple-8000-hardware-component-replacement.md).

