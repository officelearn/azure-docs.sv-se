---
title: Säkerhetsrekommendationer
description: Lär dig mer om begreppet säkerhets rekommendationer och hur de används i Defender för IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 0eccab6c3d59ad68ddc8f96c3d84c57dc1bbeeca
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90941721"
---
# <a name="security-recommendations"></a>Säkerhetsrekommendationer

Defender för IoT genomsöker dina Azure-resurser och IoT-enheter och ger säkerhets rekommendationer för att minska risken för attacker.
Säkerhets rekommendationer är åtgärds bara och syftar till att hjälpa kunder att följa rekommenderade säkerhets metoder.

I den här artikeln hittar du en lista över rekommendationer som kan utlösas på dina IoT Hub-och/eller IoT-enheter.

## <a name="recommendations-for-iot-devices"></a>Rekommendationer för IoT-enheter

Enhets rekommendationer ger insikter och förslag för att förbättra position för enhets säkerhet.

| Allvarlighetsgrad | Name                                                      | Datakälla | Beskrivning                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Medium   | Öppna portar på enheten                                      | Agent       | En lyssnande slut punkt hittades på enheten.                                                                                                                                                        |
| Medium   | Princip för att tillåta brand vägg hittas i en av kedjorna. | Agent       | Tillåten brand Väggs princip hittades (indata/utdata). Brand Väggs principen bör neka all trafik som standard och definiera regler för att tillåta nödvändig kommunikation till/från enheten.                               |
| Medium   | En tillåtande brand Väggs regel i indatamängden hittades     | Agent       | En regel i brand väggen har hittats som innehåller ett tillåtet mönster för en mängd olika IP-adresser eller portar.                                                                                    |
| Medium   | En tillåtande brand Väggs regel i utgående kedja hittades    | Agent       | En regel i brand väggen har hittats som innehåller ett tillåtet mönster för en mängd olika IP-adresser eller portar.                                                                                   |
| Medium   | Det gick inte att verifiera operativ systemets bas linje           | Agent       | Enheten är inte kompatibel med [CIS Linux-benchmarks](https://www.cisecurity.org/cis-benchmarks/).                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Drift rekommendationer för IoT-enheter

Drift rekommendationer ger insikter och förslag för att förbättra konfigurationen av säkerhets agenten.

| Allvarlighetsgrad | Name                                    | Datakälla | Beskrivning                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Låg      | Agent skickar meddelanden som inte används          | Agent       | 10% eller flera säkerhets meddelanden var mindre än 4 KB under de senaste 24 timmarna.  |
| Låg      | Säkerhet, dubbel konfiguration inte optimal | Agent       | En dubbel säkerhets konfiguration är inte optimalt.                                        |
| Låg      | Konflikt vid dubbel konfiguration av säkerhet    | Agent       | Konflikter identifierades i den dubbla säkerheten. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Rekommendationer för IoT Hub

Rekommendations aviseringar ger insikter och förslag på åtgärder för att förbättra position säkerhets miljö.

| Allvarlighetsgrad | Name                                                     | Datakälla | Beskrivning                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hög     | Identiska autentiseringsuppgifter för autentisering används av flera enheter | IoT Hub     | IoT Hub autentiseringsuppgifter för autentisering används av flera enheter. Detta kan tyda på att en Illegitimate-enhet personifierar en legitim enhet. Dubblett av autentiseringsuppgifter ökar risken för personifiering av enheter av en skadlig aktör. |
| Medium   | Standard princip för IP-filter ska nekas                  | IoT Hub     | IP-filterlistan måste ha definierade regler för tillåten trafik och ska som standard neka all annan trafik som standard.                                                                                                     |
| Medium   | IP-filterlistan innehåller stora IP-intervall                   | IoT Hub     | En Tillåt källa för IP-filterlistan är för stor. Överskrivna regler kan exponera IoT-hubben för skadliga aktörer.                                                                                       |
| Låg      | Aktivera diagnostikloggar i IoT Hub                       | IoT Hub     | Aktivera loggar och behåll dem i upp till ett år. Om du behåller loggarna kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras.                                       |
|

## <a name="next-steps"></a>Nästa steg

- [Översikt över](overview.md) Defender for IoT-tjänsten
- Lär dig hur du [kommer åt dina säkerhets data](how-to-security-data-access.md)
- Lär dig mer om att [undersöka en enhet](how-to-investigate-device.md)
