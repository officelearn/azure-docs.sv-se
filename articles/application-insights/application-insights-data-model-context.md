---
title: "Datamodell för Azure-program insikter telemetri - telemetri kontexten | Microsoft Docs"
description: Application Insights telemetri kontexten datamodellen
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/15/2017
ms.author: sergkanz
ms.openlocfilehash: d6a0cad8bda6ca68aa691867e84f540c5ac9f6f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="telemetry-context-application-insights-data-model"></a>Telemetri kontext: Application Insights-datamodell

Varje telemetri-objekt kan ha ett starkt typifierad kontexten fält. Samtliga fält gör det möjligt för ett specifikt scenario för övervakning. Använda samlingen med anpassade egenskaper för att lagra anpassade eller programspecifika relevant information.


##<a name="application-version"></a>Programversion

Information i fälten programmet kontext är alltid om programmet som skickar telemetrin. Programversion används för att analysera trend ändringar i programmets beteende och dess korrelation till distributioner.

Maxlängd: 1024


##<a name="client-ip-address"></a>Klientens IP-adress

IP-adressen för klientenheten. IPv4 och IPv6 stöds. När telemetri som skickas från en tjänst, är plats-kontexten om användaren som initierade igen i tjänsten. Application Insights extrahera informationen geografiska plats från klientens IP-Adressen och sedan trunkera den. Så att klientens IP-Adressen ensamt kan inte användas som slutanvändaren identifierbar information. 

Maxlängd: 46


##<a name="device-type"></a>Typ av enhet

Det här fältet har ursprungligen används för att ange vilken typ av enhet som används av slutanvändaren av programmet. Idag används huvudsakligen för att skilja JavaScript telemetri med typ av enhet ange webbläsare om du från serversidan telemetri med enheten ”dator”.

Maxlängd: 64


##<a name="operation-id"></a>Åtgärds-id

En unik identifierare för rot-åtgärden. Den här identifieraren kan gruppen telemetri över flera komponenter. Se [telemetri korrelation](application-insights-correlation.md) mer information. Åtgärds-id skapas av en begäran eller vyn sida. All telemetri anger det här fältet till värdet för vyn som innehåller begäran eller sidan. 

Maxlängd: 128


##<a name="parent-operation-id"></a>Överordnad åtgärds-ID

Unik identifierare för telemetri objektets omedelbart överordnade objekt. Se [telemetri korrelation](application-insights-correlation.md) mer information.

Maxlängd: 128


##<a name="operation-name"></a>Åtgärdsnamn

Namnet (gruppering) av åtgärden. Åtgärdsnamnet har skapats med en begäran eller vyn sida. Alla andra objekt i telemetri ange fältet till värdet för vyn som innehåller begäran eller sidan. Åtgärdsnamnet används för att söka efter alla objekt som telemetri för en grupp av åtgärder (till exempel ' GET-Home/Index'). Den här kontexten egenskapen används för att besvara frågor som ”vad är vanliga undantag på den här sidan”.

Maxlängd: 1024


##<a name="synthetic-source-of-the-operation"></a>Syntetiska källan för åtgärden

Namnet på syntetiska källa. Vissa telemetri från programmet kan representera syntetisk trafik. Det kan vara web crawler indexering webbplats, tillgänglighetstester för webbplatsen eller spår från diagnostik bibliotek som Application Insights SDK sig själv.

Maxlängd: 1024


##<a name="session-id"></a>Sessions-id

Sessions-ID - instans av användarinteraktion med appen. Information i fälten session kontext är alltid om slutanvändaren. När telemetri som skickas från en tjänst, är sessionskontexten om användaren som initierade igen i tjänsten.

Maxlängd: 64


##<a name="anonymous-user-id"></a>Anonym användar-id

Id för anonyma användare. Representerar slutanvändaren av programmet. När telemetri som skickas från en tjänst, är användarkontexten om användaren som initierade igen i tjänsten.

[Provtagning](app-insights-sampling.md) är en av metoderna för att minska mängden insamlade telemetri. Sampling algoritmen försöker antingen exempel in eller ut all korrelerad telemetri. Anonym användar-id används för provtagning poäng generation. Så att anonyma användar-id måste vara ett tillräckligt slumpmässiga värde. 

Använder anonym användar-id för att lagra användarnamnet är ett missbruk av fältet. Använd autentiserad användar-id.

Maxlängd: 128


##<a name="authenticated-user-id"></a>Autentiserat användar-id

Autentiserat användar-id. Motsatsen till anonyma användar-id i det här fältet motsvarar användaren med ett eget namn. Eftersom dess PII-information den har inte samlats in som standard av de flesta SDK.

Maxlängd: 1024


##<a name="account-id"></a>Konto-id

I program med flera klienter är konto-ID eller namnet som användaren fungerar med. Exempel kanske prenumerations-ID för Azure-portalen eller blogg bloggar plattform.

Maxlängd: 1024


##<a name="cloud-role"></a>Molnet roll

Namnet på rollen som programmet tillhör. Mappar direkt till rollnamn i azure. Kan också användas för att skilja micro tjänster som ingår i ett enda program.

Maxlängd: 256


##<a name="cloud-role-instance"></a>Molnet rollinstans

Namnet på den instans där programmet körs. Datornamnet för lokal instansnamn för Azure.

Maxlängd: 256


##<a name="internal-sdk-version"></a>Internt: SDK-version

SDK-version. Se https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification information.

Maxlängd: 64


##<a name="internal-node-name"></a>Internt: Nodnamnet

Det här fältet motsvarar den nodnamn som används för fakturering. Används för att åsidosätta standard identifieringen av noder.

Maxlängd: 256


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [utöka och filtrera telemetri](app-insights-api-filtering-sampling.md).
- Se [datamodellen](application-insights-data-model.md) för Application Insights typer och modell.
- Kolla standard kontexten egenskapssamlingen [configuration](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet).
