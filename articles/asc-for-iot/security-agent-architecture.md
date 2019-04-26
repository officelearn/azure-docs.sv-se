---
title: Förstå Azure Security Center för IoT-agenten Säkerhetsarkitektur förhandsversion | Microsoft Docs
description: Förstå security agent-arkitektur för agenter som används i Azure Security Center för IoT-tjänsten.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: e10cd3f60c3b12c6d5115ff34f4cbde2ef19d9fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60505415"
---
# <a name="security-agent-reference-architecture"></a>Referensarkitektur för Security-agenten

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Azure Security Center (ASC) för IoT innehåller Referensarkitektur för säkerhetsagenter som loggar in, bearbeta, sammanställa och skicka säkerhetsdata via IoT hub.

Security agenter är utformade att fungera i en begränsad IoT-miljö och är mycket anpassningsbara när det gäller värden som de tillhandahåller jämfört med de resurser de använder.

Security agenter stöd för följande funktioner:

- Samla in råa säkerhetshändelser från det underliggande operativsystemet (Linux, Windows). Läs mer om tillgängliga säkerhetsuppdateringar datainsamlare i [ASC för IoT-agentkonfiguration](how-to-agent-configuration.md).

- Aggregera raw säkerhetshändelser i meddelanden som skickas via IoT hub.

- Autentisera med befintliga enhetsidentitet och en dedikerad modulen identitet. Se [Security agent autentiseringsmetoder](concept-security-agent-authentication-methods.md) vill veta mer.

- Konfigurera via en fjärranslutning med hjälp av den **azureiotsecurity** modultvilling. Mer information finns i [konfigurera en ASC för IoT-agenten](how-to-agent-configuration.md).

ASC för IoT-säkerhet agenter har utvecklats som projekt för öppen källkod och är tillgängliga från GitHub: 

- [ASC för IoT-C-baserad agent](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [ASC för IoT C#-baserad agent](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Agent som stöds plattformar

ASC för IoT erbjuder olika installer agenter för 32-bitars och 64-bitars Windows och likadan ut för 32-bitars och 64-bitars Linux. Kontrollera att du har rätt agentinstallationsprogrammet för var och en av dina enheter enligt följande tabell:

| 32- eller 64-bitars | Linux | Windows |    Information|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32-bitars  | C  | C#  ||
| 64-bitars  | C#eller C           | C#      | Använda C-agenten för enheter med minimal resurser|

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om ASC för IoT security agent-arkitekturen och de tillgängliga installationsprogram.

Om du vill fortsätta att komma igång med ASC för IoT-distributionen, Använd följande artiklar:

- Förstå [Security agent autentiseringsmetoder](concept-security-agent-authentication-methods.md)
- Välj och distribuera en [security-agenten](how-to-deploy-agent.md)
- Granska ASC för IoT [tjänsten krav](service-prerequisites.md)
- Lär dig hur du [aktivera ASC för IoT-tjänsten i din IoT-hubb](quickstart-onboard-iot-hub.md)
- Mer information om tjänsten från den [ASC för IoT vanliga frågor och svar](resources-frequently-asked-questions.md)
