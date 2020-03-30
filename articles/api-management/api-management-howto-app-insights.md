---
title: Integrera Azure API Management med Azure Application Insights
titleSuffix: Azure API Management
description: Lär dig hur du loggar och visar händelser från Azure API Management i Azure Application Insights.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 12aeea8393a00d7d2662c826f847265bdbdc0119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442717"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Så integrerar du Azure API Management med Azure Application Insights

Azure API Management möjliggör enkel integrering med Azure Application Insights - en utökningsbar tjänst för webbutvecklare som skapar och hanterar appar på flera plattformar. Den här guiden går igenom varje steg i sådan integration och beskriver strategier för att minska prestandapåverkan på din API Management-tjänstinstans.

## <a name="prerequisites"></a>Krav

Om du vill följa den här guiden måste du ha en Azure API Management-instans. Om du inte har en, slutföra [handledningen](get-started-create-service-instance.md) först.

## <a name="create-an-azure-application-insights-instance"></a>Skapa en Azure Application Insights-instans

Innan du kan använda Azure Application Insights måste du först skapa en instans av tjänsten.

1. Öppna **Azure-portalen** och navigera till **Application Insights**.  
    ![App Insights skapa](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Klicka på **+ Lägg till**.  
    ![App Insights skapa](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Fyll i formuläret. Välj **Allmänt** som **programtyp**.
4. Klicka på **Skapa**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Skapa en anslutning mellan Azure Application Insights och Azure API Management-tjänstinstans

1. Navigera till din **Azure API Management-tjänstinstans** i **Azure-portalen**.
2. Välj **Programstatistik** på menyn till vänster.
3. Klicka på **+ Lägg till**.  
    ![Loggar för App Insights](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Välj den tidigare skapade **Application Insights-instansen** och ge en kort beskrivning.
5. Klicka på **Skapa**.
6. Du har just skapat en Azure Application Insights-logger med en instrumenteringsnyckel. Det bör nu visas i listan.  
    ![Loggar för App Insights](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> Bakom scenen skapas en [Logger-entitet](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate) i din API Management-instans, som innehåller instrumenteringsnyckeln för application insights-instansen.

## <a name="enable-application-insights-logging-for-your-api"></a>Aktivera application insights-loggning för ditt API

1. Navigera till din **Azure API Management-tjänstinstans** i **Azure-portalen**.
2. Välj **API:er** i menyn till vänster.
3. Klicka på ditt API, i det här fallet **Demo Conference API**.
4. Gå till fliken **Inställningar** i det övre fältet.
5. Bläddra ned till avsnittet **Diagnostikloggar.**  
    ![Loggar för App Insights](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Markera rutan **Aktivera.**
7. Välj din bifogade logger i rullgardinsmenyn **Destination.**
8. Mata in **100** som **sampling (%)** och markera kryssrutan **Logga alltid fel.**
9. Klicka på **Spara**.

> [!WARNING]
> Om du åsidosätter standardvärdet **0** i fältet **Första byte i brödtexten** kan avsevärt försämra prestanda för dina API:er.

> [!NOTE]
> Bakom scenen skapas en [diagnostikentitet](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/diagnostic/createorupdate) med namnet applicationinsights på API-nivå.

| Inställningsnamn                        | Värdetyp                        | Beskrivning                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aktivera                              | boolean                           | Anger om loggning av det här API:et är aktiverat.                                                                                                                                                                                                                                                                                                |
| Mål                         | Logger för Azure Application Insights | Anger loggern för Azure Application Insights som ska användas                                                                                                                                                                                                                                                                                           |
| Provtagning (%)                        | decimal                           | Värden från 0 till 100 (procent). <br/> Anger hur stor procentandel av begäranden som ska loggas till Azure Application Insights. 0% sampling innebär noll begäranden loggade, medan 100% sampling innebär alla registrerade begäranden. <br/> Den här inställningen används för att minska prestandakonsekvenserna av loggningsbegäranden till Azure Application Insights (se avsnittet nedan). |
| Logga alltid fel                   | boolean                           | Om den här inställningen är markerad loggas alla fel i Azure Application Insights, oavsett **samplingsinställningen.**                                                                                                                                                                                                                  |
| Grundläggande alternativ: Rubriker              | lista                              | Anger de rubriker som ska loggas till Azure Application Insights för begäranden och svar.  Standard: inga rubriker loggas.                                                                                                                                                                                                             |
| Grundläggande alternativ: Första byte av kroppen  | heltal                           | Anger hur många första byte av brödtexten som loggas till Azure Application Insights för begäranden och svar.  Standard: brödtexten är inte inloggad.                                                                                                                                                                                                    |
| Avancerade alternativ: Verbosity         |                                   | Anger verbositetsnivån. Endast anpassade spårningar med högre allvarlighetsgrad loggas. Standard: Information.                                                                                                                                                                                                                               |
| Avancerade alternativ: Förhandsbegäran  |                                   | Anger om och hur *klientdelsbegäranden* loggas till Azure Application Insights. *Klientbegäran* är en begäran som inkommande till Azure API Management-tjänsten.                                                                                                                                                                        |
| Avancerade alternativ: Frontend-svar |                                   | Anger om och hur *frontend-svar* loggas till Azure Application Insights. *Frontend-svar* är ett svar som utgående från Azure API Management-tjänsten.                                                                                                                                                                   |
| Avancerade alternativ: Begäran om bakåtsträvning   |                                   | Anger om och hur *backend-begäranden* loggas till Azure Application Insights. *Serverningsbegäran* är en utgående begäran från Azure API Management-tjänsten.                                                                                                                                                                        |
| Avancerade alternativ: Backend-svar  |                                   | Anger om och hur *backend-svar* loggas till Azure Application Insights. *Serverningssvar* är ett svar som inkommande till Azure API Management-tjänsten.                                                                                                                                                                       |

> [!NOTE]
> Du kan ange loggers på olika nivåer - en API logger eller en logger för alla API: er.
>  
> Om du anger båda:
> + om de är olika loggers, då båda kommer att användas (multiplexering loggar),
> + Om de är samma loggers men har olika inställningar, då den för enda API (mer detaljerad nivå) kommer att åsidosätta en för alla API: er.

## <a name="what-data-is-added-to-azure-application-insights"></a>Vilka data läggs till i Azure Application Insights

Azure Application Insights tar emot:

+ *Begär* telemetriobjekt för varje inkommande begäran *(klientdelsbegäran*, *frontend-svar),*
+ *Beroendetelemetriobjekt,* för varje begäran som vidarebefordras till en*backend-tjänst (backend-begäran,* *backend-svar),*
+ *Undantag* telemetri objekt, för varje misslyckad begäran.

En misslyckad begäran är en begäran som:

+ misslyckades på grund av en stängd klientanslutning, eller
+ utlöste ett avsnitt på fel i *API-principerna,* eller
+ har ett svar HTTP-statuskod som matchar 4xx eller 5xx.

## <a name="performance-implications-and-log-sampling"></a>Prestandakonsekvenser och loggprovtagning

> [!WARNING]
> Om du loggar alla händelser kan det få allvarliga prestandakonsekvenser, beroende på antalet inkommande begäranden.

Baserat på interna belastningstester orsakade aktivering av den här funktionen en 40-50% minskning av dataflödet när begäranden översteg 1 000 begäranden per sekund. Azure Application Insights är utformat för att använda statistisk analys för att bedöma programprestanda. Det är inte avsett att vara ett revisionssystem och lämpar sig inte för loggning av varje enskild begäran om api:er med stora volymer.

Du kan ändra antalet begäranden som loggas genom att justera **samplingsinställningen** (se stegen ovan). Värde 100 % innebär att alla begäranden loggas, medan 0 % inte återspeglar någon loggning alls. **Sampling** bidrar till att minska volymen av telemetri, effektivt förhindra betydande prestandaförsämring, samtidigt som fördelarna med loggning.

Hoppa över loggning av rubriker och brödtext av begäranden och svar kommer också att ha en positiv inverkan på att lindra prestandaproblem.

## <a name="video"></a>Video

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Nästa steg

+ Läs mer om [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/).
+ Överväg [att logga med Azure Event Hubs](api-management-howto-log-event-hubs.md).
