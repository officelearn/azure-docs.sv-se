---
title: Förstå Azure Security Center för IoT Security Agent-arkitektur | Microsoft Docs
description: Förstå säkerhets agent arkitekturen för de agenter som används i Azure Security Center för IoT-tjänsten.
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
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596902"
---
# <a name="security-agent-reference-architecture"></a>Referens arkitektur för säkerhets agent

Azure Security Center for IoT tillhandahåller referens arkitektur för säkerhets agenter som loggar, bearbetar, sammanställer och skickar säkerhets data via IoT Hub.

Säkerhets agenter är utformade för att fungera i en begränsad IoT-miljö och är mycket anpassningsbara utifrån de värden som de ger jämfört med de resurser de använder.

Säkerhets agenter har stöd för följande funktioner:

- Samla in rå säkerhets händelser från det underliggande operativ systemet (Linux, Windows). Mer information om tillgängliga säkerhets data insamlare finns [Azure Security Center för IoT agent-konfiguration](how-to-agent-configuration.md).

- Samla in rå säkerhets händelser i meddelanden som skickas via IoT Hub.

- Autentisera med befintlig enhets identitet eller en särskild modul identitet. Mer information finns i [autentiseringsmetoder för säkerhets agenter](concept-security-agent-authentication-methods.md) .

- Fjärrkonfigurera genom att använda **azureiotsecurity** -modulen, dubbla. Mer information finns i [Konfigurera ett Azure Security Center för IoT](how-to-agent-configuration.md)-agenten.

Azure Security Center för IoT-säkerhetsagenter utvecklas som projekt med öppen källkod och är tillgängliga från GitHub: 

- [Azure Security Center för IoT C-baserad agent](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [Azure Security Center för IoT C#-baserad agent](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Plattformar som stöds

Azure Security Center for IoT erbjuder olika installations agenter för 32-och 64-bitars fönster, och samma för 32-och 64-bitars Linux. Kontrol lera att du har rätt agent installations program för var och en av dina enheter enligt följande tabell:

| Arkitektur | Linux | Windows |    Information|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32  | C  | C#  ||
| 64  | C#eller C           | C#      | Vi rekommenderar att du använder C-agenten för enheter med mer begränsade eller minimala enhets resurser.|
|

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig mer om Azure Security Center för IoT Security Agent-arkitekturen och de tillgängliga installations program.

Använd följande artiklar om du vill fortsätta att komma igång med Azure Security Center för IoT-distribution:

- Förstå [autentiseringsmetoder för säkerhets agenter](concept-security-agent-authentication-methods.md)
- Välj och distribuera en [säkerhets agent](how-to-deploy-agent.md)
- Granska förutsättningarna för Azure Security Center för IoT [-tjänster](service-prerequisites.md)
- Lär dig hur du [aktiverar Azure Security Center för IoT-tjänsten i din IoT Hub](quickstart-onboard-iot-hub.md)
- Läs mer om tjänsten i [vanliga frågor och svar om Azure Security Center for IoT](resources-frequently-asked-questions.md)
