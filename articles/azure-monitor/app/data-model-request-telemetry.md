---
title: Datamodell för begäran telemetri - Azure Application Insights
description: Program Insights datamodell för begäran telemetri
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: d8a28063bf6780c3cace4ead81e289779b95eb9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671910"
---
# <a name="request-telemetry-application-insights-data-model"></a>Begär telemetri: Datamodell för Application Insights

Ett begärandetelemetriobjekt (i [Application Insights](../../azure-monitor/app/app-insights-overview.md)) representerar den logiska körningssekvensen som utlöses av en extern begäran till ditt program. Varje körning av begäran `ID` `url` identifieras av unika och innehåller alla körningsparametrar. Du kan gruppera `name` begäranden `source` efter logisk och definiera begäran. Kodkörning kan `success` resultera `fail` i eller `duration`och har en viss . Både framgång och fel avrättningar kan `resultCode`grupperas ytterligare av . Starttid för den begärandetelemetri som definierats på kuvertnivå.

Begär telemetri stöder standardutökningsmodellen med `properties` hjälp `measurements`av anpassade och .

## <a name="name"></a>Namn

Namnet på begäran representerar kodsökvägen som tagits för att bearbeta begäran. Lågt kardinalitetsvärde för att möjliggöra bättre gruppering av begäranden. För HTTP-begäranden representerar den HTTP-metoden och URL-sökvägsmallen som `GET /values/{id}` utan det faktiska `id` värdet.

Application Insights web SDK skickar begäran namn "som det är" när det gäller brevfall. Gruppering på användargränssnittet är `GET /Home/Index` skiftlägeskänslig så räknas separat från `GET /home/INDEX` även om de ofta resulterar i samma styrenhet och åtgärdskörning. Anledningen till detta är att webbadresser i allmänhet är [skiftlägeskänsliga](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). Du kanske vill se `404` om allt hände för webbadresserna som skrivits i versaler. Du kan läsa mer om begäran namn samling av ASP.NET Web SDK i [blogginlägget](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

Max längd: 1024 tecken

## <a name="id"></a>ID

Identifierare för en begäran samtalsinstans. Används för korrelation mellan begäran och andra telemetriobjekt. ID ska vara globalt unikt. Mer information finns på [korrelationssidan.](../../azure-monitor/app/correlation.md)

Max längd: 128 tecken

## <a name="url"></a>URL

Begär URL med alla frågesträngparametrar.

Max längd: 2048 tecken

## <a name="source"></a>Källa

Källa till begäran. Exempel är instrumenteringsnyckeln för den som ringer eller ip-adressen för den som ringer. Mer information finns på [korrelationssidan.](../../azure-monitor/app/correlation.md)

Max längd: 1024 tecken

## <a name="duration"></a>Varaktighet

Begäran varaktighet i `DD.HH:MM:SS.MMMMMM`format: . Måste vara positivt `1000` och mindre än dagar. Det här fältet krävs eftersom begäran telemetri representerar operationen med början och slutet.

## <a name="response-code"></a>Svarskod

Resultatet av en begäran utförande. HTTP-statuskod för HTTP-begäranden. Det kan `HRESULT` vara värde- eller undantagstyp för andra typer av begäranden.

Max längd: 1024 tecken

## <a name="success"></a>Lyckades

Uppgift om lyckat eller misslyckat samtal. Det här fältet är obligatoriskt. När den inte `false` uttryckligen anges till - en begäran anses vara framgångsrik. Ange det `false` här värdet till om åtgärden avbröts av undantag eller returnerad felresultatkod.

För webbprogrammen definierar Application Insights en begäran som lyckad när svarskoden är mindre än `400` eller lika med `401`. Det finns dock fall när den här standardmappningen inte matchar programmets semantiska. Svarskoden `404` kan indikera "inga poster", som kan vara en del av det vanliga flödet. Det kan också tyda på en bruten länk. För de brutna länkarna kan du till och med implementera mer avancerad logik. Du kan markera brutna länkar som fel endast när dessa länkar finns på samma plats genom att analysera url referrer. Eller markera dem som fel när de nås från företagets mobilapplikation. På `301` samma `302` sätt och indikerar fel när den används från klienten som inte stöder omdirigering.

Delvis accepterat `206` innehåll kan tyda på ett fel på en övergripande begäran. Till exempel tar Application Insights-slutpunkten emot en batch med telemetriartiklar som en enda begäran. Den `206` returnerar när vissa artiklar i batchen inte har bearbetats. Ökande `206` hastighet indikerar ett problem som måste undersökas. Liknande logik `207` gäller för multistatus där framgången kan vara den värsta av separata svarskoder.

Du kan läsa mer om begäran resultatkod och statuskod i [blogginlägget](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Nästa steg

- [Skriv anpassad begäran telemetri](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Se [datamodell](data-model.md) för programinsiktstyper och datamodell.
- Lär dig hur du [konfigurerar ASP.NET Core-program](../../azure-monitor/app/asp-net.md) med Application Insights.
- Kolla in [plattformar](../../azure-monitor/app/platforms.md) som stöds av Application Insights.
