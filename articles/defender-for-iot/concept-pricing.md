---
title: Prissättning och associerade kostnader
description: Lär dig mer om kostnaderna som är kopplade till Defender för IoT och hur du styr dem.
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
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: 24ae6c4014948639aa737a0d2d88ec15f98a7cb4
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90941724"
---
# <a name="pricing-and-associated-costs"></a>Prissättning och associerade kostnader

Den här artikeln beskriver Defender för IoT-prismodellen och sammanfattar alla tillhör ande kostnader och förklarar hur du hanterar dem.

## <a name="pricing"></a>Prissättning

Pris modellen för Defender för IoT består av två delar och faktureras när en IoT Hub [aktive ras](quickstart-onboard-iot-hub.md) i Defender för IoT:

- Kostnad per enhet – inbyggda säkerhetsfunktioner baserat på analys av IoT Hub loggar.

- Kostnad per meddelande – utökade säkerhetsfunktioner baserat på säkerhets meddelanden från IoT Edge eller löv enheter.

Mer information finns i [Security Center prissättning](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Tillhör ande kostnader

Defender för IoT har associerade kostnader som inte ingår i direkt prissättningen:

- Log Analytics lagrings kostnader

Du kan minska de associerade kostnaderna genom att väljer ut ur vissa lösnings funktioner. Avanmäla dig genom att ändra inställningarna.

Ändra inställningarna:

1. Öppna IoT Hub.

1. Under **säkerhet**klickar du på **Inställningar**.

1. Klicka på **data insamling**.

Följande tabell innehåller en sammanfattning av de associerade kostnaderna och konsekvenserna av varje alternativ.

| Alternativ | Användning | Kommentar |
| --- | --- | --- |
| **Log Analytics lagring** |  |
| Enhets rekommendation och aviseringar| Säkerhets rekommendationer och aviseringar som genererats av tjänsten | Inte valfritt |
| Rå säkerhets data| Rå säkerhets data från IoT-enheter som samlas in av säkerhets agenter | Inaktivera _säkerhets händelser för lagring av rå enheter_ |
|

>[!Important]
> Väljer out har allvarliga konsekvenser för tillgängligheten i Defender för IoT-säkerhetsfunktionen.

| Avanmäla | Effekter |
| --- | --- |
| _Samling med dubbla metadata_ | Inaktivera [anpassade aviseringar](quickstart-create-custom-alerts.md) |
| | Inaktivera rekommendationer för IoT Edge manifest |
| | Inaktivera rekommendationer och aviseringar för enhets identitet |
| _Lagra säkerhets händelser för RAW-enhet_ | Information om bas linje rekommendationer för enhets operativ system är inte tillgänglig |
| | Information om [varnings](concept-security-alerts.md) -och [rekommendations](concept-recommendations.md) undersökningar är inte tillgängliga |
|

## <a name="see-also"></a>Se även

- Få åtkomst till dina [rå säkerhets data](how-to-security-data-access.md)
- [Undersöka en enhet](how-to-investigate-device.md)
- Förstå och utforska [säkerhets rekommendationer](concept-recommendations.md)
- Förstå och utforska [säkerhets aviseringar](concept-security-alerts.md)
