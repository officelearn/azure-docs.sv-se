---
title: Anropa, utlösa eller kapsla arbetsflöden med HTTP-slutpunkter – Azure Logic Apps | Microsoft Docs
description: Konfigurera HTTP-slutpunkter för att anropa, utlösare, eller kapsla arbetsflöden för Azure Logic Apps
services: logic-apps
keywords: arbetsflöden, HTTP-slutpunkter
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.author: klam; LADocs
ms.openlocfilehash: c58b39f8e2d49eeb3e64c7ffce1d34d7a7b7b780
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57904379"
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-logic-apps"></a>Anropa, utlösare, eller kapsla arbetsflöden med HTTP-slutpunkter i logic apps

Internt kan du exponera synkron HTTP-slutpunkter som utlösare för logikappar så att du kan utlösa eller anropa dina logikappar via en URL. Du kan också kapsla arbetsflöden i dina logic apps genom att använda ett mönster av anropningsbara slutpunkter.

Du kan lägga till dessa utlösare så att dina logic apps kan ta emot inkommande begäranden för att skapa HTTP-slutpunkter:

* [Förfrågan](../connectors/connectors-native-reqres.md)

* [API-anslutning Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [HTTP-webhook](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > Även om de här exemplen använder den **begära** utlösare, du kan använda någon av de listade HTTP-utlösarna och alla principer gäller identiskt för andra typer av utlösare.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Konfigurera en HTTP-slutpunkt för din logikapp

Lägg till en utlösare som kan ta emot inkommande begäranden för att skapa en HTTP-slutpunkt.

1. Logga in på [Azure Portal](https://portal.azure.com "Azure Portal"). Gå till din logikapp och öppna Logic App Designer.

2. Lägg till en utlösare som gör att logikappen tar emot inkommande begäranden. Till exempel lägga till den **begära** utlösare i logikappen.

3.  Under **begär JSON-Brödtextsschema**, om du vill kan du ange en JSON-schema för nyttolasten (data) som du förväntar dig att ta emot utlösaren.

    Designern använder det här schemat för att skapa token som din logikapp kan använda för att använda parsa och skicka data från utlösaren via ditt arbetsflöde. 
    Mer om [token genereras från JSON-scheman](#generated-tokens).

    Ange schemat visas i designern för det här exemplet:

    ```json
    {
      "type": "object",
      "properties": {
        "address": {
          "type": "string"
        }
      },
      "required": [
        "address"
      ]
    }
    ```

    ![Lägg till åtgärd för begäran][1]

    > [!TIP]
    > 
    > Du kan skapa ett schema för en exempel-JSON-nyttolast från ett verktyg som [jsonschema.net](https://jsonschema.net/), eller i den **begära** utlösaren genom att välja **Använd exempel för att generera schemat**. 
    > Ange din exempelnyttolast och välj **klar**.

    Exempelvis kan den här exempelnyttolast:

    ```json
    {
       "address": "21 2nd Street, New York, New York"
    }
    ```

    skapar det här schemat:

    ```json
    {
       "type": "object",
       "properties": {
          "address": {
             "type": "string" 
          }
       }
    }
    ```

4.  Spara din logikapp. Under **HTTP POST till denna URL**, bör du nu hitta en genererad Motringnings-URL, som i följande exempel:

    ![Genererad Motringnings-URL för slutpunkten](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    Denna URL innehåller en signatur för delad åtkomst (SAS)-nyckel i frågeparametrar som används för autentisering. 
    Du kan också få HTTP slutpunkts-URL från din översikt över logikapp i Azure-portalen. Under **Utlösarhistorik**, Välj utlösaren:

    ![Hämta URL: en för HTTP-slutpunkt från Azure-portalen][2]

    Eller du kan hämta URL: en genom att göra det här anropet:

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Ändra HTTP-metoden för din utlösare

Som standard den **begäran** utlösaren förväntar sig en HTTP POST-begäran, men du kan använda en annan HTTP-metod. 

> [!NOTE]
> Du kan ange endast en metodtyp.

1. På din **begära** utlösa, Välj **visa avancerade alternativ**.

2. Öppna den **metoden** lista. Det här exemplet väljer **hämta** så att du kan testa URL: en för HTTP-slutpunkt senare.

    > [!NOTE]
    > Du kan välja andra HTTP-metoden eller ange en anpassad metod för din egen logikapp.

    ![Ändra HTTP-metod](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Acceptera parametrar via HTTP-slutpunkt-URL

Om du vill HTTP slutpunkts-URL att acceptera parametrar kan du anpassa din utlösare relativ sökväg.

1. På din **begära** utlösa, Välj **visa avancerade alternativ**. 

2. Under **metoden**, ange HTTP-metoden som du vill att din begäran om att använda. För det här exemplet väljer du den **hämta** metoden om du inte redan gjort det, så att du kan testa URL: en för HTTP-slutpunkt.

      > [!NOTE]
      > Du måste också uttryckligen ange en HTTP-metod för utlösaren när du anger en relativ sökväg för utlösaren.

3. Under **relativ sökväg**, ange den relativa sökvägen för den parameter som din URL bör godkänna, till exempel `customers/{customerID}`.

    ![Ange HTTP-metoden och relativ sökväg för parametern](./media/logic-apps-http-endpoint/relativeurl.png)

4. För att använda parametern måste du lägga till en **svar** åtgärd i logikappen. (Utlösaren och välj **nytt steg** > **Lägg till en åtgärd** > **svar**) 

5. I ditt svar **brödtext**, inkluderar token för den parameter som du angav i din utlösare relativ sökväg.

    Till exempel vill returnera `Hello {customerID}`, uppdatera ditt svar **brödtext** med `Hello {customerID token}`. 
    Den dynamiska innehållslistan ska visas och visa den `customerID` token och du kan välja.

    ![Lägg till parametern i svarstexten](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    Din **brödtext** bör se ut så här:

    ![Svarstexten med parametern](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Spara din logikapp. 

    HTTP-slutpunkt-URL innehåller nu den relativa sökvägen, till exempel: 

    https&#58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. Om du vill testa HTTP-slutpunkt, kopiera och klistra in den uppdaterade URL: en i ett nytt webbläsarfönster, men Ersätt `{customerID}` med `123456`, och tryck på RETUR.

    Webbläsaren bör visa den här texten: 

    `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Token som genereras från JSON-scheman för din logikapp

När du anger en JSON-schema i din **begära** utlösaren Logic App Designer genererar token för egenskaper i schemat. Du kan sedan använda dessa token för att skicka data via logikappens arbetsflöde.

Det här exemplet, om du lägger till den `title` och `name` egenskaper i JSON-schemat sina tokens är nu tillgängliga för användning i senare arbetsflödessteg. 

Här är den fullständiga JSON-scheman:

```json
{
   "type": "object",
   "properties": {
      "address": {
         "type": "string"
      },
      "title": {
         "type": "string"
      },
      "name": {
         "type": "string"
      }
   },
   "required": [
      "address",
      "title",
      "name"
   ]
}
```

## <a name="create-nested-workflows-for-logic-apps"></a>Skapa kapslade arbetsflöden för logikappar

Du kan kapsla arbetsflöden i din logikapp genom att lägga till andra logikappar som kan ta emot begäranden. För att inkludera dessa logikappar, lägger du till den **Azure Logic Apps – Välj ett Logic Apps-arbetsflöde** att utlösaren. Sedan kan du välja från berättigade logikappar.

![Lägg till en annan logikapp](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Anropa eller utlösa logikappar via HTTP-slutpunkter

När du har skapat din HTTP-slutpunkt kan du utlösa logikappen via en `POST` metod för att en fullständig URL. Logikappar har inbyggt stöd för direkt åtkomst slutpunkter.

> [!NOTE] 
> Om du vill köra en logikapp manuellt när som helst, i verktygsfältet Logic App Designer eller Logic App kodvy väljer **kör**.

## <a name="reference-content-from-an-incoming-request"></a>Referera till innehåll från en inkommande begäran

Om innehållets typ är `application/json`, du kan referera till egenskaper från den inkommande begäranden. I annat fall behandlas innehåll som en binär enhet som du kan skicka till andra API: er. Om du vill referera till det här innehållet i arbetsflödet, måste du konvertera innehållet. Exempel: Om du skickar `application/xml` innehåll, du kan använda `@xpath()` för ett XPath-utvinning, eller `@json()` för att konvertera XML till JSON. Lär dig mer om [arbeta med innehållstyper](../logic-apps/logic-apps-content-type.md).

Om du vill hämta utdata från en inkommande begäran, kan du använda den `@triggerOutputs()` funktion. Utdata kan se ut som i följande exempel:

```json
{
    "headers": {
        "content-type" : "application/json"
    },
    "body": {
        "myProperty" : "property value"
    }
}
```

Åtkomst till den `body` egenskapen mer specifikt kan du använda den `@triggerBody()` genväg. 

## <a name="respond-to-requests"></a>Svara på förfrågningar

Du kanske vill svara på vissa förfrågningar som startar en logikapp genom att returnera innehållet till anroparen. Du kan använda för att konstruera statuskoden, rubrik och brödtext för svar, det **svar** åtgärd. Den här åtgärden kan visas var som helst i din logikapp, inte bara i slutet av arbetsflödet.

> [!NOTE] 
> Om logikappen inte innehåller en **svar**, HTTP-slutpunkt som svarar *omedelbart* med en **202 accepterade** status. Dessutom för den ursprungliga begäran att hämta svaret, även alla steg som krävs för svaret måste avslutas inom den [tidsgränsen för begäran](./logic-apps-limits-and-config.md) såvida inte du anropa arbetsflödet som en kapslad logikapp. Om inget svar inträffar inom den här gränsen kan den inkommande begäranden når sin tidsgräns och tar emot HTTP-svaret **408 tidsgränsen på klienten**. Överordnade logikappen fortsätter att vänta tills ett svar tills slutförts, oavsett hur lång tid som krävs för kapslade logic apps.

### <a name="construct-the-response"></a>Konstruera svaret

Du kan inkludera mer än en rubrik och alla typer av innehåll i svarstexten. I exempel-svaret rubriken anger att svaret har innehållstyp `application/json`. och brödtexten innehåller `title` och `name`, baserat på JSON-schemat uppdateras tidigare för den **begära** utlösaren.

![Åtgärd för HTTP-svar][3]

Svar har följande egenskaper:

| Egenskap  | Beskrivning |
| --- | --- |
| statusCode |Anger HTTP-statuskod för att svara på den inkommande begäranden. Den här koden kan vara en giltig status-kod som börjar med 2xx, 4xx eller 5xx. 3xx statuskoder är inte tillåtna. |
| Rubriker |Definierar valfritt antal huvuden ska ingå i svaret. |
| brödtext |Anger en brödtext-objekt som kan vara en sträng, ett JSON-objekt eller även binärt innehåll som refereras till från ett tidigare steg. |

Här är JSON-schemat ser ut nu för den **svar** åtgärd:

``` json
"Response": {
   "inputs": {
      "body": {
         "title": "@{triggerBody()?['title']}",
         "name": "@{triggerBody()?['name']}"
      },
      "headers": {
           "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {},
   "type": "Response"
}
```

> [!TIP]
> Välj för att visa fullständiga JSON-definitionen för din logikapp på Logic App Designer **Kodvyer**.

## <a name="q--a"></a>Frågor och svar

#### <a name="q-what-about-url-security"></a>F: Vad är URL: en säkerhet?

S: På ett säkert sätt genererar Azure logic app Motringnings-URL: er med hjälp av en signatur för delad åtkomst (SAS). Den här signaturen passerar som frågeparameter och måste verifieras innan din logikapp kan utlöses. Azure genererar signaturen med hjälp av en unik kombination av en hemlig nyckel per logikapp, Utlösarens namn och den åtgärd som utförs. Så att de inte kan generera en giltig signatur, såvida inte någon har åtkomst till den hemliga logic app-nyckeln.

   > [!IMPORTANT]
   > För produktion och säkra system rekommenderar vi starkt mot anropa logikappen direkt från webbläsaren eftersom:
   > 
   > * Den delade åtkomstnyckeln visas i URL: en.
   > * Du kan inte hantera säkra innehållsprinciper på grund av delad domäner i Logic App-kunder.

#### <a name="q-can-i-configure-http-endpoints-further"></a>F: Kan jag konfigurera HTTP-slutpunkter ytterligare?

S: Ja, HTTP-slutpunkter stöder mer avancerade konfiguration via [ **API Management**](../api-management/api-management-key-concepts.md). Den här tjänsten erbjuder också möjligheten att konstant hantera alla dina API: er, inklusive logikappar, ställa in anpassade domännamn, använda flera autentiseringsmetoder och mycket mer, till exempel:

* [Ändra metoden för begäran](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [Ändra URL-segment för begäran](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* Ställ in din API Management-domäner i den [Azure-portalen](https://portal.azure.com/ "Azure-portalen")
* Ställa in principen för att söka efter grundläggande autentisering

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>F: Vad som ändrats när schemat migreras från den 1 December 2014-förhandsversionen?

S: Här följer en sammanfattning om dessa ändringar:

| Den 1 december 2014 preview | Den 1 juni 2016 |
| --- | --- |
| Klicka på **HTTP-lyssnare** API-App |Klicka på **manuella utlösaren** (inga API-App krävs) |
| HTTP-lyssnare inställningen ”*skickar svar automatiskt*” |Antingen inkludera en **svar** åtgärd eller inte i arbetsflödesdefinitionen |
| Konfigurera grundläggande eller OAuth-autentisering |via API-hantering |
| Konfigurera HTTP-metod |Under **visa avancerade alternativ**, Välj en HTTP-metod |
| Konfigurera relativ sökväg |Under **visa avancerade alternativ**, lägga till en relativ sökväg |
| Referera till inkommande brödtexten via `@triggerOutputs().body.Content` |Referens via `@triggerOutputs().body` |
| **Skicka HTTP-svar** åtgärd på HTTP-lyssnare |Klicka på **svarar på HTTP-förfrågan** (inga API-App krävs) |

## <a name="get-help"></a>Få hjälp

I [Azure Logic Apps-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) kan du ställa frågor, besvara frågor och se vad andra Azure Logic Apps-användare håller på med.

På [webbplatsen för Azure Logic Apps-användarfeedback](https://aka.ms/logicapps-wish) kan du hjälpa till med att förbättra Azure Logic Apps och anslutningsapparna genom att rösta på förslag eller komma med egna förslag på förbättringar.

## <a name="next-steps"></a>Nästa steg

* [Skapa Logic App-definitioner](./logic-apps-author-definitions.md)
* [Hantera fel och undantag](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png