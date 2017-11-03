---
title: "Ersätt chassi på StorSimple 8000-serieenhet | Microsoft Docs"
description: "Beskriver hur du tar bort och ersätter chassi för din StorSimple primära hölje eller EBOD hölje."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 073fcf0064f1d1482f4683d733f00cf918ff2f38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Ersätt chassi på din StorSimple-enhet
## <a name="overview"></a>Översikt
Den här självstudiekursen beskrivs hur du tar bort och ersätter ett chassi i en enhet med StorSimple 8000-serien. StorSimple 8100 modellen är en enda enhet enhet (samma chassi), medan 8600 är en dubbel höljet enhet (två chassi). För en modell av 8600 är potentiellt två chassi som kan ha i enheten: chassi för primära höljet eller chassi för EBOD höljet.

I båda fallen är ersättning chassi som levereras av Microsoft tom. Ingen ström och kylning moduler (PCMs) domänkontrollant moduler solid state disk-hårddiskar (SSD), hårddiskar (HDD) eller EBOD moduler tas med.

> [!IMPORTANT]
> Innan du tar bort och ersätter chassit, granska säkerhetsinformation i [ersättning av StorSimple maskinvara komponenten](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Ta bort chassit
Utför följande steg för att ta bort chassi på StorSimple-enheten.

#### <a name="to-remove-a-chassis"></a>Ta bort ett chassi
1. Kontrollera att StorSimple-enheten stängs av och kopplas bort från alla strömkällor.
2. Ta bort alla nätverks- och SAS-kablar, om tillämpligt.
3. Ta bort enheten från racket.
4. Ta bort var och en av enheterna och Lägg märke till de platser där de tas bort. Mer information finns i [ta bort diskenheten](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. På EBOD höljet (om detta är chassi som misslyckades), ta bort EBOD domänkontrollant moduler. Mer information finns i [ta bort en domänkontrollant för en EBOD](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    På primära höljet (om detta är chassi som misslyckades), ta bort domänkontrollanterna och Lägg märke till de platser där de tas bort. Mer information finns i [ta bort en domänkontrollant](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Installera chassit
Utför följande steg för att installera chassit på din StorSimple-enhet.

#### <a name="to-install-a-chassis"></a>Så här installerar du ett chassi
1. Montera chassi i racket. Mer information finns i [rackmontera StorSimple-8100-enhet](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) eller [rackmonterade StorSimple-8600-enhet](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. När chassit har monterats i racket, installerar du modulerna som domänkontrollant på samma platser som de tidigare har installerats i.
3. Installera enheterna i samma platser och distributionsplatser som de tidigare har installerats i.
   
   > [!NOTE]
   > Vi rekommenderar att du först installera SSD-enheterna i platserna och sedan installera hårddiskar.
  
4. När enheten är monterat i racket och de komponenter som installeras kan ansluta enheten till lämplig strömkällor och slå på enheten. Mer information finns i [Kabelanslut din 8100 StorSimple-enhet](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) eller [Kabelanslut din 8600 StorSimple-enhet](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [ersättning av StorSimple maskinvara komponenten](storsimple-8000-hardware-component-replacement.md).

