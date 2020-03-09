---
title: Ta emot och svara på HTTPS-anrop
description: Hantera HTTPS-begäranden och händelser i real tid med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewers: klam, logicappspm
ms.topic: conceptual
ms.date: 01/14/2020
tags: connectors
ms.openlocfilehash: 0949e50c5a4993dfbcc83b41ef01d2cea82350a8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386513"
---
# <a name="receive-and-respond-to-incoming-https-calls-by-using-azure-logic-apps"></a>Ta emot och svara på inkommande HTTPS-anrop genom att använda Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och den inbyggda begär ande utlösare eller svars åtgärd kan du skapa automatiserade uppgifter och arbets flöden som tar emot och svarar på inkommande HTTPS-begäranden. Du kan till exempel ha din Logic-app:

* Ta emot och svara på en HTTPS-begäran om data i en lokal databas.
* Utlös ett arbets flöde när en extern webhook-händelse inträffar.
* Ta emot och svara på ett HTTPS-anrop från en annan Logic app.

> [!NOTE]
> Begär ande utlösare stöder *endast* Transport Layer Security (TLS) 1,2 för inkommande anrop. Utgående samtal fortsätter att stödja TLS 1,0, 1,1 och 1,2. Mer information finns i [lösa problemet med TLS 1,0](https://docs.microsoft.com/security/solving-tls1-problem).
>
> Om du ser fel i SSL-handskakning kontrollerar du att du använder TLS 1,2. För inkommande samtal är följande chiffersviter som stöds:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [Logic Apps](../logic-apps/logic-apps-overview.md). Lär dig [hur du skapar din första Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om du inte har använt Logic Apps igen.

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Lägg till begär ande utlösare

Den här inbyggda utlösaren skapar en manuellt anropad HTTPS-slutpunkt som *bara* får ta emot inkommande HTTPS-begäranden. När den här händelsen inträffar utlöses utlösaren och kör Logic-appen. Mer information om utlösarens underliggande JSON-definition och hur du anropar den här utlösaren finns i [begäran om utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) och [anrop, utlösare eller kapslade arbets flöden med http-slutpunkter i Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

1. Logga in på [Azure Portal](https://portal.azure.com). Skapa en tom logikapp.

1. När Logic App Designer öppnas i sökrutan anger du "http-förfrågan" som filter. I listan utlösare väljer du alternativet **när en HTTP-begäran tas emot** , vilket är det första steget i ditt Logic app-arbetsflöde.

   ![Välj begär utlösare](./media/connectors-native-reqres/select-request-trigger.png)

   Utlösaren för begäran visar följande egenskaper:

   ![Begär utlösare](./media/connectors-native-reqres/request-trigger.png)

   | Egenskapsnamn | JSON-egenskaps namn | Krävs | Beskrivning |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST-URL** | alternativet | Ja | Slut punkts-URL: en som genereras efter att du har sparat Logic-appen och som används för att anropa din Logic app |
   | **Begär ande text JSON-schema** | `schema` | Nej | JSON-schemat som beskriver egenskaperna och värdena i den inkommande begär ande texten |
   |||||

1. I rutan **begär text-JSON-schema** kan du ange ett JSON-schema som beskriver bröd texten i den inkommande begäran, till exempel:

   ![Exempel på JSON-schema](./media/connectors-native-reqres/provide-json-schema.png)

   Designern använder det här schemat för att generera tokens för egenskaperna i begäran. På så sätt kan din Logi Kap par parsa, förbruka och passera data från begäran via utlösaren i ditt arbets flöde.

   Här är exempel schemat:

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   När du anger ett JSON-schema, visar designern en påminnelse om att inkludera `Content-Type`-huvudet i din begäran och ange värdet `application/json`. Mer information finns i [Hantera innehålls typer](../logic-apps/logic-apps-content-type.md).

   ![Påminnelse om att inkludera rubriken "Content-Type"](./media/connectors-native-reqres/include-content-type.png)

   Så här ser rubriken ut i JSON-format:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Om du vill generera ett JSON-schema baserat på förväntad nytto Last (data) kan du använda ett verktyg som [JSONSchema.net](https://jsonschema.net), eller så kan du följa dessa steg:

   1. I utlösaren för begäran väljer **du Använd exempel nytto last för att generera schemat**.

      ![Generera schema från nytto Last](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Ange exempel nytto lasten och välj **färdig**.

      ![Generera schema från nytto Last](./media/connectors-native-reqres/enter-payload.png)

      Här är exempel på nytto last:

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": { 
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Om du vill ange ytterligare egenskaper öppnar du listan **Lägg till ny parameter** och väljer de parametrar som du vill lägga till.

   | Egenskapsnamn | JSON-egenskaps namn | Krävs | Beskrivning |
   |---------------|--------------------|----------|-------------|
   | **Metod** | `method` | Nej | Metoden som inkommande begäran måste använda för att anropa Logic-appen |
   | **Relativ sökväg** | `relativePath` | Nej | Den relativa sökvägen för den parameter som den logiska appens slut punkts-URL kan acceptera |
   |||||

   I det här exemplet läggs egenskapen **metod** till:

   ![Lägg till metod parameter](./media/connectors-native-reqres/add-parameters.png)

   Egenskapen **Method** visas i utlösaren så att du kan välja en metod i listan.

   ![Välj metod](./media/connectors-native-reqres/select-method.png)

1. Nu ska du lägga till en annan åtgärd som nästa steg i arbets flödet. Under utlösaren väljer du **Nästa steg** så att du kan hitta den åtgärd som du vill lägga till.

   Du kan till exempel svara på begäran genom att [lägga till en svars åtgärd](#add-response), som du kan använda för att returnera ett anpassat svar och beskrivs senare i det här avsnittet.

   Din Logi Kap par ser till att inkommande begäran endast öppnas i en minut. Förutsatt att ditt Logic app-arbetsflöde innehåller en svars åtgärd, om Logic app inte returnerar ett svar efter den här tiden, returnerar din Logic app en `504 GATEWAY TIMEOUT` till anroparen. Annars, om din Logic app inte innehåller någon svars åtgärd, returnerar din Logic app omedelbart ett `202 ACCEPTED` svar till anroparen.

1. När du är klar sparar du din Logic app. I verktygsfältet designer väljer du **Spara**. 

   Det här steget genererar URL: en som ska användas för att skicka begäran som utlöser Logic-appen. Om du vill kopiera den här URL: en väljer du kopierings ikonen bredvid URL: en.

   ![URL som ska användas för att utlösa din Logic app](./media/connectors-native-reqres/generated-url.png)

1. Om du vill utlösa din Logic-App skickar du ett HTTP-inlägg till den genererade URL: en. Du kan till exempel använda ett verktyg som [Postman](https://www.getpostman.com/).

### <a name="trigger-outputs"></a>Utlös utdata

Här är mer information om utdata från begär ande utlösare:

| JSON-egenskaps namn | Datatyp | Beskrivning |
|--------------------|-----------|-------------|
| `headers` | Objekt | Ett JSON-objekt som beskriver huvudena från begäran |
| `body` | Objekt | Ett JSON-objekt som beskriver bröd innehållet från begäran |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Lägg till en svars åtgärd

Du kan använda svars åtgärden för att svara med en nytto Last (data) till en inkommande HTTPS-begäran, men endast i en Logic-app som utlöses av en HTTPS-begäran. Du kan lägga till svars åtgärden när som helst i arbets flödet. Mer information om den underliggande JSON-definitionen för den här utlösaren finns i [Åtgärds typen svar](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

Din Logi Kap par ser till att inkommande begäran endast öppnas i en minut. Förutsatt att ditt Logic app-arbetsflöde innehåller en svars åtgärd, om Logic app inte returnerar ett svar efter den här tiden, returnerar din Logic app en `504 GATEWAY TIMEOUT` till anroparen. Annars, om din Logic app inte innehåller någon svars åtgärd, returnerar din Logic app omedelbart ett `202 ACCEPTED` svar till anroparen.

1. I Logic App Designer, under steget där du vill lägga till en svars åtgärd väljer du **nytt steg**.

   Till exempel med hjälp av begär ande utlösare från tidigare:

   ![Lägg till nytt steg](./media/connectors-native-reqres/add-response.png)

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd**i sökrutan anger du "Response" som filter och väljer **svars** åtgärden.

   ![Välj åtgärden svars åtgärd](./media/connectors-native-reqres/select-response-action.png)

   Begär ande utlösaren komprimeras i det här exemplet för enkelhetens skull.

1. Lägg till alla värden som krävs för svarsmeddelandet. 

   I vissa fält öppnas listan med dynamiskt innehåll genom att klicka inuti rutorna. Du kan sedan välja tokens som representerar tillgängliga utdata från föregående steg i arbets flödet. Egenskaperna från det schema som anges i det tidigare exemplet visas nu i listan med dynamiskt innehåll.

   Exempel: för rutan **rubriker** inkluderar du `Content-Type` som nyckel namn och anger nyckelvärdet till `application/json` som nämnts tidigare i det här avsnittet. I rutan **brödtext** kan du välja utlösare för text i listan med dynamiskt innehåll.

   ![Information om svars åtgärd](./media/connectors-native-reqres/response-details.png)

   Om du vill visa sidhuvudena i JSON-format väljer du **Växla till text visning**.

   ![Rubriker – växla till text visning](./media/connectors-native-reqres/switch-to-text-view.png)

   Här är mer information om de egenskaper som du kan ange i svars åtgärden. 

   | Egenskapsnamn | JSON-egenskaps namn | Krävs | Beskrivning |
   |---------------|--------------------|----------|-------------|
   | **Statuskod** | `statusCode` | Ja | Status koden som ska returneras i svaret |
   | **Headers** | `headers` | Nej | Ett JSON-objekt som beskriver en eller flera huvuden som ska inkluderas i svaret |
   | **Brödtext** | `body` | Nej | Svars texten |
   |||||

1. Om du vill ange ytterligare egenskaper, till exempel ett JSON-schema för svars texten, öppnar du listan **Lägg till ny parameter** och väljer de parametrar som du vill lägga till.

1. När du är klar sparar du din Logic app. I verktygsfältet designer väljer du **Spara**. 

## <a name="next-steps"></a>Nästa steg

* [Anslutningar för Logic Apps](../connectors/apis-list.md)
