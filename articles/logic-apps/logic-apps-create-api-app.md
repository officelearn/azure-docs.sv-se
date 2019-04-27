---
title: 'Skapa webb-API: er och REST API: er för Azure Logic Apps | Microsoft Docs'
description: 'Skapa webb-API: er och REST API: er för att anropa dina API: er, tjänster eller system för system-integreringar i Azure Logic Apps'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.assetid: bd229179-7199-4aab-bae0-1baf072c7659
ms.date: 05/26/2017
ms.openlocfilehash: 620ede672d71338abeff5198fd5f94e92dc193d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681889"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Skapa anpassade API: er som du kan anropa från Azure Logic Apps

Även om Azure Logic Apps erbjuder [100 inbyggda anslutningsappar](../connectors/apis-list.md) att du kan använda i logikapparbetsflöden kan du anropa API: er, system och tjänster som inte är tillgängliga som kopplingar. Du kan skapa dina egna API: er som tillhandahåller åtgärder och utlösare som ska använda i logic apps. Här finns andra skäl till varför du kanske vill skapa dina egna API: er som du kan anropa från arbetsflöden i logikappar:

* Utöka dina aktuella system integrering och data arbetsflöden för dataintegrering.
* Hjälp kunder att använda tjänsten för att hantera professionella eller personliga uppgifter.
* Expandera den räckvidd, identifierbarhet och användning för din tjänst.

I princip kopplingar är web API: er som för anslutningsbara gränssnitt med hjälp av REST [Swagger metadataformat](https://swagger.io/specification/) för dokumentation och JSON som utbytesformat sina data. Eftersom kopplingar är REST API: er som kommunicerar via HTTP-slutpunkter, kan du använda valfritt språk som .NET, Java eller Node.js, för att skapa kopplingar. Du kan också vara värd för dina API: er på [Azure App Service](../app-service/overview.md), en platform-as-a-service (PaaS) som tillhandahåller ett av de bästa, enklaste och mest skalbara sätten som värd för API: et. 

För anpassade API: er att arbeta med logic apps, ditt API kan ge [ *åtgärder* ](./logic-apps-overview.md#logic-app-concepts) som utför olika uppgifter i logikappens arbetsflöde. Ditt API kan också fungera som en [ *utlösaren* ](./logic-apps-overview.md#logic-app-concepts) som startar en logikapparbetsflöde när nya data eller en händelse som uppfyller ett angivet villkor. Det här avsnittet beskrivs vanliga mönster som du kan följa för att skapa åtgärder och utlösare i ditt API, baserat på det beteende som du vill att ditt API för att tillhandahålla.

Du kan vara värd för dina API: er på [Azure App Service](../app-service/overview.md), en platform-as-a-service (PaaS) som tillhandahåller mycket skalbar och enkelt API som är värd för.

> [!TIP] 
> Även om du kan distribuera dina API: er som web apps måste du överväga att distribuera dina API: er som API apps, som kan göra ditt jobb enklare när du skapar, värd, och använda API: er i molnet och lokalt. Du behöver ändra någon kod i dina API: er, distribuera bara din kod till en API-app. Till exempel veta hur du skapar API apps som skapats med följande språk: 
> 
> * [ASP.NET](../app-service/app-service-web-get-started-dotnet.md). 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [PHP](../app-service/app-service-web-get-started-php.md)
> * [Python](../app-service/containers/quickstart-python.md)
> * [Ruby](../app-service/containers/quickstart-ruby.md)
>
> API-App-exempel som skapats för logic apps, finns det [Azure Logic Apps GitHub-lagringsplatsen](https://github.com/logicappsio) eller [blogg](https://aka.ms/logicappsblog).

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Hur skiljer sig anpassade API: er från anpassade anslutningsappar?

Anpassade API: er och [anpassade anslutningsappar](../logic-apps/custom-connector-overview.md) är webb-API: er som för anslutningsbara gränssnitt med hjälp av REST [Swagger metadataformat](https://swagger.io/specification/) för dokumentation och JSON som utbytesformat sina data. Och eftersom de här API: er och anslutningsappar är REST API: er som kommunicerar via HTTP-slutpunkter, du kan använda valfritt språk som .NET, Java eller Node.js, för att skapa anpassade API: er och anslutningsappar.

Anpassade API: er kan du anropa API: er som inte är kopplingar och ange slutpunkter som kan anropas med HTTP + Swagger, Azure API Management eller App Services. Anpassade anslutningsappar fungerar som anpassade API: er, men även ha dessa attribut:

* Registrerad som Logic Apps Connector resurser i Azure.
* Visas med ikoner tillsammans med Microsoft-hanterade anslutningsappar i Logic Apps Designer.
* Endast tillgängligt för kopplingarna författare och logic app-användare som har samma Azure Active Directory-klient och Azure-prenumeration i regionen där logic apps har distribuerats.

Du kan också välja registrerade anslutningar för Microsoft-certifiering. Den här processen verifierar att registrerade anslutningar uppfyller kriterierna för allmänt bruk och gör dessa anslutningsappar tillgängliga för användare i Microsoft Flow och Microsoft PowerApps.

Läs mer om anpassade anslutningsappar 

* [Översikt över anpassade anslutningsappar](../logic-apps/custom-connector-overview.md)
* [Skapa anpassade anslutningsappar från webb-API: er](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Registrera anpassade anslutningar i Azure Logic Apps](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>Användbara verktyg

Ett anpassat API som fungerar bäst med logic apps när API: et har också en [Swagger-dokument](https://swagger.io/specification/) som beskriver API: ets åtgärder och parametrar.
Många bibliotek som [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle), kan du automatiskt generera Swagger-filen åt dig. Om du vill kommentera Swagger-filen för visningsnamn, egenskapstyper och så vidare, du kan också använda [TRex](https://github.com/nihaue/TRex) så att din Swagger-fil som fungerar bra med logikappar.

<a name="actions"></a>

## <a name="action-patterns"></a>Åtgärd-mönster

För logic apps att utföra uppgifter, ditt anpassade API bör ge [ *åtgärder*](./logic-apps-overview.md#logic-app-concepts). Varje åtgärd i dina API: et mappas till en åtgärd. En grundläggande åtgärd är en domänkontrollant som accepterar HTTP-begäranden och returnerar HTTP-svar. Till exempel skickar en logikapp en HTTP-begäran till din webbapp eller API-app. Din app returnerar ett HTTP-svar, tillsammans med innehållet som logikappen kan bearbeta.

Du kan skriva en HTTP-begäran-metod i ditt API och beskriver den metoden i en Swagger-fil för en standardåtgärd. Du kan sedan anropa ditt API direkt med en [HTTP-åtgärd](../connectors/connectors-native-http.md) eller en [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) åtgärd. Som standard måste svar returneras inom den [tidsgränsen för begäran](./logic-apps-limits-and-config.md). 

![Standardåtgärd mönster](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> Så gör du en logikapp som väntar du medan ditt API är klar aktiviteter som längre körs ditt API kan den [asynkrona avsökningen mönstret](#async-pattern) eller [asynkrona webhook mönstret](#webhook-actions) som beskrivs i det här avsnittet. Föreställ dig processen för att begära en anpassad enkelt från en bagerier och för en liknelse som hjälper dig att visualisera dessa mönster på olika sätt. Avsökningen mönstret speglar beteendet där du anropar den bagerier och var tjugonde minut för att kontrollera om enkelt är klar. Webhook-mönstret speglar beteendet där den bagerier och frågar efter ditt telefonnummer så att de kan ringa dig när enkelt är klar.

Exempel finns i [Logic Apps GitHub-lagringsplatsen](https://github.com/logicappsio). Dessutom lär dig mer om [användningsmätning för åtgärder](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Utför tidskrävande uppgifter med mönstret avsökningen åtgärd

Att ha ditt API utföra uppgifter som kan köras längre än den [tidsgränsen för begäran](./logic-apps-limits-and-config.md), du kan använda mönstret för asynkrona avsökning. Det här mönstret har din API-gör fungerar i en separat tråd, men Behåll en aktiv anslutning till Logic Apps-motorn. På så sätt kan logikappen har ingen timeout eller Fortsätt med nästa steg i arbetsflödet innan ditt API slutar fungera.

Här är det allmänna mönstret:

1. Kontrollera att motorn vet att ditt API har accepterat och igång.
2. När motorn gör efterföljande förfrågningar för jobbets status, kan du motorn när ditt API är klar uppgiften.
3. Returnera relevanta data till motorn så att logikapparbetsflöde kan fortsätta.

<a name="bakery-polling-action"></a> Nu gäller den föregående bagerier och också mönstret avsökning och anta att du anropar en bagerier och och ordning en anpassad enkelt för leverans. Processen för att göra enkelt tar tid och du vill inte vänta på telefonen och den bagerier och fungerar på enkelt. Den bagerier och bekräftar din beställning och måste du anropa var tjugonde minut tills det enkelt. Efter 20 minuter skickar du anropar den bagerier och, men de talar om att din enkelt inte är klar och att du ska anropa i en annan 20 minuter. Den här processen fram och tillbaka fortsätter tills du anropar och den bagerier och talar om att din beställning är klar och levererar dina enkelt. 

Så vi mappa det här mönstret för avsökning tillbaka. Den bagerier och representerar ditt anpassade API medan du enkelt kunden, representerar Logic Apps-motorn. När motorn anropar ditt API med en begäran, bekräftar begäran ditt API och svarar med tidsintervallet när motorn kan kontrollera jobbstatus för. Motorn fortsätter att kontrollera jobbstatus tills ditt API svarar att jobbet är klart och returnerar data i logikappen som sedan vidare arbetsflöde. 

![Avsökningen åtgärd mönster](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Här är steg som är specifika för ditt API att följa, beskrivs ur API: er:

1. När din API hämtar en HTTP-begäran för att starta arbete, omedelbart returnera HTTP `202 ACCEPTED` svar med den `location` rubrik som beskrivs senare i det här steget. Svaret kan Logic Apps-motorn vet att ditt API har begäran, godkända nyttolast för begäran (indata) och bearbetar nu. 
   
   Den `202 ACCEPTED` bör svaret innehålla dessa huvuden:
   
   * *Krävs*: En `location` -huvud som anger den absoluta sökvägen till en URL där Logic Apps-motorn kan kontrollera jobbstatus för dina API: er

   * *Valfritt*: En `retry-after` -huvud som anger antalet sekunder som motorn ska vänta innan du söker i `location` URL: en för jobbets status. 

     Som standard kontrollerar motorn var 20: e sekund. Om du vill ange ett annat intervall, innehåller den `retry-after` rubrik och hur många sekunder tills nästa omröstningen.

2. När den angivna tiden går Logic Apps motorn enkäter den `location` URL: en för att kontrollera jobbstatus. Ditt API ska utföra de här kontrollerna och returnera dessa svar:
   
   * Om jobbet är klart, returnera HTTP `200 OK` svar, tillsammans med svarsnyttolasten (indata för nästa steg).

   * Om jobbet fortfarande bearbetar returnera ett annat HTTP `202 ACCEPTED` svar, men med samma sidhuvuden som det ursprungliga svaret.

När ditt API efter det här mönstret, har du inte gör någonting i arbetsflödet logikappsdefinitionen att fortsätta kontrollera jobbstatus. När motorn hämtar HTTP `202 ACCEPTED` svar och en giltig `location` rubrik, motorn respekterar asynkront mönster och kontrollerar de `location` rubriken tills ditt API returnerar ett icke-202-svar.

> [!TIP]
> Ett exempel asynkront mönster, granska [asynkrona controller svars-exemplet i GitHub](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Utför tidskrävande uppgifter med mönstret för webhook-åtgärd

Alternativt kan använda du webhook-mönstret för långvariga aktiviteter och asynkron bearbetning. Det här mönstret har logikappen pausa och vänta tills en ”motringning” från ditt API Slutför bearbetningen innan du fortsätter arbetsflöde. Den här motringning är en HTTP-POST som skickar ett meddelande till en URL när en händelse inträffar. 

<a name="bakery-webhook-action"></a> Nu gäller den föregående bagerier och också webhook-mönster och anta att du anropar en bagerier och och ordning en anpassad enkelt för leverans. Processen för att göra enkelt tar tid och du vill inte vänta på telefonen och den bagerier och fungerar på enkelt. Den bagerier och bekräftar din beställning, men den här tiden kan du ge dem ditt telefonnummer så att de kan ringa dig när enkelt är klar. Den här gången anger den bagerier och när beställningen är klar och levererar dina enkelt.

När vi mappa det här mönstret för webhook tillbaka representerar den bagerier och ditt anpassade API medan du enkelt kunden, representerar Logic Apps-motorn. Motorn anropar ditt API med en begäran och innehåller en ”återanrop”-URL.
När jobbet är klart, använder ditt API URL: en för att meddela motorn och returnera data till logikappen som sedan vidare arbetsflöde. 

Konfigurera två slutpunkter på styrenheten för det här mönstret: `subscribe` och `unsubscribe`

*  `subscribe` Slutpunkt: När körningen når ditt API-åtgärden i arbetsflödet, Logic Apps motorn anrop den `subscribe` slutpunkt. Det här steget gör logikappen att skapa en Motringnings-URL för ditt API lagrar och vänta sedan motringning från ditt API när arbetet är klart. Ditt API och sedan anropar tillbaka med en HTTP POST till URL: en och vidarebefordrar alla returnerat innehåll och rubriker som indata till logikappen.

* `unsubscribe` Slutpunkt: Om den logikapp-körningen har avbrutits, Logic Apps motorn anrop den `unsubscribe` slutpunkt. Ditt API kan sedan avregistrera Motringnings-URL och stoppa alla processer vid behov.

![Mönster för Webhook-åtgärd](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> Logic App Designer stöder för närvarande inte identifierande webhook-slutpunkter via Swagger. Så för att det här mönstret måste du lägga till en [ **Webhook** åtgärd](../connectors/connectors-native-webhook.md) och ange URL: en, rubriker och brödtext för din begäran. Se även [arbetsflödesåtgärder och utlösare](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Om du vill skicka in Motringnings-URL, kan du använda den `@listCallbackUrl()` arbetsflödesfunktion i någon av de tidigare fälten efter behov.

> [!TIP]
> Granska det för ett webhook mönster exempel [webhook-utlösare exemplet i GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Mönster för utlösare

Ditt anpassade API kan fungera som en [ *utlösaren* ](./logic-apps-overview.md#logic-app-concepts) som startar en logikapp när nya data eller en händelse som uppfyller ett angivet villkor. Den här utlösaren kan antingen Kontrollera regelbundet, eller vänta och lyssna efter nya data eller händelser vid tjänstens slutpunkt. Om nya data eller en händelse uppfyller de angivna villkoren, utlöses utlösaren och startar logikappen, som lyssnar på den utlösaren. Starta logikappar vis genom ditt API kan följa den [ *avsökningen utlösaren* ](#polling-triggers) eller [ *webhook-utlösaren* ](#webhook-triggers) mönster. Dessa mönster liknar deras motsvarigheter för [avsökningsåtgärder](#async-pattern) och [webhook-åtgärder](#webhook-actions). Dessutom lär dig mer om [användningsmätning för utlösare](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Söka efter nya data eller händelser regelbundet med mönstret avsökningen utlösare

En *avsökningen utlösaren* fungerar ungefär som den [avsökning åtgärd](#async-pattern) beskrivs tidigare i det här avsnittet. Logic Apps-motorn anropar regelbundet och kontrollerar utlösaren slutpunkten för nya data eller händelser. Om motorn hittar nya data eller en händelse som uppfyller dina villkor, utlöses utlösaren. Sedan skapar motorn en logikappinstans som bearbetar data som indata. 

![Avsökning utlösaren mönster](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Varje avsökning begäran räknas som en åtgärdskörning, även om inga logikappinstans skapas. För att förhindra bearbetar samma data flera gånger, bör utlösaren rensa data som redan har lästs in och skickats till logikappen.

Här följer specifika åtgärder för en avsökning utlösare, som beskrivs av API: er ur:

| Hitta nya data eller händelse?  | API-svar | 
| ------------------------- | ------------ |
| Hittade | Returnera ett HTTP `200 OK` status med svarsnyttolasten (indata för nästa steg). <br/>Det här svaret skapar en logikappinstans och startar arbetsflödet. | 
| Kunde inte hittas | Returnera ett HTTP `202 ACCEPTED` status med en `location` rubrik och en `retry-after` rubrik. <br/>För utlösare, den `location` rubriken ska också innehålla en `triggerState` frågeparameter som vanligtvis är ”timestamp”. Ditt API kan använda den här identifieraren för att spåra den senaste gången som logikappen utlöstes. | 
||| 

För att regelbundet kontrollera din tjänst för nya filer, till exempel kan du skapa en avsökning utlösare som har dessa beteenden:

| Begäran innehåller `triggerState`? | API-svar | 
| -------------------------------- | -------------| 
| Nej | Returnera ett HTTP `202 ACCEPTED` status plus en `location` huvud med `triggerState` inställd på aktuell tid och `retry-after` intervall till 15 sekunder. | 
| Ja | Kontrollera din tjänst för filer läggs till efter den `DateTime` för `triggerState`. | 
||| 

| Antal filer som hittades | API-svar | 
| --------------------- | -------------| 
| Enskild fil | Returnera ett HTTP `200 OK` status och innehåll nyttolasten, uppdatera `triggerState` till den `DateTime` för returnerade fil- och set `retry-after` intervall till 15 sekunder. | 
| Flera filer | Returnerar en fil i taget och en HTTP- `200 OK` status, uppdatera `triggerState`, och ange den `retry-after` intervallet 0 sekunder. </br>De här stegen kan motorn vet att mer data är tillgänglig och att motorn omedelbart ska begära data från URL: en i den `location` rubrik. | 
| Inga filer | Returnera ett HTTP `202 ACCEPTED` status, inte ändrar `triggerState`, och ange den `retry-after` intervall till 15 sekunder. | 
||| 

> [!TIP]
> Ett exempel avsökning utlösaren mönstret, granska det [avsökning utlösaren controller exemplet i GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Vänta och lyssna efter nya data eller händelser med mönstret för webhook-utlösare

En webhooksutlösare är en *push-utlösare* som väntar och lyssnar efter nya data eller händelser vid tjänstens slutpunkt. Om nya data eller en händelse uppfyller de angivna villkoren, utlöses utlösaren och skapar en logikappinstans som bearbetar data som indata.
Webhook-utlösare fungerar ungefär som den [webhook-åtgärder](#webhook-actions) tidigare i det här avsnittet och ställs in med `subscribe` och `unsubscribe` slutpunkter. 

* `subscribe` Slutpunkt: När du lägger till och spara en webhooksutlösare i logikappen Logic Apps motorn anrop den `subscribe` slutpunkt. Det här steget gör logikappen att skapa en Motringnings-URL som lagrar ditt API. Om det finns nya data eller en händelse som uppfyller de angivna villkoren, din API-anrop tillbaka med en HTTP POST till URL: en. Skicka som indata till logikappen innehåll nyttolasten och rubriker.

* `unsubscribe` Slutpunkt: Om webhook-utlösaren eller hela logikapp raderas, Logic Apps motorn anrop den `unsubscribe` slutpunkt. Ditt API kan sedan avregistrera Motringnings-URL och stoppa alla processer vid behov.

![Mönster för Webhook-utlösare](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> Logic App Designer stöder för närvarande inte identifierande webhook-slutpunkter via Swagger. Så för att det här mönstret måste du lägga till en [ **Webhook** utlösaren](../connectors/connectors-native-webhook.md) och ange URL: en, rubriker och brödtext för din begäran. Se även [HTTPWebhook utlösaren](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Om du vill skicka in Motringnings-URL, kan du använda den `@listCallbackUrl()` arbetsflödesfunktion i någon av de tidigare fälten efter behov.
>
> För att förhindra bearbetar samma data flera gånger, bör utlösaren rensa data som redan har lästs in och skickats till logikappen.

> [!TIP]
> Granska det för ett webhook mönster exempel [webhook-utlösare controller exemplet i GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="secure-calls-to-your-apis-from-logic-apps"></a>Säkra anrop till API: er från logikappar

När du har skapat ditt anpassade API: er, konfigurera autentisering för dina API: er så att du kan anropa dem på ett säkert sätt från logikappar. Lär dig [skydda anrop till anpassade API: er från logikappar](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Distribuera och anropa dina API: er

När du har konfigurerat autentisering kan du konfigurera distributionen för dina API: er. Lär dig [hur du distribuerar och anropa anpassade API: er från logikappar](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Publicera anpassade API: er i Azure

Om du vill göra dina anpassade API: er tillgängliga för andra Logic Apps-användare i Azure måste du lägga till säkerhet och registrera dem som Logic App-kopplingar. Mer information finns i [Översikt över anpassade anslutningsappar](../logic-apps/custom-connector-overview.md). 

Om du vill göra dina anpassade API: er tillgängliga för alla användare i Logic Apps, Microsoft Flow och Microsoft PowerApps, du måste lägga till säkerhet, registrera dina API: er som Logikappen kopplingar och nominera dina anslutningsappar för den [Microsoft Azure Certified-programmet](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Få support

* För detaljerad hjälp med anpassade API: er, kontakta [ customapishelp@microsoft.com ](mailto:customapishelp@microsoft.com).

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* På [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish) kan du hjälpa till med att förbättra Logic Apps genom att rösta på förslag eller komma med egna förslag på förbättringar. 

## <a name="next-steps"></a>Nästa steg

* [Hantera fel och undantag](../logic-apps/logic-apps-exception-handling.md)
* [Anropa, utlösare, eller kapsla logikappar med HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md)
* [Användningsmätning för åtgärder och utlösare](../logic-apps/logic-apps-pricing.md)
