---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 792589f4aa2a80c05378224ffe4e4d1dad2f935c
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166431"
---
<!--author=alkohli last changed:02/22/16-->

#### <a name="to-attach-the-sas-cables"></a>Att koppla SAS-kablar
1. Identifiera den primära servern och EBOD-höljen. Två höljena kan identifieras genom att titta på sina respektive tillbaka plan. Se följande bild för vägledning. 
   
    ![Plan för primära och EBOD-höljen](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Säkerhetskopiering av primära och EBOD-höljen**
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |Primär hölje |
   | 2 |EBOD hölje |
2. Leta upp serienumren på den primära servern och EBOD-höljen. Serienummer etiketten fästs på Bakåt ensa på varje kabinett. Serienumren måste vara samma på både höljen. [Kontakta Microsoft Support](../articles/storsimple/storsimple-contact-microsoft-support.md) omedelbart om serienummer som inte matchar. Se följande bild för att hitta serienummer.
   
    ![Bakifrån hölje som visar serienummer](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Platsen för serienummer dekal**
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |Ensa för höljet |
3. Använda de angivna SAS-kablarna för att ansluta EBOD-höljet till primära höljet på följande sätt:
   
   1. Identifiera de fyra SAS-portarna på primära höljet och EBOD-höljet. SAS-portar är märkta som EBOD på primära höljet och motsvarar en port på EBOD-höljet enligt SAS-kablar bilden nedan.
   2. Använd de angivna SAS-kablarna för att ansluta EBOD-port till port A.
   3. EBOD-port på kontrollenhet 0 bör anslutas till port A på EBOD-kontrollanten 0. EBOD-port på kontrollenhet 1 bör anslutas till port A på EBOD-kontrollanten 1. Se följande bild för vägledning. 
      
      ![SAS-kablar för din enhet](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **SAS-kablar**
      
      | Etikett | Beskrivning |
      |:--- |:--- |
      | A |Primär hölje |
      | B |EBOD hölje |
      | 1 |Kontrollenhet 0 |
      | 2 |Kontrollenhet 1 |
      | 3 |EBOD-kontrollanten 0 |
      | 4 |EBOD-kontrollanten 1 |
      | 5, 6 |SAS-portar på primära hölje (EBOD märkta) |
      | 7, 8 |SAS-portar på EBOD hölje (Port A) |

