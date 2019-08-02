---
title: Kom igång med Azure Security Center for IoT | Microsoft Docs
description: Kom igång med att förstå det grundläggande arbets flödet för Azure Security Center för IoT-funktioner och-tjänster.
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
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596416"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Komma igång med Azure Security Center for IoT

Den här artikeln innehåller en förklaring av de olika bygg stenarna i Azure Security Center för IoT-tjänsten och förklarar hur du kommer igång med tjänsten med två möjliga distributions alternativ.  

## <a name="deployment-options"></a>Distributionsalternativ

Välj det tjänst scenario som bäst uppfyller dina IoT-enheter och miljö krav. 

### <a name="built-in-deployment"></a>Inbyggd distribution
Med det sömlösa, inbyggda distributions alternativet kan Azure Security Center för IoT snabbt integreras i din IoT Hub och tillhandahålla säkerhets analys av IoT Hub-konfigurationen, enhetens identitet och hantering och kommunikations mönster för NAV-enheter.

Starta en [inbyggd distribution](iot-hub-integration.md) med IoT Hub övervakning och rekommendationer. 
    <br>

### <a name="enhanced-deployment"></a>Förbättrad distribution
För förbättrade säkerhetsfunktioner kan du Distribuera Azure Security Center för IoT-agenter förutom att aktivera IoT Hub säkerhet ger agentbaserade händelse insamling, analys och hot identifiering av viktiga säkerhets data från dina IoT-enheter samt omfattande funktioner för hantering av position.

Starta en [förbättrad distribution](security-agents.md) med en agent-baserad omfattande skydds-och position hanterings lösning.
   

## <a name="next-steps"></a>Nästa steg

- Aktivera [Azure Security Center för IoT](quickstart-onboard-iot-hub.md)
- Konfigurera din [lösning](quickstart-configure-your-solution.md)
- [Skapa säkerhetsmoduler](quickstart-create-security-twin.md)
- Konfigurera [anpassade aviseringar](quickstart-create-custom-alerts.md)
- [Distribuera en säkerhets agent](how-to-deploy-agent.md)
