---
title: Så här integrerar du Azure API Management med Azure Application Insights | Microsoft Docs
description: Lär dig hur du loggar och visar händelser från Azure API Management i Azure Application insikter.
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
ms.openlocfilehash: 10e8edcd3a1e781866eaee2cbe48d1536dbc1229
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073584"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Så här integrerar du Azure API Management med Azure Application Insights

Azure API Management möjliggör enkel integrering med Azure Application Insights – en utöknings bar tjänst för webbutvecklare som skapar och hanterar appar på flera plattformar. Den här guiden går igenom alla steg i denna integrering och beskriver strategier för att minska prestanda påverkan på din API Management tjänst instans.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här guiden måste du ha en Azure API Management-instans. Om du inte har någon, slutför du [själv studie kursen](get-started-create-service-instance.md) först.

## <a name="create-an-azure-application-insights-instance"></a>Skapa en instans av Azure Application insikter

Innan du kan använda Azure Application insikter måste du först skapa en instans av tjänsten.

1. Öppna **Azure Portal** och gå till **Application Insights**.  
    ![Skapa App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Klicka på **+ Lägg till**.  
    ![Skapa App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Fyll i formuläret. Välj **Allmänt** som **program typ**.
4. Klicka på **Skapa**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Skapa en anslutning mellan Azure Application Insights och Azure API Management-tjänstinstans

1. Gå till **Azure API Management Service-** instansen i **Azure Portal**.
2. Välj **Application Insights** på menyn till vänster.
3. Klicka på **+ Lägg till**.  
    ![App Insights-loggning](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Välj den tidigare skapade **Application Insights** -instansen och ange en kort beskrivning.
5. Klicka på **Skapa**.
6. Du har precis skapat en Azure Application Insights-logg med en Instrumentation-nyckel. Den bör nu visas i listan.  
    ![App Insights-loggning](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> Efter scenen skapas en [loggad](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate) entitet i API Management-instansen som innehåller Instrumentation-nyckeln för Application Insights-instansen.

## <a name="enable-application-insights-logging-for-your-api"></a>Aktivera Application Insights loggning för ditt API

1. Gå till **Azure API Management Service-** instansen i **Azure Portal**.
2. Välj **API:er** i menyn till vänster.
3. Klicka på ditt API, i det här fallet **demo konferens-API**.
4. Gå till fliken **Inställningar** från det översta fältet.
5. Rulla ned till avsnittet **diagnostiska loggar** .  
    ![App Insights-loggning](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Markera kryss rutan **Aktivera** .
7. Välj din anslutna logg i list rutan **destination** .
8. Indata **100** som **sampling (%)** och kryssa i kryss rutan **Logga alltid in fel** .
9. Klicka på **Spara**.

> [!WARNING]
> Att åsidosätta standardvärdet **0** i fältet **första byte i brödtext** kan avsevärt minska prestandan för dina API: er.

> [!NOTE]
> Från scenen skapas en [diagnostisk](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/diagnostic/createorupdate) entitet med namnet "applicationinsights" på API-nivå.

| Inställningsnamn                        | Värdetyp                        | Beskrivning                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aktivera                              | boolean                           | Anger om loggning av detta API är aktiverat.                                                                                                                                                                                                                                                                                                |
| Mål                         | Azure Application Insights-loggning | Anger Azure Application insikts logg som ska användas                                                                                                                                                                                                                                                                                           |
| Sampling (%)                        | decimal                           | Värden från 0 till 100 (procent). <br/> Anger hur många procent av begär Anden som ska loggas till Azure Application insikter. 0% sampling innebär att noll begär Anden loggas, medan 100% sampling innebär att alla begär Anden loggas. <br/> Den här inställningen används för att minska prestanda konsekvenserna av loggnings begär anden till Azure Application insikter (se avsnittet nedan). |
| Logga alltid fel                   | boolean                           | Om den här inställningen väljs loggas alla avbrott till Azure Application insikter, oavsett inställningen för **sampling** .                                                                                                                                                                                                                  |
| Grundläggande alternativ: Huvuden              | list                              | Anger de huvuden som ska loggas i Azure Application insikter om begär Anden och svar.  Standard: inga huvuden loggas.                                                                                                                                                                                                             |
| Grundläggande alternativ: Första byte i brödtext  | integer                           | Anger hur många första byte i bröd texten som loggas för att Azure Application insikter om begär Anden och svar.  Standard: bröd texten loggas inte.                                                                                                                                                                                              |
| Avancerade alternativ: Frontend-begäran  |                                   | Anger om och hur *frontend-begäranden* ska loggas för att Azure Application insikter. *Frontend-begäran* är en inkommande begäran till Azure API Management-tjänsten.                                                                                                                                                                        |
| Avancerade alternativ: Frontend-svar |                                   | Anger om och hur *klient delens svar* ska loggas för att Azure Application insikter. *Frontend-svar* är ett utgående svar från Azure API Management-tjänsten.                                                                                                                                                                   |
| Avancerade alternativ: Backend-begäran   |                                   | Anger om och hur *Server dels begär Anden* ska loggas för att Azure Application insikter. *Backend-begäran* är en begäran utgående från Azure API Management-tjänsten.                                                                                                                                                                        |
| Avancerade alternativ: Server dels svar  |                                   | Anger om och hur *Server dels svar* ska loggas för att Azure Application insikter. *Server dels svar* är ett inkommande svar till Azure API Management-tjänsten.                                                                                                                                                                       |

> [!NOTE]
> Du kan ange loggar på olika nivåer – enskild API-logg eller en loggare för alla API: er.
>  
> Om du anger båda:
> + om de är olika loggar, kommer båda att användas (flera Plex-loggar).
> + om de är samma loggar men har olika inställningar, åsidosätter det en för Single API (mer detaljerad nivå) den för alla API: er.

## <a name="what-data-is-added-to-azure-application-insights"></a>Vilka data som läggs till i Azure Application Insights

Azure Application insikter tar emot:

+ *Begär* telemetri-objekt, för varje inkommande begäran (*frontend-begäran*, frontend- *svar*).
+ Objekt för *beroende* telemetri, för alla begär Anden som vidarebefordras till en backend-tjänst (*backend-begäran*, *Server dels svar*)
+ Objekt för *undantag* av telemetri, för varje misslyckad begäran.

En misslyckad begäran är en begäran som:

+ misslyckades på grund av en stängd klient anslutning eller
+ utlöst ett *On-Error* -avsnitt i API-principerna, eller
+ har svars kodens HTTP-statuskod som matchar 4xx eller 5xx.

## <a name="performance-implications-and-log-sampling"></a>Prestanda konsekvenser och logg sampling

> [!WARNING]
> Loggning av alla händelser kan ha allvarliga prestanda effekter, beroende på antalet inkommande begär Anden.

Med hjälp av interna belastnings test gjorde aktivering av den här funktionen en 40%-50% minskning i data flödet när begär ande frekvensen överskrider 1 000 begär Anden per sekund. Azure Application Insights är utformat för att kunna använda statistisk analys för att utvärdera programmets prestanda. Den är inte avsedd att vara ett gransknings system och passar inte för att logga varje enskild begäran för API: er med hög volym.

Du kan ändra antalet begär Anden som loggas genom att justera **samplings** inställningen (se stegen ovan). Värdet 100% innebär att alla begär Anden loggas, medan 0% inte visar någon loggning alls. **Samplingen** bidrar till att minska antalet telemetri, vilket effektivt förhindrar betydande prestanda försämring, samtidigt som du fortfarande har till gång till inloggnings funktionerna.

Att hoppa över loggning av rubriker och brödtext för förfrågningar och svar har också positiv påverkan på att minska prestanda problemen.

## <a name="video"></a>Video

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Nästa steg

+ Läs mer om [Azure Application](https://docs.microsoft.com/azure/application-insights/)insikter.
+ Överväg att [Logga med Azure Event Hubs](api-management-howto-log-event-hubs.md).
