---
title: Ta emot och svara på anrop med hjälp av HTTPS
description: Hantera inkommande HTTPS-begäranden från externa tjänster med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
tags: connectors
ms.openlocfilehash: 25aafee59c7f5f7ae59aa2fd7871de8926907f68
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261376"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Ta emot och svara på inkommande HTTPS-begäranden i Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och den inbyggda åtgärden begär ande utlösare och svar kan du skapa automatiserade uppgifter och arbets flöden som tar emot och svarar på inkommande HTTPS-begäranden. Du kan till exempel ha din Logic-app:

* Ta emot och svara på en HTTPS-begäran om data i en lokal databas.

* Utlös ett arbets flöde när en extern webhook-händelse inträffar.

* Ta emot och svara på ett HTTPS-anrop från en annan Logic app.

Begär ande utlösare stöder [Azure Active Directory öppen autentisering](/azure/active-directory/develop/) (Azure AD OAuth) för att auktorisera inkommande samtal till din Logic app. Mer information om hur du aktiverar den här autentiseringen finns i [skydda åtkomst och data i Azure Logic Apps – aktivera Azure AD OAuth-autentisering](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [Logic Apps](../logic-apps/logic-apps-overview.md). Lär dig [hur du skapar din första Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om du inte har använt Logic Apps igen.

<a name="tls-support"></a>

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

* Inkommande anrop stöder *endast* Transport Layer Security (TLS) 1,2. Om du får TLS-handskakning, se till att du använder TLS 1,2. Mer information finns i [lösa problemet med TLS 1,0](https://docs.microsoft.com/security/solving-tls1-problem). Utgående anrop stöder TLS 1,0, 1,1 och 1,2, baserat på mål slut punktens funktion.

* Inkommande anrop stöder dessa chiffersviter:

  * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384

  * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256

  * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384

  * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256

  * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384

  * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256

  * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

  * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Lägg till begär ande utlösare

Den här inbyggda utlösaren skapar en manuellt anropad HTTPS-slutpunkt som *bara* får ta emot inkommande HTTPS-begäranden. När den här händelsen inträffar utlöses utlösaren och kör Logic-appen. Mer information om utlösarens underliggande JSON-definition och hur du anropar den här utlösaren finns i [begäran om utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) och [anrop, utlösare eller kapslade arbets flöden med https-slutpunkter i Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

1. Logga in på [Azure-portalen](https://portal.azure.com). Skapa en tom logikapp.

1. När Logic App Designer öppnas går du till rutan Sök och anger `http request` som filter. I listan utlösare väljer du alternativet **när en HTTP-begäran tas emot** , vilket är det första steget i ditt Logic app-arbetsflöde.

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

   När du anger ett JSON-schema visar designern en påminnelse om att inkludera `Content-Type` sidhuvudet i din begäran och ange värdet som huvud värde till `application/json` . Mer information finns i [Hantera innehålls typer](../logic-apps/logic-apps-content-type.md).

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

1. Följ dessa steg om du vill kontrol lera att det inkommande samtalet har en begär ande text som matchar det angivna schemat:

   1. I namn listen för begäran-utlösaren väljer du knappen ovaler (**...**).

   1. I utlösarens inställningar aktiverar du **schema validering**och väljer sedan **slutförd**.

      Om det inkommande samtalets begär ande text inte matchar schemat, returnerar utlösaren ett `HTTP 400 Bad Request` fel.

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

   Din Logi Kap par ser till att inkommande begäran endast öppnas under en [begränsad tid](../logic-apps/logic-apps-limits-and-config.md#request-limits). Förutsatt att ditt Logic app-arbetsflöde innehåller en svars åtgärd, om Logic app inte returnerar ett svar efter den här tiden, returnerar din Logic app en `504 GATEWAY TIMEOUT` till anroparen. Om din Logic app inte innehåller någon svars åtgärd returnerar din Logi Kap app omedelbart ett `202 ACCEPTED` svar till anroparen.

1. När du är klar sparar du din Logic app. I verktygsfältet designer väljer du **Spara**.

   Det här steget genererar URL: en som ska användas för att skicka begäran som utlöser Logic-appen. Om du vill kopiera den här URL: en väljer du kopierings ikonen bredvid URL: en.

   ![URL som ska användas för att utlösa din Logic app](./media/connectors-native-reqres/generated-url.png)

   > [!NOTE]
   > Om du vill inkludera hash-eller pund symbolen ( **#** ) i URI: n när du gör ett anrop till utlösaren för begäran använder du den här kodade versionen i stället:`%25%23`

1. Om du vill utlösa din Logic-App skickar du ett HTTP-inlägg till den genererade URL: en.

   Du kan till exempel använda ett verktyg som [Postman](https://www.getpostman.com/) för att skicka http-inlägget. Om du har [aktiverat Azure Active Directory öppna autentisering](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) (Azure AD OAuth) för att auktorisera inkommande anrop till begär ande utlösaren anropar du utlösaren med hjälp av en [URL för delad åtkomst (SAS)](../logic-apps/logic-apps-securing-a-logic-app.md#sas) eller genom att använda en autentiseringstoken, men du kan inte använda båda. Autentiseringstoken måste ange `Bearer` typen i Authorization-huvudet. Mer information finns i [skydda åtkomst och data i Azure Logic Apps-åtkomst till begär ande-baserade-utlösare](../logic-apps/logic-apps-securing-a-logic-app.md#secure-triggers).

Mer information om utlösarens underliggande JSON-definition och hur du anropar den här utlösaren finns i följande avsnitt, [begär utlösnings typ](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) och [anrop, utlösare eller kapslade arbets flöden med http-slutpunkter i Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

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

Din Logi Kap par ser till att inkommande begäran endast öppnas under en [begränsad tid](../logic-apps/logic-apps-limits-and-config.md#request-limits). Förutsatt att ditt Logic app-arbetsflöde innehåller en svars åtgärd, om Logic app inte returnerar ett svar efter den här tiden, returnerar din Logic app en `504 GATEWAY TIMEOUT` till anroparen. Om din Logic app inte innehåller någon svars åtgärd returnerar din Logi Kap app omedelbart ett `202 ACCEPTED` svar till anroparen.

> [!IMPORTANT]
> Om en svars åtgärd inkluderar dessa huvuden, tar Logic Apps bort huvudena från det genererade svarsmeddelandet utan att visa någon varning eller ett fel meddelande:
>
> * `Allow`
> * `Content-*`med dessa undantag: `Content-Disposition` , `Content-Encoding` och`Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Även om Logic Apps inte hindrar dig från att spara Logic Apps som har en svars åtgärd med dessa huvuden, ignorerar Logic Apps dessa huvuden.

1. I Logic App Designer, under steget där du vill lägga till en svars åtgärd väljer du **nytt steg**.

   Till exempel med hjälp av begär ande utlösare från tidigare:

   ![Lägg till nytt steg](./media/connectors-native-reqres/add-response.png)

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd**i sökrutan anger du "Response" som filter och väljer **svars** åtgärden.

   ![Välj åtgärden svars åtgärd](./media/connectors-native-reqres/select-response-action.png)

   Begär ande utlösaren komprimeras i det här exemplet för enkelhetens skull.

1. Lägg till alla värden som krävs för svarsmeddelandet.

   I vissa fält öppnas listan med dynamiskt innehåll genom att klicka inuti rutorna. Du kan sedan välja tokens som representerar tillgängliga utdata från föregående steg i arbets flödet. Egenskaperna från det schema som anges i det tidigare exemplet visas nu i listan med dynamiskt innehåll.

   Till exempel för **rubrik** rutan, Lägg till `Content-Type` som nyckel namn och ange nyckelvärdet till `application/json` som tidigare nämnts i det här avsnittet. I rutan **brödtext** kan du välja utlösare för text i listan med dynamiskt innehåll.

   ![Information om svars åtgärd](./media/connectors-native-reqres/response-details.png)

   Om du vill visa sidhuvudena i JSON-format väljer du **Växla till text visning**.

   ![Rubriker – växla till text visning](./media/connectors-native-reqres/switch-to-text-view.png)

   Här är mer information om de egenskaper som du kan ange i svars åtgärden.

   | Egenskapsnamn | JSON-egenskaps namn | Krävs | Beskrivning |
   |---------------|--------------------|----------|-------------|
   | **Status kod** | `statusCode` | Ja | Status koden som ska returneras i svaret |
   | **Rubriker** | `headers` | Nej | Ett JSON-objekt som beskriver en eller flera huvuden som ska inkluderas i svaret |
   | **Brödtext** | `body` | Nej | Svars texten |
   |||||

1. Om du vill ange ytterligare egenskaper, till exempel ett JSON-schema för svars texten, öppnar du listan **Lägg till ny parameter** och väljer de parametrar som du vill lägga till.

1. När du är klar sparar du din Logic app. I verktygsfältet designer väljer du **Spara**.

## <a name="next-steps"></a>Nästa steg

* [Anslutningsappar för Logic Apps](../connectors/apis-list.md)
