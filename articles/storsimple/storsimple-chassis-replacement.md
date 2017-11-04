---
title: "Ersätt chassi på StorSimple 8000-serieenhet | Microsoft Docs"
description: "Beskriver hur du tar bort och ersätter chassi för din StorSimple primära hölje eller EBOD hölje."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 537659ed-4c46-49c1-b1e4-186262f2542d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 7bc23e64331ad18c604ffaa29476766827119cd4
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Ersätt chassi på din StorSimple-enhet
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Om du vill visa versionen av den här artikeln för den nya Azure portalen, gå till [ersätta chassi på StorSimple-enheten](storsimple-8000-chassis-replacement.md). Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Översikt
Den här självstudiekursen beskrivs hur du tar bort och ersätter ett chassi i en enhet med StorSimple 8000-serien. StorSimple 8100 modellen är en enda enhet enhet (samma chassi), medan 8600 är en dubbel höljet enhet (två chassi). För en modell av 8600 är potentiellt två chassi som kan ha i enheten: chassi för primära höljet eller chassi för EBOD höljet.

I båda fallen är ersättning chassi som levereras av Microsoft tom. Ingen ström och kylning moduler (PCMs) domänkontrollant moduler solid state disk-hårddiskar (SSD), hårddiskar (HDD) eller EBOD moduler tas med.

> [!IMPORTANT]
> Innan du tar bort och ersätter chassit, granska säkerhetsinformation i [ersättning av StorSimple maskinvara komponenten](storsimple-hardware-component-replacement.md).
> 
> 

## <a name="remove-the-chassis"></a>Ta bort chassit
Utför följande steg för att ta bort chassi på StorSimple-enheten.

#### <a name="to-remove-a-chassis"></a>Ta bort ett chassi
1. Kontrollera att StorSimple-enheten stängs av och kopplas bort från alla strömkällor.
2. Ta bort alla nätverks- och SAS-kablar, om tillämpligt.
3. Ta bort enheten från racket.
4. Ta bort var och en av enheterna och Lägg märke till de platser där de tas bort. Mer information finns i [ta bort diskenheten](storsimple-disk-drive-replacement.md#remove-the-disk-drive).
5. På EBOD höljet (om detta är chassi som misslyckades), ta bort EBOD domänkontrollant moduler. Mer information finns i [ta bort en domänkontrollant för en EBOD](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller). 
   
    På primära höljet (om detta är chassi som misslyckades), ta bort domänkontrollanterna och Lägg märke till de platser där de tas bort. Mer information finns i [ta bort en domänkontrollant](storsimple-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Installera chassit
Utför följande steg för att installera chassit på din StorSimple-enhet.

#### <a name="to-install-a-chassis"></a>Så här installerar du ett chassi
1. Montera chassi i racket. Mer information finns i [rackmontera StorSimple-8100-enhet](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) eller [rackmonterade StorSimple-8600-enhet](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. När chassit har monterats i racket, installerar du modulerna som domänkontrollant på samma platser som de tidigare har installerats i.
3. Installera enheterna i samma platser och distributionsplatser som de tidigare har installerats i.
   
   > [!NOTE]
   > Vi rekommenderar att du först installera SSD-enheterna i platserna och sedan installera hårddiskar.
   > 
   > 
4. När enheten är monterat i racket och de komponenter som installeras kan ansluta enheten till lämplig strömkällor och slå på enheten. Mer information finns i [Kabelanslut din 8100 StorSimple-enhet](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) eller [Kabelanslut din 8600 StorSimple-enhet](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [ersättning av StorSimple maskinvara komponenten](storsimple-hardware-component-replacement.md).

