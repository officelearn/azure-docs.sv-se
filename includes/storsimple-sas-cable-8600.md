---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 51e1fd18b52d7e215ba43be540156199fb41778e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187377"
---
#### <a name="to-attach-the-sas-cables"></a>Så här fäster du SAS-kablarna
1. Identifiera primär- och EBOD-kapslingarna. De två kapslingarna kan identifieras genom att titta på deras respektive bakplan. Se följande bild för vägledning. 
   
    ![Bakre plan för primära och EBOD kapslingar](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Bakgrundsbild av primära och EBOD-kapslingar**
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |Primär kapsling |
   | 2 |EBOD-kapsling |
2. Leta reda på serienumren på primär- och EBOD-kapslingarna. Serienummerdekalen fästs på baköra i varje kapsling. Serienumren måste vara identiska i båda kapslingarna. [Kontakta Microsoft Support](../articles/storsimple/storsimple-contact-microsoft-support.md) omedelbart om serienumren inte stämmer överens. Se följande bild för att hitta serienummer.
   
    ![Bakifrån av hölje som visar serienummer](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Placering av serienummerdekal**
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |Kapslingens öra |
3. Använd de medföljande SAS-kablarna för att ansluta EBOD-höljet till det primära höljet enligt följande:
   
   1. Identifiera de fyra SAS-portarna på den primära höljet och EBOD-höljet. SAS-portarna är märkta som EBOD på den primära inneslutningen och motsvarar port A på EBOD-kapslingen, som visas i SAS-kabelbilden nedan.
   2. Använd de medföljande SAS-kablarna för att ansluta EBOD-porten till port A.
   3. EBOD-porten på styrenheten 0 ska anslutas till porten A på EBOD-styrenheten 0. EBOD-porten på styrenheten 1 ska anslutas till porten A på EBOD-styrenheten 1. Se följande illustration för vägledning. 
      
      ![SAS-kablar för din enhet](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **SAS-kablar**
      
      | Label (Etikett) | Beskrivning |
      |:--- |:--- |
      | A |Primär kapsling |
      | B |EBOD-kapsling |
      | 1 |Kontrollant 0 |
      | 2 |Kontrollant 1 |
      | 3 |EBOD Controller 0 |
      | 4 |EBOD Controller 1 |
      | 5, 6 |SAS-portar i primärhölje (märkt EBOD) |
      | 7, 8 |SAS-portar på EBOD-kapsling (port A) |

