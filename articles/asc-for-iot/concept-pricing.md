---
title: Prissättning och associerade kostnader
description: Lär dig mer om kostnaderna som är associerade med Azure Security Center för IoT och hur du styr dem.
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
ms.openlocfilehash: 98afd1eb1f8a73c2d6dd3f293a07cdb70780d238
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004774"
---
# <a name="pricing-and-associated-costs"></a>Prissättning och associerade kostnader

Den här artikeln beskriver Azure Security Center för IoT-prismodellen, sammanfattar alla associerade kostnader och förklarar hur du hanterar dem.

## <a name="pricing"></a>Prissättning

Azure Security Center för IoT-prismodellen består av två delar och faktureras när en IoT Hub [aktive ras](quickstart-onboard-iot-hub.md) i Azure Security Center för IoT:

- Kostnad per enhet – inbyggda säkerhetsfunktioner baserat på analys av IoT Hub loggar.

- Kostnad per meddelande – utökade säkerhetsfunktioner baserat på säkerhets meddelanden från IoT Edge eller löv enheter.

Mer information finns i [Security Center prissättning](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Tillhör ande kostnader

Azure Security Center for IoT har associerade kostnader som inte ingår i direkt prissättningen:

- Log Analytics lagrings kostnader

Du kan minska de associerade kostnaderna genom att väljer ut ur vissa lösnings funktioner. Avanmäla dig genom att ändra inställningarna.

Ändra inställningarna:

1. Öppna IoT Hub.

1. Klicka på **Översikt**under **säkerhet**.

1. Klicka på **Inställningar**.

Följande tabell innehåller en sammanfattning av de associerade kostnaderna och konsekvenserna av varje alternativ.

| Alternativ | Användning | Kommentar |
| --- | --- | --- |
| **Log Analytics lagring** |  |
| Enhets rekommendation och aviseringar| Säkerhets rekommendationer och aviseringar som genererats av tjänsten | Inte valfritt |
| Rå säkerhets data| Rå säkerhets data från IoT-enheter som samlas in av säkerhets agenter | Inaktivera _säkerhets händelser för lagring av rå enheter_ |
|

>[!Important]
> Väljer out har allvarliga följder för att Azure Security Center för tillgänglighet för IoT-säkerhetsfunktioner.

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
