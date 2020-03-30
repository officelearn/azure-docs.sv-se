---
title: Byt ut en StorSimple 8600 EBOD-styrenhet | Microsoft-dokument
description: I artikeln beskrivs hur du tar bort och ersätter en eller båda EBOD-styrenheter på en StorSimple 8600-enhet.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254890"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Byt ut en EBOD-styrenhet på StorSimple-enheten

## <a name="overview"></a>Översikt
I den här självstudien beskrivs hur du ersätter en felaktig EBOD-styrenhetsmodul på din Microsoft Azure StorSimple-enhet. Om du vill byta ut en EBOD-styrenhetsmodul måste du:

* Ta bort den felaktiga EBOD-styrenheten
* Installera en ny EBOD-styrenhet

Tänk på följande information innan du börjar:

* Tomma EBOD-moduler måste sättas in i alla oanvända fack. Höljet svalnar inte ordentligt om en plats lämnas öppen.
* EBOD-styrenheten kan bytas under drift och kan tas bort eller bytas ut. Ta inte bort en misslyckad modul förrän du har en ersättning. När du startar ersättningsprocessen måste du avsluta den inom 10 minuter.

> [!IMPORTANT]
> Innan du försöker ta bort eller byta ut en StorSimple-komponent ska du se till att du granskar [säkerhetsikonkonventionerna](storsimple-safety.md#safety-icon-conventions) och andra [säkerhetsåtgärder](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Ta bort en EBOD-styrenhet
Innan du byter ut den misslyckade EBOD-styrenhetsmodulen i StorSimple-enheten ska du se till att den andra EBOD-styrenheten är aktiv och körs. Följande procedur och tabell förklarar hur du tar bort EBOD-styrenhetsmodulen.

#### <a name="to-remove-an-ebod-module"></a>Så här tar du bort en EBOD-modul
1. Öppna Azure Portal.
2. Gå till enheten och navigera till **Inställningar** > **Maskinvaruhälsa**och kontrollera att lysdioden för den aktiva EBOD-styrenheten är grön och att lysdioden för den misslyckade EBOD-styrenheten är röd.
3. Leta reda på den misslyckade EBOD-styrenhetsmodulen på baksidan av enheten.
4. Ta bort kablarna som ansluter EBOD-styrenhetsmodulen till styrenheten innan du tar ut EBOD-modulen ur systemet.
5. Anteckna den exakta SAS-porten för EBOD-styrenhetsmodulen som var ansluten till styrenheten. Du måste återställa systemet till den här konfigurationen när du har ersatt EBOD-modulen.
   
   > [!NOTE]
   > Vanligtvis är detta port A, som är märkt som **värd i** i följande diagram.
   
    ![Bakplan för EBOD-styrenheten](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Bild 1** Baksidan av EBOD-modulen
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |Fel-LED |
   | 2 |Ström-LED |
   | 3 |SAS-kopplingar |
   | 4 |SAS lysdioder |
   | 5 |Seriella portar endast för fabriksbruk |
   | 6 |Port A (värd i) |
   | 7 |Port B (värd ut) |
   | 8 |Port C (endast fabriksanvändning) |

## <a name="install-a-new-ebod-controller"></a>Installera en ny EBOD-styrenhet
I följande procedur och tabell beskrivs hur du installerar en EBOD-styrenhetsmodul i StorSimple-enheten.

#### <a name="to-install-an-ebod-controller"></a>Så här installerar du en EBOD-styrenhet
1. Kontrollera att EBOD-enheten inte är skadad, särskilt på gränssnittskontakten. Installera inte den nya EBOD-styrenheten om några stift är böjda.
2. Med spärrarna i öppet läge, skjut modulen in i höljet tills spärrarna griper in.
   
    ![Installera EBOD-styrenhet](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Bild 2**  Installera EBOD-styrenhetsmodulen
3. Stäng spärren. Du bör höra ett klick som spärren engagerar.
   
    ![Släppa EBOD spärr](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Bild 3**  Stänga EBOD-modulspärren
4. Anslut kablarna igen. Använd den exakta konfigurationen som fanns före ersättningen. Mer information om hur du ansluter kablarna finns i följande diagram och tabell.
   
    ![Kabel din 4U-enhet för ström](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Figur 4**. Återansluta kablar
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |Primär kapsling |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Kontrollant 0 |
   | 5 |Kontrollant 1 |
   | 6 |EBOD-styrenhet 0 |
   | 7 |EBOD-styrenhet 1 |
   | 8 |EBOD-kapsling |
   | 9 |Kraftdistributionsenheter |

## <a name="next-steps"></a>Nästa steg
Läs mer om byte av [Maskinvarukomponent i StorSimple](storsimple-8000-hardware-component-replacement.md).

