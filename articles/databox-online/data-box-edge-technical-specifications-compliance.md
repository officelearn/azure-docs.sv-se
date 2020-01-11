---
title: Microsoft Azure Data Box Edge tekniska specifikationer och efterlevnad | Microsoft Docs
description: Läs om de tekniska specifikationerna och efterlevnaden för din Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/26/2019
ms.author: alkohli
ms.openlocfilehash: 9e1a7f7cd2643aae61e60d77ad74f4a08266a977
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863605"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Azure Data Box Edge tekniska specifikationer

Maskin varu komponenterna i din Microsoft Azure Data Box Edge-enhet följer de tekniska specifikationer och regler som beskrivs i den här artikeln. De tekniska specifikationerna beskriver strömförsörjnings enheter (PSUs), lagrings kapacitet, höljen och miljö standarder. 

## <a name="compute-memory-specifications"></a>Beräkning, minnes krav

Den Data Box Edge enheten har följande specifikationer för beräkning och minne:

| Specifikation           | Värde                  |
|-------------------------|----------------------------|
| Processor    | 2 X 10 kärnor                     |
| Minne              | 128 GB RAM-minne                  |


## <a name="fpga-specifications"></a>FPGA-specifikationer

En fält programmerbar grind mat ris (FPGA) ingår på varje Data Box Edge enhet som möjliggör Machine Learning-scenarier (ML). 

| Specifikation           | Värde                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> Tillgängliga DNN-modeller (djup neurala Network) är desamma som de som [stöds av moln FPGA instanser](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure).| 


## <a name="power-supply-unit-specifications"></a>Specifikationer för strömförsörjnings enhet

Data Box Edges enheten har två 100-240 V-enheter för strömförsörjning med höga prestanda (PSUs). De två PSUs ger en redundant energi konfiguration. Om ett PSU Miss lyckas fortsätter enheten att fungera normalt på den andra PSU tills den felaktiga modulen har ersatts. I följande tabell visas de tekniska specifikationerna för PSUs.

| Specifikation           | 750 W PSU                  |
|-------------------------|----------------------------|
| Maximal uteffekt    | 750 W                     |
| Frequency               | 50/60 Hz                   |
| Val av spännings intervall | Automatisk mellan: 100-240 V AC |
| Hot pluggable           | Ja                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>Storage-specifikationer

De Data Box Edge enheterna har 9 X 2,5 "NVMe-SSD, med en kapacitet på 1,6 TB. Av dessa SSD, 1 är en operativ system disk och de andra 8 är data diskar. Den totala användbara kapaciteten för enheten är ungefär 12,5 TB. I följande tabell finns information om enhetens lagrings kapacitet.

|     Specifikation                          |     Värde             |
|--------------------------------------------|-----------------------|
|    Antal solid state-hårddiskar (SSD)     |    8                  |
|    Kapacitet för enskild SSD                     |    1,6 TB             |
|    Total kapacitet                          |    12,8 TB            |
|    Total användbar kapacitet *                  |    ~ 12,5 TB            |

**lite utrymme är reserverat för internt bruk.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Bilagans dimensioner och vikt specifikationer

I följande tabeller visas de olika specifikationerna för inne slutning för dimensioner och vikt.

### <a name="enclosure-dimensions"></a>Omslutnings dimensioner

I följande tabell visas storleken på höljet i millimeter och tum.

|     Hölje     |     Millimeter     |     Mm     |
|-------------------|---------------------|----------------|
|    Höjd         |    44,45            |    1,75 "          |
|    Bredd          |    434,1           |    17,09 "          |
|    Längd          |    740,4           |    29,15 "          |

I följande tabell visas måtten för leverans paketet i millimeter och tum.

|     Paket     |     Millimeter     |     Mm     |
|-------------------|---------------------|----------------|
|    Höjd         |    311,2            |    12,25 "          |
|    Bredd          |    642,8          |    25,31 "          |
|    Längd          |   1 051,1          |    41,38 "          |

### <a name="enclosure-weight"></a>Kabinett vikt

Enhets paketet väger 66 kg. och kräver två personer för att hantera det. Enhetens vikt beror på konfigurationen av kabinett filen.

|     Hölje                                 |     Vikt          |
|-----------------------------------------------|---------------------|
|    Total vikt inklusive förpackningen       |    61 kg.          |
|    Enhetens vikt                       |    35 kg.          |

## <a name="enclosure-environment-specifications"></a>Specifikationer för kabinett miljö

I det här avsnittet visas de specifikationer som är relaterade till inne slutnings miljön, till exempel temperatur, fuktighet och höjd.

### <a name="temperature-and-humidity"></a>Temperatur och fuktighet

|     Hölje         |     Temperatur intervall för omgivning     |     Omgivande relativ fuktighet     |     Maximal dagg punkt     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Verksamhetsrelaterade        |    10 ° C – 35 ° C (50 °F-86 °F)         |    10% – 80% icke-kondenserande.         |    29 ° C (84 °F)            |
|    Fungerar inte    |    – 40 ° c till 65 ° c (-40 °F-149 °F)     |    5%-95% icke-kondenserande.          |    33 ° C (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Luft flöde, höjd, stöt, vibration, orientering, säkerhet och EMC

|     Hölje                           |     Operativa specifikationer                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Luft flöde                              |    Systemets luft flöde är framifrån och bak. Systemet måste köras med en installation med låg belastning. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maximal höjd, drift        |    3048 meter (10 000 meter) med maximal drift temperatur som bedöms enligt [specifikationerna för drifts temperatur avklassificering](#operating-temperature-de-rating-specifications).                                                                                |
|    Maximal höjd, ej fungerande    |    12 000 meter (39 370 fot)                                                                                                                                                                                         |
|    Stöt, drift                   |    6 G i 11 millisekunder med 6 orienteringar                                                                                                                                                                         |
|    Stöt, ej fungerande               |    71 G i 2 millisekunder med 6 orienteringar                                                                                                                                                                           |
|    Vibrationer, drift               |    0,26 G<sub>RMS</sub> 5 Hz till 350 Hz slumpmässig                                                                                                                                                                                     |
|    Vibrationer, ej drift           |    1,88 G<sub>RMS</sub> 10 hz till 500 Hz i 15 minuter (alla sex sidor har testats)                                                                                                                                                  |
|    Orientering och montering             |    19 "rack montering                                                                                                                                                                                        |
|    Säkerhet och godkännanden                 |    EN 60950-1:2006 + a1:2010 + a2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ed2 + a1:2009 + a2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (klass D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energi             |    Kommissionens förordning (EU) nr 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Specifikationer för drifts temperatur avklassificering

|     Värdering av drifts temperatur     |     Temperatur intervall för omgivning                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Upp till 35 ° c (95 °F)                       |    Maximal temperatur minskas med 1 ° c/300 m (1 °F/547 ft) över 950 m (3 117 ft).    |
|    35 ° c till 40 ° c (95 °F till 104 °F)            |    Maximal temperatur minskas med 1 ° c/175 m (1 °F/319 ft) över 950 m (3 117 ft).    |
|    40 ° c till 45 ° c (104 °F till 113 °F)           |    Maximal temperatur minskas med 1 ° c/125 m (1 °F/228 ft) över 950 m (3 117 ft).    |


## <a name="next-steps"></a>Nästa steg

- [Distribuera Azure Data Box Edge](data-box-edge-deploy-prep.md)
