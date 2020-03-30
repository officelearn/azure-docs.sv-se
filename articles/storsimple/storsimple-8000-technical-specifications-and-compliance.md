---
title: Tekniska specifikationer för StorSimple | Microsoft-dokument
description: Beskriver de tekniska specifikationerna och efterlevnadsinformationen för regelstandarder för StorSimples maskinvarukomponenter.
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
ms.openlocfilehash: 061194422a8c1bc449dbef0c4f04bb8e1db10dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965295"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Tekniska specifikationer och överensstämmelse för StorSimple-enheten

## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Maskinvarukomponenterna på din Microsoft Azure StorSimple-enhet följer de tekniska specifikationer och föreskrifter som beskrivs i den här artikeln. De tekniska specifikationerna beskriver pcm-moduler (Power and Cooling Modules), diskenheter, lagringskapacitet och höljen. Informationen om efterlevnad omfattar bland annat internationella standarder, säkerhet och utsläpp samt kablage.

## <a name="power-and-cooling-module-specifications"></a>Specifikationer för kraft- och kylmodul

StorSimple-enheten har två 100-240 V dubbla fläktar, SBB-kompatibla Power Cooling Modules (PCMs). Detta ger en redundant strömförsörjningskonfiguration. Om en PCM misslyckas fortsätter enheten att fungera normalt på den andra PCM:n tills den felaktiga modulen byts ut.

EBOD-höljet använder en 580 W PCM och primärhölje använder en 764 W PCM. The following tables list the technical specifications associated with the PCMs.

| Specifikation | 580 W PCM (EBOD) | 764 W PCM (primär) |
| --- | --- | --- |
| Maximal uteffekt |580 W (580 W) |764 |
| Frequency |50/60 Hz |50/60 Hz |
| Val av spänningsområde |Automatisk räckvidd: 90 – 264 V AC, 47/63 Hz |Automatisk räckvidd: 90- 264 V AC, 47/63 Hz |
| Maximal inborrström |20 A |20 A |
| Korrigering av effektfaktor |>95% nominell ingångsspänning |>95% nominell ingångsspänning |
| Övertoner |Möter EN61000-3-2 |Möter EN61000-3-2 |
| Resultat |5V Standby \@ spänning 2,0 A |5V Standby \@ spänning 2,7 A |
| +5V \@ 42 A |+5V \@ 40 A | |
| +12V \@ 38 A |+12V \@ 38 A | |
| Hot pluggable |Ja |Ja |
| Brytare och lysdioder |AC ON/OFF-brytare och fyra statusindikatorlysdioder |AC ON/OFF-brytare och sex statusindikatorlysdioder |
| Kapsling kylning |Axialkylfläktar med variabel fläkthastighetskontroll |Axialkylfläktar med variabel fläkthastighetskontroll |

## <a name="power-consumption-statistics"></a>Statistik över energiförbrukning

I följande tabell visas de typiska energiförbrukningsdata (faktiska värden kan variera från de publicerade) för de olika modellerna av StorSimple-enhet.

| Villkor | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Fläktar långsamma, kör inaktiv |1.45 A |0,31 kW |1057.76 BTU/timme |3.19 A |0,34 kW |1160.13 BTU/timme |
|  Fläktar långsamma, driver åtkomst |1.54 A |0,33 kW |1126,01 BTU/timme |3.27 A |0,36 kW |1228,37 BTU/timme |
|  Fläktar snabbt, enheter inaktiva, två nätaggregat drivs |2.14 A |0,49 kW |1671,95 BTU/timme |4.99 A |0,54 kW |1842.56 BTU/timme |
|  Fläktar snabbt, kör inaktiv, en PSU drivs en inaktiv |2.05 A |0,48 kW |1637,83 BTU/timme |4.58 A |0,50 kW |1706,07 BTU/timme |
|  Fläktar snabbt, enheter som kommer åt, två nätaggregat drivs |2.26 A |0,51 kW |1740.19 BTU/hr |4.95 A |0,54 kW |1842.56 BTU/timme |
|  Fläktar snabbt, enheter tillgång, en PSU drivs en inaktiv |2.14 A |0,49 kW |1671,95 BTU/timme |4.81 A |0,53 kW |1808,44 BTU/timme |

## <a name="disk-drive-specifications"></a>Specifikationer för diskenhet

Din StorSimple-enhet stöder upp till 12 3,5-tums formfaktor Serial Attached SCSI-diskenheter (SAS). De faktiska enheterna kan vara en blandning av SSD-enheter (Solid State Drives) eller hårddiskar ( hårddiskar), beroende på produktkonfigurationen. De 12 diskenhetsplatserna finns i en 3-4-konfiguration framför höljet. EBOD-höljet möjliggör ytterligare lagring för ytterligare 12 hårddiskar. Dessa är alltid hårddiskar.

## <a name="storage-specifications"></a>Specifikationer för lagring

StorSimple-enheterna har en blandning av hårddiskar och solid state-enheter för både 8100 och 8600. Den totala användbara kapaciteten för 8100 och 8600 är ungefär 15 TB respektive 38 TB. Följande tabell dokumenterar information om SSD, hårddisk och molnkapacitet i samband med StorSimple-lösningskapaciteten.

| Enhetsmodell / Kapacitet | 8100 | 8600 |
| --- | --- | --- |
| Antal hårddiskar |8 |19 |
| Antal SSD-enheter (Solid State Drives) |4 |5 |
| En hårddiskkapacitet |4 TB |4 TB |
| Enkel SSD-kapacitet |400 GB |800 GB |
| Outnyttjad kapacitet |4 TB |4 TB |
| Användbar hårddiskkapacitet |14 TB |36 TB |
| Användbar SSD-kapacitet |800 GB |2 TB |
| Total användbar kapacitet* |~ 15 TB |~ 38 TB |
| Maximal lösningskapacitet (inklusive moln) |200 TB |500 TB |

<sup>* </sup>- *Den totala användbara kapaciteten inkluderar den kapacitet som är tillgänglig för data, metadata och buffertar. Du kan etablera lokalt fästa volymer upp till 8,5 TB på 8100-enheten eller upp till 22,5 TB på den större 8600-enheten. Mer information finns i [StorSimple lokalt fästa volymer](storsimple-8000-local-volume-faq.md).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Höljesdimensioner och viktspecifikationer

I följande tabeller visas de olika höljesspecifikationerna för dimensioner och vikt.

### <a name="enclosure-dimensions"></a>Höljesdimensioner

I följande tabell visas höljets dimensioner i millimeter och tum.

| Inhägnad | Millimeter | Inches |
| --- | --- | --- |
| Höjd |87.9 |3.46 |
| Bredd över monteringsflänsen |483 |19.02 |
| Bredd över höljets brödtext |443 |17.44 |
| Djup från främre montering fläns till änden av kapsling kropp |577 |22.72 |
| Djup från operationspanelen till den yttersta änden av inneslutningen |630.5 |24.82 |
| Djup från montering fläns till längst änden av inneslutningen |603 |23.74 |

### <a name="enclosure-weight"></a>Kapslingsvikt

Beroende på konfigurationen kan en fullt laddad primärhölje väga från 21 till 33 kg och kräver två personer för att hantera den.

| Inhägnad | Vikt |
| --- | --- |
| Maximal vikt (beror på konfigurationen) |30 kg – 33 kg |
| Tom (inga enheter monterade) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Specifikationer för kapslingsmiljö

I det här avsnittet visas specifikationerna för kapslingsmiljön. Temperatur, luftfuktighet, höjd, stötar, vibrationer, orientering, säkerhet och elektromagnetisk kompatibilitet (EMC) ingår i denna kategori.

### <a name="temperature-and-humidity"></a>Temperatur och luftfuktighet

| Inhägnad | Omgivningstemperaturområde | Omgivnings relativ luftfuktighet | Maximal våt lampa |
| --- | --- | --- | --- |
| Operativa |5°C - 35°C(41°F - 95°F) |20% - 80% icke-kondenserande- |28°C (82°F) |
| Icke-operativa |-40°C - 70°C(40°F - 158°F) |5% - 100% icke-kondenserande |29°C (84°F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Luftflöde, höjd, stötar, vibrationer, orientering, säkerhet och EMC

| Inhägnad | Operativa specifikationer |
| --- | --- |
| Luftflöde |Systemets luftflöde är framifrån och bakåt. Systemet skall manövreras med lågtrycksinstallation bakavgas. Mottryck som skapas av rackdörrar och hinder bör inte överstiga 5 pascal (0,5 mm vattenmätare). |
| Höjd, drift |-30 meter till 3045 meter (-100 fot till 10.000 fot) med maximal driftstemperatur de-rated av 5 ° C över 7000 fot. |
| Höjd, icke-operativ |-305 meter till 12.192 meter (-1.000 fot till 40.000 fot) |
| Chock, drift |5g 10 ms 1/2 sinus |
| Chock, icke-operativ |30g 10 ms 1/2 sinus |
| Vibrationer, drift |0.21g RMS 5-500 Hz slumpmässigt |
| Vibrationer, icke-operativa |1.04g RMS 2-200 Hz slumpmässigt |
| Vibrationer, omlokalisering |3g 2-200 Hz sinus |
| Orientering och montering |19" rackfäste (2 MKB-enheter) |
| Rack skenor |Så här passar minst 700 mm (31,50 tum) djupställ som överensstämmer med IEC 297 |
| Säkerhet och godkännanden |CE och UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| Emc |EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Internationell efterlevnad av standarder

Din Microsoft Azure StorSimple-enhet uppfyller följande internationella standarder:  

* CE - EN 60950 - 1
* CB rapport till IEC 60950 - 1
* UL och cUL till UL 60950 - 1

## <a name="safety-compliance"></a>Säkerhetsefterlevnad

Din Microsoft Azure StorSimple-enhet uppfyller följande säkerhetsklassificeringar:

* Godkännande av systemprodukttyp: UL, cUL, CE
* Säkerhetsöverensstämmelse: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>EMC-efterlevnad

Din Microsoft Azure StorSimple-enhet uppfyller följande EMC-klassificeringar.

### <a name="emissions"></a>Utsläpp

Enheten är EMC-kompatibel för genomförda och utstrålade utsläppsnivåer.

* Utsläppsgränsnivåer: CFR 47 Del 15B Klass A EN55022 Klass A CISPR-klass A
* Utstrålade utsläppsgränsnivåer: CFR 47 Del 15B Klass A EN55022 Klass A CISPR-klass A

### <a name="harmonics-and-flicker"></a>Övertoner och flimmer

Enheten uppfyller EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Gränsvärden för immunitet

Enheten uppfyller EN55024.

## <a name="ac-power-cord-compliance"></a>Nätsladdens kompatibilitet

Kontakten och den kompletta nätsladden skall uppfylla de standarder som gäller för det land/den region där produkten används, och de måste ha säkerhetsgodkännanden som är godtagbara i det landet/regionen. I följande tabeller finns standarder för USA och Europa.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Nätsladdar - USA (måste nrtl listas)

| Komponent | Specifikation |
| --- | --- |
| Typ av sladd |SV eller SVT, minst 18 AWG, 3 ledare, 2,0 meter maxlängd |
| Plug |NEMA 5-15P jordningsplugg med 120 V, 10 A; eller IEC 320 C14, 250 V, 10 A |
| Socket |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>Nätsladdar - Europa

| Komponent | Specifikation |
| --- | --- |
| Typ av sladd |Harmoniserad, H05-VVF-3G1.0 |
| Socket |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Nätverkskablar som stöds

För 10 GbE-nätverksgränssnitten, DATA 2 och DATA 3, finns med i [listan över nätverkskablar och moduler som stöds](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Nästa steg

Du är nu redo att distribuera en StorSimple-enhet i ditt datacenter. Mer information finns i [Distribuera din lokala enhet](storsimple-8000-deployment-walkthrough-u2.md).

