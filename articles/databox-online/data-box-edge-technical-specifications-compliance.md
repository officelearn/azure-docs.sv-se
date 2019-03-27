---
title: Microsoft Azure Data Box Edge tekniska specifikationer och efterlevnad | Microsoft Docs
description: Lär dig mer om de tekniska specifikationerna och efterlevnad för Azure Data Box-Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 52fb32a8b34c62fe94ab35e2c051d996ab8bef10
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449192"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Azure Data Box Edge tekniska specifikationer

Maskinvarukomponenter för Microsoft Azure Data Box Edge-enheten följa de tekniska specifikationerna och regelverk som beskrivs i den här artikeln. De tekniska specifikationerna beskrivs Power anger enheter (PSUs), lagringskapacitet, bilagor och standarder för miljön. 

## <a name="power-supply-unit-specifications"></a>Power-strömförsörjning enhet specifikationer

Data Box Edge-enhet har två 100-240 V Power anger enheter (PSUs) med höga prestanda. Två PSUs tillhandahåller en redundant kraft-konfigurationen. Om en PSU misslyckas, fortsätter enheten att fungera normalt på den andra PSU förrän modulen misslyckade ersätts. I följande tabell visas de tekniska specifikationerna för PSUs.

| Specifikationen           | 750 W PSU                  |
|-------------------------|----------------------------|
| Högsta uteffekt    | 750 W                     |
| Frekvens               | 50/60 Hz                   |
| Val av tidsintervall spänning | Automatisk sträcker sig: 100-240 V AC |
| Hot plug           | Ja                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>Storage-specifikationer

Data Box Edge-enheter har 10 X 2,5-tums NVMe SSD, var och en med en kapacitet på 1,6 TB. I dessa SSD 2 är operativsystemdiskar och de andra 8 finns datadiskar. Den totala kapaciteten som kan användas för enheten är ungefär 12,5 TB. I följande tabell innehåller information för lagringskapaciteten för enheten.

|     Specifikationen                          |     Värde             |
|--------------------------------------------|-----------------------|
|    Antalet solid-state-hårddiskar (SSD)     |    8                  |
|    Enkel SSD-kapacitet                     |    1,6 TB             |
|    Total kapacitet                          |    12,8 TB            |
|    Totalt antal användbar kapacitet *                  |    ~ 12,5 TB            |

**Utrymme är reserverat för internt bruk.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Hölje dimensioner och vikt specifikationer

I tabellerna nedan listas de olika hölje specifikationerna för dimensioner och vikt.

### <a name="enclosure-dimensions"></a>Dimensioner för hölje

I följande tabell visas måtten på höljet i millimeter och tum.

|     Hölje     |     Millimeter     |     Tum     |
|-------------------|---------------------|----------------|
|    Höjd         |    44.45            |    1.75"          |
|    Bredd          |    434.1           |    17.09"          |
|    Längd          |    740.4           |    29.15"          |

I följande tabell visas dimensioner för leveranser paketet i millimeter och tum.

|     Paket     |     Millimeter     |     Tum     |
|-------------------|---------------------|----------------|
|    Höjd         |    311.2            |    12.25"          |
|    Bredd          |    642.8          |    25.31"          |
|    Längd          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>Vikt för hölje

Enheten paketet väger 66 lbs. och kräver två personer kan hantera den. Vikten för enheten beror på konfigurationen av höljet.

|     Hölje                                 |     Vikt          |
|-----------------------------------------------|---------------------|
|    Total vikt, inklusive paketering       |    61 lbs.          |
|    Vikten för enheten                       |    35 lbs.          |

## <a name="enclosure-environment-specifications"></a>Hölje miljö specifikationer

Det här avsnittet listas de specifikationer som rör hölje-miljö, till exempel temperatur, fuktighet och höjd.

### <a name="temperature-and-humidity"></a>Temperatur och fuktighet

|     Hölje         |     Omgivande temperaturintervall     |     Omgivande relativ fuktighet     |     Den maximala dagg punkt     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    I drift        |    10°C - 35°C (50°F - 86°F)         |    10 – 80% vid icke-förenklas.         |    29°C (84°F)            |
|    Kan inte användas    |    40 ° C till 65 ° C (40 ° F - 149 ° F)     |    5-95% vid icke-förenklas.          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Luftflödet, höjd, chocka, vibrationer, orientering, säkerhet och EMC

|     Hölje                           |     Operativa specifikationer                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Luftflödet                              |    System luftflödet är framifrån och bakåt. Systemet måste köras med en low-pressure, bakre avgaser installation. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maximal höjd operativa        |    3048 mätare (10 000 fot) med högsta operativsystemet temperatur ta bort görs systemets [temperatur ta bort omdöme specifikationer för drift](#operating-temperature-de-rating-specifications).                                                                                |
|    Maximal höjd som inte kan användas    |    12 000 mätare (39,370 fot)                                                                                                                                                                                         |
|    Chocka, operativa                   |    6 G för 11 millisekunder i 6 riktningar                                                                                                                                                                         |
|    Chocka, inte kan användas               |    71 G för 2 millisekunder i 6 riktningar                                                                                                                                                                           |
|    Vibrationer, operativa               |    0,26 G<sub>RMS</sub> 5 Hz till 350 Hz slumpmässiga                                                                                                                                                                                     |
|    Vibrationer, inte kan användas           |    1.88 G<sub>RMS</sub> 10 Hz till 500 Hz i 15 minuter (alla sex sidor testas.)                                                                                                                                                  |
|    Orientering och montering             |    19-tums rackmontera                                                                                                                                                                                        |
|    Säkerhet och godkännanden                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014 / IEC 61000-3-2:2014 (klassen D)   <br>EN 61000-3-3:2013 / IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energi             |    Kommissionen förordning (EU) Nej. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Ta bort klassificering specifikationer temperatur vid drift

|     Operativ temperatur ta bort omdöme     |     Omgivande temperaturintervall                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Upp till 35 oC (95° F)                       |    Högsta temperatur minskar med 1 oC/300 m (1F/547 ft) över 950 m (3,117 ft).    |
|    35 oC 40 oC (95° F till 104° F)            |    Högsta temperatur minskar med 1 oC/175 m (1F/319 ft) över 950 m (3,117 ft).    |
|    40 oC 45 oC (104° F till 113° F)           |    Högsta temperatur minskar med 1 oC/125 m (1F/228 ft) över 950 m (3,117 ft).    |


## <a name="next-steps"></a>Nästa steg

- [Distribuera din Azure Data Box-Edge](data-box-edge-deploy-prep.md)
