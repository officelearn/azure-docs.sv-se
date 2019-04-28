---
title: Ersätt chassit på enhet i StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du tar bort och Ersätt chassit för din StorSimple primära hölje eller EBOD-höljet.
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
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 7dfc39f4d08c8a49d1564a0a5bd7e3ef4156e3fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61312453"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Ersätt chassit på StorSimple-enheten
## <a name="overview"></a>Översikt
Den här självstudien beskrivs hur du tar bort och ersätter ett chassi i en enhet i StorSimple 8000-serien. StorSimple 8100-modellen är en enda hölje enhet (samma chassi), medan 8600 är en dubbel hölje enhet (två chassin). Det finns potentiellt två chassin som kan ha i enheten för en modell 8600: chassi för primära höljet eller chassi för EBOD-höljet.

I båda fallen är ersättning-chassi som levereras av Microsoft tom. Ingen ström och kylning moduler (PCMs), domänkontrollant-moduler, solid state disk-hårddiskar (SSD), hårddiskar (HDD) eller EBOD-moduler tas med.

> [!IMPORTANT]
> Innan du tar bort och ersätter chassin, granska säkerhetsinformation i [StorSimple maskinvaruersättning komponenten](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Ta bort chassit
Utför följande steg för att ta bort chassin på StorSimple-enheten.

#### <a name="to-remove-a-chassis"></a>Ta bort ett chassi
1. Se till att StorSimple-enheten stängs av och kopplas bort från alla strömkällor.
2. Ta bort alla nätverks- och SAS-kablar, om tillämpligt.
3. Ta bort enheten från racket.
4. Ta bort var och en av enheterna och Observera de platser som de tas bort. Mer information finns i [ta bort diskenheten](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. På EBOD-hölje (om detta är det chassi som misslyckades) ta bort EBOD controller moduler. Mer information finns i [ta bort en EBOD-kontrollanten](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    På primära enheten (om detta är det chassi som misslyckades), ta bort styrenheterna och notera de platser som de tas bort. Mer information finns i [ta bort en kontrollant](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Installera chassit
Utför följande steg för att installera chassit på StorSimple-enheten.

#### <a name="to-install-a-chassis"></a>Installera ett chassi
1. Montera chassi racken. Mer information finns i [rackmontera StorSimple 8100-enhet](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) eller [rackmontera StorSimple 8600-enhet](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. När chassit är monterad i racket, installera controller-moduler på samma platser som de tidigare har installerats i.
3. Installera enheterna i samma positioner och platser som de tidigare har installerats i.
   
   > [!NOTE]
   > Vi rekommenderar att du först installera SSD-enheterna i platserna och sedan installera hårddiskar.
  
4. Enheten är monterad i racket och komponenter som är installerade kan ansluta enheten till lämplig strömkällor och aktivera enheten. Mer information finns i [Kabelansluta en StorSimple 8100-enhet](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) eller [Kabelansluta en StorSimple 8600-enhet](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Nästa steg
Läs mer om [StorSimple maskinvaruersättning komponenten](storsimple-8000-hardware-component-replacement.md).

