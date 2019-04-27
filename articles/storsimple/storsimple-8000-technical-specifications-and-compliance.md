---
title: StorSimple tekniska specifikationer | Microsoft Docs
description: Beskriver de tekniska specifikationerna och regelverk kompatibilitetsinformation för maskinvarukomponenter för StorSimple.
services: storsimple
documentationcenter: NA
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
ms.openlocfilehash: b36d721896bd7b4f95d831eded500a96969937c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631898"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Tekniska specifikationer och efterlevnad för StorSimple-enhet

## <a name="overview"></a>Översikt

Maskinvarukomponenter för Microsoft Azure StorSimple-enheten följa de tekniska specifikationerna och regelverk som beskrivs i den här artikeln. De tekniska specifikationerna beskrivs Power och kylning moduler (PCMs), diskenheter, lagringskapacitet och bilagor. Kompatibilitetsinformationen innehåller sådant som internationell standard, säkerhet och utsläpp och kablar.

## <a name="power-and-cooling-module-specifications"></a>Ström och kylning modulen specifikationer

StorSimple-enheten har två 100-240 V dubbla fläktar, flesta SBB-kompatibla Power kylning moduler (PCMs). Detta ger en redundant kraft-konfiguration. Om en PCM misslyckas, fortsätter enheten att fungera normalt på andra PCM förrän modulen misslyckade ersätts.

EBOD-höljet använder en 580 W PCM och primära hölje använder en 764 W PCM. I tabellerna nedan listas de tekniska specifikationerna som är associerade med PCMs.

| Specifikationen | 580 W PCM (EBOD) | 764 W PCM (primär) |
| --- | --- | --- |
| Högsta uteffekt |580 W |764 |
| Frekvens |50/60 Hz |50/60 Hz |
| Val av tidsintervall spänning |Automatisk sträcker sig: 90 – 264 V AC, 47/63 Hz |Automatisk sträcker sig: 90- 264 V AC, 47/63 Hz |
| Maximal översvämning aktuella |20 A |20 A |
| Power factor korrigering |> 95% nominell ingångsspänning |> 95% nominell ingångsspänning |
| Övertoner |Uppfyller EN61000-3-2 |Uppfyller EN61000-3-2 |
| Resultat |5v vänteläge spänning \@ 2.0 A |5v vänteläge spänning \@ 2.7 A |
| +5V \@ 42 A |+5V \@ 40 A | |
| +12V \@ 38 A |+12V \@ 38 A | |
| Hot plug |Ja |Ja |
| Växlar och led: ar |AC på/av växeln och fyra status övervakningsindikatorer |AC på/av växeln och sex status övervakningsindikatorer |
| Hölje kylning |Axial kylfläktar med varierande hastighet fläktkontroll |Axial kylfläktar med varierande hastighet fläktkontroll |

## <a name="power-consumption-statistics"></a>Power förbrukningsstatistik

I följande tabell visas vanliga strömförbrukningsdata (faktiska värdena kan variera från den publicerade) för de olika modellerna för StorSimple-enhet.

| Villkor | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Fans långsam, enheter som är inaktiv |1,45 A |0.31 kW |1057.76 BTU/timme |3.19 A |0.34 kW |1160.13 BTU/timme |
|  Fans långsammare enheter åtkomst till |1.54 A |0.33 kW |1126.01 BTU/timme |3.27 A |0.36 kW |1228.37 BTU/timme |
|  Fans snabbt, enheter inaktiv, två PSUs drivs |PUNKT 2.14 A |0.49 kW |1671.95 BTU/timme |4.99 A |0.54 kW |1842.56 BTU/timme |
|  Snabb, fans enheter inaktiv finns en PSU drivs en inaktiv |2.05 A |0.48 kW |1637.83 BTU/timme |4.58 A |0,50 kW |1706.07 BTU/timme |
|  Fans snabbt, enheter åtkomst till två PSUs drivs |2,26 A |0.51 kW |1740.19 BTU/timme |4.95 A |0.54 kW |1842.56 BTU/timme |
|  Snabb fläktar, enheter som har åtkomst till, en PSU drivs en inaktiv |PUNKT 2.14 A |0.49 kW |1671.95 BTU/timme |4.81 A |0.53 kW |1808.44 BTU/timme |

## <a name="disk-drive-specifications"></a>Diskenhet specifikationer

Din StorSimple-enhet stöder upp till 12 3,5-tums formuläret faktor seriellt ansluten SCSI (SAS) diskenheter. De faktiska enheterna kan vara en blandning av SSD (solid-state drive) eller hårddiskar (HDD), beroende på hur produkten. 12 diskenheten platserna befinner sig i en konfiguration med 3 av 4 framför höljet. EBOD-höljet kan ytterligare lagringsutrymme för en annan 12 diskenheter. Det här är alltid hårddiskar.

## <a name="storage-specifications"></a>Storage-specifikationer

StorSimple-enheter har en blandning av hårddiskar och SSD-enheter för både 8100 och 8600. Den totala kapaciteten som kan användas för 8100 och 8600 är ungefär 15 TB och 38 TB. I följande tabell beskrivs information om SSD och HDD-molnkapacitet i samband med kapacitet för StorSimple-lösning.

| Enhetsmodell / kapacitet | 8100 | 8600 |
| --- | --- | --- |
| Antalet hårddiskar (HDD) |8 |19 |
| Antalet solid-state-hårddiskar (SSD) |4 |5 |
| Enkel hårddiskkapacitet |4 TB |4 TB |
| Enkel SSD-kapacitet |400 GB |800 GB |
| Ledig kapacitet |4 TB |4 TB |
| Användbar HDD-kapacitet |14 TB |36 TB |
| Användbar SSD-kapacitet |800 GB |2 TB |
| Totalt antal användbar kapacitet * |~ 15 TB |~ 38 TB |
| Lösning för maximal kapacitet (inklusive molnet) |200 TB |500 TB |

<sup>* </sup>- *Totalt antal användbar kapacitet innehåller den tillgängliga kapaciteten för data, metadata och buffertar. Du kan etablera lokalt fästa volymer upp till 8,5 TB på 8100-enheten eller upp till 22,5 TB på den större 8600-enheten. Mer information går du till [StorSimple lokalt fixerade volymer](storsimple-8000-local-volume-faq.md).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Hölje dimensioner och vikt specifikationer

I tabellerna nedan listas de olika hölje specifikationerna för dimensioner och vikt.

### <a name="enclosure-dimensions"></a>Dimensioner för hölje

I följande tabell visas måtten på höljet i millimeter och tum.

| Hölje | Millimeter | Tum |
| --- | --- | --- |
| Höjd |87.9 |3.46 |
| Bredd för montering flänsad |483 |19.02 |
| Bredd i brödtexten för hölje |443 |17.44 |
| Djup från fronten till slutet av hölje brödtext |577 |22.72 |
| Djup operations-panelen till sista delen av hölje |630.5 |24.82 |
| Djup från montering flänsad till sista delen av hölje |603 |23.74 |

### <a name="enclosure-weight"></a>Vikt för hölje

Beroende på konfigurationen, ett lästs in helt primära hölje kan väg från 21 med 33 kg och kräver två personer kan hantera den.

| Hölje | Vikt |
| --- | --- |
| Totalvikt (beroende på konfigurationen) |30 kg – 33 kg |
| Tom (inga enheter monterade) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Hölje miljö specifikationer

Det här avsnittet listas de specifikationer som rör hölje-miljö. Temperatur, fuktighet, höjd, chocka, vibrationer, orientering, säkerhet och elektromagnetisk kompatibilitet (EMC) ingår i den här kategorin.

### <a name="temperature-and-humidity"></a>Temperatur och fuktighet

| Hölje | Omgivande temperaturintervall | Omgivande relativ fuktighet | Maximal våt termometerkulans |
| --- | --- | --- | --- |
| I drift |5°C - 35°C(41°F - 95°F) |20% till 80% icke-förenklas- |28°C (82°F) |
| Kan inte användas |-40 OC - 70 OC 40° F - 158° F |icke förenklas 5% till 100% |29°C (84°F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Luftflödet, höjd, chocka, vibrationer, orientering, säkerhet och EMC

| Hölje | Operativa specifikationer |
| --- | --- |
| Luftflödet |System luftflödet är framifrån och bakåt. Systemet måste köras med en low-pressure, bakre avgaser installation. Tryck skapats av rack dörrar och hinder får inte överstiga 5 Pascal (0,5 mätare). |
| Höjd operativa |-30 värden med 3045 (-100 fot till 10 000 fot) med högsta operativsystem temperatur, ta bort klassificerats av 5 oC ovan 7000 fot. |
| Höjden som inte kan användas |-305 värden med 12 192 (– 1 000 fot till 40 000 fot) |
| Chocka, operativa |5g 10 ms ½ sinus |
| Chocka, inte kan användas |30g 10 ms ½ sinus |
| Vibrationer, operativa |0.21g RMS 5 500 Hz slumpmässiga |
| Vibrationer, inte kan användas |1.04g RMS 2 200 Hz slumpmässiga |
| Vibrationer, dataflytt |3g 2 200 Hz sinus |
| Orientering och montering |19-tums rackmontera (2 EIA enheter) |
| Rack rails |För att passa minsta 700 mm (31.50 tum) djup rack följer IEC 297 |
| Säkerhet och godkännanden |CE och UL SV 61000-3, IEC 61000-3, UL 61000-3 |
| EMC |EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Internationell standard efterlevnad

Microsoft Azure StorSimple-enheten uppfyller följande internationella standarder:  

* CE - EN 60950 - 1
* CB rapporten till IEC 60950-1
* UL och cUL till UL 60950-1

## <a name="safety-compliance"></a>Säkerhet

Microsoft Azure StorSimple-enheten uppfyller följande säkerhet klassificeringar:

* System produkten typgodkännande: UL, cUL, CE
* Säkerhet efterlevnad: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>EMC efterlevnad

Microsoft Azure StorSimple-enheten uppfyller följande EMC-klassificeringar.

### <a name="emissions"></a>Utsläpp

Enheten är EMC-kompatibel för ledda och UTSTRÅLAD utsläpp nivåer.

* Från utsläpp gränsen nivåer: CFR 47 del 15B klass A EN55022 klass A CISPR klass A
* Radiated utsläpp gränsen nivåer: CFR 47 del 15B klass A EN55022 klass A CISPR klass A

### <a name="harmonics-and-flicker"></a>Övertoner och flimmer

Enheten uppfyller EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Gränsen för immunitetsnivåerna

Enheten uppfyller EN55024.

## <a name="ac-power-cord-compliance"></a>AC power kabel efterlevnad

Plugin och fullständig power kabel sammansättningen måste uppfylla standarder det land där enheten som används och de måste ha säkerhet godkännanden som accepteras i respektive land. I tabellerna nedan listas standarder för USA och Europa.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Strömkablar AC - USA (måste vara NRTL visas)

| Komponent | Specifikationen |
| --- | --- |
| Typ av kabel |SA eller SVT 18 AWG minimum, 3 ledare 2.0 taxor maximal längd |
| Plugin |NEMA 5 till 15P erfarenhet av att arbeta-type bilaga plugin görs 120 V, 10 a. eller IEC 320 C14 250 V, 10 A |
| Socket |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>Strömkablar AC - Europa

| Komponent | Specifikationen |
| --- | --- |
| Typ av kabel |Harmoniserade H05-VVF-3G1.0 |
| Socket |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Stöds nätverkskablarna

För de 10 GbE-nätverksgränssnitt DATA 2 och DATA 3 refererar till den [listan över stöds nätverkskablarna och moduler](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Nästa steg

Du är nu redo att distribuera en StorSimple-enhet i ditt datacenter. Mer information finns i [distribuera din lokala enhet](storsimple-8000-deployment-walkthrough-u2.md).

