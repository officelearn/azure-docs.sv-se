---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: db2104020e9478b1fedf68e1c9467f75e16044e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187380"
---
#### <a name="to-cable-for-power"></a>Till kabel för ström
1. Se till att strömbrytarna på var och en av energi- och kylmodulerna (PCM) är i off-läge.
2. Anslut nätsladdarna till var och en av pcm:erna i det primära höljet.
3. Fäst nätsladdarna på rackeffektfördelningsenheterna (PD: er) enligt följande bild. Kontrollera att de två pcm-datorerna använder separata strömkällor.
   
   > [!IMPORTANT]
   > För att säkerställa hög tillgänglighet för ditt system rekommenderar vi att du strikt följer det effektkablarschema som visas i följande diagram. 
   > 
   > 
   
    ![Kabel din 2U-enhet för ström](./media/storsimple-cable-8100-for-power/HCSCableYour2UDeviceforPower.png)
   
    **Strömkablar på en 8100-enhet**
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |Kontrollant 1 |
   | 3 |Kontrollant 0 |
   | 4 |PCM 1 |
   | 5 |PD:er |
4. Om du vill slå på systemet vrider du strömbrytarna på båda pcm-datorerna till påläge.

