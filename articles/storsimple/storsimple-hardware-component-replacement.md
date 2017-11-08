---
title: "Ersättning av StorSimple maskinvara komponenten | Microsoft Docs"
description: "Beskriver hur du ersätter den PCMs, batteri, domänkontrollant moduler, EBOD domänkontrollanter, diskenheter och chassi av StorSimple-enhet på ett säkert sätt."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: e8087ba7-0b66-4f59-8988-e53aad52ee21
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2c8945dd06f0372c4260e0553e15627607be6979
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Ersätta en maskinvarukomponent på enheten StorSimple 8000-serien
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Om du vill visa versionen av den här artikeln för den nya Azure portalen, gå till [ersätta en maskinvarukomponent på enheten StorSimple 8000-serien](storsimple-8000-hardware-component-replacement.md). Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Översikt
Maskinvara komponenten ersättning självstudier beskriver maskinvarukomponenter för din enhet för Microsoft Azure StorSimple 8000-serien och stegen för att byta ut. Den här artikeln beskriver säkerhet ikoner, ger länkar till detaljerade självstudier och visar en lista över de komponenter som är utbytbara.

> [!IMPORTANT]
> Innan du försöker ta bort eller ersätta en StorSimple-komponent, se till att du läser den [säkerhet ikonen konventioner](#safety-icon-conventions) och andra [säkerhetsåtgärderna](storsimple-safety.md).
> 
> 

### <a name="safety-icon-conventions"></a>Ikonen konventioner för säkerhet
I följande tabell beskrivs de säkerhet ikoner som används i de här kurserna. Var uppmärksam på ikonerna säkerhet när du går igenom stegen för att ta bort och ersätter komponenter.

| Ikon | Text | Ytterligare information |
|:--- |:--- |:--- |
| ![Varningsikon](./media/storsimple-hardware-component-replacement/Warning.png) |**RISK!** |Visar en farliga situation som, om inte undvikas resulterar i död eller allvarliga skador. Signal ordet är begränsad till extrema fall. |
| ![Varningsikon](./media/storsimple-hardware-component-replacement/Warning.png) |**VARNING!** |Visar en farliga situation som, om inte undvikas, kan leda till död eller allvarliga skador. |
| ![Varningsikon](./media/storsimple-hardware-component-replacement/Caution.png) |**VARNING!** |Visar en farliga situation som, om inte undvikas, kan leda till lägre eller måttlig skada. |
| ![Meddelande-ikon](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**MEDDELANDE:** |Anger information som anses vara viktiga, men inte risker relaterade. |
| ![Elektrisk undanröjs ikon](./media/storsimple-hardware-component-replacement/Electric.png) |**Elektrisk undanröjs risk** |Anger hög spänning. |
| ![Ikon för tunga vikt](./media/storsimple-hardware-component-replacement/Weight.png) |**Tunga vikt** | |
| ![Ingen användare användbar delar ikon](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Inga användare användbar delar** |Inte kommer åt såvida inte korrekt utbildade. |
| ![Läs anvisningarna ikon](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Läsa alla instruktioner först** | |
| ![Risken tipsikon](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Tips risk** | |

### <a name="before-you-begin"></a>Innan du börjar
Bekanta dig med säkerhetsinformation om din enhets- och ikoner som används i den här kursen. Gå till [på ett säkert sätt installera och använda din StorSimple-enhet](storsimple-safety.md) fullständig information. Se till att granska den [säkerhetsåtgärderna](storsimple-safety.md#handling-precautions) innan du hanterar din StorSimple-enhet. 

Tänk på följande innan du försöker ersätta en komponent.

![Varningsikon](./media/storsimple-hardware-component-replacement/Warning.png) ![elektrisk undanröjs ikonen](./media/storsimple-hardware-component-replacement/Electric.png) **varning!** 

* Bakgrund själv korrekt genom att använda en elektrostatisk utsläpp eller antistatiska mat vid hantering av moduler och komponenter i din StorSimple-enhet.
* Touch inte någon kretsar. Använd angivna referenser och guider vid hantering av komponenter som kan ha exponerade kretsar.

![Varningsikon](./media/storsimple-hardware-component-replacement/Warning.png) ![notera ikonen](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **meddelande:**

När du ersätter en modul **lämnar aldrig ett tomt fack på baksidan av höljet**. Hämta en ersättare eller ett tomt modulen innan du tar bort delar.

## <a name="hardware-component-replacement-procedures"></a>Procedurer för maskinvara komponenten ersättning
Enheten StorSimple 8000-serien består av flera plugin-program i den primära servern och/eller EBOD höljen. 8100 har en enda primär enhet medan 8600 är en dubbel hölje-enhet med en primär enhet och en EBOD bilaga.

De huvudsakliga maskinvarukomponenterna i enheten sammanfattas i följande tabeller. Klicka på länken i den **ersättning proceduren** kolumnen att gå till den associerade kursen.

| Komponenter | # Finns | Plugin-modul? | Ersättning proceduren |
|:--- |:--- |:--- |:--- |
| Chassi |1 |Nej |[Ersätt chassi på din StorSimple-enhet](storsimple-chassis-replacement.md) |
| Primära domänkontrollanter |2 |Ja |[Ersätta en domänkontrollant modul på din StorSimple-enhet](storsimple-controller-replacement.md) |
| 764W ström och kylning moduler (PCMs) |2 |Ja |[Ersätta en ström och kylning modulen på din StorSimple-enhet](storsimple-power-cooling-module-replacement.md) |
| Säkerhetskopiering batteri |2 |Ja |[Ersätt batterimodulen säkerhetskopiering på din StorSimple-enhet](storsimple-battery-replacement.md) |
| Diskenheter |12 |Ja |[Ersätta en diskenhet på din StorSimple-enhet](storsimple-disk-drive-replacement.md) |

**Tabell 1** maskinvarukomponenter i primära höljet

Primär höljet och EBOD höljet skiljer sig åt i sina i/o-moduler. Dessutom har PCMs olika effekt. PCMs i det primära höljet är 764 V, medan de i EBOD höljet är 580 W. PCMs i primära höljet också innehålla en reserv batteri modul.

| Komponenter | # Finns | Plugin-modul? | Ersättning proceduren |
|:--- |:--- |:--- |:--- |
| Chassi |1 |Nej |[Ersätt chassi på din StorSimple-enhet](storsimple-chassis-replacement.md) |
| EBOD domänkontrollanter |2 |Ja |[Ersätta en domänkontrollant för en EBOD på din StorSimple-enhet](storsimple-ebod-controller-replacement.md) |
| 580 w vid ström och kylning moduler (PCMs) |2 |Ja |[Ersätta en ström och kylning modulen på din StorSimple-enhet](storsimple-power-cooling-module-replacement.md) |
| Diskenheter |12 |Ja |[Ersätta en diskenhet på din StorSimple-enhet](storsimple-disk-drive-replacement.md) |

**Tabell 2** maskinvarukomponenter i EBOD höljet

Plugin-moduler på enheten är markerade i följande främre och bakre diagram. Du kan använda dessa diagram för att avgöra var olika plugin-moduler om det krävs en ersättning. Främre diagram visar diskenheterna och bakre diagrammen i EBOD höljet och primära höljet visa plugin-moduler.

![Frontplane av enheten med diskenheter](./media/storsimple-hardware-component-replacement/IC741028.png)

**Bild 1** främre för enheten

| Etikett | Beskrivning |
|:--- |:--- |
| 0 - 11 |Diskenheter (totalt 12) |

Både primära höljet och EBOD höljet har enheten operatör moduler. Chassit inrymmer tolv 3,5-tums diskenheter ordnade i formatet 3 av 4.

![Bakplan av primära Höljesmoduler](./media/storsimple-hardware-component-replacement/IC740994.png)

**Bild 2** baksidan primära höljet

| Etikett | Beskrivning |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Styrenhet 0 |
| 4 |Kontrollant 1 |

![Bakplan av EBOD hölje plugin-moduler](./media/storsimple-hardware-component-replacement/IC769599.png)

**Bild 3** baksidan EBOD höljet

| Etikett | Beskrivning |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD styrenhet 0 |
| 4 |EBOD kontrollant 1 |

## <a name="field-replaceable-units"></a>Fältet utbytbara enheter
Följande fält utbytbara enheter (FRU) är tillgängliga för din StorSimple-enhet:

* Chassi (inklusive panelen integrerade åtgärder)
* 764 W AC PCM
* 580 W AC PCM
* Hårddisk med enheten operatör modul
* Modul för domänkontrollant
* EBOD domänkontrollant modul
* Modul för säkerhetskopiering batteri
* Rackmonterade tåg kit

Kontrollera [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md) att ordna någon av dessa enheter ersättning.

## <a name="next-steps"></a>Nästa steg
Granska alla [säkerhetsinformation](storsimple-safety.md) innan du försöker ersätta en StorSimple-maskinvarukomponent.

