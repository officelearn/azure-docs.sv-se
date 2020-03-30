---
title: Azure Security Center för IoT-förutsättningar| Microsoft-dokument
description: Information om allt som behövs för att komma igång med Azure Security Center för IoT-tjänstförutsättning.
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
ms.openlocfilehash: 4440fec98d1f561da6375bcaadba4282076cc53b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71299467"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Azure Security Center för IoT-förutsättningar

Den här artikeln innehåller en förklaring av de olika byggstenarna i Azure Security Center for IoT-tjänsten, vad du behöver börja och förklarar de grundläggande begreppen för att förstå tjänsten. 

## <a name="minimum-requirements"></a>Minimikrav

- Standardnivå för IoT Hub
    - Privilegier på **RBAC-rollägare** 
- [Arbetsyta för logganalys](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (rekommenderas)
    - Användning av Azure Security Center är en rekommendation och inte ett krav. Utan Azure Security Center kan du inte visa dina andra Azure-resurser i IoT Hub. 
 
## <a name="working-with-azure-security-center-for-iot-service"></a>Arbeta med Azure Security Center för IoT-tjänst

Azure Security Center för IoT-insikter och rapportering är tillgängliga med Azure IoT Hub och Azure Security Center. För att aktivera Azure Security Center för IoT på din Azure IoT Hub krävs ett konto med behörighet på **ägarnivå.** När du har aktiverat ASC för IoT i din IoT-hubb visas Azure Security Center for IoT-insikter som **säkerhetsfunktionen** i Azure IoT Hub och som **IoT** i Azure Security Center. 

## <a name="supported-service-regions"></a>Serviceregioner som stöds 

Azure Security Center för IoT stöds för närvarande för IoT-hubbar i följande Azure-regioner:
  - USA, centrala  
  - USA, östra 
  - USA, östra 2
  - USA, västra centrala
  - USA, västra
  - USA, västra 2
  - Centrala SÖDRA USA
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

Azure Security Center för IoT dirigerar all trafik från alla europeiska regioner till det regionala datacentret i Västeuropa och alla återstående regioner till det centrala regionala datacentret i USA.  
  
## <a name="wheres-my-iot-hub"></a>Var är min IoT Hub?

Kontrollera din IoT Hub-plats för att verifiera tjänstens tillgänglighet innan du börjar. 

1. Öppna din IoT Hub. 
2. Klicka på **Översikt**. 
3. Verifiera platsen i listan matchar en av de [serviceområden som stöds](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Plattformar som stöds för agenter 

Azure Security Center för IoT-agenter stöder en växande lista över enheter och plattformar. Se [plattformslistan som stöds](how-to-deploy-agent.md) för att kontrollera ditt befintliga eller planerade enhetsbibliotek.  

## <a name="next-steps"></a>Nästa steg
- Läs [översikten](overview.md) över Azure IoT-säkerhet
- Läs om hur du [aktiverar tjänsten](quickstart-onboard-iot-hub.md)
- Läs vanliga frågor om [Azure Security Center för IoT](resources-frequently-asked-questions.md)
- Utforska hur du [förstår Azure Security Center för IoT-aviseringar](concept-security-alerts.md)
