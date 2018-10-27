---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8e6db54853efcba4d648c1d3bc793a9d1ce57441
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165099"
---
<!--author=alkohli last changed: 9/16/15-->


#### <a name="to-cable-your-device-for-power"></a>Att kabelansluta den kraft
> [!NOTE]
> Båda höljen på StorSimple-enheten inkluderar redundant PCMs. För varje kabinett måste PCMs installerat och anslutet till olika strömkällor att säkerställa hög tillgänglighet.
> 
> 

1. Se till att strömbrytare på alla PCMs är i OFF-läge.
2. Anslut strömkablar till båda PCMs från primära höljet. Strömkablar identifieras i rött i power går diagrammet nedan.
3. Se till att två PCMs på primära enheten använder separata strömkällor.
4. Koppla strömkablar till ström på rack distribution enheter enligt kraften kablar diagram.
5. Upprepa steg 2 till 4 för EBOD-höljet.
6. Aktivera EBOD-höljet genom att vända på strömknappen på varje PCM till på plats.
7. Kontrollera att EBOD-hölje är på genom att kontrollera att de gröna led: ar på baksidan av EBOD-kontrollanten är aktiverade.
8. Aktivera primära höljet väljer varje PCM-växel till på plats.
9. Kontrollera att systemet är på genom att kontrollera att enhetens styrenhet led: ar har aktiverat.
10. Kontrollera att anslutningen mellan EBOD-kontrollanten och enhetens styrenhet är aktiv genom att verifiera att fyra led: ar bredvid den SAS-porten på EBOD-kontrollanten är grönt.
    
    > [!IMPORTANT]
    > För att säkerställa hög tillgänglighet för ditt system, rekommenderar vi att du följer strikt till potensen kablar schemat visas i följande diagram.
    > 
    > 
    
    ![Kabelansluta den 4U kraft](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Kablar**
    
    | Etikett | Beskrivning |
    |:--- |:--- |
    | 1 |Primär hölje |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |Kontrollenhet 0 |
    | 5 |Kontrollenhet 1 |
    | 6 |EBOD-kontrollanten 0 |
    | 7 |EBOD-kontrollanten 1 |
    | 8 |EBOD hölje |
    | 9 |PDU |

