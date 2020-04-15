---
title: Händelsesammansättning
description: Lär dig mer om Azure Security Center för IoT-händelseaggregering.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d40dac0b-abd0-4ff5-82eb-0f511ee13725
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: f72ef8cc5161bd6f885249e7d39344a57fa2368e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311422"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>Azure Security Center för IoT-händelseaggregering

Azure Security Center för IoT-säkerhetsagenter samlar in data- och systemhändelser från din lokala enhet och skickar dessa data till Azure-molnet för bearbetning och analys. Säkerhetsagenten samlar in många typer av enhetshändelser, inklusive nya processhändelser och nya anslutningshändelser. Både nya processhändelser och nya anslutningshändelser kan lagligen inträffa på en enhet inom en sekund, och även om det är viktigt för robust och omfattande säkerhet kan antalet meddelanden som säkerhetsagenter tvingas skicka snabbt nå eller överskrida din IoT Hub-kvot och kostnadsgränser. Dessa händelser innehåller dock mycket värdefull säkerhetsinformation som är avgörande för att skydda din enhet.

Azure Security Center for IoT Agents samlar dessa typer av händelser för att minska den extra kvoten och kostnaderna samtidigt som dina enheter skyddas.

Händelseaggregering är **På** som standard och kan inte när som helst stängas **av** manuellt.

Aggregering är för närvarande tillgänglig för följande typer av händelser:

* ProcessCreate
* AnslutningSkapa
* ProcessTerminate (endast Windows)

## <a name="how-does-event-aggregation-work"></a>Hur fungerar händelseaggregering?

När händelseaggregering lämnas **på**, Azure Security Center för IoT-agenter aggregerade händelser för intervallperioden eller tidsperioden.
När intervallperioden har passerat skickar agenten de aggregerade händelserna till Azure-molnet för vidare analys.
De aggregerade händelserna lagras i minnet tills de skickas till Azure-molnet.

För att minska agentens minnesavtryck ökar agenten träffantalet för den här specifika händelsen när agenten samlar in en identisk händelse till en som redan hålls i minnet. När aggregeringstidsfönstret passerar skickar agenten träffantalet för varje specifik typ av händelse som inträffade. Händelseaggregering är helt enkelt aggregering av träffen räknas av varje insamlad typ av händelse.

Händelser anses vara identiska endast när följande villkor är uppfyllda:

* ProcessCreate-händelser - när **kommandorad**, **körbar,** **användarnamn**och **userid** är identiska
* ConnectionCreate-händelser – när **commandLine,** **userId,** **riktning,** **lokal adress,** **fjärradress**, **protokoll och **målport** är identiska
* ProcessTerminera händelser - när **körbar** status och **avslutningsstatus** är identiska

### <a name="working-with-aggregated-events"></a>Arbeta med aggregerade händelser

Under aggregering ignoreras händelseegenskaper som inte aggregeras och visas i logganalys med värdet 0.

* ProcessCreate-händelser - **processId**och **parentProcessId** är inställda på 0
* ConnectionCreate-händelser - **processId**och **källporten** är inställda på 0

## <a name="event-aggregation-based-alerts"></a>Händelseaggregeringsbaserade aviseringar

Efter analys skapar Azure Security Center för IoT säkerhetsaviseringar för misstänkta aggregerade händelser. Aviseringar som skapas från aggregerade händelser visas bara en gång för varje aggregerad händelse.

Aggregering starttid, sluttid och träffantal för varje händelse loggas i fältet **ExtraDetails** i Log Analytics för användning under undersökningar.

Varje aggregerad händelse representerar en 24-timmarsperiod av insamlade aviseringar. Med hjälp av menyn händelsealternativ längst upp till vänster i varje händelse kan du **stänga** av varje enskild aggregerad händelse.

## <a name="event-aggregation-twin-configuration"></a>Konfiguration av tvillingkonfiguration för händelseaggregering

Gör ändringar i konfigurationen av Azure Security Center för IoT-händelseaggregering i [agentkonfigurationsobjektet](how-to-agent-configuration.md) för modulens tvillingidentitet för **azureiotsecurity-modulen.**

| Konfigurationsnamn | Möjliga värden | Information | Anmärkningar |
|:-----------|:---------------|:--------|:--------|
| aggregeringEnbarprocessskapa | boolean | Aktivera/inaktivera händelseaggregering för processskapande händelser |
| aggregeringIntervalprocessSkapera | Strängar för ISO8601 Tidsspann | Aggregeringsintervall för processskapande händelser |
| aggregeringEnbaranslutningSkapa | boolean| Aktivera/inaktivera händelseaggregering för anslutningsskapande händelser |
| aggregeringIntervalConnectionSkapa | Strängar för ISO8601 Tidsspann | Aggregeringsintervall för anslutningsskapande händelser |
| aggregeringEnbarprocessterminera | boolean | Aktivera/inaktivera händelseaggregering för processsluthändelser | Endast Windows|
| aggregeringIntervalprocessTerminera | Strängar för ISO8601 Tidsspann | Aggregeringsintervall för processsluthändelser | Endast Windows|
|

## <a name="default-configurations-settings"></a>Inställningar för standardkonfigurationer

| Konfigurationsnamn | Standardvärden |
|:-----------|:---------------|
| aggregeringEnbarprocessskapa | true |
| aggregeringIntervalprocessSkapera | "PT1H"|
| aggregeringEnbaranslutningSkapa | true |
| aggregeringIntervalConnectionSkapa | "PT1H"|
| aggregeringEnbarprocessterminera | true |
| aggregeringIntervalprocessTerminera | "PT1H"|
|

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig mer om Azure Security Center för IoT-säkerhetsagentaggregering och tillgängliga händelsekonfigurationsalternativ.

Om du vill fortsätta komma igång med Azure Security Center för IoT-distribution använder du följande artiklar:

- Förstå [autentiseringsmetoder för säkerhetsagenter](concept-security-agent-authentication-methods.md)
- Välja och distribuera en [säkerhetsagent](how-to-deploy-agent.md)
- Granska Azure Security Center för [IoT-tjänstkrav](service-prerequisites.md)
- Lär dig hur du [aktiverar Azure Security Center för IoT-tjänst i din IoT-hubb](quickstart-onboard-iot-hub.md)
- Läs mer om tjänsten från vanliga frågor och svar om [Azure Security Center för IoT](resources-frequently-asked-questions.md)
