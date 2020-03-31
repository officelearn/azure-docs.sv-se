---
title: Byt chassi på StorSimple 8000-seriens enhet | Microsoft-dokument
description: Beskriver hur du tar bort och byter chassit för ditt StorSimple-huvudhölje eller EBOD-hölje.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61312453"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Byt ut chassit på StorSimple-enheten
## <a name="overview"></a>Översikt
I den här självstudien beskrivs hur du tar bort och byter ut ett chassi i en StorSimple 8000-serieenhet. StorSimple 8100-modellen är en enda kapslingsenhet (ett chassi), medan 8600 är en enhet med dubbla kapslingar (två chassin). För en 8600-modell finns det potentiellt två chassin som kan misslyckas i enheten: chassit för det primära höljet eller chassit för EBOD-höljet.

I båda fallen är ersättningschassit som levereras av Microsoft tomt. Inga power- och kylmoduler (PCM), styrenheter, SSD-enheter (Solid State Disk drives), hårddiskar (HDDs) eller EBOD-moduler kommer att ingå.

> [!IMPORTANT]
> Innan du tar bort och byter chassit bör du läsa säkerhetsinformationen i [StorSimples maskinvarukomponentbyte](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Ta bort chassit
Utför följande steg för att ta bort chassit på Din StorSimple-enhet.

#### <a name="to-remove-a-chassis"></a>Så här tar du bort ett chassi
1. Kontrollera att StorSimple-enheten är avstängd och frånkopplad från alla strömkällor.
2. Ta bort alla nätverks- och SAS-kablar, om tillämpligt.
3. Ta bort enheten från racket.
4. Ta bort var och en av enheterna och notera de platser från vilka de tas bort. Mer information finns i [Ta bort diskenheten](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. På EBOD-höljet (om detta är chassit som misslyckades) tar du bort EBOD-styrenheterna. Mer information finns i [Ta bort en EBOD-styrenhet](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    På den primära höljet (om detta är chassit som misslyckades), ta bort styrenheterna och notera de platser från vilka de tas bort. Mer information finns i [Ta bort en styrenhet](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Installera chassit
Utför följande steg för att installera chassit på Din StorSimple-enhet.

#### <a name="to-install-a-chassis"></a>Så här installerar du ett chassi
1. Montera chassit i racket. Mer information finns i [Rackmontera Din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) eller [Rackmontera Din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. När chassit är monterat i racket installerar du styrenhetsmodulerna i samma lägen som de tidigare installerades i.
3. Installera enheterna i samma positioner och platser som de tidigare installerades i.
   
   > [!NOTE]
   > Vi rekommenderar att du installerar SSD:erna i kortplatserna först och sedan installerar hårddiskarna.
  
4. När enheten är monterad i racket och komponenterna installerade ansluter du enheten till lämpliga strömkällor och slår på enheten. Mer information finns i [Kabel din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) eller [Kabel din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Nästa steg
Läs mer om byte av [Maskinvarukomponent i StorSimple](storsimple-8000-hardware-component-replacement.md).

