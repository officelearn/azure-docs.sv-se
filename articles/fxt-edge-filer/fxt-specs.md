---
title: Specifikationer för Microsoft Azure FXT Edge Filer | Microsoft Docs
description: Fysiskt och miljömässigt specifikationer för Azure FXT Edge Filer maskinvara
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 0679bce8eae515aa6b90e34fcfd15ee9b4e56b31
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542879"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Azure FXT Edge Filer specifikationer

Den här artikeln förklarar maskinvaruspecifikationer för Azure FXT Edge Filer maskinvarunoder. I praktiken är tre eller fler noder konfigurerade tillsammans för att tillhandahålla klustrade cache-system.

## <a name="hardware-specifications"></a>Maskinvaruspecifikationer

| Komponent | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Processorkärnor |  16 | 16 |
| DRAM  | 1536 GB | 768 GB |
| Nätverksportar | 6 x 25/10 Gb + 2 x 1 Gb | 6 x 25/10 Gb + 2 x 1 Gb |
| NVMe SSD-kapacitet | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>Specifikationer för enheten

Systemet har tio enhetsfack, kan nås från början. Varje enhet som fylls i automatiskt är märkt med kapacitetsinformation till höger. 

Enheten siffror trycks på utrymmet på enheter. Enheten 0 är längst ned till vänster enhet i Azure FXT Edge filservern, och enheten 1 är direkt under den.

![Foto av en hårddisk fack i FXT chassi, som visar enheten siffror och kapacitet etiketter](media/fxt-drives-photo.png)

| Enheten siffror    |  Användning   |  Specifikationer |
|------------------|--------|-----------------|
| 0, 1             | OS     | 480 GB SATA SSD |
| 2, 3, 4, 5, 6, 7, 8, 9 | Data   | FXT 6600: 3.2 TB NVMe SSD <br> FXT 6400: 1.6 TB NVMe SSD |


## <a name="dimensions-and-weight"></a>Mått och vikt

Azure FXT Edge Filer har utformats för att få plats i ett standard 19-tums utrustning rack och är en rackenhet hög (1U). 

<!-- 10x2.5 inches version -->

| Filer dimensioner           |                          |
|-----------------------------|--------------------------|
| Höjd                      | 42.8 mm (1.68 tum)    |
| Bredd (inklusive rack öronen) | 482.0 mm (18.97 tum)  |
| Bredd - huvudhölje      | 434.0 mm (17.08 tum) |
| Djup - rack öronen till baksidan av huvudhölje                   | 733.82 mm (29.61 tum) |
| Djup - rack öronen till sista bakre protrusion                 | 772.67 mm (30.42 tum) |
| Djup - rack öronen till sista främre protrusion utan ram | 22.0 mm (0.87 tum)  |
| Djup - rack öronen till sista främre protrusion med ram    | 35.84 mm (1.41 tum) |

| Vikt | |
|-----------------|----------------------|
| Noden vikt (utan förpackning, utan tillbehör) | 40 lbs (18,1 kg) |
| NET vikt (utan förpackning, inklusive tillbehör) | 51 lbs (23.1 kg)|
| Bruttomarginal vikt (som levererats, inklusive alla förpackning) |  64 lbs (29.0 kg) |

### <a name="shipping-dimensions"></a>Levererade dimensioner

| Paketet dimension | Millimeter | Tum |
|-------------------|-------------|--------|
| Höjd            | 311.2       | 12.25" |
| Bredd             | 642.8       | 25.31" |
| Längd            | 1,051.1     | 41.38" |

## <a name="power-and-thermal-specifications"></a>Kraften och termiska specifikationer

Det här avsnittet ger power omdömen och mätning av faktisk användning för Azure FXT Edge-Filer.

### <a name="nameplate-ratings"></a>Märkskylten betyg

| Märkskylten klassificeringar för FXT 6000 serien modeller |
|----------------|
| 100 - 240 V ~    |
| 10A – 5A (X 2)  |
| 50 / 60Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Kraften och termiska mätning av faktisk användning 

Azure Filer för FXT Edge-noder använder fläktar med olika hastigheter, så power beror på temperatur och belastning. Maximal fläkt hastigheter kan nås på vissa kombinationer av hög belastning och utökade omgivande temperaturer. 

Dessa diagram ger energiförbrukningen och termiska utdata mätning av faktisk användning för vanliga spänning frekvens kombinationer. 

| FXT 6600 power vid rummet temperatur <br />(22° C, 71.6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Spänning (V) | 100 | 120 | 208 | 230 | 240 | 
| Frekvens (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuell (A) | 5.02 | 4.16 |2.40 | 2.20 | 2.16 |
| Skenbar effekt (VA) | 502 | 499 | 499 | 506 | 518|
| Power-faktor | 0,99 | 0,99 |0.98 | 0.98 | 0.98 |
| Verklig kraft (W) | 497 |494 | 489 | 496 | 508 |
| Termisk nedbrytning (BTU/tim) |1696 | 1686 | 1669 | 1692 | 1733 |

| FXT 6600 power hastigheter maximala-fläkt | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Spänning (V) | 100 |120 | 208 | 230 | 240| 
| Frekvens (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuell (A) | 5.98 | 5.01 | 2.81 | 2.55 | 2.48 |
| Skenbar effekt (VA) | 598 | 601 | 584 | 587 | 595 |
| Power-faktor | 0,99 | 0,99 | 0.98 | 0.98 | 0.98 |
| Verklig kraft (W) | 592 | 595 | 573 | 575 | 583 |
| Termisk nedbrytning (BTU/tim) | 2020 |2031 | 1954 | 1961 | 1990 |

| FXT 6400 power vid rummet temperatur <br />(22° C, 71.6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Spänning (V) | 100 | 120 | 208 | 230 | 240 |
| Frekvens (Hz) |60 | 60 | 60 | 50 | 50 |
| Aktuell (A) | 4.63 | 3.86 | 2.24 | 2.04 | 1.94 |
| Skenbar effekt (VA) | 463 | 463 | 466 | 469 | 466 |
| Power-faktor | 0,99 | 0,99 | 0.98 | 0.98 | 0.98 | 
| Verklig kraft (W) | 458 | 459 | 457 | 460 | 456 |
| Termisk nedbrytning (BTU/tim) | 1564 | 1565 | 1558 | 1569 | 1557 |

| FXT 6400 power hastigheter maximala-fläkt | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Spänning (V) | 100 | 120 | 208 | 230 | 240 |
| Frekvens (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuell (A) | 5.15 | 4.28 | 2.48 | 2.28 | 2.13 |
| Skenbar effekt (VA) | 515 | 514 | 516 | 524 | 511 |
| Power-faktor | 0,99 | 0,99 | 0.98 | 0.98 | 0.98 |
| Verklig kraft (W) | 510 | 508 | 506 | 514 | 501 |
| Termisk nedbrytning (BTU/tim) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Miljökrav

Det här avsnittet innehåller specifikationerna för omgivande maskinvarumiljö.

### <a name="temperature-and-humidity"></a>Temperatur och fuktighet

| Miljömässig attribut   | Operativ intervall                   | Icke-drift intervall         |
|---------------------------|-----------------------------------|-----------------------------|
| Omgivande temperaturintervall | 10 oC 35 oC (50 – 86° F)          | 40 ° C till 65 ° C (40-149 ° F) |
| Omgivande relativ fuktighet | icke förenklas 10 – 80%          | 5-95% icke-förenklas     |
| Den maximala dagg punkt         | 29°C (84°F)                       | 33°C (91°F)                 |
| Höjd                  | upp till 3048 mätare (10 000 fot) omfattas av temperatur ta bort klassificering som anges nedan | upp till 12 000 mätare (39,370 fot) |

> [!NOTE] 
> **Höjd temperatur ta bort klassificering:** Högsta temperatur minskar med 1 oC/300 m (1F/547 ft) över 950 m (3,117 ft).

### <a name="airflow-shock-and-vibration"></a>Luftflödet och chocka vibrationer 

| Attribut         | Specifikationen |
|-------------------|---------------|
| Luftflödet                    | System luftflödet är framifrån och bakåt. Systemet måste köras med en low-pressure bakre avgaser-installation. |
| Chocka, operativa         | 6 G för 11 millisekunder (har testats i 6 riktningar) |
| Chocka, inte kan användas     | 71 G för 2 millisekunder (har testats i 6 riktningar) |
| Vibrationer, operativa     | 0,26 G<sub>RMS</sub> 5 Hz till 350 Hz slumpmässiga         |
| Vibrationer, inte kan användas | 1.88 G<sub>RMS</sub> 10 Hz till 500 Hz i 15 minuter (alla sex sidor testade)  |

## <a name="safety-regulation-compliance"></a>Säkerhet förordning efterlevnad 

Azure FXT Edge Filer följer de listade reglerna. 

| Category       | Regel-specifikation | 
|----------------|--------------------------|
| Allmän säkerhet | EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 <br>EN 62311:2008 | 
| EMC            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014 / IEC 61000-3-2:2014 (klassen D)   <br>EN 61000-3-3:2013 / IEC 61000-3-3:2013 |
| Energi         | Kommissionen förordning (EU) Nej. 617/2013  |
| RoHS           |    EN 50581:2012   |