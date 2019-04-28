---
title: Förstå Azure Security Center för IoT kostar förhandsversion | Microsoft Docs
description: Läs mer om kostnaderna för IoT och hur du kan kontrollera dem med Azure Security Center.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 41b2d012ef2f6dd1ca5f57e04da43eb1a06dafde
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61360157"
---
# <a name="pricing-and-associated-costs"></a>Prissättning och associerade kostnader

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln förklarar Azure Security Center (ASC) IoT prissättningsmodell, sammanfattar alla associerade kostnader och förklarar hur du hanterar dem.

## <a name="pricing"></a>Prissättning

ASC för IoT prismodellen består av två delar och debiteras när en IoT-hubb är [aktiverat](quickstart-onboard-iot-hub.md) i ASC för IoT:

- Kostnad per enhet – inbyggda säkerhetsfunktioner baserat på analysen av loggar för IoT Hub.

- Kostnad per meddelande - funktioner för förbättrad säkerhet utifrån security meddelanden från IoT Edge eller löv enheter.

  >[!Note]
  > Säkerhetsvarningar medför också kvot för användning på IoT Hub.

Mer information finns i [Security Center-prissättning](https://azure.microsoft.com/en-us/pricing/details/security-center/).

## <a name="associated-costs"></a>Associerade kostnader

ASC för IoT har två typer av relaterade kostnader, som inte ingår i den direkta betalning:

- Förbrukning för IoT Hub-kvot

- Logga in kostnader för lagring av Analytics

Du kan minska kostnaderna genom att ta slut vissa funktioner genom att ändra dina inställningar.

Du ändrar inställningarna:

1. Öppna IoT-hubb.

2. Under **Security**, klickar du på **översikt**.

3. Klicka på **Inställningar**.

Följande tabell innehåller en sammanfattning av associerade kostnader och konsekvenserna av varje alternativ.

|     | Användning | Kommentar |
| --- | --- | --- |
| **Förbrukning för IoT Hub-kvot** |  |
| [Exportera enheten](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) jobb (twin export) | En gång om dagen | Inaktivera _twin metadatasamlingen_ |
| **Log Analytics-lagring** |  |
| Rekommendation för enheten och aviseringar| Säkerhetsrekommendation och aviseringar som genereras av tjänsten | Inte valfritt |
| Rå säkerhetsdata| Rå säkerhetsdata från IoT-enheter, som samlas in av security agenter | Inaktivera _lagra säkerhetshändelser för raw-enhet_ |

>[!Important]
> Avanmäla har allvarliga konsekvenser till tillgängliga säkerhetsfunktioner.
  
| Avanmäl | Effekter |
| --- | --- |
| _Twin metadatasamlingen_ | Inaktivera [anpassade aviseringar](quickstart-create-custom-alerts.md) |
| | Inaktivera manifest IoT Edge-rekommendationer |
| | Inaktivera enheten identitetsbaserade rekommendationer och aviseringar |
| _Säkerhetshändelser i Store raw-enhet_ | Information om enhetens OS grundläggande rekommendationer är inte tillgängliga |
| | Information på [avisering](concept-security-alerts.md) och [rekommendation](concept-recommendations.md) undersökningar är inte tillgängliga |


## <a name="see-also"></a>Se också

- Åtkomst till din [raw säkerhetsdata](how-to-security-data-access.md)
- [Undersöka en enhet](how-to-investigate-device.md)
- Förstå och utforska [säkerhetsrekommendationer](concept-recommendations.md)
- Förstå och utforska [säkerhetsaviseringar](concept-security-alerts.md)
