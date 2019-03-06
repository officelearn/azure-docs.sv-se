---
title: StorSimple 8000-serien-komponenten maskinvaruersättning | Microsoft Docs
description: Beskriver hur du ersätter den PCMs, batteri, controller moduler, EBOD-styrenheter, diskenheter och chassi för en StorSimple-enhet på ett säkert sätt.
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
ms.date: 06/02/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: e05a37122647d4979089f0ba00b1fc15f9b84b0f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433662"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Ersätt en maskinvarukomponent på din enhet i StorSimple 8000-serien

## <a name="overview"></a>Översikt
Maskinvara komponenten ersättning självstudier beskriver maskinvarukomponenter för din enhet i Microsoft Azure StorSimple 8000-serien och stegen för att byta ut. Den här artikeln beskriver ikoner för säkerhet, har pekare till de detaljerade självstudierna och visar en lista över de komponenter som är utbytbara.

> [!IMPORTANT]
> Innan du försöker ta bort eller ersätta en StorSimple-komponent, se till att du läser igenom den [säkerhet ikonen konventioner](#safety-icon-conventions) och andra [säkerhetsåtgärder](storsimple-safety.md).


### <a name="safety-icon-conventions"></a>Ikonen konventioner för säkerhet
I följande tabell beskrivs de säkerhet ikoner som används i de här självstudierna. Var uppmärksam på dessa ikoner för säkerhet när du går igenom stegen för att ta bort och Ersätt komponenter.

| Ikon | Text | Ytterligare information |
|:--- |:--- |:--- |
| ![Varningsikon](./media/storsimple-hardware-component-replacement/Warning.png) |**FARA!** |Anger en farlig situation som, om inte undvikas leder död eller allvarlig skada. Den här signalen word är begränsad till extrema situationer. |
| ![Varningsikon](./media/storsimple-hardware-component-replacement/Warning.png) |**VARNING!** |Anger en farlig situation som, om inte undvikas kan resultera i död eller allvarlig skada. |
| ![Varningsikon](./media/storsimple-hardware-component-replacement/Caution.png) |**VARNING!** |Anger en farlig situation som, om inte undvikas kan resultera i att mindre eller måttlig skada. |
| ![Observera ikonen](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**MEDDELANDE:** |Anger information som anses viktiga, men inte risk-relaterade. |
| ![Elektriska chocka ikon](./media/storsimple-hardware-component-replacement/Electric.png) |**Elektriska chocka risk** |Anger hög spänning. |
| ![Kraftiga ikon](./media/storsimple-hardware-component-replacement/Weight.png) |**Kraftiga** | |
| ![Inga användbara delar Användarikon](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Inga användbara delar användare** |Inte kommer åt såvida inte korrekt utbildade. |
| ![Läs anvisningarna ikon](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Läsa alla instruktioner först** | |
| ![Varningsikon tipsikon](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Tips risk** | |

### <a name="before-you-begin"></a>Innan du börjar
Bekanta dig med säkerhetsinformation om din enhets- och ikoner som används i den här självstudien. Gå till [på ett säkert sätt installera och använda din StorSimple-enhet](storsimple-safety.md) fullständig information. Se till att granska den [säkerhetsåtgärder](storsimple-safety.md#handling-precautions) innan du hantera din StorSimple-enhet.

Tänk på följande innan du försöker ersätta en komponent.

![Varningsikon](./media/storsimple-hardware-component-replacement/Warning.png) ![elektriska chocka ikonen](./media/storsimple-hardware-component-replacement/Electric.png) **varning!**

* Början själv korrekt med hjälp av en elektrostatisk utsläpp eller antistatiska mat vid hantering av moduler och komponenter i din StorSimple-enhet.
* Rör inte några kretsar. Använd den angivna hanterar och guider vid hantering av komponenter som kan ha exponerade kretsar.

![Varningsikon](./media/storsimple-hardware-component-replacement/Warning.png) ![Observera ikonen](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **meddelande:**

När du ersätter en modul **aldrig lämnar ett tomt fack på baksidan av höljet**. Hämta en ersättare eller ett tomt modulen innan du tar bort delen av problemet.

## <a name="hardware-component-replacement-procedures"></a>Maskinvara-komponenten ersättning procedurer
Din enhet i StorSimple 8000-serien består av flera plugin-program i den primära servern och/eller EBOD-höljen. 8100 har en enda primär enhet medan 8600 är en dubbel hölje-enhet med en primär lagringsenhet och ett EBOD-hölje.

De huvudsakliga maskinvarukomponenterna i enheten sammanfattas i följande tabeller. Klicka på länken i den **ersättning proceduren** kolumn att gå till den associerade självstudien.

| Komponenter | # Finns | Plugin-modulen? | Ersättning proceduren |
|:--- |:--- |:--- |:--- |
| Chassi |1 |Nej |[Ersätt chassit på StorSimple-enheten](storsimple-8000-chassis-replacement.md) |
| Primära domänkontrollanter |2 |Ja |[Ersätta en controller-modulen på StorSimple-enheten](storsimple-8000-controller-replacement.md) |
| 764W strömförsörjning och kylning moduler (PCMs) |2 |Ja |[Ersätta en energi- och kylningsmodul på StorSimple-enheten](storsimple-8000-power-cooling-module-replacement.md) |
| Extrabatteri |2 |Ja |[Ersätta modulen för extrabatteriet på StorSimple-enheten](storsimple-8000-battery-replacement.md) |
| Diskenheter |12 |Ja |[Ersätt en diskenhet på StorSimple-enheten](storsimple-8000-disk-drive-replacement.md) |

**Tabell 1** maskinvarukomponenter i det primära höljet

Primär höljet och EBOD-kabinett skiljer sig åt i sina i/o-moduler. Dessutom har PCMs olika effekt. PCMs i det primära höljet är 764 W, medan de i EBOD-hölje är 580 W. PCMs i primära kabinetten också innehålla en extrabatteri-modul.

| Komponenter | # Finns | Plugin-modulen? | Ersättning proceduren |
|:--- |:--- |:--- |:--- |
| Chassi |1 |Nej |[Ersätt chassit på StorSimple-enheten](storsimple-8000-chassis-replacement.md) |
| EBOD-styrenheter |2 |Ja |[Ersätt EBOD-kontrollanten på StorSimple-enheten](storsimple-8000-ebod-controller-replacement.md) |
| 580 w vid strömförsörjning och kylning moduler (PCMs) |2 |Ja |[Ersätta en energi- och kylningsmodul på StorSimple-enheten](storsimple-8000-power-cooling-module-replacement.md) |
| Diskenheter |12 |Ja |[Ersätt en diskenhet på StorSimple-enheten](storsimple-8000-disk-drive-replacement.md) |

**Tabell 2** maskinvarukomponenter i EBOD-hölje

Plugin-moduler på enheten är markerad i följande främre och bakre diagram. Du kan använda dessa diagram för att fastställa platsen för olika plugin-moduler om ersättning krävs. Främre diagrammet visar diskenheterna och bakre diagrammen i EBOD-höljet och den primära hölje visa plugin-moduler.

![Frontpanel för enheten med diskenheter](./media/storsimple-hardware-component-replacement/IC741028.png)

**Bild 1** Front för enheten

| Label (Etikett) | Beskrivning |
|:--- |:--- |
| 0 - 11 |Diskenheter (totalt 12) |

Både primära höljet och EBOD-kabinett har enheten operatör moduler. Chassit inrymmer tolv 3,5-tums diskenheter ordnade i ett 3 av 4-format.

![Serverdelen av enhetens primära Höljesmoduler](./media/storsimple-hardware-component-replacement/IC740994.png)

**Bild 2** baksidan av primära höljet

| Label (Etikett) | Beskrivning |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Kontrollenhet 0 |
| 4 |Kontrollenhet 1 |

![Serverdelen av enheten EBOD hölje plugin-moduler](./media/storsimple-hardware-component-replacement/IC769599.png)

**Bild 3** baksidan av EBOD-hölje

| Label (Etikett) | Beskrivning |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD-kontrollanten 0 |
| 4 |EBOD-kontrollanten 1 |

## <a name="field-replaceable-units"></a>Fältet replaceable enheter
Följande fält replaceable enheter (FRU) är tillgängliga för din StorSimple-enhet:

* Chassi (inklusive panelen integrerade åtgärder)
* 764 W AC PCM
* 580 W AC PCM
* Hårddisk med enheten operatör-modulen
* Modul för domänkontrollant
* EBOD controller-modulen
* Extrabatteri modul
* Rackmonterade rail kit

. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) att ordna någon av dessa ersättning-enheter.

## <a name="next-steps"></a>Nästa steg
Granska alla [säkerhetsinformation](storsimple-safety.md) innan du försöker ersätta en StorSimple-maskinvarukomponent.

