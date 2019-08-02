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
ms.date: 07/28/2019
ms.author: mlottner
ms.openlocfilehash: cc2dc3a190e3ad06bdc048f2a5770eae2a6990ec
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596827"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Azure Security Center för IoT-krav

Den här artikeln innehåller en förklaring av de olika bygg stenarna i Azure Security Center (ASC) för IoT-tjänsten, vad du behöver för att börja och beskriver de grundläggande begreppen för att hjälpa till att förstå tjänsten. 

## <a name="minimum-requirements"></a>Minimi krav

- IoT Hub standard nivå
    - Behörigheter för **ägare** av RBAC-roll 
- [Log Analytics arbets yta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (rekommenderas)
    - Användning av Azure Security Center är en rekommendation och inte ett krav. Utan Azure Security Center kan du inte Visa dina andra Azure-resurser i IoT Hub. 
 
## <a name="working-with-asc-for-iot-service"></a>Arbeta med ASC för IoT-tjänsten

ASC för IoT Insights och rapportering är tillgängligt med Azure IoT Hub och Azure Security Center. Om du vill aktivera ASC för IoT på din Azure-IoT Hub, krävs ett konto med behörighet som **ägare** nivå. När du har aktiverat ASC för IoT i din IoT Hub visas ASC för IoT Insights som **säkerhetsfunktionen** i Azure IoT Hub och som **IoT** i Azure Security Center. 

## <a name="supported-service-regions"></a>Service regioner som stöds 

ASC för IoT stöds för närvarande för IoT-hubbar i följande Azure-regioner:
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
  
## <a name="wheres-my-iot-hub"></a>Var finns mina IoT Hub?

Kontrol lera din IoT Hub plats för att kontrol lera tjänstens tillgänglighet innan du börjar. 

1. Öppna din IoT Hub. 
2. Klicka på **Översikt**. 
3. Kontrol lera att platsen i listan matchar någon av de [tjänst regioner som stöds](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Plattformar som stöds för agenter 

ASC för IoT-agenter har stöd för en växande lista över enheter och plattformar. Se [listan över plattformar som stöds](how-to-deploy-agent.md) för att kontrol lera det befintliga eller planerade enhets biblioteket.  

## <a name="next-steps"></a>Nästa steg
- Läs [översikten över](overview.md) Azure IoT-säkerhet
- Lär dig hur du [aktiverar tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar om Azure Security Center for IoT](resources-frequently-asked-questions.md)
- Utforska hur du [förstår Azure Security Center för IoT](concept-security-alerts.md) -aviseringar
