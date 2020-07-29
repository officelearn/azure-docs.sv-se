---
title: Anropa tjänst slut punkter med HTTP eller HTTPS
description: Skicka utgående HTTP-eller HTTPS-begäranden till tjänst slut punkter från Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 06/09/2020
tags: connectors
ms.openlocfilehash: 8c7a0ddb80ba28548fc1821cc2063e500af0fa66
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286639"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Anropa tjänstslutpunkter via HTTP eller HTTPS från Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och den inbyggda http-utlösaren eller åtgärden kan du skapa automatiserade uppgifter och arbets flöden som skickar begär anden till tjänstens slut punkter via http eller https. Du kan till exempel övervaka tjänst slut punkten för din webbplats genom att kontrol lera slut punkten enligt ett angivet schema. När den angivna händelsen inträffar i slut punkten, till exempel om din webbplats går nedåt, utlöser händelsen din Logic app-arbetsflöde och kör åtgärderna i det arbets flödet. Om du vill ta emot och svara på inkommande HTTPS-anrop i stället använder du den inbyggda [begär ande utlösaren eller svars åtgärden](../connectors/connectors-native-reqres.md).

* Om du vill kontrol lera eller *polla* en slut punkt i ett återkommande schema [lägger du till http-utlösaren](#http-trigger) som det första steget i arbets flödet. Varje gången som utlösaren kontrollerar slut punkten anropar utlösaren eller skickar en *begäran* till slut punkten. Svaret på slut punkten avgör om din Logic Apps-arbetsflöde körs. Utlösaren skickar allt innehåll från slut punktens svar till åtgärder i din Logic app.

* [Lägg till HTTP-åtgärden](#http-action)om du vill anropa en slut punkt från någon annan plats i arbets flödet. Svaret på slut punkten avgör hur arbets flödets återstående åtgärder ska köras.

Den här artikeln visar hur du lägger till en HTTP-utlösare eller åtgärd i din Logic app-arbetsflöde.

## <a name="prerequisites"></a>Förutsättningar

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

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

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

<a name="tls-support"></a>

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Baserat på mål slut punktens kapacitet, stöder utgående anrop Transport Layer Security (TLS), som tidigare Secure Sockets Layer (SSL), version 1,0, 1,1 och 1,2. Logic Apps förhandlar med slut punkten genom att använda den högsta version som stöds.

Om slut punkten till exempel stöder 1,2 använder HTTP-anslutaren 1,2 först. Annars använder anslutnings tjänsten den näst högsta version som stöds.

<a name="self-signed"></a>

## <a name="self-signed-certificates"></a>Självsignerade certifikat

* För logi Kap par i den globala Azure-miljön för flera klienter tillåter HTTP-anslutaren inte självsignerade TLS/SSL-certifikat. Om din Logic app gör ett HTTP-anrop till en server och visar ett certifikat för TLS/SSL, kan HTTP-anropet Miss lyckas med ett `TrustFailure` fel.

* För logi Kap par i en [integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)tillåts http-anslutningen självsignerade certifikat för TLS/SSL-handskakning. Du måste dock först [Aktivera stöd för självsignerade certifikat](../logic-apps/create-integration-service-environment-rest-api.md#request-body) för en befintlig ISE eller ny ISE med hjälp av Logic Apps REST API och installera det offentliga certifikatet på `TrustedRoot` platsen.

## <a name="content-with-multipartform-data-type"></a>Innehåll med multipart/form-datatyp

Om du vill hantera innehåll som har en `multipart/form-data` typ i HTTP-begäranden kan du lägga till ett JSON-objekt som innehåller `$content-type` `$multipart` attributen och för HTTP-begärans text med det här formatet.

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

Anta till exempel att du har en logisk app som skickar en HTTP POST-begäran för en Excel-fil till en webbplats genom att använda webbplatsens API, som stöder `multipart/form-data` typen. Så här kan den här åtgärden se ut:

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

<a name="asynchronous-pattern"></a>

## <a name="asynchronous-request-response-behavior"></a>Svars beteende för asynkron begäran

Som standard följer alla HTTP-baserade åtgärder i Azure Logic Apps standard mönstret för [asynkron åtgärd](/azure/architecture/patterns/async-request-reply). Det här mönstret anger att efter att en HTTP-åtgärd anropar eller skickar en begäran till en slut punkt, tjänst, system eller API, returnerar mottagaren omedelbart ett ["202 godkänt"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) -svar. Den här koden bekräftar att mottagaren har accepterat begäran men inte avslutat bearbetning. Svaret kan innehålla ett `location` huvud som anger URL: en och ett uppdaterings-ID som anroparen kan använda för att avsöka eller kontrol lera status för den asynkrona begäran tills mottagaren slutar bearbeta och returnerar svaret ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) eller något annat icke-202-svar. Anroparen behöver dock inte vänta på begäran att slutföra bearbetningen och kan fortsätta att köra nästa åtgärd. Mer information finns i avsnittet om [asynkron mikrotjänst integrering framtvingar sig oberoende av mikrotjänster](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging).

* I Logic Apps designer har HTTP-åtgärden, men inte utlösaren, en **asynkron mönster** inställning som är aktive rad som standard. Den här inställningen anger att anroparen inte väntar på att bearbetningen ska slutföras och kan gå vidare till nästa åtgärd, men fortsätter att kontrol lera statusen tills bearbetningen stoppas. Om inställningen är inaktive rad anger den här inställningen att anroparen väntar på att bearbetningen ska slutföras innan den går vidare till nästa åtgärd.

  Du hittar den här inställningen genom att följa dessa steg:

  1. I HTTP-åtgärdens namn List väljer du knappen med tre punkter (**...**), som öppnar åtgärdens inställningar.

  1. Hitta inställningen för **asynkrona mönster** .

     ![Inställningen "asynkront mönster"](./media/connectors-native-http/asynchronous-pattern-setting.png)

* HTTP-åtgärdens underliggande JavaScript Object Notation-definition (JSON) följer implicit efter det asynkrona åtgärds mönstret.

<a name="disable-asynchronous-operations"></a>

## <a name="disable-asynchronous-operations"></a>Inaktivera asynkrona åtgärder

Ibland kanske du vill använda HTTP-åtgärdens asynkrona beteende i vissa scenarier, till exempel när du vill:

* [Undvik HTTP-tidsgräns för tids krävande uppgifter](#avoid-http-timeouts)
* [Inaktivera kontroll av plats rubriker](#disable-location-header-check)

<a name="turn-off-asynchronous-pattern-setting"></a>

### <a name="turn-off-asynchronous-pattern-setting"></a>Inaktivera **asynkron mönster** inställning

1. I Logic Apps designer, i HTTP-åtgärdens namn List, väljer du knappen med tre punkter (**...**), som öppnar åtgärdens inställningar.

1. Hitta inställningen för **asynkront mönster** **, aktivera inställningen om den** är aktive rad och välj **färdig**.

   ![Inaktivera inställningen "asynkront mönster"](./media/connectors-native-http/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Inaktivera asynkront mönster i åtgärdens JSON-definition

I HTTP-åtgärdens underliggande JSON-definition [lägger du till `"DisableAsyncPattern"` alternativet åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options) i åtgärdens definition så att åtgärden följer synkront åtgärds mönster i stället. Mer information finns även i [köra åtgärder i ett synkront åtgärds mönster](../logic-apps/logic-apps-workflow-actions-triggers.md#disable-asynchronous-pattern).

<a name="avoid-http-timeouts"></a>

## <a name="avoid-http-timeouts-for-long-running-tasks"></a>Undvik HTTP-tidsgräns för tids krävande uppgifter

HTTP-begäranden har en [tids](../logic-apps/logic-apps-limits-and-config.md#http-limits)gräns. Om du har en tids krävande HTTP-åtgärd som har nått tids gränsen på grund av den här gränsen har du följande alternativ:

* [Inaktivera HTTP-åtgärdens asynkrona åtgärds mönster](#disable-asynchronous-operations) så att åtgärden inte avsöks kontinuerligt eller kontrol lera status för begäran. I stället väntar åtgärden på att mottagaren ska svara med status och resultat när begäran har slutfört bearbetningen.

* Ersätt HTTP-åtgärden med [åtgärden http-webhook](../connectors/connectors-native-webhook.md), som väntar på att mottagaren ska svara med status och resultat när begäran har slutfört bearbetningen.

<a name="disable-location-header-check"></a>

## <a name="disable-checking-location-headers"></a>Inaktivera kontroll av plats rubriker

Vissa slut punkter, tjänster, system eller API: er returnerar ett "202 ACCEPTERAt"-svar som inte har någon `location` rubrik. För att undvika att en HTTP-åtgärd kontinuerligt kontrollerar status för begäran när `location` rubriken inte finns kan du välja mellan följande alternativ:

* [Inaktivera HTTP-åtgärdens asynkrona åtgärds mönster](#disable-asynchronous-operations) så att åtgärden inte avsöks kontinuerligt eller kontrol lera status för begäran. I stället väntar åtgärden på att mottagaren ska svara med status och resultat när begäran har slutfört bearbetningen.

* Ersätt HTTP-åtgärden med [åtgärden http-webhook](../connectors/connectors-native-webhook.md), som väntar på att mottagaren ska svara med status och resultat när begäran har slutfört bearbetningen.

## <a name="known-issues"></a>Kända problem

<a name="omitted-headers"></a>

### <a name="omitted-http-headers"></a>Utelämnade HTTP-huvuden

Om en HTTP-utlösare eller åtgärd inkluderar dessa huvuden, tar Logic Apps bort huvudena från det genererade begär ande meddelandet utan att visa någon varning eller ett fel:

* `Accept-*`
* `Allow`
* `Content-*`med dessa undantag: `Content-Disposition` , `Content-Encoding` och`Content-Type`
* `Cookie`
* `Expires`
* `Host`
* `Last-Modified`
* `Origin`
* `Set-Cookie`
* `Transfer-Encoding`

Även om Logic Apps inte hindrar dig från att spara Logi Kap par som använder en HTTP-utlösare eller en åtgärd med dessa huvuden, Logic Apps ignorerar dessa huvuden.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer information om utlösare och åtgärds parametrar finns i följande avsnitt:

* [Parametrar för HTTP-utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Parametrar för HTTP-åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Information om utdata

Här är mer information om utdata från en HTTP-utlösare eller åtgärd som returnerar denna information:

| Egenskap | Typ | Beskrivning |
|----------|------|-------------|
| `headers` | JSON-objekt | Huvudena från begäran |
| `body` | JSON-objekt | Objektet med bröd text innehållet från begäran |
| `status code` | Heltal | Status koden från begäran |
|||

| Statuskod | Beskrivning |
|-------------|-------------|
| 200 | OK |
| 202 | Har godkänts |
| 400 | Felaktig begäran |
| 401 | Behörighet saknas |
| 403 | Förbjudet |
| 404 | Hittades inte |
| 500 | Internt serverfel. Ett okänt fel uppstod. |
|||

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)

