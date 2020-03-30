---
title: Microsoft Azure FXT Edge Filer specifikationer | Microsoft-dokument
description: Fysiska och miljömässiga specifikationer för Azure FXT Edge Filer-maskinvara
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: c06b0c79e01257eebf566b9752269cb88c072d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264731"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Azure FXT Edge Filer specifikationer

I den här artikeln beskrivs maskinvaruspecifikationerna för azure FXT Edge Filer-maskinvarunoder. I praktiken konfigureras tre eller flera noder tillsammans för att tillhandahålla det klustrade cachesystemet.

## <a name="hardware-specifications"></a>Maskinvaruspecifikationer

| Komponent | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Processorkärnor |  16 | 16 |
| Dram  | 1536 GB | 768 GB |
| Nätverksportar | 6 x 25/10 Gb + 2 x 1 Gb | 6 x 25/10 Gb + 2 x 1 Gb |
| NVMe SSD-kapacitet | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>Körspecifikationer

Systemet har tio drivfack, tillgängliga framifrån. Varje ifylld enhet är märkt till höger med kapacitetsinformation. 

Enhetsnummer skrivs ut på utrymmet mellan enheterna. I Azure FXT Edge Filer är enhet 0 den övre vänstra enheten och enhet 1 är direkt under den.

![foto av ett hårddiskfack i FXT-chassit, som visar drivnummer och kapacitetsetiketter](media/fxt-drives-photo.png)

| Enhetsnummer    |  Användning   |  Specifikationer |
|------------------|--------|-----------------|
| 0, 1             | Operativsystem     | 480 GB SATA SSD |
| 2, 3, 4, 5, 6, 7, 8, 9 | Data   | FXT 6600: 3,2 TB NVMe SSD <br> FXT 6400: 1,6 TB NVMe SSD |


## <a name="dimensions-and-weight"></a>Mått och vikt

Azure FXT Edge Filer är utformad för att passa i ett standard 19-tums utrustningsställ och är en rackenhet hög (1U). 

<!-- 10x2.5 inches version -->

| Filer dimensioner           |                          |
|-----------------------------|--------------------------|
| Höjd                      | 42,8 mm (1,68 tum)    |
| Bredd (inklusive racköron) | 482,0 mm (18,97 tum)  |
| Bredd - huvudhölje      | 434,0 mm (17,08 tum) |
| Djup - racköron på baksidan av huvudhöljet                   | 733,82 mm (29,61 tum) |
| Djup - racköron längst bak utsprång                 | 772,67 mm (30,42 tum) |
| Djup - rack öron längst fram utsprång, utan bezel | 22,0 mm (0,87 tum)  |
| Djup - rack öron längst fram utsprång, med bezel    | 35,84 mm (1,41 tum) |

| Vikt | |
|-----------------|----------------------|
| Nodvikt (utan förpackning, utan tillbehör) | 18,1 kg |
| Nettovikt (utan förpackning, inklusive tillbehör) | 23,1 kg|
| Bruttovikt (som levereras, inkluderar alla förpackningar) |  29,0 kg |

### <a name="shipping-dimensions"></a>Leveransdimensioner

| Paketdimension | Millimeter | Inches |
|-------------------|-------------|--------|
| Höjd            | 311.2       | 12.25" |
| Bredd             | 642.8       | 25.31" |
| Längd            | 1,051.1     | 41.38" |

## <a name="power-and-thermal-specifications"></a>Effekt- och värmespecifikationer

Det här avsnittet innehåller energiklassificeringar och mått för Azure FXT Edge Filer.

### <a name="nameplate-ratings"></a>Klassificeringar av namnskylt

| Namnskyltsklassificeringar för modeller i FXT 6000-serien |
|----------------|
| 100 - 240V ~    |
| 10A - 5A (X2)  |
| 50/60Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Effekt- och termiska mätningar 

Azure FXT Edge Filer-noder använder fläktar med variabel hastighet, så strömmen beror på temperatur och belastning. Maximala fläkthastigheter kan uppnås vid vissa kombinationer av hög belastning och förhöjda omgivningstemperaturer. 

Dessa diagram ger strömförbrukning och termiska effektmätningar för vanliga spänningsfrekvenskombinationer. 

| FXT 6600 effekt vid rumstemperatur <br />(22° C, 71.6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Spänning (V) | 100 | 120 | 208 | 230 | 240 | 
| Frekvens (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuell (A) | 5.02 | 4.16 |2.40 | 2.20 | 2.16 |
| Skenbar effekt (VA) | 502 | 499 | 499 | 506 | 518|
| Effektfaktor | 0.99 | 0.99 |0.98 | 0.98 | 0.98 |
| Verklig kraft (W) | 497 |494 | 489 | 496 | 508 |
| Termisk avledning (BTU/Hr) |1696 | 1686 | 1669 | 1692 | 1733 |

| FXT 6600 effekt vid maximala fläkthastigheter | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Spänning (V) | 100 |120 | 208 | 230 | 240| 
| Frekvens (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuell (A) | 5.98 | 5.01 | 2.81 | 2.55 | 2.48 |
| Skenbar effekt (VA) | 598 | 601 | 584 | 587 | 595 |
| Effektfaktor | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Verklig kraft (W) | 592 | 595 | 573 | 575 | 583 |
| Termisk avledning (BTU/Hr) | 2020 |2031 | 1954 | 1961 | 1990 |

| FXT 6400 effekt vid rumstemperatur <br />(22° C, 71.6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Spänning (V) | 100 | 120 | 208 | 230 | 240 |
| Frekvens (Hz) |60 | 60 | 60 | 50 | 50 |
| Aktuell (A) | 4,63 | 3.86 | 2.24 | 2.04 | 1.94 |
| Skenbar effekt (VA) | 463 | 463 | 466 | 469 | 466 |
| Effektfaktor | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 | 
| Verklig kraft (W) | 458 | 459 | 457 | 460 | 456 |
| Termisk avledning (BTU/Hr) | 1564 | 1565 | 1558 | 1569 | 1557 |

| FXT 6400 effekt vid maximal fläkthastighet | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Spänning (V) | 100 | 120 | 208 | 230 | 240 |
| Frekvens (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuell (A) | 5.15 | 4.28 | 2.48 | 2.28 | 2.13 |
| Skenbar effekt (VA) | 515 | 514 | 516 | 524 | 511 |
| Effektfaktor | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Verklig kraft (W) | 510 | 508 | 506 | 514 | 501 |
| Termisk avledning (BTU/Hr) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Miljökrav

Det här avsnittet innehåller specifikationer för maskinvarans omgivning.

### <a name="temperature-and-humidity"></a>Temperatur och luftfuktighet

| Miljöattribut   | Räckvidd                   | Ej driftsområde         |
|---------------------------|-----------------------------------|-----------------------------|
| Omgivningstemperaturområde | 10°C till 35°C (50 - 86°F)          | -40°C till 65°C (-40 - 149°F) |
| Omgivnings relativ luftfuktighet | 10% - 80% icke-kondenserande          | 5% - 95% icke-kondenserande     |
| Maximal daggpunkt         | 29°C (84°F)                       | 33°C (91°F)                 |
| Höjd                  | upp till 3048 meter( 10.000 fot), med förbehåll för temperatur de-rating noteras nedan | upp till 12 000 meter |

> [!NOTE] 
> **Höjdtemperatur de-rating:** Den maximala temperaturen sänks med 1°C/300 m över 950 m (3 117 fot).

### <a name="airflow-shock-and-vibration"></a>Luftflöde, stötar och vibrationer 

| Attribut         | Specifikation |
|-------------------|---------------|
| Luftflöde                    | Systemets luftflöde är framifrån och bakåt. Systemet skall användas med en lågtrycksbakavgasinstallation. |
| Chock, drift         | 6 G för 11 millisekunder (testade i 6 riktningar) |
| Chock, icke-operativ     | 71 G för 2 millisekunder (testade i 6 riktningar) |
| Vibrationer, drift     | 0,26 G<sub>RMS</sub> 5 Hz till 350 Hz slumpmässigt         |
| Vibrationer, icke-operativa | 1,88 G<sub>RMS</sub> 10 Hz till 500 Hz i 15 minuter (alla sex sidorna testade)  |

## <a name="safety-regulation-compliance"></a>Efterlevnad av säkerhetsföreskrifter 

Azure FXT Edge Filer följer de angivna reglerna. 

| Kategori       | Specifikation av föreskrifter | 
|----------------|--------------------------|
| Allmän säkerhet | EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 <br>EN 62311:2008 | 
| Emc            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (klass D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energi         | Kommissionens förordning (EU) nr. 617/2013  |
| Rohs           |    EN 50581:2012   |