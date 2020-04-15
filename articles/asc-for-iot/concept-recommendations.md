---
title: Säkerhetsrekommendationer
description: Lär dig mer om begreppet säkerhetsrekommendationer och hur de används i Azure Security Center för IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: 213595ac69efc90ec855b2891641e1f00bd1ba92
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311551"
---
# <a name="security-recommendations"></a>Säkerhetsrekommendationer

Azure Security Center for IoT söker igenom dina Azure-resurser och IoT-enheter och ger säkerhetsrekommendationer för att minska din attackyta.
Säkerhetsrekommendationer är genomförbara och syftar till att hjälpa kunderna att följa bästa säkerhetspraxis.

I den här artikeln hittar du en lista med rekommendationer som kan utlösas på din IoT Hub och /eller IoT-enheter.

## <a name="recommendations-for-iot-devices"></a>Rekommendationer för IoT-enheter

Enhetsrekommendationer ger insikter och förslag för att förbättra enhetens säkerhetsposition.

| Severity | Namn                                                      | Datakälla | Beskrivning                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Medel   | Öppna portar på enheten                                      | Agent       | En lyssningsslutpunkt hittades på enheten .                                                                                                                                                        |
| Medel   | Tillåtande brandväggspolicy som finns i en av kedjorna. | Agent       | Tillåten brandväggsprincip hittades (INPUT/OUTPUT). Brandväggsprincipen bör neka all trafik som standard och definiera regler för att möjliggöra nödvändig kommunikation till/från enheten.                               |
| Medel   | Tillåtande brandväggsregel i indatakedjan hittades     | Agent       | En regel i brandväggen har hittats som innehåller ett tillåtande mönster för ett brett spektrum av IP-adresser eller portar.                                                                                    |
| Medel   | Tillåtande brandväggsregel i utdatakedjan hittades    | Agent       | En regel i brandväggen har hittats som innehåller ett tillåtande mönster för ett brett spektrum av IP-adresser eller portar.                                                                                   |
| Medel   | Valideringen av driftsystemets baslinje misslyckades           | Agent       | Enheten uppfyller inte [CIS Linux-riktmärken.](https://www.cisecurity.org/cis-benchmarks/)                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Driftsrekommendationer för IoT-enheter

Driftrekommendationer ger insikter och förslag för att förbättra konfigurationen av säkerhetsagenter.

| Severity | Namn                                    | Datakälla | Beskrivning                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Låg      | Agenten skickar outnyttjade meddelanden          | Agent       | 10 % eller mer av säkerhetsmeddelandena var mindre än 4 kB under de senaste 24 timmarna.  |
| Låg      | Konfiguration av säkerhetstvilling inte optimal | Agent       | Konfiguration av säkerhetstvilling är inte optimal.                                        |
| Låg      | Konfigurationskonflikt för säkerhetstvilling    | Agent       | Konflikter identifierades i säkerhets twin konfigurationen. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Rekommendationer för IoT Hub

Rekommendationsvarningar ger insikt och förslag på åtgärder för att förbättra säkerhetshållningen i din miljö.

| Severity | Namn                                                     | Datakälla | Beskrivning                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hög     | Identiska autentiseringsuppgifter som används av flera enheter | IoT Hub     | Autentiseringsuppgifter för IoT Hub används av flera enheter. Detta kan tyda på att en illegitim enhet utger sig för att vara en legitim enhet. Användning av dubblettautentiseringsuppgifter ökar risken för att en skadlig aktör personifierar enheten. |
| Medel   | Standard-IP-filterprincipen bör nekas                  | IoT Hub     | IP-filterkonfiguration bör ha regler definierade för tillåten trafik och bör som standard neka all annan trafik som standard.                                                                                                     |
| Medel   | IP-filterregeln innehåller stort IP-intervall                   | IoT Hub     | IP-filterregelkällans IP-intervall är för stort. Alltför tillåtande regler kan exponera din IoT-hubb för skadliga aktörer.                                                                                       |
| Låg      | Aktivera diagnostikloggar i IoT Hub                       | IoT Hub     | Aktivera loggar och behåll dem i upp till ett år. Om du behåller loggar kan du återskapa aktivitetsspår i undersökningssyfte när en säkerhetsincident inträffar eller nätverket äventyras.                                       |
|

## <a name="next-steps"></a>Nästa steg

- Översikt över Azure Security [Overview](overview.md) Center för IoT-tjänst
- Läs om hur du [kommer åt dina säkerhetsdata](how-to-security-data-access.md)
- Läs mer om [att undersöka en enhet](how-to-investigate-device.md)
