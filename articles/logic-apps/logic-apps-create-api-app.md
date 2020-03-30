---
title: Skapa webb-API:er & REST-API:er för Azure Logic Apps
description: Skapa webb-API:er & REST-API:er för att anropa dina API:er, tjänster eller system för systemintegreringar i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: bb6c99ea12e5b53631d42a04b36b7bfef2337e42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270542"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Skapa anpassade API:er som du kan anropa från Azure Logic Apps

Även om Azure Logic Apps erbjuder [hundratals anslutningsappar](../connectors/apis-list.md) som du kan använda i logikapparbetsflöden, kanske du vill anropa API:er, system och tjänster som inte är tillgängliga som anslutningsappar. Du kan skapa egna API:er som tillhandahåller åtgärder och utlösare som ska användas i logikappar. Här är andra anledningar till varför du kanske vill skapa egna API:er som du kan anropa från logikapparbetsflöden:

* Utöka dina nuvarande arbetsflöden för systemintegration och dataintegration.
* Hjälp kunderna att använda din tjänst för att hantera professionella eller personliga uppgifter.
* Utöka räckvidden, upptäckbarheten och användningen för din tjänst.

I grund och botten är kopplingar webb-API:er som använder REST för pluggbara gränssnitt, [Swagger metadataformat](https://swagger.io/specification/) för dokumentation och JSON som sitt datautbytesformat. Eftersom kopplingar är REST-API:er som kommunicerar via HTTP-slutpunkter kan du använda vilket språk som helst, till exempel .NET, Java, Python eller Node.js, för att skapa kopplingar. Du kan också vara värd för dina API:er på [Azure App Service](../app-service/overview.md), ett PaaS-erbjudande (Platform-as-a-Service) som ger ett av de bästa, enklaste och mest skalbara sätten för API-hosting. 

För att anpassade API:er ska fungera med logikappar kan ditt API tillhandahålla [*åtgärder*](./logic-apps-overview.md#logic-app-concepts) som utför specifika uppgifter i logikapparbetsflöden. Ditt API kan också fungera som en [*utlösare*](./logic-apps-overview.md#logic-app-concepts) som startar ett logikapparbetsflöde när nya data eller en händelse uppfyller ett angivet villkor. I det här avsnittet beskrivs vanliga mönster som du kan följa för att skapa åtgärder och utlösare i ditt API, baserat på det beteende som du vill att ditt API ska tillhandahålla.

Du kan vara värd för dina API:er på [Azure App Service](../app-service/overview.md), ett PaaS-erbjudande (Platform-as-a-Service) som ger mycket skalbar och enkel API-värd.

> [!TIP] 
> Även om du kan distribuera dina API:er som webbappar kan du överväga att distribuera dina API:er som API-appar, vilket kan göra jobbet enklare när du skapar, är värd och använder API:er i molnet och lokalt. Du behöver inte ändra någon kod i dina API:er – distribuera bara koden till en API-app. Lär dig till exempel hur du skapar API-appar som skapats med följande språk: 
> 
> * [ASP.NET](../app-service/app-service-web-get-started-dotnet.md). 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [Php](../app-service/app-service-web-get-started-php.md)
> * [Python](../app-service/containers/quickstart-python.md)
> * [Ruby](../app-service/containers/quickstart-ruby.md)
>
> För API App-exempel som skapats för logikappar besöker du [Azure Logic Apps GitHub-databasen](https://github.com/logicappsio) eller [bloggen](https://aka.ms/logicappsblog).

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Hur skiljer sig anpassade API:er från anpassade kopplingar?

Anpassade API:er och [anpassade kopplingar](../logic-apps/custom-connector-overview.md) är webb-API:er som använder REST för kontaktbara gränssnitt, [Swagger-metadataformat](https://swagger.io/specification/) för dokumentation och JSON som datautbytesformat. Och eftersom dessa API:er och kopplingar är REST-API:er som kommunicerar via HTTP-slutpunkter kan du använda vilket språk som helst, till exempel .NET, Java, Python eller Node.js, för att skapa anpassade API:er och kopplingar.

Med anpassade API:er kan du anropa API:er som inte är kopplingar och tillhandahålla slutpunkter som du kan anropa med HTTP + Swagger, Azure API Management eller App Services. Anpassade kopplingar fungerar som anpassade API:er men har även följande attribut:

* Registrerade som Logic Apps Connector-resurser i Azure.
* Visas med ikoner bredvid Microsoft-hanterade kopplingar i Logic Apps Designer.
* Endast tillgängligt för kopplingarnas författare och logikappanvändare som har samma Azure Active Directory-klientorganisation och Azure-prenumeration i regionen där logikapparna distribueras.

Du kan också nominera registrerade anslutningsappar för Microsoft-certifiering. Den här processen verifierar att registrerade anslutningsappar uppfyller kriterierna för offentlig användning och gör dessa anslutningsappar tillgängliga för användare i Power Automate och Microsoft Power Apps.

Mer information om anpassade kopplingar finns i 

* [Översikt över anpassade anslutningsappar](../logic-apps/custom-connector-overview.md)
* [Skapa anpassade kopplingar från webb-API:er](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Registrera anpassade kopplingar i Azure Logic Apps](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>Användbara verktyg

Ett anpassat API fungerar bäst med logikappar när API:et också har ett [Swagger-dokument](https://swagger.io/specification/) som beskriver API:ets åtgärder och parametrar.
Många bibliotek, som [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle), kan automatiskt generera Swagger filen för dig. Om du vill kommentera Swagger-filen för visningsnamn, egenskapstyper och så vidare kan du också använda [TRex](https://github.com/nihaue/TRex) så att swagger-filen fungerar bra med logikappar.

<a name="actions"></a>

## <a name="action-patterns"></a>Åtgärdsmönster

För att logikappar ska kunna utföra uppgifter bör ditt anpassade API tillhandahålla [*åtgärder*](./logic-apps-overview.md#logic-app-concepts). Varje åtgärd i ditt API mappar till en åtgärd. En grundläggande åtgärd är en styrenhet som accepterar HTTP-begäranden och returnerar HTTP-svar. Så till exempel skickar en logikapp en HTTP-begäran till din webbapp eller API-app. Appen returnerar sedan ett HTTP-svar tillsammans med innehåll som logikappen kan bearbeta.

För en standardåtgärd kan du skriva en HTTP-begäran metod i ditt API och beskriva den metoden i en Swagger-fil. Du kan sedan anropa ditt API direkt med en [HTTP-åtgärd](../connectors/connectors-native-http.md) eller en [HTTP + Swagger-åtgärd.](../connectors/connectors-native-http-swagger.md) Som standard måste svaren returneras inom [tidsgränsen för begäran](./logic-apps-limits-and-config.md). 

![Standardåtgärdsmönster](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a>Om du vill att en logikapp ska vänta medan ditt API slutför längre aktiviteter kan ditt API följa det [asynkrona avsökningsmönstret](#async-pattern) eller det [asynkrona webhook-mönstret](#webhook-actions) som beskrivs i det här avsnittet. För en analogi som hjälper dig att visualisera dessa mönster olika beteenden, föreställa sig processen för att beställa en anpassad kaka från ett bageri. Röstningsmönstret speglar beteendet där du ringer bageriet var 20:e minut för att kontrollera om kakan är klar. Den webhook mönster speglar beteendet där bageriet ber dig om ditt telefonnummer så att de kan ringa dig när kakan är klar.

Exempel på exempel finns i [Logic Apps GitHub-databasen](https://github.com/logicappsio). Läs också mer om [användningsmätning för åtgärder](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Utföra tidskrävande uppgifter med avsökningsåtgärdsmönstret

Om du vill att ditt API ska utföra uppgifter som kan köras längre än [tidsgränsen för begäran](./logic-apps-limits-and-config.md)kan du använda det asynkrona avsökningsmönstret. Det här mönstret har ditt API fungerar i en separat tråd, men behålla en aktiv anslutning till Logic Apps-motorn. På så sätt tar logikappen inte time out eller fortsätter med nästa steg i arbetsflödet innan ditt API slutar fungera.

Här är det allmänna mönstret:

1. Kontrollera att motorn vet att ditt API har accepterat begäran och började arbeta.
2. När motorn gör efterföljande begäranden om jobbstatus meddelar du motorn när ditt API är klart för aktiviteten.
3. Returnera relevanta data till motorn så att logikapparbetsflödet kan fortsätta.

<a name="bakery-polling-action"></a>Nu tillämpa det tidigare bageri analogi till röstning mönster, och föreställa sig att du ringer ett bageri och beställa en anpassad tårta för leverans. Processen för att göra kakan tar tid, och du vill inte vänta på telefonen medan bageriet fungerar på kakan. Bageriet bekräftar din beställning och har du ringer var 20:e minut för kakans status. Efter 20 minuter passera, ringer du bageriet, men de säger att din kaka inte är klar och att du ska ringa i ytterligare 20 minuter. Denna fram och tillbaka process fortsätter tills du ringer, och bageriet berättar att din beställning är klar och levererar din kaka. 

Så låt oss kartlägga detta röstningsmönster tillbaka. Bageriet representerar ditt anpassade API, medan du, kakkunden, representerar Logic Apps-motorn. När motorn anropar ditt API med en begäran bekräftar ditt API begäran och svarar med tidsintervallet när motorn kan kontrollera jobbstatus. Motorn fortsätter att kontrollera jobbstatus tills ditt API svarar att jobbet är klart och returnerar data till logikappen, som sedan fortsätter arbetsflödet. 

![Åtgärdsmönster för avsökning](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Här är de specifika stegen för ditt API att följa, som beskrivs ur API: s perspektiv:

1. När ditt API får en HTTP-begäran om `202 ACCEPTED` att `location` börja arbeta returnerar du omedelbart ett HTTP-svar med huvudet som beskrivs senare i det här steget. Det här svaret gör att Logic Apps-motorn vet att ditt API fick begäran, accepterade begäran nyttolast (datainmatning) och bearbetas nu. 
   
   Svaret `202 ACCEPTED` bör innehålla följande rubriker:
   
   * *Obligatoriskt* `location` : Ett huvud som anger den absoluta sökvägen till en URL där Logic Apps-motorn kan kontrollera ditt API:s jobbstatus

   * *Valfritt* `retry-after` : Ett sidhuvud som anger hur många sekunder `location` motorn ska vänta innan du kontrollerar URL:en för jobbstatus. 

     Som standard kontrollerar motorn var 20:e sekund. Om du vill ange `retry-after` ett annat intervall inkluderar du huvudet och antalet sekunder fram till nästa omröstning.

2. När den angivna tiden har gått avsbÃ¤nder logic `location` apps-motorn URL:en för att kontrollera jobbstatus. Ditt API bör utföra dessa kontroller och returnera dessa svar:
   
   * Om jobbet är klart returnerar du ett HTTP-svar `200 OK` tillsammans med svarsnyttolasten (indata för nästa steg).

   * Om jobbet fortfarande bearbetas returnerar du ett annat HTTP-svar, `202 ACCEPTED` men med samma rubriker som det ursprungliga svaret.

När ditt API följer det här mönstret behöver du inte göra något i logikappens arbetsflödesdefinition för att fortsätta kontrollera jobbstatus. När motorn får `202 ACCEPTED` ett HTTP-svar och ett giltigt `location` huvud respekterar motorn det `location` asynkrona mönstret och kontrollerar huvudet tills ditt API returnerar ett svar som inte är 202.

> [!TIP]
> Ett exempel på asynkront mönster finns i det här [asynkrona kontrollgranskningssvarsexemplet i GitHub](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Utföra tidskrävande uppgifter med åtgärdsmönstret webhook

Som ett alternativ kan du använda webhook-mönstret för tidskrävande uppgifter och asynkron bearbetning. Det här mönstret har logikappen paus och vänta på en "motringning" från ditt API för att slutföra bearbetningen innan du fortsätter arbetsflödet. Den här motringningen är ett HTTP-POST som skickar ett meddelande till en URL när en händelse inträffar. 

<a name="bakery-webhook-action"></a>Nu tillämpa den tidigare bageri analogi till webhook mönster, och föreställa sig att du ringer ett bageri och beställa en anpassad kaka för leverans. Processen för att göra kakan tar tid, och du vill inte vänta på telefonen medan bageriet fungerar på kakan. Bageriet bekräftar din beställning, men den här gången ger du dem ditt telefonnummer så att de kan ringa dig när kakan är klar. Den här gången berättar bageriet när din beställning är klar och levererar din kaka.

När vi kartlägger det här webhook-mönstret representerar bageriet ditt anpassade API, medan du som kakkund representerar Logic Apps-motorn. Motorn anropar ditt API med en begäran och innehåller en "motringnings-URL".
När jobbet är klart använder ditt API URL:en för att meddela motorn och returnera data till logikappen, som sedan fortsätter arbetsflödet. 

För det här mönstret ställer du in `subscribe` två slutpunkter på handkontrollen: och`unsubscribe`

*  `subscribe`slutpunkt: När körningen når ditt API:s åtgärd i arbetsflödet anropar Logic Apps-motorn `subscribe` slutpunkten. Det här steget gör att logikappen skapar en motringnings-URL som ditt API lagrar och sedan väntar på motringning från ditt API när arbetet är klart. Ditt API anropar sedan tillbaka med ett HTTP-INLÄGG till webbadressen och skickar allt returnerat innehåll och rubriker som indata till logikappen.

* `unsubscribe`slutpunkt: Om logikappkörningen avbryts anropar Logic `unsubscribe` Apps-motorn slutpunkten. Ditt API kan sedan avregistrera motringnings-URL:en och stoppa alla processer efter behov.

![Åtgärdsmönster för Webhook](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> För närvarande stöder Logic App Designer inte upptäcka webhook slutpunkter via Swagger. Så för det här mönstret måste du lägga till en [ **Webhook-åtgärd** ](../connectors/connectors-native-webhook.md) och ange URL, rubriker och brödtext för din begäran. Se även [Arbetsflödesåtgärder och utlösare](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Om du vill skicka in motringnings-URL:en kan du använda `@listCallbackUrl()` arbetsflödesfunktionen i något av de tidigare fälten efter behov.

> [!TIP]
> Ett exempel på webhook-mönster finns i det här [webhook-utlösarexemplet i GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Utlösarmönster

Ditt anpassade API kan fungera som en [*utlösare*](./logic-apps-overview.md#logic-app-concepts) som startar en logikapp när nya data eller en händelse uppfyller ett angivet villkor. Den här utlösaren kan antingen regelbundet eller vänta och lyssna efter nya data eller händelser på tjänstslutpunkten. Om nya data eller en händelse uppfyller det angivna villkoret utlöses utlösaren och startar logikappen, som lyssnar på den utlösaren. Om du vill starta logikappar på det här sättet kan ditt API följa [*avsökningsutlösaren*](#polling-triggers) eller [*webhook-utlösarmönstret.*](#webhook-triggers) Dessa mönster liknar deras motsvarigheter för [avsökningsåtgärder](#async-pattern) och [webhook åtgärder](#webhook-actions). Läs också mer om [användningsmätning för utlösare](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Sök efter nya data eller händelser regelbundet med avsökningsutlösarens mönster

En *avsökningsutlösare* fungerar ungefär som den [avsökningsåtgärd](#async-pattern) som tidigare beskrivits i det här avsnittet. Logic Apps-motorn anropar och kontrollerar regelbundet utlösarslutpunkten för nya data eller händelser. Om motorn hittar nya data eller en händelse som uppfyller ditt angivna villkor utlöses utlösaren. Sedan skapar motorn en logikappinstans som bearbetar data som indata. 

![Utlösningsmönster för avsökning](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Varje avsökningsbegäran räknas som en åtgärdskörning, även om ingen logikappinstans skapas. Om du vill förhindra bearbetning av samma data flera gånger bör utlösaren rensa data som redan lästs och skickats till logikappen.

Här är specifika steg för en avsökningsutlösare, som beskrivs ur API:ets perspektiv:

| Hittade du nya data eller händelser?  | API-svar | 
| ------------------------- | ------------ |
| Hittade | Returnera en `200 OK` HTTP-status med svarsnyttolasten (indata för nästa steg). <br/>Det här svaret skapar en logikappinstans och startar arbetsflödet. | 
| Hittades inte | Returnera en `202 ACCEPTED` HTTP-status med ett `location` sidhuvud och ett `retry-after` sidhuvud. <br/>För utlösare `location` ska huvudet också `triggerState` innehålla en frågeparameter, som vanligtvis är en "tidsstämpel". Ditt API kan använda den här identifieraren för att spåra den senaste gången logikappen utlöstes. | 
||| 

Om du till exempel regelbundet kontrollerar om det finns nya filer i tjänsten kan du skapa en avsökningsutlösare med följande egenskaper:

| Begäran `triggerState`omfattar? | API-svar | 
| -------------------------------- | -------------| 
| Inga | Returnera en `202 ACCEPTED` HTTP-status plus ett `location` huvud med `triggerState` inställt på aktuell tid och `retry-after` intervallet till 15 sekunder. | 
| Ja | Kontrollera om det finns `DateTime` filer `triggerState`som lagts till efter för . | 
||| 

| Antal hittade filer | API-svar | 
| --------------------- | -------------| 
| Enkel fil | Returnera en `200 OK` HTTP-status och innehållsnyttolasten, uppdatera `triggerState` till `retry-after` `DateTime` den returnerade filen och ange intervallet till 15 sekunder. | 
| Flera filer | Returnera en fil i taget `200 OK` och `triggerState`en HTTP-status, uppdatera och ange `retry-after` intervallet till 0 sekunder. </br>De här stegen gör det möjligt för motorn att veta att mer data `location` är tillgängliga och att motorn omedelbart bör begära data från URL:en i sidhuvudet. | 
| Inga filer | Returnera en `202 ACCEPTED` HTTP-status, `triggerState`ändra inte `retry-after` och ställ in intervallet till 15 sekunder. | 
||| 

> [!TIP]
> Ett exempel på avsökningsutlösare mönster, granska den här [avsökningsutlösarens kontrollantprov i GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Vänta och lyssna efter nya data eller händelser med webhook-utlösarmönstret

En webhook-utlösare är en *push-utlösare* som väntar och lyssnar efter nya data eller händelser på tjänstslutpunkten. Om nya data eller en händelse uppfyller det angivna villkoret utlöses utlösaren och skapar en logikappinstans som sedan bearbetar data som indata.
Webhook-utlösare fungerar ungefär som de [webhook-åtgärder](#webhook-actions) som tidigare `subscribe` `unsubscribe` beskrivits i det här avsnittet och ställs in med och slutpunkter. 

* `subscribe`slutpunkt: När du lägger till och sparar en webhook-utlösare i logikappen anropar Logic Apps-motorn `subscribe` slutpunkten. Det här steget gör att logikappen skapar en motringnings-URL som api:et lagrar. När det finns nya data eller en händelse som uppfyller det angivna villkoret anropas ditt API med ett HTTP-POST till webbadressen. Innehållsnyttolasten och rubrikerna går som indata till logikappen.

* `unsubscribe`slutpunkt: Om webhook-utlösaren eller hela logikappen tas `unsubscribe` bort anropar Logic Apps-motorn slutpunkten. Ditt API kan sedan avregistrera motringnings-URL:en och stoppa alla processer efter behov.

![Webhook-utlösarmönster](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> För närvarande stöder Logic App Designer inte upptäcka webhook slutpunkter via Swagger. Så för det här mönstret måste du lägga till en [ **Webhook-utlösare** ](../connectors/connectors-native-webhook.md) och ange URL, rubriker och brödtext för din begäran. Se även [HTTPWebhook-utlösaren](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Om du vill skicka in motringnings-URL:en kan du använda `@listCallbackUrl()` arbetsflödesfunktionen i något av de tidigare fälten efter behov.
>
> Om du vill förhindra bearbetning av samma data flera gånger bör utlösaren rensa data som redan lästs och skickats till logikappen.

> [!TIP]
> Ett exempel på webhook-mönster finns i det här [exemplet med webhook-utlösare i GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="improve-security-for-calls-to-your-apis-from-logic-apps"></a>Förbättra säkerheten för samtal till dina API:er från logikappar

När du har skapat dina anpassade API:er konfigurerar du autentisering för dina API:er så att du kan anropa dem säkert från logikappar. Läs om hur du [förbättrar säkerheten för samtal till anpassade API:er från logikappar](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Distribuera och anropa dina API:er

När du har konfigurerat autentisering konfigurerar du distributionen för dina API:er. Lär dig hur du [distribuerar och anropar anpassade API:er från logikappar](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Publicera anpassade API:er till Azure

Om du vill göra dina anpassade API:er tillgängliga för andra Logic Apps-användare i Azure måste du lägga till säkerhet och registrera dem som Logic App-kopplingar. Mer information finns i [Översikt över anpassade anslutningsappar](../logic-apps/custom-connector-overview.md). 

Om du vill göra dina anpassade API:er tillgängliga för alla användare i Logic Apps, Power Automate och Microsoft Power Apps måste du lägga till säkerhet, registrera api:erna som Logic App-kopplingar och nominera dina anslutningsappar för [Microsoft Azure Certified-programmet](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Få support

* Kontakta [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com)om du vill ha specifik hjälp med anpassade API:er.

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* På [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish) kan du hjälpa till med att förbättra Logic Apps genom att rösta på förslag eller komma med egna förslag på förbättringar. 

## <a name="next-steps"></a>Nästa steg

* [Hantera fel och undantag](../logic-apps/logic-apps-exception-handling.md)
* [Anropa, utlösa eller kapsla logikappar med HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md)
* [Användningsmätning för åtgärder och utlösare](../logic-apps/logic-apps-pricing.md)
