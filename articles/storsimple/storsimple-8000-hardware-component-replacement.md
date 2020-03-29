---
title: Ersättning av hårdvarukomponent i StorSimple 8000-serien | Microsoft-dokument
description: Beskriver hur du på ett säkert sätt byter ut pcm, batteri, styrenhetsmoduler, EBOD-styrenheter, hårddiskar och chassin på en StorSimple-enhet.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60321827"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Byt ut en maskinvarukomponent på storsmultnad 8000-serien

## <a name="overview"></a>Översikt
I självstudiekurserna för ersättning av maskinvarukomponenter beskrivs maskinvarukomponenterna i microsoft Azure StorSimple 8000-serien och vilka steg som krävs för att ta bort och ersätta dem. I den här artikeln beskrivs säkerhetsikonerna, pekare till detaljerade självstudier och de komponenter som kan ersättas.

> [!IMPORTANT]
> Innan du försöker ta bort eller byta ut en StorSimple-komponent ska du se till att du granskar [säkerhetsikonkonventionerna](#safety-icon-conventions) och andra [säkerhetsåtgärder](storsimple-safety.md).


### <a name="safety-icon-conventions"></a>Konventioner om säkerhetsikon
I följande tabell beskrivs de säkerhetsikoner som används i dessa självstudier. Var uppmärksam på dessa säkerhetsikoner när du går igenom stegen för att ta bort och ersätta enhetskomponenter.

| Ikon | Text | Ytterligare information |
|:--- |:--- |:--- |
| ![Varningsikon](./media/storsimple-hardware-component-replacement/Warning.png) |**Fara!** |Indikerar en farlig situation som, om den inte undviks, kommer att leda till dödsfall eller allvarlig skada. Detta signalord är begränsat till de mest extrema situationerna. |
| ![Varningsikon](./media/storsimple-hardware-component-replacement/Warning.png) |**Varning!** |Indikerar en farlig situation som, om den inte undviks, kan leda till dödsfall eller allvarlig skada. |
| ![Ikon för Försiktighet](./media/storsimple-hardware-component-replacement/Caution.png) |**Försiktighet!** |Anger en farlig situation som, om den inte undviks, kan leda till mindre eller måttliga skador. |
| ![Ikon för Meddelande](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**Märker:** |Anger information som anses viktig, men inte farorelaterad. |
| ![Ikon för elektrisk stöt](./media/storsimple-hardware-component-replacement/Electric.png) |**Risk för elektriska stötar** |Indikerar hög spänning. |
| ![Ikon för tung vikt](./media/storsimple-hardware-component-replacement/Weight.png) |**Tung vikt** | |
| ![Ikon för delar som kan servas av användaren](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Inga delar som kan servas av användaren** |Få inte åtkomst om inte korrekt utbildad. |
| ![Ikon för läsinstruktioner](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Läs alla instruktioner först** | |
| ![Ikon för tipsfara](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Tips Fara** | |

### <a name="before-you-begin"></a>Innan du börjar
Bekanta dig med säkerhetsinformationen om din enhet och säkerhetsikoner som används i den här självstudien. Gå till [Säkert installera och använd din StorSimple-enhet](storsimple-safety.md) för fullständig information. Var noga med att granska [säkerhetsåtgärder](storsimple-safety.md#handling-precautions) innan du hanterar din StorSimple-enhet.

Innan du försöker ersätta en komponent bör du tänka på följande information.

![](./media/storsimple-hardware-component-replacement/Warning.png) ![Varningsikon elektrisk](./media/storsimple-hardware-component-replacement/Electric.png) stöt ikon **VARNING!**

* Jorda dig själv ordentligt genom att använda en elektrostatisk urladdning eller antistatisk matta vid hantering av moduler och komponenter i Din StorSimple-enhet.
* Rör inga kretsar. Använd de medföljande handtagen och guiderna när du hanterar komponenter som kan ha exponerade kretsar.

![Meddelande](./media/storsimple-hardware-component-replacement/Warning.png) ![om](./media/storsimple-hardware-component-replacement/NoticeIcon.png) meddelande om varningsikon **meddelande:**

När du byter ut en modul **lämnar DU ALDRIG ett tomt fack på baksidan av höljet**. Skaffa en ersättnings- eller tom modul innan du tar bort problemdelen.

## <a name="hardware-component-replacement-procedures"></a>Ersättningsprocedurer för maskinvarukomponenter
Enheten i StorSimple 8000-serien består av flera plug-in-moduler i de primära och/eller EBOD-kapslingarna. 8100 har en enda primär kapsling, medan 8600 är en dubbelkapsenhet med en primär kapsling och en EBOD-kapsling.

De viktigaste maskinvarukomponenterna i enheten sammanfattas i följande tabeller. Klicka på länken i kolumnen **Ersättningsprocedur** för att gå till den associerade självstudien.

| Komponenter | # Närvarande | Plug-in modul? | Ersättningsförfarande |
|:--- |:--- |:--- |:--- |
| Chassi |1 |Inga |[Byt ut chassit på StorSimple-enheten](storsimple-8000-chassis-replacement.md) |
| Primära styrenheter |2 |Ja |[Byt ut en styrenhetsmodul på StorSimple-enheten](storsimple-8000-controller-replacement.md) |
| 764W ström- och kylmoduler (PCM) |2 |Ja |[Ersätta en energi- och kylningsmodul på StorSimple-enheten](storsimple-8000-power-cooling-module-replacement.md) |
| Batteri för säkerhetskopiering |2 |Ja |[Ersätta modulen för extrabatteriet på StorSimple-enheten](storsimple-8000-battery-replacement.md) |
| Diskenheter |12 |Ja |[Byta ut en diskenhet på StorSimple-enheten](storsimple-8000-disk-drive-replacement.md) |

**Tabell 1** Maskinvarukomponenter i den primära höljet

Primärhöljet och EBOD-höljet skiljer sig åt i sina I/O-moduler. Dessutom har PCM:erna olika wattal. PcM:erna i den primära höljet är 764 W, medan de i EBOD-höljet är 580 W. PcM:erna i det primära höljet innehåller också en batterimodul för säkerhetskopiering.

| Komponenter | # Närvarande | Plug-in modul? | Ersättningsförfarande |
|:--- |:--- |:--- |:--- |
| Chassi |1 |Inga |[Byt ut chassit på StorSimple-enheten](storsimple-8000-chassis-replacement.md) |
| EBOD-styrenheter |2 |Ja |[Byt ut en EBOD-styrenhet på StorSimple-enheten](storsimple-8000-ebod-controller-replacement.md) |
| 580W ström- och kylmoduler (PCM) |2 |Ja |[Ersätta en energi- och kylningsmodul på StorSimple-enheten](storsimple-8000-power-cooling-module-replacement.md) |
| Diskenheter |12 |Ja |[Byta ut en diskenhet på StorSimple-enheten](storsimple-8000-disk-drive-replacement.md) |

**Tabell 2** Maskinvarukomponenter i EBOD-höljet

Plugin-modulerna på enheten är markerade i följande främre och bakre diagram. Du kan använda dessa diagram för att bestämma platsen för de olika plugin-modulerna om en ersättning krävs. Det främre diagrammet visar diskenheterna och de bakre diagrammen i EBOD-höljet och det primära höljet visar plugin-modulerna.

![Frontpanel på enheten med hårddiskar](./media/storsimple-hardware-component-replacement/IC741028.png)

**Bild 1** Enhetens framsida

| Label (Etikett) | Beskrivning |
|:--- |:--- |
| 0 - 11 |Hårddiskar (totalt 12) |

Både den primära kapslingen och EBOD-höljet har drivhållaremoduler. I chassit finns tolv 3,5-tums hårddiskar ordnade i ett 3-4-format.

![Bakplan av enhetens primära kapslingsmoduler](./media/storsimple-hardware-component-replacement/IC740994.png)

**Bild 2** Baksidan av primärhöljet

| Label (Etikett) | Beskrivning |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Kontrollant 0 |
| 4 |Kontrollant 1 |

![Bakplan av enheten EBOD kapsling plug-in moduler](./media/storsimple-hardware-component-replacement/IC769599.png)

**Bild 3** Baksidan av EBOD-inneslutningen

| Label (Etikett) | Beskrivning |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD Controller 0 |
| 4 |EBOD Controller 1 |

## <a name="field-replaceable-units"></a>Fält utbytbara enheter
Följande fältutbytbara enheter (FRUs) är tillgängliga för Din StorSimple-enhet:

* Chassi (inklusive den integrerade operationspanelen)
* 764 W AC PCM
* 580 W AC PCM
* Hårddisk med enhetsbärarmodul
* Controller modul
* EBOD controller modul
* Batterimodul för säkerhetskopiering
* Rack montering järnväg kit

[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att beställa någon av dessa ersättningsenheter.

## <a name="next-steps"></a>Nästa steg
Granska all [säkerhetsinformation](storsimple-safety.md) innan du försöker ersätta en StorSimple-maskinvarukomponent.

