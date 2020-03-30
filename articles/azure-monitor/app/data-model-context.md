---
title: Telemetridatamodell för Azure Application Insights – telemetrikontext | Microsoft-dokument
description: Programinsikter telemetrikontextdatamodell
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 8a2e3296643b13a54c4fceb11f044a2808cf2877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671871"
---
# <a name="telemetry-context-application-insights-data-model"></a>Telemetrikontext: Datamodell för Application Insights

Varje telemetriobjekt kan ha ett starkt skrivet kontextfält. Varje fält möjliggör ett specifikt övervakningsscenario. Använd den anpassade egenskapssamlingen för att lagra anpassad eller programspecifik kontextuell information.


## <a name="application-version"></a>Programversion

Information i programkontextfälten handlar alltid om programmet som skickar telemetrin. Programversion används för att analysera trendförändringar i programbeteendet och dess korrelation till distributionerna.

Max längd: 1024


## <a name="client-ip-address"></a>Klientens IP-adress

KLIENTenhetens IP-adress. Har stöd för IPv4 och IPv6. När telemetri skickas från en tjänst handlar platskontexten om användaren som initierade åtgärden i tjänsten. Application Insights extraherar geo-platsinformation från klient-IP och trunkerar den sedan. Klient-IP kan därför inte användas som identifierbar information för slutanvändare. 

Max längd: 46


## <a name="device-type"></a>Enhetstyp

Ursprungligen användes det här fältet för att ange vilken typ av enhet slutanvändaren av programmet använder. Idag används främst för att skilja JavaScript telemetri med enheten typ "Browser" från server-side telemetri med enheten typ "PC".

Max längd: 64


## <a name="operation-id"></a>Åtgärd id

En unik identifierare för rotåtgärden. Med den här identifieraren kan du gruppera telemetri mellan flera komponenter. Mer information finns i [telemetrikorrelationen.](../../azure-monitor/app/correlation.md) Åtgärds-ID:t skapas antingen av en begäran eller en sidvy. Alla andra telemetri anger det här fältet till värdet för den innehållande begäran eller sidvyn. 

Max längd: 128


## <a name="parent-operation-id"></a>Överordnat åtgärds-ID

Den unika identifieraren för telemetriobjektets närmaste överordnade. Mer information finns i [telemetrikorrelationen.](../../azure-monitor/app/correlation.md)

Max längd: 128


## <a name="operation-name"></a>Åtgärdsnamn

Namnet (gruppen) för operationen. Åtgärdsnamnet skapas antingen av en begäran eller en sidvy. Alla andra telemetriobjekt anger det här fältet till värdet för den innehållande begäran eller sidvyn. Operationsnamn används för att hitta alla telemetriartiklar för en grupp operationer (till exempel GET Home/Index). Den här sammanhangsegenskapen används för att svara på frågor som "vilka är de typiska undantagen som genereras på den här sidan".

Max längd: 1024


## <a name="synthetic-source-of-the-operation"></a>Syntetisk källa för operationen

Namn på syntetisk källa. Viss telemetri från programmet kan representera syntetisk trafik. Det kan vara sökrobot som indexerar webbplatsen, platstillgänglighetstester eller spår från diagnostikbibliotek som Application Insights SDK själv.

Max längd: 1024


## <a name="session-id"></a>Sessions-ID

Sessions-ID - instansen av användarens interaktion med appen. Information i sessionskontextfälten handlar alltid om slutanvändaren. När telemetri skickas från en tjänst handlar sessionskontexten om användaren som initierade åtgärden i tjänsten.

Max längd: 64


## <a name="anonymous-user-id"></a>Anonymt användar-ID

Anonymt användar-ID. Representerar slutanvändaren av programmet. När telemetri skickas från en tjänst handlar användarkontexten om användaren som initierade åtgärden i tjänsten.

[Sampling](../../azure-monitor/app/sampling.md) är en av teknikerna för att minimera mängden insamlad telemetri. Samplingsalgoritmen försöker antingen ta prov på in eller ut all korrelerad telemetri. Anonymt användar-ID används för urvalspoänggenerering. Så anonym användar-ID bör vara ett slumpmässigt tillräckligt värde. 

Använda anonymt användar-ID för att lagra användarnamn är ett missbruk av fältet. Använd äkta användar-ID.

Max längd: 128


## <a name="authenticated-user-id"></a>Äkta användar-ID

Autentiserat användar-ID. Motsatsen till anonymt användar-ID, det här fältet representerar användaren med ett eget namn. Eftersom dess PII-information samlas den inte in som standard av de flesta SDK.

Max längd: 1024


## <a name="account-id"></a>Konto-ID

I program med flera innehavare är detta konto-ID eller namn, som användaren agerar med. Exempel kan vara prenumerations-ID för Azure-portal eller bloggnamn för en bloggplattform.

Max längd: 1024


## <a name="cloud-role"></a>Molnroll

Namn på den roll som programmet är en del av. Mappar direkt till rollnamnet i azure. Kan också användas för att särskilja mikrotjänster, som ingår i ett enda program.

Max längd: 256


## <a name="cloud-role-instance"></a>Molnrollinstans

Namn på den instans där programmet körs. Datornamn för lokalt förekomstnamn för Azure.

Max längd: 256


## <a name="internal-sdk-version"></a>Internt: SDK-version

SDK-version. Mer information [finns i den här artikeln.](https://github.com/microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/SDK-VERSIONS.md)

Max längd: 64


## <a name="internal-node-name"></a>Internt: Nodnamn

Det här fältet representerar nodnamnet som används för fakturering. Använd den för att åsidosätta standardidentifiering av noder.

Max längd: 256


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [utökar och filtrerar telemetri](../../azure-monitor/app/api-filtering-sampling.md).
- Se [datamodell](data-model.md) för programinsiktstyper och datamodell.
- Kolla in [standardkonfiguration](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)för samlingskontextegenskaper .
