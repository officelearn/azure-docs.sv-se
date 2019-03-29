---
title: Så här fungerar ASC för IoT-agenten Säkerhetsarkitektur förhandsversion | Microsoft Docs
description: Förstå security agent-arkitektur för agenter som används i ASC för IoT-tjänsten.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 7de4307f7eef95a637dc828ac1738bd0ed39c7c7
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578466"
---
# <a name="security-agent-reference-architecture"></a>Referensarkitektur för Security-agenten

> [!IMPORTANT]
> ASC för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


ASC för IoT innehåller Referensarkitektur för säkerhetsagenter som loggar in, bearbeta, sammanställa och skicka säkerhetsdata via IoT hub.

Security agenter är utformade att fungera i en begränsad IoT-miljö och är mycket anpassningsbara när det gäller värden som de tillhandahåller jämfört med de resurser de använder.

Security agenter stöd för följande funktioner:

- Samla in råa säkerhetshändelser från det underliggande operativsystemet (Linux, Windows). Läs mer om tillgängliga säkerhetsuppdateringar datainsamlare i [ASC för IoT-agentkonfiguration](how-to-agent-configuration.md).

- Aggregera raw säkerhetshändelser i meddelanden som skickas via IoT hub.

- Autentisera med befintliga enhetsidentitet och en dedikerad modulen identitet. Se [Security agent autentiseringsmetoder](concept-security-agent-authentication-methods.md) vill veta mer.

- Konfigurera via en fjärranslutning med hjälp av den **azureiotsecurity** modultvilling. Mer information finns i [konfigurera en ASC för IoT-agenten](how-to-agent-configuration.md).

ASC för IoT-säkerhet agenter har utvecklats som projekt för öppen källkod och är tillgängliga från GitHub: 

- [IoT-ASC-Agent-C](https://github.com/Azure/IoT-ASC-Agent-C) 
- [IoT-ASC-Agent-CS](https://github.com/Azure/IoT-ASC-Agent-CS)

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