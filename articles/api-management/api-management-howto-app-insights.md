---
title: Hur du integrerar Azure API Management med Azure Application Insights | Microsoft Docs
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
ms.openlocfilehash: 69f36773b702d9f0059e0cd27dbb864ccd7f7b2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60527622"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Hur du integrerar Azure API Management med Azure Application Insights

Azure API Management kan för enkel integrering med Azure Application Insights - tjänstens extensible för webbutvecklare att skapa och hantera appar på flera plattformar. Den här guiden beskriver varje steg på sådana integrering och beskrivs strategier för att minska prestandaförsämring på din API Management-tjänstinstans.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill följa den här guiden behöver du en Azure API Management-instans. Om du inte har någon kan slutföra den [självstudien](get-started-create-service-instance.md) första.

## <a name="create-an-azure-application-insights-instance"></a>Skapa en Azure Application Insights-instans

Innan du kan använda Azure Application Insights, måste du först skapa en instans av tjänsten.

1. Öppna den **Azure-portalen** och gå till **Application Insights**.  
    ![Skapa App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Klicka på **+ Lägg till**.  
    ![Skapa App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Fyll i formuläret. Välj **Allmänt** som den **programtyp**.
4. Klicka på **Skapa**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Skapa en anslutning mellan Azure Application Insights och Azure API Management-tjänstinstans

1. Gå till din **Azure API Management-tjänstinstans** i den **Azure-portalen**.
2. Välj **Application Insights** på menyn till vänster.
3. Klicka på **+ Lägg till**.  
    ![App Insights loggare](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Välj den tidigare skapade **Application Insights** instans och ange en kort beskrivning.
5. Klicka på **Skapa**.
6. Du har just skapat ett Azure Application Insights-loggaren med en instrumenteringsnyckel. Det bör nu visas i listan.  
    ![App Insights loggare](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> Bakom scenen, en [loggaren](https://docs.microsoft.com/rest/api/apimanagement/logger/createorupdate) entitet skapas i din API Management-instans som innehåller Instrumenteringsnyckeln för Application Insights-instans.

## <a name="enable-application-insights-logging-for-your-api"></a>Aktivera loggning för Application Insights för ditt API

1. Gå till din **Azure API Management-tjänstinstans** i den **Azure-portalen**.
2. Välj **API:er** i menyn till vänster.
3. Klicka på ditt API i det här fallet **Demokonferens-API**.
4. Gå till den **inställningar** fliken från det översta fältet.
5. Rulla ned till den **diagnostikloggar** avsnittet.  
    ![App Insights loggare](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Kontrollera den **aktivera** box.
7. Välj din bifogade loggaren i den **mål** listrutan.
8. Indata **100** som **Sampling (%)** och markera den **alltid logga fel** kryssrutan.
9. Klicka på **Spara**.

> [!WARNING]
> Åsidosätta standardvärdet **0** i den **första byten i brödtexten** fältet kan avsevärt minska prestandan för dina API: er.

> [!NOTE]
> Bakom scenen, en [diagnostiska](https://docs.microsoft.com/rest/api/apimanagement/diagnostic/createorupdate) entitet med namnet 'applicationinsights' skapas på API-nivå.

| Inställningsnamn                        | Värdetyp                        | Beskrivning                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aktivera                              | boolesk                           | Anger om detta API har aktiverats.                                                                                                                                                                                                                                                                                                |
| Mål                         | Azure Application Insights-logg | Anger Azure Application Insights loggaren som ska användas                                                                                                                                                                                                                                                                                           |
| Sampling (%)                        | decimal                           | Värden mellan 0 och 100 (procent). <br/> Anger vilken procentandel av förfrågningar kommer att loggas till Azure Application Insights. 0% sampling innebär noll förfrågningar loggas, medan sampling med 100% innebär att alla begäranden som loggats. <br/> Den här inställningen används för att minska prestanda följderna av att logga begäranden till Azure Application Insights (se avsnittet nedan). |
| Alltid logga fel                   | boolesk                           | Om den här inställningen väljs alla fel loggas till Azure Application Insights, oavsett den **Sampling** inställningen.                                                                                                                                                                                                                  |
| Grundläggande alternativ: Rubriker              | lista                              | Anger sidhuvuden som kommer att loggas till Azure Application Insights för begäranden och svar.  Standard: inga rubriker loggas.                                                                                                                                                                                                             |
| Grundläggande alternativ: Första byten i brödtexten  | heltal                           | Anger hur många första byten i texten som loggas i Azure Application Insights för begäranden och svar.  Standard: brödtext loggas inte.                                                                                                                                                                                              |
| Avancerade alternativ: Frontend-begäran  |                                   | Anger om och hur *klientdel begäranden* loggas till Azure Application Insights. *Frontend-begäran* är en begäran inkommande till Azure API Management-tjänsten.                                                                                                                                                                        |
| Avancerade alternativ: Frontend-svar |                                   | Anger om och hur *klientdel svar* loggas till Azure Application Insights. *Frontend-svar* är utgående svar från Azure API Management-tjänsten.                                                                                                                                                                   |
| Avancerade alternativ: Backend-begäran   |                                   | Anger om och hur *serverdel begäranden* loggas till Azure Application Insights. *Backend-begäran* är en begäran som utgående från Azure API Management-tjänsten.                                                                                                                                                                        |
| Avancerade alternativ: Backend-svar  |                                   | Anger om och hur *backend-svar* loggas till Azure Application Insights. *Backend-svar* är ett svar inkommande till Azure API Management-tjänsten.                                                                                                                                                                       |

> [!NOTE]
> Du kan ange tangenttryckningar på olika nivåer – enkel API logger eller en loggare för alla API: er.
>  
> Om du anger båda:
> + Om de olika tangenttryckningar kan används båda (multiplexing loggar)
> + Om de är samma tangenttryckningar ha men olika inställningar och sedan en för enkel API (mer detaljerad nivå) att åsidosätta det för alla API: er.

## <a name="what-data-is-added-to-azure-application-insights"></a>Vilka data som har lagts till i Azure Application Insights

Azure Application Insights tar emot:

+ *Begäran* telemetriobjekt för varje inkommande begäran (*klientdel begäran*, *klientdel svar*),
+ *Beroende* telemetriobjekt för varje begäran som vidarebefordras till en serverdelstjänst (*serverdel begäran*, *backend-svar*),
+ *Undantag* telemetriobjekt för alla misslyckade begäranden.

En misslyckad begäran är en begäran som:

+ misslyckades på grund av en stängd klientanslutning eller
+ utlöses en *vid fel* avsnittet för API-principer, eller
+ har en svarskod HTTP-status matchande 4xx eller 5xx.

## <a name="performance-implications-and-log-sampling"></a>Prestanda och log sampling

> [!WARNING]
> Logga alla händelser kan ha allvarligt prestandaskäl, beroende på inkommande begäranden-hastighet.

Baserat på interna tester kan orsakade den här funktionen aktiveras en 40-50% av dataflöde när förfrågningar har överskridits 1 000 begäranden per sekund. Azure Application Insights har utformats för att använda statistiska analyser för att utvärdera prestanda för programmet. Det är inte avsedd att vara en granskning system och är inte lämpligt för loggning av varje enskild begäran för API: er för stora volymer.

Du kan ändra antalet begäranden som du loggar in genom att justera den **Sampling** inställning (se ovan). Värdet 100% innebär att alla förfrågningar loggas, medan 0% är medräknad ingen loggning alls. **Sampling** hjälper till att minska mängden telemetri som effektivt förhindrar märkbar försämring av prestanda, fortfarande sin fördelarna med loggning.

Hoppar över loggning av rubriker och brödtext för begäranden och svar har också positiv inverkan på lindra prestandaproblem.

## <a name="video"></a>Video

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Nästa steg

+ Läs mer om [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/).
+ Överväg att [loggning med Azure Event Hubs](api-management-howto-log-event-hubs.md).
