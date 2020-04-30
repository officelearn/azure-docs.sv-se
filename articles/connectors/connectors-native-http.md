---
title: Anropa tjänst slut punkter med HTTP eller HTTPS
description: Skicka utgående HTTP-eller HTTPS-begäranden till tjänst slut punkter från Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 9ed3d960b3f5653ea8706b39559c9d5a71c45a6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81867640"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Anropa tjänst slut punkter via HTTP eller HTTPS från Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och den inbyggda http-utlösaren eller åtgärden kan du skapa automatiserade uppgifter och arbets flöden som skickar begär anden till tjänstens slut punkter via http eller https. Du kan till exempel övervaka tjänst slut punkten för din webbplats genom att kontrol lera slut punkten enligt ett angivet schema. När den angivna händelsen inträffar i slut punkten, till exempel om din webbplats går nedåt, utlöser händelsen din Logic app-arbetsflöde och kör åtgärderna i det arbets flödet. Om du vill ta emot och svara på inkommande HTTPS-anrop i stället använder du den inbyggda [begär ande utlösaren eller svars åtgärden](../connectors/connectors-native-reqres.md).

> [!NOTE]
> Baserat på mål slut punktens kapacitet stöder HTTP-anslutaren Transport Layer Security (TLS 1,0), 1,1 och 1,2. Logic Apps förhandlar med slut punkten genom att använda den högsta version som stöds. Om slut punkten till exempel stöder 1,2 använder anslutnings tjänsten 1,2 först. Annars använder anslutnings tjänsten den näst högsta version som stöds.
>
> HTTP-anslutningen har inte stöd för mellanliggande TLS/SSL-certifikat för autentisering.

Om du vill kontrol lera eller *polla* en slut punkt i ett återkommande schema [lägger du till http-utlösaren](#http-trigger) som det första steget i arbets flödet. Varje gången som utlösaren kontrollerar slut punkten anropar utlösaren eller skickar en *begäran* till slut punkten. Svaret på slut punkten avgör om din Logic Apps-arbetsflöde körs. Utlösaren skickar allt innehåll från slut punktens svar till åtgärder i din Logic app.

[Lägg till HTTP-åtgärden](#http-action)om du vill anropa en slut punkt från någon annan plats i arbets flödet. Svaret på slut punkten avgör hur arbets flödets återstående åtgärder ska köras.

> [!IMPORTANT]
> Om en HTTP-utlösare eller åtgärd inkluderar dessa huvuden, tar Logic Apps bort huvudena från det genererade begär ande meddelandet utan att visa någon varning eller ett fel:
>
> * `Accept-*`
> * `Allow`
> * `Content-*`med dessa undantag: `Content-Disposition`, `Content-Encoding`och`Content-Type`
> * `Cookie`
> * `Expires`
> * `Host`
> * `Last-Modified`
> * `Origin`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Även om Logic Apps inte hindrar dig från att spara Logi Kap par som använder en HTTP-utlösare eller en åtgärd med dessa huvuden, Logic Apps ignorerar dessa huvuden.

Den här artikeln visar hur du lägger till en HTTP-utlösare eller åtgärd i din Logic app-arbetsflöde.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* URL: en för den mål slut punkt som du vill anropa

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md)?

* Den Logic-app från vilken du vill anropa mål slut punkten. Börja med HTTP-utlösaren genom att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda HTTP-åtgärden startar du din Logic-app med valfri utlösare som du vill använda. I det här exemplet används HTTP-utlösaren som det första steget.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Lägg till en HTTP-utlösare

Den här inbyggda utlösaren gör ett HTTP-anrop till den angivna URL: en för en slut punkt och returnerar ett svar.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna din tomma Logic-app i Logic App Designer.

1. Välj **inbyggd i**rutan Sök i designer. I rutan Sök anger `http` du som filter. Välj **http-** utlösare i listan **utlösare** .

   ![Välj HTTP-utlösare](./media/connectors-native-http/select-http-trigger.png)

   I det här exemplet byter du ut utlösaren till "HTTP-utlösare" så att steget får ett mer beskrivande namn. Exemplet lägger också till en HTTP-åtgärd och båda namnen måste vara unika.

1. Ange värdena för de [http trigger-parametrar](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) som du vill inkludera i anropet till mål slut punkten. Konfigurera upprepningen för hur ofta du vill att utlösaren ska kontrol lera mål slut punkten.

   ![Ange parametrar för HTTP-utlösare](./media/connectors-native-http/http-trigger-parameters.png)

   Om du väljer en annan autentiseringstyp än **ingen**är autentiseringsinställningarna annorlunda beroende på ditt val. Mer information om autentiseringstyper som är tillgängliga för HTTP finns i följande avsnitt:

   * [Lägg till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autentisera åtkomst till resurser med hanterade identiteter](../logic-apps/create-managed-service-identity.md)

1. Om du vill lägga till andra tillgängliga parametrar öppnar du listan **Lägg till ny parameter** och väljer de parametrar som du vill använda.

1. Fortsätt att skapa ditt Logic Apps-arbetsflöde med åtgärder som körs när utlösaren utlöses.

1. Kom ihåg att spara din Logic app när du är klar. I verktygsfältet designer väljer du **Spara**.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Lägg till en HTTP-åtgärd

Den här inbyggda åtgärden gör ett HTTP-anrop till den angivna URL: en för en slut punkt och returnerar ett svar.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna din Logic app i Logic App Designer.

   I det här exemplet används HTTP-utlösaren som det första steget.

1. Under steget där du vill lägga till HTTP-åtgärden väljer du **nytt steg**.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd**väljer du **inbyggt**. I rutan Sök anger `http` du som filter. Välj **http-** åtgärd i listan **åtgärder** .

   ![Välj HTTP-åtgärd](./media/connectors-native-http/select-http-action.png)

   Det här exemplet byter namn på åtgärden till "HTTP-åtgärd" så att steget har ett mer beskrivande namn.

1. Ange värdena för de [http-åtgärds parametrar](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) som du vill ta med i anropet till mål slut punkten.

   ![Ange HTTP-åtgärds parametrar](./media/connectors-native-http/http-action-parameters.png)

   Om du väljer en annan autentiseringstyp än **ingen**är autentiseringsinställningarna annorlunda beroende på ditt val. Mer information om autentiseringstyper som är tillgängliga för HTTP finns i följande avsnitt:

   * [Lägg till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autentisera åtkomst till resurser med hanterade identiteter](../logic-apps/create-managed-service-identity.md)

1. Om du vill lägga till andra tillgängliga parametrar öppnar du listan **Lägg till ny parameter** och väljer de parametrar som du vill använda.

1. Kom ihåg att spara din Logic app när du är klar. I verktygsfältet designer väljer du **Spara**.

## <a name="content-with-multipartform-data-type"></a>Innehåll med multipart/form-datatyp

Om du vill hantera innehåll `multipart/form-data` som har en typ i HTTP-begäranden kan du lägga till ett JSON `$content-type` - `$multipart` objekt som innehåller attributen och för HTTP-begärans text med det här formatet.

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

Anta till exempel att du har en logisk app som skickar en HTTP POST-begäran för en Excel-fil till en webbplats genom att använda webbplatsens API, `multipart/form-data` som stöder typen. Så här kan den här åtgärden se ut:

![Multiform-data för multipart](./media/connectors-native-http/http-action-multipart.png)

Här är samma exempel som visar HTTP-åtgärdens JSON-definition i den underliggande arbets flödes definitionen:

```json
"HTTP_action": {
   "inputs": {
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

* [Parametrar för HTTP-utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Parametrar för HTTP-åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Information om utdata

Här är mer information om utdata från en HTTP-utlösare eller åtgärd som returnerar denna information:

| Egenskapsnamn | Typ | Beskrivning |
|---------------|------|-------------|
| sidhuvud | objekt | Huvudena från begäran |
| body | objekt | JSON-objekt | Objektet med bröd text innehållet från begäran |
| statuskod | int | Status koden från begäran |
|||

| Statuskod | Beskrivning |
|-------------|-------------|
| 200 | OK |
| 202 | Accepterad |
| 400 | Felaktig begäran |
| 401 | Behörighet saknas |
| 403 | Förbjudet |
| 404 | Hittades inte |
| 500 | Internt serverfel. Ett okänt fel uppstod. |
|||

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
