---
title: Ersätt en StorSimple 8600 EBOD-kontrollanten | Microsoft Docs
description: Beskriver hur du tar bort och ersätter en eller båda EBOD-domänkontrollanter på en StorSimple 8600-enhet.
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
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: b05d1f36d1e74b3d915e216676859654fbcbacf3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578699"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Ersätt EBOD-kontrollanten på StorSimple-enheten

## <a name="overview"></a>Översikt
Den här självstudien beskrivs hur du ersätter en felaktig EBOD controller modul på Microsoft Azure StorSimple-enheten. Om du vill ersätta en domänkontrollant EBOD-modul, måste du:

* Ta bort den felaktiga EBOD-kontrollanten
* Installera en ny EBOD-kontrollanten

Tänk på följande innan du börjar:

* Tom EBOD-moduler måste läggas till alla platser som inte används. Höljet lågfrekvent kommer inte korrekt om en plats som lämnas öppna.
* EBOD-kontrollanten är växlas och kan tas bort eller ersatts. Ta inte bort en misslyckad modul förrän du har en ersättning. När du startar ersättningsprocessen, måste du slutföra det inom 10 minuter.

> [!IMPORTANT]
> Innan du försöker ta bort eller ersätta en StorSimple-komponent, se till att du läser igenom den [säkerhet ikonen konventioner](storsimple-safety.md#safety-icon-conventions) och andra [säkerhetsåtgärder](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Ta bort en EBOD-kontrollanten
Kontrollera att den andra EBOD-controller-modulen är aktiv och körs innan du ersätter den misslyckade EBOD controller-modulen i din StorSimple-enhet. Följande procedur och tabell förklarar hur du tar bort modulen EBOD-domänkontrollant.

#### <a name="to-remove-an-ebod-module"></a>Ta bort en EBOD-modul
1. Öppna Azure Portal.
2. Gå till din enhet och gå till **inställningar** > **hälsotillstånd för maskinvara**, och kontrollera att statusen för Indikator för modulen controller active EBOD är grön och Indikator för det misslyckade EBOD-kontrollanten modulen är röda.
3. Leta upp modulen misslyckade EBOD controller på baksidan av enheten.
4. Ta bort kablarna som Anslut modulen EBOD controller kontrollanten innan du tar EBOD-modul från systemet.
5. Anteckna den exakta SAS-porten av domänkontrollant EBOD-modulen som anslöts till kontrollanten. Du kommer att behöva återställa systemet till den här konfigurationen när du ersätter EBOD-modulen.
   
   > [!NOTE]
   > Detta kommer normalt att Port A, som är märkta som **värd i** i följande diagram.
   
    ![Bakplan EBOD-kontrollanten](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Bild 1** tillbaka of EBOD-modul
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |Fel Indikator |
   | 2 |Indikator för ström |
   | 3 |SAS-anslutningar |
   | 4 |SAS-led: ar |
   | 5 |Seriella portar för factory bruk |
   | 6 |Port en (värd i) |
   | 7 |Port B (Host ut) |
   | 8 |Port C (endast Factory användning) |

## <a name="install-a-new-ebod-controller"></a>Installera en ny EBOD-kontrollanten
Följande procedur och tabell förklarar hur du installerar en domänkontrollant EBOD-modulen i din StorSimple-enhet.

#### <a name="to-install-an-ebod-controller"></a>Installera en EBOD-kontrollanten
1. Kontrollera EBOD-enhet för skador, särskilt gränssnittet kopplingen. Installera inte den nya EBOD-kontrollanten om någon PIN-koder är böjda.
2. Skjut modulen i höljet med lås i öppet läge tills Lås engagera.
   
    ![Installera EBOD-kontrollanten](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Bild 2** installera modulen EBOD-styrenhet
3. Stäng låset. Du bör höra ett klick som talar med låset.
   
    ![Släpper EBOD spärr](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Bild 3** stänger EBOD-modulen spärr
4. Återanslut kablarna. Använd den exakta konfigurationen som fanns innan ersättningen. Se följande diagram och tabell finns information om hur du ansluter kablarna.
   
    ![Kabelansluta den 4U kraft](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Bild 4**. Återansluter kablar
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |Primär hölje |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Kontrollenhet 0 |
   | 5 |Kontrollenhet 1 |
   | 6 |EBOD-kontrollanten 0 |
   | 7 |EBOD-kontrollanten 1 |
   | 8 |EBOD hölje |
   | 9 |Kraftfördelningsenheter |

## <a name="next-steps"></a>Nästa steg
Läs mer om [StorSimple maskinvaruersättning komponenten](storsimple-8000-hardware-component-replacement.md).

