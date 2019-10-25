---
title: Anropa HTTP-och HTTPS-slutpunkter – Azure Logic Apps
description: Skicka utgående begär anden till HTTP-och HTTPS-slutpunkter med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: 4f585778808b133fe5c731ab6a3189594cbfc0f9
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804225"
---
# <a name="send-outgoing-calls-to-http-or-https-endpoints-by-using-azure-logic-apps"></a>Skicka utgående anrop till HTTP-eller HTTPS-slutpunkter med hjälp av Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och den inbyggda http-utlösaren eller åtgärden kan du skapa automatiserade uppgifter och arbets flöden som regelbundet skickar begär anden till http-eller https-slutpunkter. Använd den inbyggda [begär ande utlösaren eller svars åtgärden](../connectors/connectors-native-reqres.md)för att ta emot och svara på inkommande http-eller https-anrop i stället.

Du kan till exempel övervaka tjänst slut punkten för din webbplats genom att kontrol lera slut punkten enligt ett angivet schema. När en viss händelse inträffar vid den slut punkten, till exempel om din webbplats går ned, utlöser händelsen din Logic app-arbetsflöde och kör de angivna åtgärderna.

Om du vill kontrol lera eller *avsöka* en slut punkt enligt ett regelbundet schema kan du använda http-utlösaren som det första steget i arbets flödet. Vid varje kontroll skickar utlösaren ett anrop eller en *begäran* till slut punkten. Svaret på slut punkten avgör om din Logic Apps-arbetsflöde körs. Utlösaren passerar allt innehåll från svar till åtgärder i din Logic app.

Du kan använda HTTP-åtgärden som andra steg i arbets flödet för att anropa slut punkten när du vill. Svaret på slut punkten avgör hur arbets flödets återstående åtgärder ska köras.

Baserat på mål slut punktens kapacitet stöder HTTP-anslutaren Transport Layer Security (TLS 1,0), 1,1 och 1,2. Logic Apps förhandlar med slut punkten genom att använda den högsta version som stöds. Om slut punkten till exempel stöder 1,2 använder anslutaren 1,2 först. Annars använder anslutnings tjänsten den näst högsta version som stöds.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* URL: en för den mål slut punkt som du vill anropa

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md)?

* Den Logic-app från vilken du vill anropa mål slut punkten. Börja med HTTP-utlösaren genom att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda HTTP-åtgärden startar du din Logic-app med valfri utlösare som du vill använda. I det här exemplet används HTTP-utlösaren som det första steget.

## <a name="add-an-http-trigger"></a>Lägg till en HTTP-utlösare

Den här inbyggda utlösaren gör ett HTTP-anrop till den angivna URL: en för en slut punkt och returnerar ett svar.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna din tomma Logic-app i Logic App Designer.

1. Under **Välj en åtgärd**i rutan Sök anger du "http" som filter. Välj **http-** utlösare i listan **utlösare** .

   ![Välj HTTP-utlösare](./media/connectors-native-http/select-http-trigger.png)

   I det här exemplet byter du ut utlösaren till "HTTP-utlösare" så att steget får ett mer beskrivande namn. Exemplet lägger också till en HTTP-åtgärd och båda namnen måste vara unika.

1. Ange värdena för de [http trigger-parametrar](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) som du vill inkludera i anropet till mål slut punkten. Konfigurera upprepningen för hur ofta du vill att utlösaren ska kontrol lera mål slut punkten.

   Om du väljer en annan autentiseringstyp än **ingen**är autentiseringsinställningarna annorlunda beroende på ditt val. Mer information finns i [lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   ![Ange parametrar för HTTP-utlösare](./media/connectors-native-http/http-trigger-parameters.png)

1. Om du vill lägga till andra tillgängliga parametrar öppnar du listan **Lägg till ny parameter** och väljer de parametrar som du vill använda.

1. Fortsätt att skapa ditt Logic Apps-arbetsflöde med åtgärder som körs när utlösaren utlöses.

1. Kom ihåg att spara din Logic app när du är klar. I verktygsfältet designer väljer du **Spara**.

## <a name="add-an-http-action"></a>Lägg till en HTTP-åtgärd

Den här inbyggda åtgärden gör ett HTTP-anrop till den angivna URL: en för en slut punkt och returnerar ett svar.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna din Logic app i Logic App Designer.

   I det här exemplet används HTTP-utlösaren som det första steget.

1. Under steget där du vill lägga till HTTP-åtgärden väljer du **nytt steg**.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd**i rutan Sök anger du "http" som filter. Välj **http-** åtgärd i listan **åtgärder** .

   ![Välj HTTP-åtgärd](./media/connectors-native-http/select-http-action.png)

   Det här exemplet byter namn på åtgärden till "HTTP-åtgärd" så att steget har ett mer beskrivande namn.

1. Ange värdena för de [http-åtgärds parametrar](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) som du vill ta med i anropet till mål slut punkten.

   Om du väljer en annan autentiseringstyp än **ingen**är autentiseringsinställningarna annorlunda beroende på ditt val. Mer information finns i [lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   ![Ange HTTP-åtgärds parametrar](./media/connectors-native-http/http-action-parameters.png)

1. Om du vill lägga till andra tillgängliga parametrar öppnar du listan **Lägg till ny parameter** och väljer de parametrar som du vill använda.

1. Kom ihåg att spara din Logic app när du är klar. I verktygsfältet designer väljer du **Spara**.

## <a name="content-with-multipartform-data-type"></a>Innehåll med multipart/form-datatyp

Om du vill hantera innehåll som har `multipart/form-data` typ i HTTP-begäranden kan du lägga till ett JSON-objekt som innehåller attributen `$content-type` och `$multipart` för HTTP-begärans brödtext med hjälp av det här formatet.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Anta till exempel att du har en logisk app som skickar en HTTP POST-begäran för en Excel-fil till en webbplats med hjälp av den webbplatsens API, som stöder `multipart/form-data` typen. Så här kan den här åtgärden se ut:

![Multiform-data för multipart](./media/connectors-native-http/http-action-multipart.png)

Här är samma exempel som visar HTTP-åtgärdens JSON-definition i den underliggande arbets flödes definitionen:

```json
{
   "HTTP_action": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer information om utlösare och åtgärds parametrar finns i följande avsnitt:

* [Parametrar för HTTP-utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [Parametrar för HTTP-åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>Information om utdata

Här är mer information om utdata från en HTTP-utlösare eller åtgärd som returnerar denna information:

| Egenskapsnamn | Typ | Beskrivning |
|---------------|------|-------------|
| sidhuvud | objekt | Huvudena från begäran |
| brödtext | objekt | JSON-objekt | Objektet med bröd text innehållet från begäran |
| status kod | int | Status koden från begäran |
|||

| Statuskod | Beskrivning |
|-------------|-------------|
| 200 | OK |
| 202 | Accept |
| 400 | Felaktig begäran |
| 401 | Behörighet saknas |
| 403 | Förbjudet |
| 404 | Hittades inte |
| 500 | Internt Server fel. Ett okänt fel uppstod. |
|||

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
