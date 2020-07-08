---
title: Komponenter & krav
description: Information om allt som behövs för att komma igång med Azure Security Center för IoT service-krav.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.custom: references_regions
ms.openlocfilehash: 19e4ea37aa10c90d15a2b7dcdf962c131c8e473d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84193216"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Azure Security Center för IoT-krav

Den här artikeln innehåller en förklaring av de olika komponenterna i Azure Security Center för IoT-tjänsten, vad du behöver för att börja och beskriver de grundläggande begreppen för att hjälpa till att förstå tjänsten.

## <a name="minimum-requirements"></a>Minimikrav

- IoT Hub standard nivå
  - Behörigheter för **ägare** av RBAC-roll
- [Log Analytics arbets yta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure Security Center (rekommenderas)
  - Användning av Azure Security Center är en rekommendation och inte ett krav. Utan Azure Security Center kan du inte Visa dina andra Azure-resurser i IoT Hub.

## <a name="working-with-azure-security-center-for-iot-service"></a>Arbeta med Azure Security Center för IoT-tjänsten

Azure Security Center för IoT Insights och rapportering finns i Azure IoT Hub och Azure Security Center. För att aktivera Azure Security Center för IoT på din Azure-IoT Hub krävs ett konto med behörighet som **ägares** nivå. När du har aktiverat ASC för IoT i din IoT Hub visas Azure Security Center för IoT Insights som **säkerhetsfunktionen** i Azure IoT Hub och som **IoT** i Azure Security Center.

## <a name="supported-service-regions"></a>Service regioner som stöds

Azure Security Center for IoT stöds för närvarande för IoT Hub i följande Azure-regioner:

- USA, centrala
- USA, östra
- USA, östra 2
- USA, västra centrala
- USA, västra
- USA, västra 2
- Centrala USA, södra
- USA, norra centrala
- Kanada, centrala
- Kanada, östra
- Europa, norra
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

Azure Security Center for IoT dirigerar all trafik från alla europeiska regioner till regionala Data Center i västra Europa och alla återstående regioner till det centrala amerikanska regionala data centret.

## <a name="wheres-my-iot-hub"></a>Var finns mina IoT Hub?

Kontrol lera din IoT Hub plats för att kontrol lera tjänstens tillgänglighet innan du börjar.

1. Öppna din IoT Hub.
1. Klicka på **Översikt**.
1. Kontrol lera att platsen i listan matchar någon av de [tjänst regioner som stöds](#supported-service-regions).

## <a name="supported-platforms-for-agents"></a>Plattformar som stöds för agenter

Azure Security Center för IoT-agenter har stöd för en växande lista med enheter och plattformar. Se [listan över plattformar som stöds](how-to-deploy-agent.md) för att kontrol lera det befintliga eller planerade enhets biblioteket.

## <a name="next-steps"></a>Nästa steg

- Läs [översikten över](overview.md) Azure IoT-säkerhet
- Lär dig hur du [aktiverar tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar om Azure Security Center for IoT](resources-frequently-asked-questions.md)
- Utforska hur du [förstår Azure Security Center för IoT-aviseringar](concept-security-alerts.md)
