---
title: Ta emot och svara på samtal med https
description: Hantera inkommande HTTPS-begäranden från externa tjänster med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewers: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 1885d7f8713b3801ce0c9846b7a8509b3864032a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656307"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Ta emot och svara på inkommande HTTPS-begäranden i Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och den inbyggda åtgärden Begäran trigger eller svar kan du skapa automatiserade uppgifter och arbetsflöden som tar emot och svarar på inkommande HTTPS-begäranden. Du kan till exempel ha logikappen:

* Ta emot och svara på en HTTPS-begäran om data i en lokal databas.
* Utlösa ett arbetsflöde när en extern webhook-händelse inträffar.
* Ta emot och svara på ett HTTPS-samtal från en annan logikapp.

> [!NOTE]
> Utlösaren begäran stöder *endast* TLS (Transport Layer Security) 1.2 för inkommande samtal. Utgående samtal fortsätter att stödja TLS 1.0, 1.1 och 1.2. Mer information finns i [Lösa TLS 1.0-problemet](https://docs.microsoft.com/security/solving-tls1-problem).
>
> Om du ser TLS handskakningsfel kontrollerar du att du använder TLS 1.2. För inkommande samtal, här är de chiffersviter som stöds:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en prenumeration kan du [registrera dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [logikappar](../logic-apps/logic-apps-overview.md). Om du inte har tidigare i logikappar kan du läsa om hur du [skapar din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Lägga till utlösare för begäran

Den här inbyggda utlösaren skapar en manuellt callable HTTPS-slutpunkt som *endast* kan ta emot inkommande HTTPS-begäranden. När den här händelsen inträffar utlöses utlösaren och kör logikappen. Mer information om utlösarens underliggande JSON-definition och hur du anropar den här utlösaren finns i [typen Begäran trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) och [Anropa, utlösare eller kapsla arbetsflöden med HTTP-slutpunkter i Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

1. Logga in på [Azure-portalen](https://portal.azure.com). Skapa en tom logikapp.

1. När Logic App Designer öppnas anger du "http-begäran" som filter i sökrutan. I listan utlösare väljer du **utlösaren När en HTTP-begäran tas emot,** vilket är det första steget i logikapparbetsflödet.

   ![Välj utlösare för begäran](./media/connectors-native-reqres/select-request-trigger.png)

   Utlösaren Begäran visar följande egenskaper:

   ![Utlösare för begäran](./media/connectors-native-reqres/request-trigger.png)

   | Egenskapsnamn | Domännamn för JSON | Krävs | Beskrivning |
   |---------------|--------------------|----------|-------------|
   | **URL FÖR HTTP-INLÄGG** | {ingen} | Ja | Slutpunkts-URL:en som genereras när du har sparat logikappen och används för att anropa logikappen |
   | **Begär JSON-schema för begäran** | `schema` | Inga | JSON-schemat som beskriver egenskaperna och värdena i den inkommande begärandetexten |
   |||||

1. I rutan **JSON-schema** för begäran anger du eventuellt ett JSON-schema som beskriver brödtexten i den inkommande begäran, till exempel:

   ![Exempel på JSON-schema](./media/connectors-native-reqres/provide-json-schema.png)

   Designern använder det här schemat för att generera token för egenskaperna i begäran. På så sätt kan din logikapp tolka, använda och vidarebefordra data från begäran via utlösaren i arbetsflödet.

   Här är exempelschemat:

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

   När du anger ett JSON-schema visar designern en påminnelse om att inkludera `Content-Type` huvudet i din begäran och ange rubrikvärdet till `application/json`. Mer information finns i [Hantera innehållstyper](../logic-apps/logic-apps-content-type.md).

   ![Påminnelse om att inkludera "Content-Type"-rubrik](./media/connectors-native-reqres/include-content-type.png)

   Så här ser den här rubriken ut i JSON-format:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Om du vill generera ett JSON-schema som baseras på den förväntade nyttolasten (data) kan du använda ett verktyg som [JSONSchema.net](https://jsonschema.net)eller så kan du följa följande steg:

   1. I utlösaren Begäran väljer du **Använd exempelnyttolast för att generera schema**.

      ![Generera schema från nyttolast](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Ange exempelnyttolasten och välj **Klar**.

      ![Generera schema från nyttolast](./media/connectors-native-reqres/enter-payload.png)

      Här är exempel nyttolasten:

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

   | Egenskapsnamn | Domännamn för JSON | Krävs | Beskrivning |
   |---------------|--------------------|----------|-------------|
   | **Metod** | `method` | Inga | Den metod som den inkommande begäran måste använda för att anropa logikappen |
   | **Relativ sökväg** | `relativePath` | Inga | Den relativa sökvägen för parametern som logikappens slutpunkts-URL kan acceptera |
   |||||

   I det här exemplet läggs egenskapen **Metod** till:

   ![Parametern Lägg till metod](./media/connectors-native-reqres/add-parameters.png)

   Egenskapen **Method** visas i utlösaren så att du kan välja en metod i listan.

   ![Välj metod](./media/connectors-native-reqres/select-method.png)

1. Lägg nu till ytterligare en åtgärd som nästa steg i arbetsflödet. Under utlösaren väljer du **Nästa steg** så att du kan hitta den åtgärd som du vill lägga till.

   Du kan till exempel svara på begäran genom att lägga till [en svarsåtgärd](#add-response)som du kan använda för att returnera ett anpassat svar och beskrivs senare i det här avsnittet.

   Logikappen håller den inkommande begäran öppen endast i en minut. Om du antar att logikappens arbetsflöde innehåller en svarsåtgärd, om logikappen inte `504 GATEWAY TIMEOUT` returnerar ett svar när den här tiden har gått, returnerar logikappen en till anroparen. Annars, om logikappen inte innehåller en svarsåtgärd, `202 ACCEPTED` returnerar logikappen omedelbart ett svar till anroparen.

1. När du är klar sparar du logikappen. Välj **Spara**i designerverktygsfältet . 

   Det här steget genererar url:en som ska användas för att skicka begäran som utlöser logikappen. Om du vill kopiera webbadressen markerar du kopieringsikonen bredvid webbadressen.

   ![URL för att använda utlösa din logikapp](./media/connectors-native-reqres/generated-url.png)

1. Om du vill utlösa logikappen skickar du ett HTTP-POST till den genererade webbadressen. Du kan till exempel använda ett verktyg som [Postman](https://www.getpostman.com/).

### <a name="trigger-outputs"></a>Utdata för utlösare

Här finns mer information om utdata från utlösaren Begäran:

| Domännamn för JSON | Datatyp | Beskrivning |
|--------------------|-----------|-------------|
| `headers` | Objekt | Ett JSON-objekt som beskriver rubrikerna från begäran |
| `body` | Objekt | Ett JSON-objekt som beskriver brödtextinnehållet från begäran |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Lägga till en svarsåtgärd

Du kan använda åtgärden Svar för att svara med en nyttolast (data) på en inkommande HTTPS-begäran, men bara i en logikapp som utlöses av en HTTPS-begäran. Du kan lägga till svarsåtgärden när som helst i arbetsflödet. Mer information om den underliggande JSON-definitionen för den här utlösaren finns i [åtgärdstypen Svar](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

Logikappen håller den inkommande begäran öppen endast i en minut. Om du antar att logikappens arbetsflöde innehåller en svarsåtgärd, om logikappen inte `504 GATEWAY TIMEOUT` returnerar ett svar när den här tiden har gått, returnerar logikappen en till anroparen. Annars, om logikappen inte innehåller en svarsåtgärd, `202 ACCEPTED` returnerar logikappen omedelbart ett svar till anroparen.

> [!IMPORTANT]
> Om en svarsåtgärd innehåller dessa rubriker tar Logic Apps bort dessa rubriker från det genererade svarsmeddelandet utan att visa någon varning eller något fel:
>
> * `Allow`
> * `Content-*`med dessa `Content-Disposition`undantag: , `Content-Encoding`och`Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Även om Logic Apps inte hindrar dig från att spara logikappar som har en svarsåtgärd med dessa rubriker, ignorerar Logic Apps dessa rubriker.

1. Välj **Nytt steg**under det steg där du vill lägga till en svarsåtgärd i Logic App Designer.

   Till exempel med hjälp av begäran utlösaren från tidigare:

   ![Lägg till nytt steg](./media/connectors-native-reqres/add-response.png)

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan dessa steg. Markera plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd**anger du "svar" som filter i sökrutan och väljer åtgärden **Svar.**

   ![Välj åtgärden Svar](./media/connectors-native-reqres/select-response-action.png)

   Utlösaren Begäran komprimeras i det här exemplet för enkelhetens skull.

1. Lägg till alla värden som krävs för svarsmeddelandet. 

   I vissa fält öppnas listan med dynamiskt innehåll genom att klicka i deras rutor. Du kan sedan välja token som representerar tillgängliga utdata från tidigare steg i arbetsflödet. Egenskaper från schemat som angetts i det tidigare exemplet visas nu i listan med dynamiskt innehåll.

   För rutan Rubriker inkludera `Content-Type` du till exempel som nyckelnamn och `application/json` ange nyckelvärdet till som tidigare **nämnts** i det här avsnittet. För rutan **Brödtext** kan du välja utdata från utlösartexten i listan med dynamiskt innehåll.

   ![Information om svarsåtgärd](./media/connectors-native-reqres/response-details.png)

   Om du vill visa rubrikerna i JSON-format väljer du **Växla till textvy**.

   ![Rubriker - Växla till textvy](./media/connectors-native-reqres/switch-to-text-view.png)

   Här finns mer information om de egenskaper som du kan ange i åtgärden Svar. 

   | Egenskapsnamn | Domännamn för JSON | Krävs | Beskrivning |
   |---------------|--------------------|----------|-------------|
   | **Statuskod** | `statusCode` | Ja | Statuskoden som ska returneras i svaret |
   | **Rubriker** | `headers` | Inga | Ett JSON-objekt som beskriver ett eller flera rubriker som ska inkluderas i svaret |
   | **Brödtext** | `body` | Inga | Svarsorganet |
   |||||

1. Om du vill ange ytterligare egenskaper, till exempel ett JSON-schema för svarstexten, öppnar du listan **Lägg till ny parameter** och väljer de parametrar som du vill lägga till.

1. När du är klar sparar du logikappen. Välj **Spara**i designerverktygsfältet . 

## <a name="next-steps"></a>Nästa steg

* [Anslutningsappar för Logic Apps](../connectors/apis-list.md)
