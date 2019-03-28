---
title: Välj och distribuera en ASC om IoT-agenten förhandsversionen | Microsoft Docs
description: Lär dig mer om hur väljer och distribuera ASC för IoT security agenter på IoT-enheter.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 5ce583fe98acb7e0a4aaeb720cb2d5ed5eebb9e3
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541969"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Välj och distribuera en säkerhetsagenten på din IoT-enhet

> [!IMPORTANT]
> ASC för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


ASC för IoT innehåller referensarkitekturer för säkerhetsagenter som övervakar och samlar in data från IoT-enheter.

Security agenter har utvecklats som projekt med öppen källkod och finns i två varianter: <br> [C](https://aka.ms/iot-security-github-c), och [ C# ](https://aka.ms/iot-security-github-cs).

## <a name="supported-platforms-for-asc-for-iot-agents"></a>Plattformar som stöds för ASC för IoT-agenter

Följande lista innehåller alla plattformar som stöds för närvarande.  

|ASC för IoT-agent |Operativsystem |Arkitektur |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core build 17763 |x64|


## <a name="which-flavor-should-i-use"></a>Vilken smak ska jag använda?

Tänk på följande frågor med avseende på dina IoT-enheter:

- Använder du _Windows Server_ eller _Windows IoT Core_? 

    Använd [ASC för IoT-installation för Windows med den C#-baserade säkerhetsagenten](tutorial-deploy-windows-cs.md).

- Du använder en Linux-distribution med x86 arkitektur? 

    Använd [ASC för IoT-installation för Linux med C-baserade säkerhetsagenten](tutorial-deploy-linux-c.md).
- Du använder en Linux-distribution med x64 arkitektur?

    Du kan använda båda varianter. <br>
    [ASC för IoT-installation för Linux med C-baserade säkerhetsagenten](tutorial-deploy-linux-c.md) och/eller [ASC för IoT-installation för Linux med den C#-baserade säkerhetsagenten](tutorial-deploy-linux-cs.md).

Båda varianter har samma uppsättning funktioner och stöder liknande konfigurationsalternativ. C-baserade security-agenten har en lägre minneskrav.


## <a name="next-steps"></a>Nästa steg
- [Översikt](overview.md)
- [Security-agenter](security-agent-architecture.md)
- [Security-agenten autentiseringsmetoder](concept-security-agent-authentication-methods.md)
- [ASC för IoT vanliga frågor och svar](resources-frequently-asked-questions.md)