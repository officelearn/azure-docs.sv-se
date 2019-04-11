---
title: Azure Security Center för IoT-krav förhandsversion | Microsoft Docs
description: Information om allt som behövs för att komma igång med Azure Security Center för IoT-tjänsten krav.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: f09d768c0c6c1d351f737b053da9fd3282867099
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58861697"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Azure Security Center för IoT-krav

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln innehåller en förklaring av de olika byggstenarna för den Azure Security Center (ASC) för IoT-tjänsten, vad du behöver att påbörja och grundläggande begrepp för att förstå tjänsten. 

## <a name="minimum-requirements"></a>Minimikrav

- Standardnivån för IoT-hubb
    - RBAC-roll **ägare** privilegier 
- [Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (rekommenderas)
    - Användning av Azure Security Center är bara en rekommendation och inte ett krav, utan detta kommer du inte visa dina andra Azure-resurser i IoT Hub. 
 
## <a name="working-with-asc-for-iot-service"></a>Arbeta med ASC för IoT-tjänsten

ASC för IoT insikter och rapportering är tillgängliga med hjälp av Azure IoT Hub och Azure Security Center. Att aktivera ASC för IoT på Azure IoT Hub, ett konto med **ägare** rätt behörighet krävs. När du har aktiverat ASC för IoT i din IoT-hubb, ASC för IoT insights visas som den **Security** funktionen i Azure IoT Hub samt **IoT** i Azure Security Center. 

## <a name="supported-service-regions"></a>Stöds tjänstregioner 

ASC för IoT stöds för närvarande för IoT-hubbar i följande Azure-regioner:
  - Centrala USA
  - Norra Europa
  - Sydostasien

## <a name="wheres-my-iot-hub"></a>Var är Mina IoT Hub?

Kontrollera din IoT Hub-plats för att verifiera tjänsttillgänglighet innan du börjar. 

1. Öppna din IoT-hubb. 
2. Klicka på **Översikt**. 
3. Kontrollera den plats som anges som matchar ett av de [service regioner som stöds](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Plattformar som stöds för agenter 

ASC för IoT-agenter stöder en växande lista med enheter och plattformar. Se den [stöds plattformslistan](how-to-deploy-agent.md) att kontrollera biblioteket befintligt eller planerat enheten.  

## <a name="next-steps"></a>Nästa steg
- [Översikt](overview.md)
- [Aktivera tjänsten](quickstart-onboard-iot-hub.md)
- [ASC för IoT vanliga frågor och svar](resources-frequently-asked-questions.md)
- [Så här fungerar ASC för IoT-aviseringar](concept-security-alerts.md)
