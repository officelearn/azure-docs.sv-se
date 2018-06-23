---
title: Integrera Azure API Management med Azure Application Insights | Microsoft Docs
description: Lär dig mer om att logga in och visa händelser från Azure API Management i Azure Application Insights.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 7740da505f7635944536252d60ec2c2039295975
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320585"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Integrera Azure API Management med Azure Application Insights

Azure API Management gör det möjligt för enkel integrering med Azure Application Insights - en utökningsbar tjänst för webbutvecklare bygga och hantera appar på flera plattformar. Den här guiden går igenom varje steg på sådana integrering och beskriver strategier för att minska inverkan på prestanda på din API Management service-instans.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha en Azure API Management-instans om du vill följa den här guiden. Om du inte har någon Slutför den [kursen](get-started-create-service-instance.md) första.

## <a name="create-an-azure-application-insights-instance"></a>Skapa en instans av Azure Application Insights

Innan du kan använda Azure Application Insights, måste du först skapa en instans av tjänsten.

1. Öppna den **Azure-portalen** och gå till **Programinsikter**.  
    ![Skapa App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Klicka på **+ Lägg till**.  
    ![Skapa App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Fylla i formuläret. Välj **allmänna** som den **programtyp**.
4. Klicka på **Skapa**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Skapa en anslutning mellan Azure Application Insights och Azure API Management service-instans

1. Navigera till din **Azure API Management service-instans** i den **Azure-portalen**.
2. Välj **Programinsikter** på menyn till vänster.
3. Klicka på **+ Lägg till**.  
    ![App Insights-logg](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Välj den tidigare skapade **Programinsikter** instansen och ange en kort beskrivning.
5. Klicka på **Skapa**.
6. Du har just skapat ett Azure Application Insights loggaren med instrumentation nyckel. Det bör nu visas i listan.  
    ![App Insights-logg](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

## <a name="enable-application-insights-logging-for-your-api"></a>Aktivera loggning för Application Insights för din API

1. Navigera till din **Azure API Management service-instans** i den **Azure-portalen**.
2. Välj **API: er** på menyn till vänster.
3. Klicka på din API i det här fallet **Demo konferens API**.
4. Gå till den **inställningar** fliken från översta raden.
5. Rulla ned till den **diagnostik loggar** avsnitt.  
    ![App Insights-logg](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Kontrollera den **aktivera** rutan.
7. Välj din bifogade loggaren i den **mål** listrutan.
8. Indata **100** som **samplingsfrekvensen (%)** och markera den **alltid logga fel** kryssrutan.
9. Indata **1024** i den **första byten av text** fältet.
10. Klicka på **Spara**.

| Inställningsnamn                        | Värdetyp                        | Beskrivning                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aktivera                              | boolesk                           | Anger om loggning för detta API är aktiverat.                                                                                                                                                                                                                                                                                                |
| Mål                         | Azure Application Insights-logg | Anger Azure Application Insights-logg som ska användas                                                                                                                                                                                                                                                                                           |
| Provtagning (%)                        | Decimal                           | Värden från 0 till 100 (procent). <br/> Anger vilken procentandel av förfrågningar kommer att loggas till Azure Application Insights. 0% provtagning innebär noll begäranden som loggas när provtagning 100% innebär att alla begäranden som loggas. <br/> Den här inställningen används för att minska prestanda följderna av att logga begäranden till Azure Application Insights (se avsnittet nedan). |
| Alltid logga fel                   | boolesk                           | Om den här inställningen är markerad alla fel loggas till Azure Application Insights, oberoende av den **provtagning** inställningen.                                                                                                                                                                                                                  |
| Grundläggande alternativ: rubriker              | lista                              | Anger sidhuvuden som kommer att loggas till Azure Application Insights för begäranden och -svar.  Standard: inga huvuden loggas.                                                                                                                                                                                                             |
| Grundläggande alternativ: Första byten av text  | heltal                           | Anger hur många första byten i brödtexten loggas till Azure Application Insights för begäranden och svar.  Standard: body är inte inloggad.                                                                                                                                                                                              |
| Avancerade alternativ: Frontend-begäran  |                                   | Anger om och hur *klientdel begäranden* loggas till Azure Application Insights. *Klientdel begäran* är en begäran inkommande till Azure API Management-tjänsten.                                                                                                                                                                        |
| Avancerade alternativ: Frontend-svar |                                   | Anger om och hur *klientdel svar* loggas till Azure Application Insights. *Klientdel svar* är utgående svar från Azure API Management-tjänsten.                                                                                                                                                                   |
| Avancerade alternativ: Backend-begäran   |                                   | Anger om och hur *backend begäranden* loggas till Azure Application Insights. *Backend-begäran* är en begäran om utgående från Azure API Management-tjänsten.                                                                                                                                                                        |
| Avancerade alternativ: Backend-svar  |                                   | Anger om och hur *backend svar* loggas till Azure Application Insights. *Backend-svar* är ett svar inkommande till Azure API Management-tjänsten.                                                                                                                                                                       |

> [!NOTE]
> Du kan ange loggare på olika nivåer - enda API-loggaren eller en logg för alla API: er.
>  
> Om du anger både:
> + Om de olika loggar tangenttryckningar kan används båda (multiplexering loggar)
> + Om de är samma loggare som har men olika inställningar och sedan ett enda API: t (mer detaljerad nivå) åsidosätter en för alla API: er.

## <a name="what-data-is-added-to-azure-application-insights"></a>Vilka data som har lagts till i Azure Application Insights

Azure Application Insights tar emot:

+ *Begäran* telemetri objekt för varje inkommande begäran (*klientdel begäran*, *klientdel svar*),
+ *Beroende* telemetri objekt för varje begäran som vidarebefordras till en backend-tjänst (*backend begäran*, *backend svar*),
+ *Undantag* telemetri objekt, för alla misslyckade begäranden.

En misslyckad begäran är en begäran som:

+ misslyckades på grund av en stängd klientanslutning eller
+ utlöses en *vid fel* avsnitt av API-principer eller
+ har en matchande 4xx för svar HTTP-status-kod eller 5xx.

## <a name="performance-implications-and-log-sampling"></a>Konsekvenser för prestanda och logg provtagning

> [!WARNING]
> Loggning av alla händelser kan ha allvarliga prestandaproblem konsekvenser, beroende på mottagningshastighet för begäranden.

Baserat på interna tester, den här funktionen aktiveras på grund en 40-50% minskning i dataflöde när begärandehastighet överskrider 1 000 begäranden per sekund. Azure Application Insights är utformad för statistiska analyser för att bedöma programmets prestanda. Det är inte avsedd att vara en audit-system och är inte lämpligt för loggning av varje enskild begäran för API: er för stora volymer.

Du kan ändra antalet begäranden som du loggar in genom att justera det **provtagning** inställning (se ovan). Värdet 100% innebär alla begäranden som loggas när 0% visar ingen loggning alls. **Provtagning** hjälper till att minska telemetrivolym effektivt förhindrar nedsatt prestanda, fortfarande sin fördelarna med loggning.

Hoppar över loggning av rubriker och brödtexten i begäran och svar har också positiva inverkan på lindra prestandaproblem.

## <a name="next-steps"></a>Nästa steg

+ Lär dig mer om [Azure Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/).
+ Överväg att [loggning med Händelsehubbar](api-management-howto-log-event-hubs.md).
