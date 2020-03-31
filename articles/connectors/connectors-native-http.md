---
title: Anropa tjänstslutpunkter med http- eller HTTPS
description: Skicka utgående HTTP- eller HTTPS-begäranden till tjänstslutpunkter från Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 8aefe851708c0b8d8780d03e4364e034e783bf4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297224"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Anropa tjänstslutpunkter via HTTP eller HTTPS från Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och den inbyggda HTTP-utlösaren eller åtgärden kan du skapa automatiserade uppgifter och arbetsflöden som skickar begäranden till tjänstslutpunkter via HTTP eller HTTPS. Du kan till exempel övervaka tjänstens slutpunkt för din webbplats genom att kontrollera slutpunkten enligt ett visst schema. När den angivna händelsen inträffar vid den slutpunkten, till exempel att din webbplats går nedåt, utlöser händelsen logikappens arbetsflöde och kör åtgärderna i arbetsflödet. Om du vill ta emot och svara på inkommande HTTPS-anrop i stället använder du den inbyggda [utlösaren begäran eller svarsåtgärden](../connectors/connectors-native-reqres.md).

> [!NOTE]
> HTTP-anslutningspunkten är baserad på målslutpunktens kapacitet och stöder TLS-versionerna 1.0, 1.1 och 1.2. Logic Apps förhandlar med slutpunkten över med högsta möjliga version som stöds. Så om slutpunkten stöder 1.2 använder kopplingen 1.2 först. Annars använder kopplingen den näst högsta versionen som stöds.

Om du vill kontrollera eller *avsöka* en slutpunkt enligt ett återkommande schema [lägger du till HTTP-utlösaren](#http-trigger) som det första steget i arbetsflödet. Varje gång utlösaren kontrollerar slutpunkten anropar eller skickar utlösaren en *begäran* till slutpunkten. Slutpunktens svar avgör om logikappens arbetsflöde körs. Utlösaren skickar innehåll från slutpunktens svar på åtgärderna i logikappen.

Om du vill anropa en slutpunkt från någon annanstans i arbetsflödet [lägger du till HTTP-åtgärden](#http-action). Slutpunktens svar avgör hur arbetsflödets återstående åtgärder körs.

> [!IMPORTANT]
> Om en HTTP-utlösare eller -åtgärd innehåller dessa rubriker tar Logic Apps bort dessa rubriker från det genererade begärandemeddelandet utan att visa någon varning eller något fel:
>
> * `Accept-*`
> * `Allow`
> * `Content-*`med dessa `Content-Disposition`undantag: , `Content-Encoding`och`Content-Type`
> * `Cookie`
> * `Expires`
> * `Host`
> * `Last-Modified`
> * `Origin`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Även om Logic Apps inte hindrar dig från att spara logikappar som använder en HTTP-utlösare eller -åtgärd med dessa rubriker, ignorerar Logic Apps dessa rubriker.

Den här artikeln visar hur du lägger till en HTTP-utlösare eller åtgärd i logikappens arbetsflöde.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* URL:en för målslutpunkten som du vill anropa

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Logikappen där du vill anropa målslutpunkten. Om du vill börja med HTTP-utlösaren [skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda HTTP-åtgärden startar du logikappen med valfri utlösare. I det här exemplet används HTTP-utlösaren som det första steget.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Lägga till en HTTP-utlösare

Den här inbyggda utlösaren anropar ett HTTP-anrop till den angivna URL:en för en slutpunkt och returnerar ett svar.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna din tomma logikapp i Logic App Designer.

1. Under designerns sökruta väljer du **Inbyggd**. Ange som filter `http` i sökrutan. Välj **HTTP-utlösaren** i listan **Utlösare.**

   ![Välj HTTP-utlösare](./media/connectors-native-http/select-http-trigger.png)

   I det här exemplet byter du namn på utlösaren till HTTP-utlösaren så att steget får ett mer beskrivande namn. Exemplet lägger också till en HTTP-åtgärd och båda namnen måste vara unika.

1. Ange värdena för [de HTTP-utlösarparametrar](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) som du vill inkludera i anropet till målslutpunkten. Ställ in upprepningen för hur ofta du vill att utlösaren ska kontrollera målslutpunkten.

   ![Ange HTTP-utlösarparametrar](./media/connectors-native-http/http-trigger-parameters.png)

   Om du väljer en annan autentiseringstyp än **Ingen**skiljer sig autentiseringsinställningarna åt beroende på ditt val. Mer information om tillgängliga autentiseringstyper för HTTP finns i följande avsnitt:

   * [Lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autentisera åtkomst till resurser med hanterade identiteter](../logic-apps/create-managed-service-identity.md)

1. Om du vill lägga till andra tillgängliga parametrar öppnar du listan **Lägg till ny parameter** och väljer önskade parametrar.

1. Fortsätt att skapa logikappens arbetsflöde med åtgärder som körs när utlösaren utlöses.

1. När du är klar, kom ihåg att spara din logikapp. Välj **Spara**i designerverktygsfältet .

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Lägga till en HTTP-åtgärd

Den här inbyggda åtgärden gör ett HTTP-anrop till den angivna URL:en för en slutpunkt och returnerar ett svar.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna logikappen i Logic App Designer.

   I det här exemplet används HTTP-utlösaren som det första steget.

1. Under det steg där du vill lägga till HTTP-åtgärden väljer du **Nytt steg**.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Markera plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd**väljer du **Inbyggd**. Ange som filter `http` i sökrutan. Välj **HTTP-åtgärden** i listan **Åtgärder.**

   ![Välj HTTP-åtgärd](./media/connectors-native-http/select-http-action.png)

   I det här exemplet byter du namn på åtgärden till "HTTP-åtgärd" så att steget får ett mer beskrivande namn.

1. Ange värdena för de [HTTP-åtgärdsparametrar](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) som du vill inkludera i anropet till målslutpunkten.

   ![Ange HTTP-åtgärdsparametrar](./media/connectors-native-http/http-action-parameters.png)

   Om du väljer en annan autentiseringstyp än **Ingen**skiljer sig autentiseringsinställningarna åt beroende på ditt val. Mer information om tillgängliga autentiseringstyper för HTTP finns i följande avsnitt:

   * [Lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autentisera åtkomst till resurser med hanterade identiteter](../logic-apps/create-managed-service-identity.md)

1. Om du vill lägga till andra tillgängliga parametrar öppnar du listan **Lägg till ny parameter** och väljer önskade parametrar.

1. När du är klar, kom ihåg att spara din logikapp. Välj **Spara**i designerverktygsfältet .

## <a name="content-with-multipartform-data-type"></a>Innehåll med datatyp med flera delar/formulär

Om du vill `multipart/form-data` hantera innehåll som har typ i HTTP-begäranden kan du lägga till ett JSON-objekt som innehåller `$content-type` och `$multipart` attributen till HTTP-begärans brödtext med det här formatet.

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

Anta till exempel att du har en logikapp som skickar en HTTP POST-begäran om en `multipart/form-data` Excel-fil till en webbplats med hjälp av webbplatsens API, som stöder typen. Så här kan den här åtgärden se ut:

![Data för formulär med flera delar](./media/connectors-native-http/http-action-multipart.png)

Här är samma exempel som visar HTTP-åtgärdens JSON-definition i den underliggande arbetsflödesdefinitionen:

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

Mer information om utlösar- och åtgärdsparametrar finns i följande avsnitt:

* [HTTP-utlösarparametrar](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP-åtgärdsparametrar](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Information om utdata

Här finns mer information om utdata från en HTTP-utlösare eller -åtgärd som returnerar den här informationen:

| Egenskapsnamn | Typ | Beskrivning |
|---------------|------|-------------|
| Headers | objekt | Rubrikerna från begäran |
| body | objekt | JSON-objekt | Objektet med brödtextinnehållet från begäran |
| statuskod | int | Statuskoden från begäran |
|||

| Statuskod | Beskrivning |
|-------------|-------------|
| 200 | OK |
| 202 | Accepterad |
| 400 | Felaktig begäran |
| 401 | Behörighet saknas |
| 403 | Förbjudet |
| 404 | Hittades inte |
| 500 | Internt serverfel. Okänt fel uppstod. |
|||

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
