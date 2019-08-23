---
title: Ansluta till SAP-system – Azure Logic Apps | Microsoft Docs
description: Så här får du åtkomst till och hanterar SAP-resurser genom att automatisera arbets flöden med Azure Logic Apps
author: ecfan
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 31f6a3fc281b8dc309ddcd237246c870c85ae20b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971630"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Ansluta till SAP-system från Azure Logic Apps

> [!NOTE]
> Denna SAP-koppling är schemalagd för utfasning. Använd eller migrera till en [nyare och mer avancerad SAP-anslutning](./logic-apps-using-sap-connector.md). 

Den här artikeln visar hur du kan komma åt dina SAP-resurser inifrån en Logic-app med hjälp av SAP Application Server och SAP Message Server-kopplingar. På så sätt kan du automatisera uppgifter, processer och arbets flöden som hanterar dina SAP-data och-resurser genom att skapa Logic Apps.

I det här exemplet används en Logic-app som du kan utlösa med en HTTP-begäran. Logic-appen skickar ett mellanliggande dokument (IDoc) till en SAP-server och returnerar ett svar till den begär Ande som anropade Logic-appen.
De aktuella SAP-anslutningarna har åtgärder, men inte utlösare, så det här exemplet använder [http-begäran](../connectors/connectors-native-reqres.md) utlösare som det första steget i Logic app-arbetsflödet. Information om SAP Connector-teknisk information finns i följande referens artiklar: 

* <a href="https://docs.microsoft.com/connectors/sap" target="blank">SAP Application Server-anslutning</a>
* <a href="https://docs.microsoft.com/connectors/sap/#send-message-to-sap" target="blank">Anslutning till SAP Message Server</a>

Om du inte har någon Azure-prenumeration ännu kan du <a href="https://azure.microsoft.com/free/" target="_blank">Registrera dig för ett kostnads fritt Azure-konto</a>.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa med i den här artikeln behöver du följande objekt:

* Den Logic-app från vilken du vill komma åt SAP-systemet och en utlösare som startar din Logic app-arbetsflöde. SAP-kopplingarna tillhandahåller för närvarande endast åtgärder. Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabb start: Skapa din första Logic-](../logic-apps/quickstart-create-first-logic-app-workflow.md)app.

* Din <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">SAP</a> -Programserver eller <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP Message Server</a>

* Hämta och installera den senaste [lokala](https://www.microsoft.com/download/details.aspx?id=53127) datagatewayen på en lokal dator. Se till att konfigurera din gateway i Azure Portal innan du fortsätter. Gatewayen hjälper dig att få säker åtkomst till data och resurser lokalt. Mer information finns i [Installera lokal datagateway för Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Hämta och installera det senaste SAP-klientcertifikatet, som för närvarande är <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">SAP Connector (NCo) 3.0.20.0 för Microsoft .NET Framework 4,0 och Windows 64-bitars (x64)</a>, på samma dator som den lokala datagatewayen. Installera den här versionen eller senare av följande anledningar:

  * Tidigare SAP NCo-versioner kan bli död när fler än ett IDoc-meddelande skickas på samma gång. 
  Det här villkoret blockerar alla senare meddelanden som skickas till SAP-målet, vilket leder till timeout.

  * Den lokala datagatewayen körs bara på 64-bitars system. 
  Annars får du fel meddelandet "felaktig avbildning" eftersom värd tjänsten för data Gateway inte stöder 32-bitars sammansättningar.

  * Både data Gateway-värdservern och Microsoft SAP-adaptern använder .NET Framework 4,5. SAP-NCo för .NET Framework 4,0 fungerar med processer som använder .NET Runtime 4,0 till 4.7.1. 
  SAP-NCo för .NET Framework 2,0 fungerar med processer som använder .NET Runtime 2,0 till 3,5 och fungerar inte längre med den senaste lokala datagatewayen.

* Meddelande innehåll som du kan skicka till din SAP-server, till exempel en IDoc-fil. Innehållet måste vara i XML-format och innehålla namn området för den SAP-åtgärd som du vill använda.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>Lägg till utlösare för HTTP-begäran

I Azure Logic Apps måste varje Logi Kap par starta med en [](../logic-apps/logic-apps-overview.md#logic-app-concepts)utlösare som utlöses när en enskild händelse inträffar eller när ett särskilt villkor uppfylls. Varje gång utlösaren utlöses skapar Logic Apps-motorn en Logic App-instans och börjar köra appens arbets flöde.

I det här exemplet skapar du en Logic-app med en slut punkt i Azure så att du kan skicka *http post-begäranden* till din Logic app. När din Logic app tar emot dessa HTTP-förfrågningar utlöses utlösaren och kör nästa steg i arbets flödet.

1. I Azure Portal skapar du en tom Logic-app som öppnar Logic Apps designer. 

2. I rutan Sök anger du "http-begäran" som filter. Välj den här utlösaren i listan utlösare: **Förfrågan – när en HTTP-begäran tas emot**

   ![Lägg till utlösare för HTTP-begäran](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. Spara din Logic app så att du kan skapa en slut punkts-URL för din Logic app.
Välj **Spara** i designerverktygsfältet. 

   Slut punkts-URL: en visas nu i utlösaren, till exempel:

   ![Generera URL för slut punkt](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>Lägg till SAP-åtgärd

I Azure Logic Apps är en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) ett steg i arbets flödet som följer en utlösare eller en annan åtgärd. Om du inte har lagt till en utlösare i din Logic app och vill följa det här exemplet lägger du till utlösaren som [beskrivs i det här avsnittet](#add-trigger).

1. I Logic App Designer går du till utlösaren och väljer **nytt steg** > **Lägg till en åtgärd**.

   ![Lägga till en åtgärd](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. I rutan Sök anger du "SAP-server" som filter. I listan åtgärder väljer du åtgärden för din SAP-server: 

   * **SAP-program Server – skicka till SAP**
   * **SAP-meddelande Server – skicka till SAP**

   I det här exemplet används den här åtgärden: **SAP-program Server – skicka till SAP**

   ![Välj "SAP-program Server" eller "SAP Message Server"](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. Om du uppmanas att ange anslutnings information skapar du din SAP-anslutning nu. Annars, om anslutningen redan finns, Fortsätt med nästa steg så att du kan konfigurera din SAP-åtgärd. 

   **Skapa lokal SAP-anslutning**

   1. För **gateways**väljer du **Anslut via lokal datagateway** så att egenskaperna för den lokala anslutningen visas.

   2. Ange anslutnings informationen för din SAP-server. 
   För egenskapen **Gateway** väljer du den datagateway som du skapade i Azure Portal för gateway-installationen, till exempel:

      **SAP-program Server**

      ![Skapa SAP Application Server-anslutning](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **SAP-meddelande Server**

      ![Skapa anslutning till SAP Message Server](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. När du är klar väljer du **Skapa**.

      Logic Apps konfigurerar och testar anslutningen och kontrollerar att anslutningen fungerar korrekt.

4. Nu hittar och väljer du en åtgärd från SAP-servern. 

   1. I rutan **SAP-åtgärd** väljer du mappikonen. 
   I mapplistan söker du efter och väljer den åtgärd som du vill använda. 

      I det här exemplet väljs kategorin **iDOC** för åtgärden iDOC. 

      ![Sök och Välj åtgärden IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      Om du inte hittar den åtgärd du vill ha kan du ange en sökväg manuellt, till exempel:

      ![Ange sökväg till IDoc-åtgärden manuellt](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      Mer information om IDoc-åtgärder finns i [meddelande scheman för iDOC-åtgärder](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

   2. Klicka i rutan **inmatat meddelande** så att listan med dynamiskt innehåll visas. 
   Välj fältet **brödtext** under **när en http-begäran tas emot**i listan. 

      Det här steget inkluderar bröd innehållet från din HTTP Request-utlösare och skickar utdata till SAP-servern.

      ![Välj fältet brödtext](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      När du är klar ser din SAP-åtgärd ut som i det här exemplet:

      ![Slutför SAP-åtgärd](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>Lägg till HTTP-svars åtgärd

Lägg nu till en svars åtgärd i din Logic Apps-arbetsflöde och inkludera utdata från SAP-åtgärden. På så sätt returnerar din Logic-app resultatet från din SAP-server till den ursprungliga begär Ande. 

1. I Logic Apps designer, under SAP-åtgärden, väljer du **nytt steg** > **Lägg till en åtgärd**.

2. I rutan Sök anger du "Response" som filter. I listan åtgärder väljer du den här åtgärden: **Request-Response**

3. Klicka i rutan **brödtext** så att listan med dynamiskt innehåll visas. I listan, under **Skicka till SAP**, väljer du fältet **brödtext** . 

   ![Slutför SAP-åtgärd](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. Spara din logikapp. 

## <a name="test-your-logic-app"></a>Testa din Logic app

1. Om din Logic app inte redan är aktive rad väljer du **Översikt**på din Logic app-meny. Välj **Aktivera**i verktygsfältet. 

2. I verktygsfältet Logic App Designer väljer du **Kör**. Det här steget startar din Logic app manuellt.

3. Utlös din Logic app genom att skicka en HTTP POST-begäran till URL: en i din HTTP Request-utlösare och inkludera ditt meddelande innehåll med din begäran. Du kan använda ett verktyg som Postman för att skicka begäran. [](https://www.getpostman.com/apps) 

   I den här artikeln skickar begäran en IDoc-fil som måste vara i XML-format och innehålla namn området för den SAP-åtgärd som du använder, till exempel: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. När du har skickat din HTTP-begäran väntar du på svaret från din Logic app.

> [!NOTE]
> Din Logi Kap par kan ta lång tid om alla steg som krävs för svaret inte slutförs inom [tids gränsen för begäran](./logic-apps-limits-and-config.md). Om det här tillståndet inträffar kan begär Anden blockeras. För att hjälpa dig att diagnostisera problem kan du läsa mer om hur du kan [kontrol lera och övervaka dina Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Grattis, nu har du skapat en logisk app som kan kommunicera med din SAP-server. Nu när du har konfigurerat en SAP-anslutning för din Logic app kan du utforska andra tillgängliga SAP-åtgärder, till exempel BAPI och RFC.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om anslutningen enligt beskrivningen i anslutningens Swagger-filer, finns i följande referens artiklar: 

* [SAP-program Server](/connectors/sap)
* [SAP-meddelande Server](/connectors/sap/#send-message-to-sap)

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Ansluta till lokala system](../logic-apps/logic-apps-gateway-connection.md) från Logic Apps
* Lär dig hur du verifierar, transformerar och andra meddelande åtgärder med [Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
