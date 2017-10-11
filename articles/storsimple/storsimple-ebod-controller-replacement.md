---
title: "Ersätta en StorSimple EBOD styrenhet | Microsoft Docs"
description: "Beskriver hur du tar bort och ersätter en eller båda EBOD domänkontrollanter på en StorSimple 8600-enhet."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 8cbfa507-1a56-4e24-99dd-7db9abd3b850
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 23d819ddc3bbcbaf2847cdcc9191407ead0ff43d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Ersätta en domänkontrollant för en EBOD på din StorSimple-enhet
## <a name="overview"></a>Översikt
Den här självstudiekursen beskrivs hur du ersätta en felande EBOD domänkontrollant modul på Microsoft Azure StorSimple-enheten. Om du vill ersätta en EBOD Styrenhetsmodul, måste du:

* Ta bort felaktiga EBOD-styrenhet
* Installera en ny domänkontrollant EBOD

Tänk på följande innan du börjar:

* Tom EBOD moduler måste infogas i alla oanvända platser. Höljet ska inte kall korrekt om en plats är öppna.
* EBOD domänkontrollant är växlas och kan tas bort eller ersättas. Ta inte bort en misslyckad modul tills du har en ersättning. När du startar ersättningsprocessen, måste du slutföra det inom 10 minuter.

> [!IMPORTANT]
> Innan du försöker ta bort eller ersätta en StorSimple-komponent, se till att du läser den [säkerhet ikonen konventioner](storsimple-safety.md#safety-icon-conventions) och andra [säkerhetsåtgärderna](storsimple-safety.md).
> 
> 

## <a name="remove-an-ebod-controller"></a>Ta bort en domänkontrollant för en EBOD
Kontrollera att andra EBOD domänkontrollant modulen är aktiv och körs innan du ersätter den misslyckade EBOD domänkontrollant modulen i din StorSimple-enhet. Följande procedur och tabell förklarar hur du tar bort EBOD domänkontrollant modul.

#### <a name="to-remove-an-ebod-module"></a>Ta bort en EBOD-modul
1. Öppna den klassiska Azure-portalen.
2. Gå till **enheter** > **Underhåll** > **maskinvarustatus**, och kontrollera att status för Indikator för modulen controller active EBOD är grön Indikator för modulen controller misslyckade EBOD är röda.
3. Leta upp modulen misslyckade EBOD domänkontrollant på baksidan av enheten.
4. Ta bort kablarna som ansluter modulen EBOD domänkontrollant till styrenhet innan du tar modulen EBOD från systemet.
5. Anteckna exakt SAS-porten för modulen EBOD domänkontrollant som var ansluten till styrenhet. Du behöver återställa datorn till den här konfigurationen när du ersätter EBOD-modulen. 
   
   > [!NOTE]
   > Normalt Port A, som är märkta som blir **värd i** i följande diagram.
   > 
   > 
   
    ![Bakplan EBOD domänkontrollant](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Bild 1** tillbaka of EBOD modul
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |Fel Indikator |
   | 2 |Indikator för ström |
   | 3 |SAS-anslutningar |
   | 4 |SAS-indikatorer |
   | 5 |Serieportar factory endast för användning |
   | 6 |Port en (värd i) |
   | 7 |Port B (Host ut) |
   | 8 |Port C (endast Factory användning) |

## <a name="install-a-new-ebod-controller"></a>Installera en ny domänkontrollant EBOD
Följande procedur och tabell förklarar hur du installerar en domänkontrollant EBOD-modul i din StorSimple-enhet.

#### <a name="to-install-an-ebod-controller"></a>Att installera en domänkontrollant för en EBOD
1. Kontrollera enheten EBOD skador, särskilt för att gränssnittet kopplingen. Installera inte nya EBOD styrenhet om någon PIN-koder är böjda.
2. Skjut modulen i höljet med lås i öppet läge tills Lås engagera.
   
    ![Installera EBOD domänkontrollant](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Bild 2** installera modulen EBOD domänkontrollant
3. Stäng låset. Du bör höra en klickning som snabbt tillkallar låset.
   
    ![Släppa EBOD spärren](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Bild 3** stänger EBOD modulen spärren
4. Återanslut kablarna. Använd den exakta konfigurationen som fanns innan du ersätter. Finns i följande diagram och tabell för information om hur du ansluter kablarna.
   
    ![Kabelansluta den 4U för ström](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Bild 4**. Ansluta kablar
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |Primär enhet |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Styrenhet 0 |
   | 5 |Kontrollant 1 |
   | 6 |EBOD styrenhet 0 |
   | 7 |EBOD kontrollant 1 |
   | 8 |EBOD hölje |
   | 9 |Kraftfördelningsenheter |

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [ersättning av StorSimple maskinvara komponenten](storsimple-hardware-component-replacement.md).

