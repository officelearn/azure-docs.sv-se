---
title: Komponenter & krav
description: Information om allt som behövs för att komma igång med krav för Azure Defender för IoT-tjänster.
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
ms.custom: references_regions
ms.openlocfilehash: 19263f8db58c8d20288d3ae74c24efd85667bc33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937839"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Krav för Azure Defender för IoT

Den här artikeln innehåller en förklaring av de olika komponenterna i Defender for IoT-tjänsten, vad du behöver för att börja och beskriver de grundläggande begreppen för att förstå tjänsten.

## <a name="minimum-requirements"></a>Minimikrav

- IoT Hub standard nivå
  - Behörighet för **ägare** till Azure-roll
- [Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure Security Center (rekommenderas)
  - Användning av Azure Security Center är en rekommendation och inte ett krav. Utan Azure Security Center kan du inte Visa dina andra Azure-resurser i IoT Hub.

## <a name="working-with-defender-for-iot-service"></a>Arbeta med Defender för IoT-tjänsten

Defender för IoT Insights och rapportering är tillgängligt med Azure IoT Hub och Azure Security Center. För att aktivera Defender för IoT på din Azure-IoT Hub, krävs ett konto med behörighet som **ägare** nivå. När du har aktiverat ASC för IoT i din IoT Hub visas Defender för IoT Insights som **säkerhetsfunktionen** i Azure IoT Hub och som  **IoT** i Azure Security Center.

## <a name="supported-service-regions"></a>Service regioner som stöds

Defender för IoT stöds för närvarande för IoT-hubbar i följande Azure-regioner:

- Central US
- East US
- USA, östra 2
- USA, västra centrala
- USA, västra
- USA, västra 2
- Centrala USA, södra
- USA, norra centrala
- Kanada, centrala
- Kanada, östra
- Norra Europa
- Brasilien, södra
- Frankrike, centrala
- Storbritannien, västra
- Storbritannien, södra
- Europa, västra
- Nordeuropa
- Japan, västra
- Japan, östra
- Australien, sydöstra
- Australien, östra
- Asien, östra
- Sydostasien
- Sydkorea, centrala
- Sydkorea, södra
- Indien, centrala
- Indien, södra

Defender för IoT dirigerar all trafik från alla europeiska regioner till regionala Data Center i västra Europa och alla återstående regioner till det centrala amerikanska regionala data centret.

## <a name="wheres-my-iot-hub"></a>Var finns mina IoT Hub?

Kontrol lera din IoT Hub plats för att kontrol lera tjänstens tillgänglighet innan du börjar.

1. Öppna din IoT Hub.
1. Klicka på **Översikt**.
1. Kontrol lera att platsen i listan matchar någon av de [tjänst regioner som stöds](#supported-service-regions).

## <a name="supported-platforms-for-agents"></a>Plattformar som stöds för agenter

Defender för IoT-agenter har stöd för en växande lista över enheter och plattformar. Se [listan över plattformar som stöds](how-to-deploy-agent.md) för att kontrol lera det befintliga eller planerade enhets biblioteket.

## <a name="next-steps"></a>Nästa steg

- Läs [översikten över](overview.md) Azure IoT-säkerhet
- Lär dig hur du [aktiverar tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar om Defender för IoT](resources-frequently-asked-questions.md)
- Utforska hur du [förstår Defender för IoT-aviseringar](concept-security-alerts.md)
