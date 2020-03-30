---
title: Förstå Azure Security Center för IoT-kostnader| Microsoft-dokument
description: Lär dig mer om kostnaderna för Azure Security Center för IoT och hur du kontrollerar dem.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2019
ms.author: mlottner
ms.openlocfilehash: dc9dcbfd00b5205fa5c66e334b30c76d549d8a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71348516"
---
# <a name="pricing-and-associated-costs"></a>Prissättning och associerade kostnader

I den här artikeln beskrivs Azure Security Center för IoT-prismodell, alla tillhörande kostnader sammanfattas och hur du hanterar dem.

## <a name="pricing"></a>Prissättning

Azure Security Center for IoT-prismodellen består av två delar och faktureras när en IoT-hubb är [aktiverad](quickstart-onboard-iot-hub.md) i Azure Security Center för IoT:

- Kostnad per enhet - inbyggda säkerhetsfunktioner baserat på analys av IoT Hub-loggar.

- Kostnad per meddelande – förbättrade säkerhetsfunktioner baserat på säkerhetsmeddelanden från IoT Edge- eller lövenheter.


Mer information finns i [Security Center-priser](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Tillhörande kostnader

Azure Security Center för IoT har associerade kostnader, som inte ingår i den direkta prissättningen:


- Lagringskostnader för Logganalys

Du kan minska tillhörande kostnader genom att välja bort vissa lösningsfunktioner. Välj bort genom att ändra dina inställningar.

Så här ändrar du inställningarna:

1. Öppna IoT Hub.

2. Klicka på **Översikt**under **Säkerhet**.

3. Klicka på **Inställningar**.

I följande tabell finns en sammanfattning av tillhörande kostnader och konsekvenser för varje alternativ.

|     | Användning | Kommentar |
| --- | --- | --- |
| **Lagring av logganalys** |  |
| Enhetsrekommendation och varningar| Säkerhet rekommendation och varningar som genereras av tjänsten | Inte valfritt |
| Råa säkerhetsdata| Råsäkerhetsdata från IoT-enheter, som samlats in av säkerhetsagenter | Inaktivera _säkerhetshändelser för lagra rå enhet_ |
|

>[!Important]
> Att välja bort har allvarliga konsekvenser för Azure Security Center för IoT-säkerhetsfunktionstillgänglighet. 
  
| Välj bort | Effekter |
| --- | --- |
| _Insamling av dubbla metadata_ | Inaktivera [anpassade aviseringar](quickstart-create-custom-alerts.md) |
| | Inaktivera rekommendationer för IoT Edge-manifest |
| | Inaktivera enhetsidentitetsbaserade rekommendationer och aviseringar |
| _Lagra säkerhetshändelser för rå enhet_ | Information om baslinjerekommendationer för enhetsoperativsystem är inte tillgängliga |
| | Information om [larm-](concept-security-alerts.md) och [rekommendationsundersökningar](concept-recommendations.md) finns inte tillgängliga |
|


## <a name="see-also"></a>Se även

- Få tillgång till [rådata](how-to-security-data-access.md)
- [Undersöka en enhet](how-to-investigate-device.md)
- Förstå och utforska [säkerhetsrekommendationer](concept-recommendations.md)
- Förstå och utforska [säkerhetsvarningar](concept-security-alerts.md)
