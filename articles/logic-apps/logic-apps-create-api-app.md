---
title: "Skapa webb-API: er & REST API: er för Azure Logic Apps | Microsoft Docs"
description: "Skapa webb-API: er & REST API: er för att anropa dina API: er, tjänster eller system från logik app arbetsflöden för integrering med system"
keywords: "Web API: er, REST-API: er, arbetsflöden, system-integrering"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: bd229179-7199-4aab-bae0-1baf072c7659
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/26/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 2a8b883975ed0c0a2a6ee9a2a7ad0c0b1e938fd4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-apis-that-you-can-call-from-logic-app-workflows"></a>Skapa anpassade API: er som du kan anropa från logik app arbetsflöden

Även om Azure Logikappar erbjuder [100 + inbyggda kopplingar](../connectors/apis-list.md) att du kan använda i logik app arbetsflöden, kanske du vill anropa API: er, system och tjänster som inte är tillgängliga som kopplingar. Du kan skapa dina egna API: er som innehåller åtgärder och utlösare för användning i logikappar. Här finns andra skäl till varför du kanske vill skapa egna API: er som du kan anropa från logik app arbetsflöden:

* Utöka det aktuella systemet integrering och data integration arbetsflöden.
* Hjälp kunder att använda tjänsten för att hantera professional eller personliga uppgifter.
* Utöka räckvidden, synlighet och användning för din tjänst.

I princip kopplingar är web API: er som använder REST för anslutningsbara gränssnitt [Swagger metadataformat](http://swagger.io/specification/) för dokumentation och JSON som exchange format. Eftersom kopplingar REST API: er som kommunicerar via HTTP-slutpunkter kan använda du alla språk som .NET, Java eller Node.js, för att skapa kopplingar. Du kan också vara värd för dina API: er på [Azure App Service](../app-service/app-service-web-overview.md), en plattform-som-en tjänst (PaaS) erbjudande som ger ett sätt bästa enklaste och mest skalbara för API-värd. 

För anpassade API: er att arbeta med logic apps din API kan tillhandahålla [ *åtgärder* ](./logic-apps-what-are-logic-apps.md#logic-app-concepts) som utför olika uppgifter i logik app arbetsflöden. Din API kan också fungera som en [ *utlösaren* ](./logic-apps-what-are-logic-apps.md#logic-app-concepts) som startar en logik app arbetsflödet när nya data eller en händelse uppfyller ett angivet villkor. Det här avsnittet beskrivs vanliga mönster som du kan följa för att skapa åtgärder och utlösare i ditt API, baserat på det beteende som du vill att ditt API för att tillhandahålla.

Du kan vara värd för dina API: er på [Azure App Service](../app-service/app-service-web-overview.md), en plattform som-en-tjänst (PaaS) erbjudande som ger mycket skalbar och lätt API värd.

> [!TIP] 
> Även om du kan distribuera dina API: er som webbappar, Överväg att distribuera dina API: er som API-appar som kan underlätta ditt jobb när du skapar, värd, och använda API: er i molnet och lokalt. Du behöver ändra någon kod i dina API: er, distribuera bara din kod till API-app. Exempelvis veta hur du skapar API apps som skapats med följande språk: 
> 
> * [ASP.NET](../app-service/app-service-web-get-started-dotnet.md). 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [PHP](../app-service/app-service-web-get-started-php.md)
> * [Python](../app-service/app-service-web-get-started-python.md)
>
> API-App sampel som skapats för logikappar, finns det [Azure Logic Apps GitHub-lagringsplatsen](http://github.com/logicappsio) eller [blogg](http://aka.ms/logicappsblog).

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Hur skiljer sig anpassade API: er från anpassade kopplingar?

Anpassade API: er och [anpassade kopplingar](../logic-apps/custom-connector-overview.md) är webb-API: er som använder REST för anslutningsbara gränssnitt [Swagger metadataformat](http://swagger.io/specification/) för dokumentation och JSON som exchange format. Och eftersom dessa API: er och kopplingar är REST API: er som kommunicerar via HTTP-slutpunkter, kan du använda alla språk som .NET, Java eller Node.js, för att skapa anpassade API: er och kopplingar.

Anpassade API: er kan du anropa API: er som inte är kopplingar och ge slutpunkter som du kan anropa HTTP + Swagger, Azure API Management eller Apptjänster. Anpassade kopplingar fungerar som anpassade API: er men samma attribut:

* Registrerad som Logic Apps Connector resurser i Azure.
* Visas med ikoner tillsammans med Microsoft-hanterade anslutningar i Logic Apps Designer.
* Endast tillgängliga för kopplingarna författare och logik appanvändare som har samma Azure Active Directory-klient och Azure-prenumeration i den region där logikappar har distribuerats.

Du kan också välja registrerade anslutningar för Microsoft-certifikat. Den här processen kontrollerar att registrerade anslutningar uppfyller villkoren för offentliga och tillgängliggör de kopplingarna för användare i Microsoft Flow och Microsoft PowerApps.

Läs mer om anpassade kopplingar 

* [Översikt över anpassade anslutningsappar](../logic-apps/custom-connector-overview.md)
* [Skapa egna kopplingar från Web API: er](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Registrera anpassade kopplingar i Azure Logic Apps](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>Användbara verktyg

En anpassad API fungerar bäst med logic apps när API: et har också en [Swagger-dokument](http://swagger.io/specification/) som beskriver API åtgärder och parametrar.
Många bibliotek som [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle), kan generera Swagger-filen automatiskt åt dig. Anteckna Swagger-filen för visningsnamn, egenskapstyperna och så vidare, kan du också använda [TRex](https://github.com/nihaue/TRex) så att din Swagger-fil som fungerar bra med logic apps.

<a name="actions"></a>

## <a name="action-patterns"></a>Åtgärden mönster

För logic apps arbetsuppgifter, anpassat API bör ge [ *åtgärder*](./logic-apps-what-are-logic-apps.md#logic-app-concepts). Varje åtgärd i ditt API mappar till en åtgärd. En grundläggande åtgärd är en domänkontrollant som accepterar HTTP-begäranden och returnerar HTTP-svar. Exempelvis skickar en logikapp en HTTP-begäran till webbprogrammet eller API-app. Din app returnerar ett HTTP-svar, tillsammans med innehåll som kan bearbeta logikappen.

För en åtgärd som standard kan skriva en metod för HTTP-begäran i ditt API och beskriver den metoden i en Swagger-fil. Du kan sedan anropa din API direkt med en [HTTP-åtgärd](../connectors/connectors-native-http.md) eller en [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) åtgärd. Som standard måste svar returneras inom den [tidsgränsen för begäran](./logic-apps-limits-and-config.md). 

![Standard åtgärd mönster](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a>Så gör du en logikapp Vänta medan din API är klar längre körs uppgifter din API kan den [asynkron avsökning mönster](#async-pattern) eller [asynkron webhook mönster](#webhook-actions) beskrivs i det här avsnittet. Tänk dig processen för att begära en anpassad enkelt från en bageri för en liknelsen som hjälper dig att visualisera olika beteenden för dessa mönster. Avsökningen mönstret speglar beteendet där du anropar bageri var tjugonde minut för att kontrollera huruvida enkelt är klar. Webhook-mönstret speglar beteendet där den bagerier och frågar efter ditt telefonnummer så att de kan ringa dig när enkelt är klar.

Exempel, finns det [Logic Apps GitHub-lagringsplatsen](https://github.com/logicappsio). Dessutom lär dig mer om [användningsmätning för åtgärder](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Utför tidskrävande uppgifter med avsökning åtgärd mönster

Har din API som utför uppgifter som kan köras längre än den [tidsgränsen för begäran](./logic-apps-limits-and-config.md), du kan använda asynkrona avsökning mönstret. Det här mönstret har din API gör fungerar i en separat tråd men behålla en aktiv anslutning till Logic Apps-motorn. På så sätt kan logikappen har ingen timeout eller Fortsätt med nästa steg i arbetsflödet innan din API slutar fungera.

Här är det allmänna mönstret:

1. Kontrollera att motorn vet att din API accepterat begäran och igång.
2. När motorn gör efterföljande begäranden om jobbstatus, kan motorn veta när din API avslutar uppgiften.
3. Returnera relevanta data till motorn så att logik app arbetsflödet kan fortsätta.

<a name="bakery-polling-action"></a>Nu gäller den föregående bageri också avsökning mönstret och anta att du anropar en bageri och ordning anpassade enkelt för leverans. Processen för att göra enkelt tar tid och du vill inte vänta på telefonen medan bageri fungerar på enkelt. Bageri bekräftar din beställning och har du anropa var tjugonde minut för den enkelt status. Efter 20 minuter skickar du anropar bageri, men de talar om att din enkelt inte gjort och att du ska anropa i en annan 20 minuter. Fram och tillbaka flera gånger innan du anropar och bageri talar om att din order är klar och levererar ditt enkelt. 

Vi mappa tillbaka den här avsökningen mönster. Bageri representerar din anpassade API medan du enkelt-kund representerar Logic Apps-motorn. När motorn anropar din API med en begäran, din API bekräftar begäran och svarar med tidsintervallet när motorn kan kontrollera jobbstatus. Motorn fortsätter att kontrollera jobbstatus tills din API svarar att jobbet görs och returnerar data till logikappen som sedan fortsätter arbetsflödet. 

![Avsökningen åtgärd mönster](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Här är de specifika stegen för din API ska följa, beskrivs ur API: er:

1. När din API hämtar en HTTP-begäran att börja arbeta, omedelbart returnera ett HTTP `202 ACCEPTED` svar med den `location` huvud som beskrivs senare i det här steget. Svaret kan Logic Apps motorn vet att din API fick begäran, accepteras nyttolasten i begäran (indata) och bearbetar nu. 
   
   Den `202 ACCEPTED` svar ska inkludera dessa huvuden:
   
   * *Krävs*: A `location` huvud som anger den absoluta sökvägen till en URL där Logic Apps-motorn kan kontrollera status för ditt API

   * *Valfria*: A `retry-after` huvud som anger antalet sekunder som motorn ska vänta innan den `location` URL: en för jobbstatus. 

     Som standard kontrollerar motorn var 20 sekunder. Ange ett annat intervall genom att inkludera den `retry-after` sidhuvud och antalet sekunder innan nästa omröstningen.

2. När den angivna tiden går Logic Apps motorn avsökningar i `location` URL: en för att kontrollera jobbstatus. Din API ska utföra dessa kontroller och returnera dessa svar:
   
   * Om jobbet är klart returnerar ett HTTP `200 OK` svar, tillsammans med nyttolasten (indata till nästa steg) i svaret.

   * Om jobbet fortfarande bearbetar returnera ett annat HTTP `202 ACCEPTED` svar, men med samma sidhuvuden som det ursprungliga svaret.

När din API följer detta mönster, behöver du göra något i Arbetsflödesdefinitionen för logik app fortsätta kontrollera jobbstatus. När motorn hämtar en HTTP `202 ACCEPTED` svar och en giltig `location` huvud, motorn respekterar asynkront mönster och kontrollerar de `location` huvud tills din API returnerar ett icke-202 svar.

> [!TIP]
> Ett exempel asynkront mönster granska [asynkron controller svar exemplet i GitHub](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Utför tidskrävande uppgifter med webhook åtgärd mönster

Alternativt kan använda du webhook-mönster för asynkron bearbetning och tidskrävande uppgifter för. Det här mönstret har logikappen stanna och vänta på ett ”återanrop” från din API skulle avsluta bearbetningen innan du fortsätter arbetsflödet. Den här återanropet är en HTTP POST som skickar ett meddelande till en URL när en händelse inträffar. 

<a name="bakery-webhook-action"></a>Nu gäller den föregående bageri också webhook mönstret och anta att du anropar en bageri och ordning anpassade enkelt för leverans. Processen för att göra enkelt tar tid och du vill inte vänta på telefonen medan bageri fungerar på enkelt. Bageri bekräftar din order, men den här tiden kan du ge dem ditt telefonnummer så att de kan ringa dig när enkelt är klar. Nu är anger bageri när din order är klar och levererar ditt enkelt.

När vi mappa det här mönstret för webhook tillbaka representerar bageri din anpassade API medan du enkelt-kund representerar Logic Apps-motorn. Motorn din API: n med en begäran och innehåller en ”callback”-URL.
När jobbet är klart använder din API URL: en för att meddela motorn och returnera data till logikappen som sedan fortsätter arbetsflödet. 

Konfigurera två slutpunkter på styrenheten för det här mönstret: `subscribe` och`unsubscribe`

*  `subscribe`slutpunkten: när körningen når din API-åtgärd i arbetsflödet, Logic Apps motorn anrop av `subscribe` slutpunkt. Det här steget gör logikappen att skapa en motringning URL som din API lagrar och vänta på motringning från din API när arbetet är klar. Din API och sedan anropar tillbaka med en HTTP POST till URL: en och vidarebefordrar alla returnerade innehåll och rubriker som indata till logikappen.

* `unsubscribe`slutpunkten: om logikappen kör avbryts Logic Apps motorn anrop av `unsubscribe` slutpunkt. Din API kan sedan avregistrera återanrop URL och stoppa alla processer som krävs.

![Webhook åtgärd mönster](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> Logik App Designer stöder för närvarande inte identifierande webhook slutpunkterna via Swagger. Så för att det här mönstret måste du lägga till en [ **Webhook** åtgärd](../connectors/connectors-native-webhook.md) och ange URL: en, rubriker och brödtext för din begäran. Se även [arbetsflödesåtgärder och utlösare](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Om du vill skicka in URL: en motringning som du kan använda den `@listCallbackUrl()` arbetsflödesfunktion i någon av de tidigare fälten efter behov.

> [!TIP]
> Ett exempel webhook-mönster, granska [webhook utlösaren exemplet i GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Utlösaren mönster

Anpassat API som kan fungera som en [ *utlösaren* ](./logic-apps-what-are-logic-apps.md#logic-app-concepts) som startar en logikapp när nya data eller en händelse uppfyller ett angivet villkor. Den här utlösaren kan antingen Kontrollera regelbundet, eller vänta och lyssna efter nya data eller händelser på tjänsteslutpunkt. Om nya data eller en händelse uppfyller de angivna villkoren, utlöses utlösaren och startar logikappen som lyssnar på den här utlösaren. Logikappar sätt din API kan så startar den [ *avsökning utlösaren* ](#polling-triggers) eller [ *webhook utlösaren* ](#webhook-triggers) mönster. Dessa mönster liknar motsvarigheterna för [avsökning åtgärder](#async-pattern) och [webhook-åtgärder](#webhook-actions). Dessutom lär dig mer om [användningsmätning för utlösare](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Söka efter nya data eller händelser regelbundet med avsökning utlösaren mönstret

En *avsökning utlösaren* fungerar ungefär som den [avsökning åtgärd](#async-pattern) beskrivs tidigare i det här avsnittet. Logic Apps motorn anropar regelbundet och kontrollerar utlösaren slutpunkten för nya data eller händelser. Om motorn söker efter nya data eller en händelse som uppfyller dina villkor, utlöses utlösaren. Sedan skapas motorn logik app-instansen, som bearbetar data som indata. 

![Avsökning utlösaren mönster](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Varje avsökning begäran räknas som en körning av åtgärden, även när inga logik app-instansen har skapats. Om du vill förhindra att bearbeta flera gånger för samma data, bör utlösaren rensa data som redan har lästs in och skickats till logikappen.

Här följer specifika steg för en avsökning utlösare som beskrivs ur API: er:

| Hitta nya data eller händelsen?  | API-svar | 
| ------------------------- | ------------ |
| Hitta | Returnera ett HTTP `200 OK` status med nyttolasten (indata för nästa steg) i svaret. <br/>Det här svaret skapar en logik app-instansen och startar arbetsflödet. | 
| Det gick inte att hitta | Returnera ett HTTP `202 ACCEPTED` status med en `location` rubrik och en `retry-after` huvud. <br/>För utlösare, den `location` huvud måste även innehålla en `triggerState` frågeparameter som vanligtvis är ”tidsstämpel”. Din API kan använda den här identifieraren för att spåra den senaste gången logikappen utlöstes. | 
||| 

För att regelbundet kontrollera din tjänst för nya filer, kan du exempelvis skapa en avsökning utlösare som har dessa beteenden:

| Begäran innehåller `triggerState`? | API-svar | 
| -------------------------------- | -------------| 
| Nej | Returnera ett HTTP `202 ACCEPTED` status plus en `location` huvud med `triggerState` inställd på den aktuella tiden och `retry-after` intervallet 15 sekunder. | 
| Ja | Kontrollera din tjänst för filer som lagts till efter den `DateTime` för `triggerState`. | 
||| 

| Antal filer hittades | API-svar | 
| --------------------- | -------------| 
| Enskild fil | Returnera ett HTTP `200 OK` status och innehåll nyttolast, uppdatering `triggerState` till den `DateTime` för returnerade fil- och set `retry-after` intervallet 15 sekunder. | 
| Flera filer | Returnera en fil i taget och en HTTP- `200 OK` status, uppdatera `triggerState`, och ange den `retry-after` intervallet 0 sekunder. </br>De här stegen kan motorn vet att flera data är tillgängliga och att motorn omedelbart ska begära data från en URL i den `location` rubrik. | 
| Inga filer | Returnera ett HTTP `202 ACCEPTED` status, ändras inte `triggerState`, och ange den `retry-after` intervallet 15 sekunder. | 
||| 

> [!TIP]
> Ett exempel avsökning utlösaren mönster, granska detta [avsökning utlösaren controller exemplet i GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Vänta och lyssna efter nya data eller händelser med webhook utlösaren mönster

En webhook-utlösare är en *push-utlösare* som väntar och lyssnar efter nya data eller händelser på tjänsteslutpunkt. Om nya data eller en händelse uppfyller de angivna villkoren, utlöses utlösaren och skapar en logik app-instansen, som bearbetar data som indata.
Webhook-utlösare fungerar ungefär som den [webhook-åtgärder](#webhook-actions) tidigare i det här avsnittet och ställs in med `subscribe` och `unsubscribe` slutpunkter. 

* `subscribe`slutpunkten: när du lägger till och spara en webhook-utlösare i din logikapp Logic Apps motorn anrop av `subscribe` slutpunkt. Det här steget gör logikappen att skapa en motringning URL som lagras av din API. När det finns nya data eller en händelse som uppfyller de angivna villkoren är din API-anrop tillbaka med en HTTP POST till URL: en. Skicka som indata till logikappen innehåll nyttolasten och rubriker.

* `unsubscribe`slutpunkten: om webhook utlösare eller hela logikapp raderas Logic Apps motorn anrop av `unsubscribe` slutpunkt. Din API kan sedan avregistrera återanrop URL och stoppa alla processer som krävs.

![Webhook-utlösare mönster](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> Logik App Designer stöder för närvarande inte identifierande webhook slutpunkterna via Swagger. Så för att det här mönstret måste du lägga till en [ **Webhook** utlösaren](../connectors/connectors-native-webhook.md) och ange URL: en, rubriker och brödtext för din begäran. Se även [HTTPWebhook utlösaren](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Om du vill skicka in URL: en motringning som du kan använda den `@listCallbackUrl()` arbetsflödesfunktion i någon av de tidigare fälten efter behov.
>
> Om du vill förhindra att bearbeta flera gånger för samma data, bör utlösaren rensa data som redan har lästs in och skickats till logikappen.

> [!TIP]
> Ett exempel webhook-mönster, granska [webhook utlösaren controller exemplet i GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="secure-calls-to-your-apis-from-logic-apps"></a>Säker anrop till dina API: er från logikappar

När du har skapat dina anpassade API: er, konfigurera autentisering för ditt API: er så att du kan anropa dem på ett säkert sätt från logikappar. Läs [säkra anrop till anpassade API: er från logikappar](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Distribuera och anropa dina API: er

När du har konfigurerat autentisering, konfigurera distribution för dina API: er. Läs [hur du distribuerar och anropa anpassade API: er från logikappar](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Publicera anpassade API: er till Azure

För att göra dina anpassade API: er med andra Logic Apps i Azure, måste du lägga till säkerhet och registrera dem som Logikapp kopplingar. Mer information finns i [anpassade kopplingar översikt](../logic-apps/custom-connector-overview.md). 

För att dina anpassade API: er ska vara tillgängliga för alla användare i Logic Apps och Microsoft Flow Microsoft PowerApps du lägger till säkerhet, registrera dina API: er som Logikapp kopplingar och utse kopplingar för den [Microsoft Azure-certifierad programmet](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Få support

* För detaljerad hjälp med anpassade API: er Kontakta [ customapishelp@microsoft.com ](mailto:customapishelp@microsoft.com).

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* För att förbättra Logic Apps, rösta eller skicka in förslag på den [Logic Apps användaren feedbackwebbplats](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Nästa steg

* [Hantera fel och undantag](../logic-apps/logic-apps-exception-handling.md)
* [Anropa utlösare eller kapsla logikappar med HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md)
* [Användningsmätning för åtgärder och utlösare](../logic-apps/logic-apps-pricing.md)
