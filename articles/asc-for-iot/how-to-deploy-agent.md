---
title: Välj och distribuera Azure Security Center om IoT-agenten förhandsversionen | Microsoft Docs
description: Lär dig mer om hur väljer och distribuera Azure Security Center för IoT security agenter på IoT-enheter.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 8f5a6187b0a651da9dd8de1cb5670a8faffded1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61358411"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Välj och distribuera en säkerhetsagenten på din IoT-enhet

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Security Center (ASC) för IoT innehåller referensarkitekturer för säkerhetsagenter som övervakar och samlar in data från IoT-enheter.
Se [Security agent Referensarkitektur](security-agent-architecture.md) vill veta mer.

Agenter har utvecklats som projekt med öppen källkod och finns i två varianter: <br> [C](https://aka.ms/iot-security-github-c), och [ C# ](https://aka.ms/iot-security-github-cs).

I den här artikeln kan du se hur du: 
> [!div class="checklist"]
> * Jämför security agent varianter
> * Identifiera agent som stöds plattformar
> * Välj rätt agenten smak för din lösning

## <a name="understand-security-agent-options"></a>Förstå säkerhetsalternativ för agenten

Varje ASC för IoT security agent smak erbjuder samma uppsättning funktioner och stöder liknande konfigurationsalternativ. 

C-baserade säkerhetsagenten har en lägre minnesavtrycket som krävs och är det perfekta valet för enheter med färre tillgängliga resurser. 

|     | C-baserade security-agenten | C#-baserade säkerhetsagenten |
| --- | ----------- | --------- |
| Öppen källkod | Tillgängliga under [MIT-licensen](https://en.wikipedia.org/wiki/MIT_License) i [Github](https://aka.ms/iot-security-github-cs) | Tillgängliga under [MIT-licensen](https://en.wikipedia.org/wiki/MIT_License) i [Github](https://aka.ms/iot-security-github-c) |
| Programmeringsspråk    | C | C# |
| Plattformar som stöds Windows? | Nej | Ja |
| Krav för Windows | --- | [WMI](https://docs.microsoft.com/en-us/windows/desktop/wmisdk/) |
| Plattformar som stöds Linux? | Ja, x64 och x86 | Ja, x64 endast |
| Förutsättningar för Linux | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins, sudo, netstat, iptables |
| Diskutrymme | 10,5 MB | 90MB |
| Minnesfotavtryck (i genomsnitt) | 5.5 MB | 33MB |
| [Autentisering](concept-security-agent-authentication-methods.md) till IoT Hub | Ja | Ja |
| Säkerhetsdata [samling](how-to-agent-configuration.md#supported-security-events) | Ja | Ja |
| Händelse-aggregering | Ja | Ja |
| Fjärrkonfiguration via [security modultvilling](concept-security-module.md) | Ja | Ja |


## <a name="choose-an-agent-flavor"></a>Välj en agent smak 

Besvara följande frågor om dina IoT-enheter att välja rätt agenten:

- Använder du _Windows Server_ eller _Windows IoT Core_? 

    [Distribuera en C#-baserade security-agenten för Windows](how-to-deploy-windows-cs.md).

- Du använder en Linux-distribution med x86 arkitektur? 

    [Distribuera en C-baserade security-agenten för Linux](how-to-deploy-linux-c.md).

- Du använder en Linux-distribution med x64 arkitektur?

    Du kan använda antingen agenten smak. <br>
    [Distribuera en C-baserade security-agenten för Linux](how-to-deploy-linux-c.md) och/eller [distribuera en C#-baserade security-agenten för Linux](how-to-deploy-linux-cs.md).

Båda agenten varianter ger samma uppsättning funktioner och stöder liknande konfigurationsalternativ.
Se [Security agent jämförelse](how-to-deploy-agent.md#understand-security-agent-options) vill veta mer.

## <a name="supported-platforms"></a>Plattformar som stöds

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

## <a name="next-steps"></a>Nästa steg

Mer information om konfigurationsalternativ fortsätter du att den här guiden för agentkonfiguration av. 
> [!div class="nextstepaction"]
> [Agentkonfiguration så hjälper](./how-to-agent-configuration.md)
