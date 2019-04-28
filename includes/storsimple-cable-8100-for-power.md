---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: db2104020e9478b1fedf68e1c9467f75e16044e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60482781"
---
#### <a name="to-cable-for-power"></a>Till power-kabel
1. Se till att kraften växlar på varje kraften och kylning moduler (PCMs) är i OFF-läge.
2. Anslut strömkablar till var och en av PCMs i primära höljet.
3. Koppla strömkablar till rack kraftfördelningsenheter (PDU) enligt följande bild. Se till att två PCMs användning separata strömkällor.
   
   > [!IMPORTANT]
   > För att säkerställa hög tillgänglighet för ditt system, rekommenderar vi att du följer strikt till potensen kablar schemat visas i följande diagram. 
   > 
   > 
   
    ![Kabelansluta den 2U kraft](./media/storsimple-cable-8100-for-power/HCSCableYour2UDeviceforPower.png)
   
    **Power kablar på en 8100-enhet**
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |Kontrollenhet 1 |
   | 3 |Kontrollenhet 0 |
   | 4 |PCM 1 |
   | 5 |PDU |
4. Om du vill aktivera systemet Vänd power-växlar på båda PCMs till på plats.

