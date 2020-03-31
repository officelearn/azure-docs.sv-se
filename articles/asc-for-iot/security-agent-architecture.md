---
title: Så här fungerar Azure Security Center för IoT-arkitektur för säkerhetsagent| Microsoft-dokument
description: Förstå säkerhetsagentarkitektur för agenter som används i Azure Security Center for IoT-tjänsten.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 998aeab197931a75579fc39b28e3a248b85fc57b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596902"
---
# <a name="security-agent-reference-architecture"></a>Referensarkitektur för säkerhetsagenter

Azure Security Center för IoT tillhandahåller referensarkitektur för säkerhetsagenter som loggar, bearbetar, sammanställer och skickar säkerhetsdata via IoT Hub.

Säkerhetsagenter är utformade för att fungera i en begränsad IoT-miljö och är mycket anpassningsbara när det gäller värden som de tillhandahåller jämfört med de resurser de förbrukar.

Säkerhetsagenter har stöd för följande funktioner:

- Samla in råa säkerhetshändelser från det underliggande operativsystemet (Linux, Windows). Mer information om tillgängliga säkerhetsdatainsamlare finns i [Azure Security Center för IoT-agentkonfiguration](how-to-agent-configuration.md).

- Aggregera råsäkerhetshändelser i meddelanden som skickas via IoT Hub.

- Autentisera med befintlig enhetsidentitet eller en dedikerad modulidentitet. Mer information finns i [autentiseringsmetoder för säkerhetsagenter.](concept-security-agent-authentication-methods.md)

- Konfigurera fjärrstyrd med hjälp av azureiotsecurity-modultvillingen. **azureiotsecurity** Mer information finns i [Konfigurera ett Azure Security Center för IoT-agent](how-to-agent-configuration.md).

Azure Security Center för IoT-säkerhetsagenter utvecklas som projekt med öppen källkod och är tillgängliga från GitHub: 

- [Azure Security Center för IoT C-baserad agent](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [Azure Security Center för IoT C#-baserad agent](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Plattformar som stöds av agent

Azure Security Center för IoT erbjuder olika installationsagenter för 32bit och 64bit Windows, och samma för 32bit och 64bit Linux. Kontrollera att du har rätt agentinstallationsanordning för var och en av dina enheter enligt följande tabell:

| Arkitektur | Linux | Windows |    Information|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32bit (32bit)  | C  | C#  ||
| 64bit  | C# eller C           | C#      | Vi rekommenderar att du använder C-agenten för enheter med mer begränsade eller minimala enhetsresurser.|
|

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig mer om Azure Security Center för IoT-säkerhetsagentarkitektur och tillgängliga installationsprogram.

Om du vill fortsätta komma igång med Azure Security Center för IoT-distribution använder du följande artiklar:

- Förstå [autentiseringsmetoder för säkerhetsagenter](concept-security-agent-authentication-methods.md)
- Välja och distribuera en [säkerhetsagent](how-to-deploy-agent.md)
- Granska azure security center för [IoT-tjänstkrav](service-prerequisites.md)
- Lär dig hur du [aktiverar Azure Security Center för IoT-tjänst i din IoT Hub](quickstart-onboard-iot-hub.md)
- Läs mer om tjänsten från vanliga frågor och svar om [Azure Security Center för IoT](resources-frequently-asked-questions.md)
