---
title: Azure Security Center för IoT-krav | Microsoft Docs
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
ms.openlocfilehash: 4440fec98d1f561da6375bcaadba4282076cc53b
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299467"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Azure Security Center för IoT-krav

Den här artikeln innehåller en förklaring av de olika bygg stenarna i Azure Security Center för IoT-tjänsten, vad du behöver för att börja och beskriver de grundläggande begreppen för att hjälpa till att förstå tjänsten. 

## <a name="minimum-requirements"></a>Minimi krav

- IoT Hub standard nivå
    - Behörigheter för **ägare** av RBAC-roll 
- [Log Analytics arbets yta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (rekommenderas)
    - Användning av Azure Security Center är en rekommendation och inte ett krav. Utan Azure Security Center kan du inte Visa dina andra Azure-resurser i IoT Hub. 
 
## <a name="working-with-azure-security-center-for-iot-service"></a>Arbeta med Azure Security Center för IoT-tjänsten

Azure Security Center för IoT Insights och rapportering finns i Azure IoT Hub och Azure Security Center. För att aktivera Azure Security Center för IoT på din Azure-IoT Hub krävs ett konto med behörighet som **ägares** nivå. När du har aktiverat ASC för IoT i din IoT Hub visas Azure Security Center för IoT Insights som **säkerhetsfunktionen** i Azure IoT Hub och som **IoT** i Azure Security Center. 

## <a name="supported-service-regions"></a>Service regioner som stöds 

Azure Security Center for IoT stöds för närvarande för IoT Hub i följande Azure-regioner:
  - Centrala USA  
  - East US 
  - USA, östra 2
  - Västra centrala USA
  - Västra USA
  - USA, västra 2
  - Centrala USA, södra
  - Norra centrala USA
  - Centrala Kanada
  - Östra Kanada 
  - Norra Europa    
  - Södra Brasilien
  - Frankrike, centrala  
  - Storbritannien, västra 
  - Storbritannien, södra
  - Västra Europa 
  - Norra Europa 
  - Västra Japan  
  - Östra Japan  
  - Sydöstra Australien
  - Östra Australien
  - Östasien   
  - Sydostasien
  - Sydkorea, centrala
  - Sydkorea, södra 
  - Indien, centrala
  - Indien, södra

Azure Security Center for IoT dirigerar all trafik från alla europeiska regioner till regionala Data Center i västra Europa och alla återstående regioner till det centrala amerikanska regionala data centret.  
  
## <a name="wheres-my-iot-hub"></a>Var finns mina IoT Hub?

Kontrol lera din IoT Hub plats för att kontrol lera tjänstens tillgänglighet innan du börjar. 

1. Öppna din IoT Hub. 
2. Klicka på **Översikt**. 
3. Kontrol lera att platsen i listan matchar någon av de [tjänst regioner som stöds](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Plattformar som stöds för agenter 

Azure Security Center för IoT-agenter har stöd för en växande lista med enheter och plattformar. Se [listan över plattformar som stöds](how-to-deploy-agent.md) för att kontrol lera det befintliga eller planerade enhets biblioteket.  

## <a name="next-steps"></a>Nästa steg
- Läs [översikten över](overview.md) Azure IoT-säkerhet
- Lär dig hur du [aktiverar tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar om Azure Security Center for IoT](resources-frequently-asked-questions.md)
- Utforska hur du [förstår Azure Security Center för IoT-aviseringar](concept-security-alerts.md)
