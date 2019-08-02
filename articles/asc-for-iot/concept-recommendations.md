---
title: Förstå Azure Security Center för IoT Security-rekommendationer | Microsoft Docs
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
ms.openlocfilehash: 6f1efa365b4f3a1ab65cc89c84af57c88325e9d2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597281"
---
# <a name="security-recommendations"></a>Säkerhetsrekommendationer

Azure Security Center for IoT söker igenom dina Azure-resurser och IoT-enheter och ger säkerhets rekommendationer för att minska risken för attacker. Säkerhets rekommendationer är åtgärds bara och syftar till att hjälpa kunder att följa rekommenderade säkerhets metoder.

I den här artikeln hittar du en lista över rekommendationer som kan utlösas på dina IoT Hub-och/eller IoT-enheter.

## <a name="recommendations-for-iot-devices"></a>Rekommendationer för IoT-enheter

Enhets rekommendationer ger insikter och förslag för att förbättra position för enhets säkerhet. 

| severity | Namn                                                      | Datakälla | Beskrivning                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Medel   | Öppna portar på enheten                                      | agent       | En lyssnande slut punkt hittades på enheten.                                                                                                                                                        |
| Medel   | Princip för att tillåta brand vägg hittas i en av kedjorna. | agent       | Tillåten brand Väggs princip hittades (indata/utdata). Brand Väggs principen bör neka all trafik som standard och definiera regler för att tillåta nödvändig kommunikation till/från enheten.                               |
| Medel   | En tillåtande brand Väggs regel i indatamängden hittades     | agent       | En regel i brand väggen har hittats som innehåller ett tillåtet mönster för en mängd olika IP-adresser eller portar.                                                                                    |
| Medel   | En tillåtande brand Väggs regel i utgående kedja hittades    | agent       | En regel i brand väggen har hittats som innehåller ett tillåtet mönster för en mängd olika IP-adresser eller portar.                                                                                   |
| Medel   | Det gick inte att verifiera operativ systemets bas linje           | agent       | Enheten är inte kompatibel med [CIS Linux](https://www.cisecurity.org/cis-benchmarks/)-benchmarks.                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Drift rekommendationer för IoT-enheter

Drift rekommendationer ger insikter och förslag för att förbättra konfigurationen av säkerhets agenten.

| severity | Namn                                    | Datakälla | Beskrivning                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Låg      | Agent skickar meddelanden som inte används          | agent       | 10% eller flera säkerhets meddelanden var mindre än 4 KB under de senaste 24 timmarna.  |
| Låg      | Säkerhet, dubbel konfiguration inte optimal | agent       | En dubbel säkerhets konfiguration är inte optimalt.                                        |
| Låg      | Konflikt vid dubbel konfiguration av säkerhet    | agent       | Konflikter identifierades i den dubbla säkerheten. |                          |


## <a name="recommendations-for-iot-hub"></a>Rekommendationer för IoT Hub

Rekommendations aviseringar ger insikter och förslag på åtgärder för att förbättra position säkerhets miljö.  

| severity | Namn                                                     | Datakälla | Beskrivning                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hög     | Identiska autentiseringsuppgifter för autentisering används av flera enheter | IoT-hubb     | IoT Hub autentiseringsuppgifter för autentisering används av flera enheter. Detta kan tyda på att en Illegitimate-enhet personifierar en legitim enhet. Dubblett av autentiseringsuppgifter ökar risken för personifiering av enheter av en skadlig aktör. |
| Medel   | Standard princip för IP-filter ska nekas                  | IoT-hubb     | IP-filterlistan måste ha definierade regler för tillåten trafik och ska som standard neka all annan trafik som standard.                                                                                                     |
| Medel   | IP-filterlistan innehåller stora IP-intervall                   | IoT-hubb     | En Tillåt källa för IP-filterlistan är för stor. Överskrivna regler kan exponera IoT-hubben för skadliga aktörer.                                                                                       |
| Låg      | Aktivera diagnostikloggar i IoT Hub                       | IoT-hubb     | Aktivera loggar och behåll dem i upp till ett år. Om du behåller loggarna kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras.                                       |
|

## <a name="next-steps"></a>Nästa steg

- [Översikt över](overview.md) Azure Security Center för IoT-tjänsten
- Lär dig hur du [kommer åt dina säkerhets data](how-to-security-data-access.md)
- Lär dig mer om att [undersöka en enhet](how-to-investigate-device.md)