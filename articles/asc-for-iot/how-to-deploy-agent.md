---
title: Välja och distribuera säkerhetsagenter
description: Lär dig mer om hur du väljer och distribuerar Azure Security Center för IoT-säkerhetsagenter på IoT-enheter.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: c3b514c79be87cd136375b4853226426965f4185
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311203"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Välja och distribuera en säkerhetsagent på din IoT-enhet

Azure Security Center för IoT tillhandahåller referensarkitekturer för säkerhetsagenter som övervakar och samlar in data från IoT-enheter.
Mer information finns i [Referensarkitektur för säkerhetsagenter](security-agent-architecture.md).

Agenter utvecklas som projekt med öppen källkod och finns i två smaker: <br> [C](https://aka.ms/iot-security-github-c)och [C#](https://aka.ms/iot-security-github-cs).

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Jämför smaker av säkerhetsagenter
> * Upptäck agentplattformar som stöds
> * Välj rätt agent smak för din lösning

## <a name="understand-security-agent-options"></a>Förstå alternativ för säkerhetsagenter

Varje Azure Security Center för IoT-säkerhetsagentsmak erbjuder samma uppsättning funktioner och stöder liknande konfigurationsalternativ.

Den C-baserade säkerhetsagenten har ett lägre minnesavtryck och är det perfekta valet för enheter med färre tillgängliga resurser.

|     | C-baserad säkerhetsagent | C#-baserad säkerhetsagent |
| --- | ----------- | --------- |
| Öppen källkod | Tillgänglig under [MIT-licens](https://en.wikipedia.org/wiki/MIT_License) i [GitHub](https://aka.ms/iot-security-github-cs) | Tillgänglig under [MIT-licens](https://en.wikipedia.org/wiki/MIT_License) i [GitHub](https://aka.ms/iot-security-github-c) |
| Utvecklingsspråk    | C | C# |
| Windows-plattformar som stöds? | Inga | Ja |
| Förutsättningar för Windows | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Stöds Linux-plattformar? | Ja, x64 och x86 | Endast ja, x64 |
| Förutsättningar för Linux | libunwind8, libcurl3, uuid-runtime, auditade, audispd-plugins | libunwind8, libcurl3, uuid-runtime, auditade, audispd-plugins, sudo, netstat, iptables |
| Diskfotavtryck | 10,5 MB | 90 MB |
| Minnesavtryck (i genomsnitt) | 5,5 MB | 33 MB |
| [Autentisering](concept-security-agent-authentication-methods.md) till IoT-hubb | Ja | Ja |
| [Insamling](how-to-agent-configuration.md#supported-security-events) av säkerhetsdata | Ja | Ja |
| Händelsesammansättning | Ja | Ja |
| Fjärrkonfiguration via [tvilling av säkerhetsmodul](concept-security-module.md) | Ja | Ja |
|

## <a name="security-agent-installation-guidelines"></a>Riktlinjer för installation av säkerhetsagenter

För **Windows:** Skriptet Install SecurityAgent.ps1 måste köras från ett PowerShell-administratörsfönster.

För **Linux:** InstallSecurityAgent.sh måste köras som superanvändare. Vi rekommenderar att du prefixar installationskommandot med "sudo".

## <a name="choose-an-agent-flavor"></a>Välj en agent smak

Svara på följande frågor om dina IoT-enheter för att välja rätt agent:

- Använder du _Windows Server_ eller Windows _IoT Core?_

    [Distribuera en C#-baserad säkerhetsagent för Windows](how-to-deploy-windows-cs.md).

- Använder du en Linux-distribution med x86-arkitektur?

    [Distribuera en C-baserad säkerhetsagent för Linux](how-to-deploy-linux-c.md).

- Använder du en Linux-distribution med x64-arkitektur?

    Båda agent smaker kan användas. <br>
    [Distribuera en C-baserad säkerhetsagent för Linux](how-to-deploy-linux-c.md) och/eller [distribuera en C#-baserad säkerhetsagent för Linux](how-to-deploy-linux-cs.md).

Båda agent smaker erbjuder samma uppsättning funktioner och stödja liknande konfigurationsalternativ.
Se [Jämförelsen säkerhetsagent](how-to-deploy-agent.md#understand-security-agent-options) om du vill veta mer.

## <a name="supported-platforms"></a>Plattformar som stöds

Följande lista innehåller alla plattformar som stöds för närvarande.

|Azure Security Center för IoT-agent |Operativsystem |Arkitektur |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    x64|
|C|Ubuntu 18.04 |    x64, ARMv7|
|C|Debian 9 |    x64, x86|
|C#|Ubuntu 16.04     |x64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core, bygg 17763    |x64|
|

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om konfigurationsalternativ fortsätter du till den programväga som gäller för agentkonfiguration.
> [!div class="nextstepaction"]
> [Agent konfiguration hur man guidar](./how-to-agent-configuration.md)
