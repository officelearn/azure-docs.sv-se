---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187379"
---
#### <a name="to-cable-your-device-for-power"></a>Så här kabelr du enheten för ström
> [!NOTE]
> Båda kapslingarna på StorSimple-enheten innehåller redundanta PCM-moduler. För varje hölje måste pcm-datorerna installeras och anslutas till olika strömkällor för att säkerställa hög tillgänglighet.
> 
> 

1. Se till att strömbrytarna på alla PCM-datorer är i off-läge.
2. Anslut nätsladdarna till båda pcM:erna på det primära höljet. Nätsladdarna identifieras i rött i kraftkablardiagrammet nedan.
3. Kontrollera att de två pcm-datorerna i det primära höljet använder separata strömkällor.
4. Fäst nätsladdarna på strömmen på rackfördelningsenheterna enligt vad som visas i kraftkablardiagrammet.
5. Upprepa steg 2 till och med 4 för EBOD-kapslingen.
6. Slå på EBOD-höljet genom att vrida strömbrytaren på varje PCM till påläge.
7. Kontrollera att EBOD-höljet är aktiverat genom att kontrollera att de gröna lysdioderna på baksidan av EBOD-styrenheten är påslagna.
8. Slå på den primära höljet genom att vrida varje PCM-omkopplare till påläge.
9. Kontrollera att systemet är aktiverat genom att se till att lysdioderna för enhetsstyrenheten har slagits på.
10. Kontrollera att anslutningen mellan EBOD-styrenheten och enhetsstyrenheten är aktiv genom att kontrollera att de fyra lysdioderna bredvid SAS-porten på EBOD-styrenheten är gröna.
    
    > [!IMPORTANT]
    > För att säkerställa hög tillgänglighet för ditt system rekommenderar vi att du strikt följer det effektkablarschema som visas i följande diagram.
    > 
    > 
    
    ![Kabel din 4U-enhet för ström](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Strömkablar**
    
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
    | 9 |PD:er |

