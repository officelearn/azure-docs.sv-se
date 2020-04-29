---
title: Säkerhetsrekommendationer
description: Lär dig mer om begreppet säkerhets rekommendationer och hur de används i Azure Security Center för IoT.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311551"
---
# <a name="security-recommendations"></a>Säkerhetsrekommendationer

Azure Security Center for IoT söker igenom dina Azure-resurser och IoT-enheter och ger säkerhets rekommendationer för att minska risken för attacker.
Säkerhets rekommendationer är åtgärds bara och syftar till att hjälpa kunder att följa rekommenderade säkerhets metoder.

I den här artikeln hittar du en lista över rekommendationer som kan utlösas på dina IoT Hub-och/eller IoT-enheter.

## <a name="recommendations-for-iot-devices"></a>Rekommendationer för IoT-enheter

Enhets rekommendationer ger insikter och förslag för att förbättra position för enhets säkerhet.

| Severity | Name                                                      | Datakälla | Beskrivning                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Medel   | Öppna portar på enheten                                      | Agent       | En lyssnande slut punkt hittades på enheten.                                                                                                                                                        |
| Medel   | Princip för att tillåta brand vägg hittas i en av kedjorna. | Agent       | Tillåten brand Väggs princip hittades (indata/utdata). Brand Väggs principen bör neka all trafik som standard och definiera regler för att tillåta nödvändig kommunikation till/från enheten.                               |
| Medel   | En tillåtande brand Väggs regel i indatamängden hittades     | Agent       | En regel i brand väggen har hittats som innehåller ett tillåtet mönster för en mängd olika IP-adresser eller portar.                                                                                    |
| Medel   | En tillåtande brand Väggs regel i utgående kedja hittades    | Agent       | En regel i brand väggen har hittats som innehåller ett tillåtet mönster för en mängd olika IP-adresser eller portar.                                                                                   |
| Medel   | Det gick inte att verifiera operativ systemets bas linje           | Agent       | Enheten är inte kompatibel med [CIS Linux-benchmarks](https://www.cisecurity.org/cis-benchmarks/).                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Drift rekommendationer för IoT-enheter

Drift rekommendationer ger insikter och förslag för att förbättra konfigurationen av säkerhets agenten.

| Severity | Name                                    | Datakälla | Beskrivning                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Låg      | Agent skickar meddelanden som inte används          | Agent       | 10% eller flera säkerhets meddelanden var mindre än 4 KB under de senaste 24 timmarna.  |
| Låg      | Säkerhet, dubbel konfiguration inte optimal | Agent       | En dubbel säkerhets konfiguration är inte optimalt.                                        |
| Låg      | Konflikt vid dubbel konfiguration av säkerhet    | Agent       | Konflikter identifierades i den dubbla säkerheten. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Rekommendationer för IoT Hub

Rekommendations aviseringar ger insikter och förslag på åtgärder för att förbättra position säkerhets miljö.

| Severity | Name                                                     | Datakälla | Beskrivning                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hög     | Identiska autentiseringsuppgifter för autentisering används av flera enheter | IoT Hub     | IoT Hub autentiseringsuppgifter för autentisering används av flera enheter. Detta kan tyda på att en Illegitimate-enhet personifierar en legitim enhet. Dubblett av autentiseringsuppgifter ökar risken för personifiering av enheter av en skadlig aktör. |
| Medel   | Standard princip för IP-filter ska nekas                  | IoT Hub     | IP-filterlistan måste ha definierade regler för tillåten trafik och ska som standard neka all annan trafik som standard.                                                                                                     |
| Medel   | IP-filterlistan innehåller stora IP-intervall                   | IoT Hub     | En Tillåt källa för IP-filterlistan är för stor. Överskrivna regler kan exponera IoT-hubben för skadliga aktörer.                                                                                       |
| Låg      | Aktivera diagnostikloggar i IoT Hub                       | IoT Hub     | Aktivera loggar och behåll dem i upp till ett år. Om du behåller loggarna kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras.                                       |
|

## <a name="next-steps"></a>Nästa steg

- [Översikt över](overview.md) Azure Security Center för IoT-tjänsten
- Lär dig hur du [kommer åt dina säkerhets data](how-to-security-data-access.md)
- Lär dig mer om att [undersöka en enhet](how-to-investigate-device.md)
