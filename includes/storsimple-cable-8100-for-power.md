---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0e8db8779958afe1fd3cf4bf12f2a6fd4a97c61c
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165824"
---
<!--author=alkohli last changed: 9/16/15-->

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
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |Kontrollenhet 1 |
   | 3 |Kontrollenhet 0 |
   | 4 |PCM 1 |
   | 5 |PDU |
4. Om du vill aktivera systemet Vänd power-växlar på båda PCMs till på plats.

