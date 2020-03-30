---
title: Tekniska specifikationer och efterlevnad av Microsoft Azure Data Box Edge| Microsoft-dokument
description: Lär dig mer om de tekniska specifikationerna och efterlevnaden för din Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: b646ee9b727d5adf4ec1c8b5c769b3d8f5c0fc1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252037"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Tekniska specifikationer för Azure Data Box Edge

Maskinvarukomponenterna i Microsoft Azure Data Box Edge-enheten följer de tekniska specifikationer och föreskrifter som beskrivs i den här artikeln. De tekniska specifikationerna beskriver nätaggregaten, lagringskapacitet, kapslingar och miljönormer. 

## <a name="compute-memory-specifications"></a>Beräkning, minnesspecifikationer

Data Box Edge-enheten har följande specifikationer för beräkning och minne:

| Specifikation           | Värde                  |
|-------------------------|----------------------------|
| Processor    | 2 X 10 kärnprocessor                     |
| Minne              | 128 GB RAM-minne                  |


## <a name="fpga-specifications"></a>FPGA-specifikationer

En FPGA (Field Programmemable Gate Array) ingår i alla Data Box Edge-enheter som aktiverar scenarier för maskininlärning (ML). 

| Specifikation           | Värde                  |
|-------------------------|----------------------------|
| FPGA (på andra sätt)   | Intel Arria 10 <br> DNN-modeller (Available Deep Neural Network) är desamma som de som [stöds av moln-FPGA-instanser](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure).| 


## <a name="power-supply-unit-specifications"></a>Specifikationer för strömförsörjningsenhet

Data Box Edge-enheten har två 100-240 V nätaggregat med högpresterande fläktar. De två nätaggregaten tillhandahåller en redundant strömförsörjningskonfiguration. Om ett nätagg misslyckas fortsätter enheten att fungera normalt på det andra nätagget tills den felaktiga modulen byts ut. I följande tabell visas de tekniska specifikationerna för de nätaggregat som gäller.

| Specifikation           | 750 W nätagg                  |
|-------------------------|----------------------------|
| Maximal uteffekt    | 750 W                     |
| Frequency               | 50/60 Hz                   |
| Val av spänningsområde | Automatisk intervall: 100-240 V AC |
| Hot pluggable           | Ja                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="network-interface-specifications"></a>Specifikationer för nätverksgränssnitt

You Data Box Edge-enheten har 6 nätverksgränssnitt, PORT1- PORT6.

| Specifikation           | Beskrivning                 |
|-------------------------|----------------------------|
|  Nätverksgränssnitt    | 2 x 1 GbE-gränssnitt – 1 för hantering som inte kan konfigureras av användaren används för installationen. Det andra gränssnittet kan konfigureras av användaren, kan användas för dataöverföring och är DHCP som standard. <br>2 x 25 GbE-gränssnitt – dessa kan även fungera som 10 GbE-gränssnitt. Dessa datagränssnitt kan konfigureras av användaren som DHCP (standard) eller statiska. <br> 2 x 25 GbE-gränssnitt – dessa datagränssnitt kan konfigureras av användaren som DHCP (standard) eller statiska.                  |

## <a name="storage-specifications"></a>Specifikationer för lagring

Data Box Edge-enheterna har 9 X 2,5-tums NVMe SSD-enheter, var och en med en kapacitet på 1,6 TB. Av dessa SSD är 1 en operativsystemdisk och de övriga 8 är datadiskar. Den totala användbara kapaciteten för enheten är ungefär 12,5 TB. Följande tabell innehåller information om enhetens lagringskapacitet.

|     Specifikation                          |     Värde             |
|--------------------------------------------|-----------------------|
|    Antal SSD-enheter (Solid State Drives)     |    8                  |
|    Enkel SSD-kapacitet                     |    1,6 TB             |
|    Total kapacitet                          |    12,8 TB            |
|    Total användbar kapacitet*                  |    ~ 12,5 TB            |

**En del utrymme är reserverat för internt bruk.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Höljesdimensioner och viktspecifikationer

I följande tabeller visas de olika höljesspecifikationerna för dimensioner och vikt.

### <a name="enclosure-dimensions"></a>Höljesdimensioner

I följande tabell visas höljets dimensioner i millimeter och tum.

|     Inhägnad     |     Millimeter     |     Inches     |
|-------------------|---------------------|----------------|
|    Höjd         |    44.45            |    1.75"          |
|    Bredd          |    434.1           |    17.09"          |
|    Längd          |    740.4           |    29.15"          |

I följande tabell visas dimensionerna för leveranspaketet i millimeter och tum.

|     Paket     |     Millimeter     |     Inches     |
|-------------------|---------------------|----------------|
|    Höjd         |    311.2            |    12.25"          |
|    Bredd          |    642.8          |    25.31"          |
|    Längd          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>Kapslingsvikt

Enhetspaketet väger 66 lbs. och kräver två personer att hantera det. Enhetens vikt beror på höljets konfiguration.

|     Inhägnad                                 |     Vikt          |
|-----------------------------------------------|---------------------|
|    Totalvikt inklusive förpackning       |    £ 61.          |
|    Enhetens vikt                       |    £ 35.          |

## <a name="enclosure-environment-specifications"></a>Specifikationer för kapslingsmiljö

I det här avsnittet visas specifikationerna för kapslingsmiljön, till exempel temperatur, luftfuktighet och höjd.

### <a name="temperature-and-humidity"></a>Temperatur och luftfuktighet

|     Inhägnad         |     Omgivningstemperaturområde     |     Omgivnings relativ luftfuktighet     |     Maximal daggpunkt     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operativa        |    10°C - 35°C (50°F - 86°F)         |    10% - 80% icke-kondensering.         |    29°C (84°F)            |
|    Icke-operativa    |    -40°C till 65°C (-40°F - 149°F)     |    5% - 95% icke-kondenserande.          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Luftflöde, höjd, stötar, vibrationer, orientering, säkerhet och EMC

|     Inhägnad                           |     Operativa specifikationer                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Luftflöde                              |    Systemets luftflöde är framifrån och bakåt. Systemet skall manövreras med lågtrycksinstallation bakavgas. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maximal höjd, drift        |    3048 meter (10.000 fot) med maximal driftstemperatur de-rated bestäms av [drift temperatur de-rating specifikationer](#operating-temperature-de-rating-specifications).                                                                                |
|    Högsta höjd, ej i drift    |    12 000 meter                                                                                                                                                                                         |
|    Chock, drift                   |    6 G för 11 millisekunder i 6 riktningar                                                                                                                                                                         |
|    Chock, icke-operativ               |    71 G för 2 millisekunder i 6 riktningar                                                                                                                                                                           |
|    Vibrationer, drift               |    0,26 G<sub>RMS</sub> 5 Hz till 350 Hz slumpmässigt                                                                                                                                                                                     |
|    Vibrationer, icke-operativa           |    1,88 G<sub>RMS</sub> 10 Hz till 500 Hz i 15 minuter (alla sex sidorna testas.)                                                                                                                                                  |
|    Orientering och montering             |    19-tums rackfäste                                                                                                                                                                                        |
|    Säkerhet och godkännanden                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    Emc                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (klass D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energi             |    Kommissionens förordning (EU) nr. 617/2013                                                                                                                                                                                        |
|    Rohs           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Specifikationer för de-klassning av driftstemperatur

|     De-rating för driftstemperatur     |     Omgivningstemperaturområde                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Upp till 35°C (95°F)                       |    Den maximala temperaturen sänks med 1°C/300 m över 950 m (3 117 fot).    |
|    35°C till 40°C (95°F till 104°F)            |    Den maximala temperaturen sänks med 1°C/175 m över 950 m (3 117 fot).    |
|    40°C till 45°C (104°F till 113°F)           |    Den maximala temperaturen sänks med 1°C/125 m över 950 m (3 117 fot).    |


## <a name="next-steps"></a>Nästa steg

- [Distribuera din Azure Data Box Edge](data-box-edge-deploy-prep.md)
