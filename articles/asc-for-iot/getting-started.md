---
title: Komma igång med Azure Security Center för IoT| Microsoft-dokument
description: Kom igång med att förstå det grundläggande arbetsflödet för Azure Security Center för IoT-funktioner och -tjänster.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 86c6c0bac5caae0873d0067c6abcb5a8ac864c88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596416"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Komma igång med Azure Security Center for IoT

Den här artikeln innehåller en förklaring av de olika byggstenarna i Azure Security Center for IoT-tjänsten och förklarar hur du kommer igång med tjänsten med två möjliga distributionsalternativ.  

## <a name="deployment-options"></a>Distributionsalternativ

Välj det servicescenario som bäst uppfyller ioT-enhets- och miljökraven. 

### <a name="built-in-deployment"></a>Inbyggd distribution
Med det sömlösa, inbyggda distributionsalternativet kan Azure Security Center for IoT snabbt integreras i din IoT-hubb och tillhandahålla säkerhetsanalys av IoT-hubbens konfiguration, enhetsidentitet och hantering samt kommunikationsmönster för navenheter.

Starta en [inbyggd distribution](iot-hub-integration.md) med övervakning och rekommendationer för IoT Hub. 
    <br>

### <a name="enhanced-deployment"></a>Förbättrad distribution
För förbättrade säkerhetsfunktioner ger distribution av Azure Security Center för IoT-agenter förutom att aktivera IoT Hub-säkerhet agentbaserad händelseinsamling, analys och hotidentifiering av viktiga säkerhetsdata från dina IoT-enheter samt omfattande kapacitet för hantering av säkerhetspositioner.

Starta en [förbättrad distribution](security-agents.md) med en agentbaserad omfattande lösning för skydd mot hot och säkerhetsposition.
   

## <a name="next-steps"></a>Nästa steg

- Aktivera [Azure Security Center för IoT](quickstart-onboard-iot-hub.md)
- Konfigurera [din lösning](quickstart-configure-your-solution.md)
- [Skapa säkerhetsmoduler](quickstart-create-security-twin.md)
- Konfigurera [anpassade aviseringar](quickstart-create-custom-alerts.md)
- [Distribuera en säkerhetsagent](how-to-deploy-agent.md)
