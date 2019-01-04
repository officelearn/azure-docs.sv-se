---
title: Datamodell för Azure Application Insights telemetri – telemetri kontext | Microsoft Docs
description: Datamodell för Application Insights telemetri kontext
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 7c1f47c9b88bd68b326b3c8923ba5b81d425c3e4
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015482"
---
# <a name="telemetry-context-application-insights-data-model"></a>Telemetri kontext: Application Insights-datamodell

Varje telemetriobjekt kan ha en starkt typifierad kontext-fält. Varje fält gör det möjligt för ett specifikt scenario för övervakning. Använda anpassade egenskaper-samlingen för att lagra anpassade eller programspecifika kontextinformation.


## <a name="application-version"></a>Programversion

Informationen i fälten programmet kontext är alltid om programmet som skickar telemetri. Programversion används för att analysera trend ändringar i programmets beteende och dess korrelation i distributionerna.

Maxlängd: 1024


## <a name="client-ip-address"></a>Klientens IP-adress

IP-adressen för klientenheten. IPv4 och IPv6 stöds. När telemetri skickas från en tjänst, är plats-kontexten om användaren som initierade åtgärden i tjänsten. Application Insights extrahera geografiska information från klientens IP-Adressen och sedan trunkera den. Så att klientens IP-adress ensamt kan inte användas som slutanvändaren identifierbar information. 

Maxlängd: 46


## <a name="device-type"></a>Enhetstyp

Det här fältet har ursprungligen används för att ange vilken typ av enhet med hjälp av slutanvändaren av programmet. Idag används främst för att skilja JavaScript telemetri med typ av enhet ange webbläsare om du från serversidan telemetri med enheten ”dator”.

Maxlängd: 64


## <a name="operation-id"></a>Åtgärds-ID

En unik identifierare för rotåtgärden. Den här identifieraren kan gruppen telemetri över flera komponenter. Se [telemetrikorrelation](../../azure-monitor/app/correlation.md) information. Åtgärds-id skapas av en begäran eller en Sidvisning. All telemetri anger det här fältet till värdet för den som innehåller förfrågan eller sidvisning. 

Maxlängd: 128


## <a name="parent-operation-id"></a>Överordnad åtgärds-ID

Den unika identifieraren för telemetri-objektets omedelbart överordnade objekt. Se [telemetrikorrelation](../../azure-monitor/app/correlation.md) information.

Maxlängd: 128


## <a name="operation-name"></a>Åtgärdsnamn

Namn (grupp) för åtgärden. Åtgärdens namn skapas av en begäran eller en Sidvisning. Alla andra objekt i telemetrin ange fältet till värdet för den som innehåller förfrågan eller sidvisning. Åtgärdens namn används för att söka efter alla objekt på telemetri för en grupp av åtgärder (till exempel ”GET Home/Index”). Den här Kontextegenskapen används för att besvara frågor som ”vad är de vanliga undantag på den här sidan”.

Maxlängd: 1024


## <a name="synthetic-source-of-the-operation"></a>Syntetisk källan för åtgärden

Namnet på syntetiska källa. Telemetri från programmet kan representera syntetisk trafik. Det kan vara web crawler indexering webbplats, tillgänglighetstester för platsen och spårningar från diagnostik-bibliotek som Application Insights SDK själva.

Maxlängd: 1024


## <a name="session-id"></a>Sessions-ID

Sessions-ID - instansen av användarens interaktion med appen. Informationen i fälten session kontext är alltid om användaren. När telemetri skickas från en tjänst är sessionskontexten om användaren som initierade åtgärden i tjänsten.

Maxlängd: 64


## <a name="anonymous-user-id"></a>Anonym användar-id

Anonym användar-id. Representerar slutanvändaren av programmet. När telemetri skickas från en tjänst, är användarkontexten om användaren som initierade åtgärden i tjänsten.

[Sampling](../../azure-monitor/app/sampling.md) är en av tekniker för att minska mängden insamlade telemetri. Samplingsalgoritmen försöker antingen exemplet in eller ut korrelerad telemetri. Anonym användar-id används för sampling poäng generation. Anonym användar-id bör vara ett tillräckligt slumpmässigt värde. 

Använder anonym användar-id för att lagra användarnamn är ett missbruk av fältet. Använd autentiserad användar-id.

Maxlängd: 128


## <a name="authenticated-user-id"></a>Autentiserat användar-id

Id för autentiserad användare. Motsatsen till anonym användar-id i det här fältet motsvarar användaren med ett eget namn. Sedan dess PII-information är det inte in som standard av de flesta SDK.

Maxlängd: 1024


## <a name="account-id"></a>Konto-ID

I program för flera innehavare är detta konto-ID eller namnet som användaren fungerar med. Exempel kanske prenumerations-ID för Azure-portalen eller blogg namn bloggplattform.

Maxlängd: 1024


## <a name="cloud-role"></a>Molnroll

Namnet på rollen programmet är en del av. Mappar direkt till rollnamn i azure. Kan också användas för att skilja mikrotjänster som ingår i ett enda program.

Maxlängd: 256


## <a name="cloud-role-instance"></a>Instans av molntjänstroll (klassisk)

Namnet på den instans där programmet körs. Namnet på den dator på plats, instansnamn för Azure.

Maxlängd: 256


## <a name="internal-sdk-version"></a>Internt: SDK-version

SDK-version. Se https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification information.

Maxlängd: 64


## <a name="internal-node-name"></a>Internt: Nodnamn

Det här fältet representerar namnet på noden som används för faktureringsändamål. Du kan använda den för att åsidosätta standard identifiering av noder.

Maxlängd: 256


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [utöka och filtrera telemetri](../../azure-monitor/app/api-filtering-sampling.md).
- Se [datamodellen](data-model.md) för Application Insights och modellen.
- Kolla in standard kontext egenskapssamlingen [configuration](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet).
