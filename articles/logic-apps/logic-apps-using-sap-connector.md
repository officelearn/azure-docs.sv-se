---
title: Ansluta till SAP system - Azure Logic Apps | Microsoft Docs
description: Hur du åt och hantera SAP-resurser genom att automatisera arbetsflöden med Azure Logikappar
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 3837896911b92361e0a6d0a7166a1b17651d6fe3
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112868"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Ansluta till SAP-system från Azure Logic Apps

Den här artikeln visar hur du kan komma åt din SAP-resurser från inuti en logikapp genom att använda SAP-programserver och SAP Message Server-kopplingar. På så sätt kan du automatisera uppgifter, processer och arbetsflöden som hanterar din SAP-data och resurser genom att skapa logikappar.

Det här exemplet används en logikapp som du kan utlösa med en HTTP-begäran. Logikappen skickar ett mellanliggande dokument (IDoc) till en SAP-server och returnerar ett svar till den begärande som kallas logikappen.
De aktuella SAP-kopplingarna har åtgärder, men inte utlösare, så det här exemplet används den [HTTP-begäran utlösaren](../connectors/connectors-native-reqres.md) som det första steget i den logikapp arbetsflöde. SAP connector-specifik teknisk information finns i dessa referensartiklar: 

* <a href="https://docs.microsoft.com/connectors/sapapplicationserver/" target="blank">SAP Application Server-koppling</a>
* <a href="https://docs.microsoft.com/connectors/sapmessageserver/" target="blank">SAP meddelandet Server-anslutningen</a>

Om du inte har en Azure-prenumeration ännu, <a href="https://azure.microsoft.com/free/" target="_blank">registrera dig för ett kostnadsfritt Azure-konto</a>.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här artikeln är vad du behöver:

* Logikapp från där du vill komma åt din SAP-system och en utlösare som startar din logikapp arbetsflöde. SAP-anslutningar innehåller för närvarande endast åtgärder. Om du har använt logikappar granska [vad är Azure Logikappar](../logic-apps/logic-apps-overview.md) och [Snabbstart: skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Din <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">SAP-programserver</a> eller <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP Message Server</a>

* Hämta och installera senaste [lokala datagateway](https://www.microsoft.com/download/details.aspx?id=53127) på en lokal dator. Kontrollera att du konfigurerar din gateway i Azure-portalen innan du fortsätter. Gatewayen hjälper dig att på ett säkert sätt komma åt data och resurser som är lokalt. Mer information finns i [installera lokala datagateway för Azure Logikappar](../logic-apps/logic-apps-gateway-install.md).

* Hämta och installera det senaste SAP klientbiblioteket som för närvarande är <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">SAP-anslutningen (NCo) 3.0.20.0 för Microsoft .NET Framework 4.0 och Windows 64-bitars (x64)</a>, på samma dator som lokala datagateway. Installera den här versionen eller senare av dessa orsaker:

  * Tidigare versioner av SAP NCo kan bli ett dödläge när mer än en IDoc meddelanden skickas samtidigt. 
  Det här villkoret blockerar alla senare meddelanden som skickas till SAP-målet orsakar meddelanden timeout.

  * Lokala datagateway körs bara på 64-bitarsdatorer. 
  Annars kan felmeddelandet du ”felaktig bild” eftersom data gateway-värdtjänsten inte har stöd för 32-bitars sammansättningar.

  * Använd .NET Framework 4.5 både data gateway-värdtjänsten och Microsoft SAP-kort. SAP NCo för .NET Framework 4.0 fungerar med processer som använder .NET runtime 4.0 4.7.1. 
  SAP NCo för .NET Framework 2.0 fungerar med processer som använder .NET runtime 2.0 3.5 och inte längre fungerar med den senaste lokala datagatewayen.

* Meddelandeinnehåll som du kan skicka till din SAP-server, t.ex en exempelfil IDoc. Det här innehållet måste vara i XML-format och innehålla namnområdet för SAP-åtgärd som du vill använda.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>Lägga till http-begäran utlösare

I Azure Logic Apps varje logikapp måste börja med en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en viss händelse inträffar eller när ett specifikt villkor uppfylls. Varje gång utlösaren-utlöses Logic Apps motorn skapar en logik app-instansen och börjar köras appens arbetsflöde.

I det här exemplet skapar du en logikapp med en slutpunkt i Azure så att du kan skicka *HTTP POST-begäranden* till din logikapp. När logikappen tar emot dessa HTTP-begäranden, utlöses utlösaren och kör nästa steg i arbetsflödet.

1. Skapa en tom logikapp, vilket öppnar designern logik App i Azure-portalen. 

2. I sökrutan anger du ”http-begäran” som filter. Välj den här utlösaren från listan över utlösare: **begäran - när en HTTP-begäran tas emot**

   ![Lägga till HTTP-begäran-utlösare](./media/logic-apps-using-sap-connector/add-trigger.png)

3. Spara nu logikappen så att du kan skapa en slutpunkts-URL för din logikapp.
Välj **Spara** i designerverktygsfältet. 

   Slutpunkten URL visas nu i utlösaren, till exempel:

   ![Generera en URL för slutpunkten](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>Lägg till SAP-åtgärd

I Azure Logikappar en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. Om du inte har lagt till en utlösare logikappen ännu och vill följa det här exemplet [lägga till utlösare som beskrivs i det här avsnittet](#add-trigger).

1. I logik App Designer under utlösare, Välj **nytt steg** > **lägga till en åtgärd**.

   ![Lägga till en åtgärd](./media/logic-apps-using-sap-connector/add-action.png) 

2. I sökrutan anger du ”sap-server” som filter. Välj åtgärden för SAP-server från listan över åtgärder: 

   * **Programservern SAP - skicka till SAP**
   * **SAP-Server för meddelande - Skicka till SAP**

   Det här exemplet använder den här åtgärden: **programserver SAP - skicka till SAP**

   ![Välj ”SAP-programserver” eller ”SAP meddelandet Server”](media/logic-apps-using-sap-connector/select-sap-action.png)

3. Om du uppmanas att ange anslutningsinformation skapa din SAP-anslutning nu. Annars, om anslutningen redan finns fortsätter du med nästa steg så att du kan ställa in din SAP-åtgärd. 

   **Skapa lokal SAP-anslutning**

   1. För **Gateways**väljer **Anslut via lokala datagateway** så att de lokala anslutningsegenskaperna visas.

   2. Ange anslutningsinformation för SAP-server. 
   För den **gateway** egenskap, Välj datagateway du skapade i Azure-portalen för gatewayinstallationen, till exempel:

      **SAP-programserver**

      ![Skapa serveranslutningen för SAP-program](./media/logic-apps-using-sap-connector/create-SAP-app-server-connection.png)  

      **SAP Message Server**

      ![Skapa SAP meddelandet serveranslutning](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. När du är klar väljer du **Skapa**.

      Logic Apps konfigurerar och testar anslutningen, se till att anslutningen inte fungerar korrekt.

4. Nu hittar och välj en åtgärd från din SAP-server. 

   1. I den **SAP åtgärd** väljer du mappikonen. 
   Hitta och välj den åtgärd som du vill använda från listan. 

      Det här exemplet väljer den **IDOC** kategori för åtgärden IDoc. 

      ![Sök efter och välj IDoc åtgärd](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Om du inte hittar den åtgärd du vill kan du manuellt ange en sökväg, till exempel:

      ![Ange sökvägen till IDoc åtgärden manuellt](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      Läs mer om IDoc åtgärder [meddelande scheman för IDOC åtgärder](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

   2. Klicka i den **indatameddelande** så att dynamiskt innehåll visas. 
   I listan under **när en HTTP-begäran tas emot**, Välj den **brödtext** fältet. 

      Det här steget inkluderar brödtextinnehåll från http-begäran utlösaren och skickar som utdata till SAP-server.

      ![Markera fältet ”brödtext”](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      När du är klar ser ut din SAP-åtgärd som det här exemplet:

      ![Slutföra åtgärden för SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

6. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>Lägga till http-svar åtgärd

Nu lägga till en åtgärd för svar i din logikapp arbetsflöde och innehåller utdata från SAP-åtgärd. På så sätt kan din logikapp returnerar resultatet från din SAP-server till den ursprungliga begäranden. 

1. I logik App Designer under SAP-åtgärd väljer **nytt steg** > **lägga till en åtgärd**.

2. I sökrutan anger du ”svaret” som filter. Markera den här åtgärden från listan över åtgärder: **begäran - svar**

3. Klicka i den **brödtext** så att dynamiskt innehåll visas. Den listrutan under **skicka till SAP**, Välj den **brödtext** fältet. 

   ![Slutföra åtgärden för SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

4. Spara din logikapp. 

## <a name="test-your-logic-app"></a>Testa logikappen

1. Om din logikapp inte redan har aktiverats på logiken app-menyn väljer du **översikt**. I verktygsfältet, välja **aktivera**. 

2. Välj verktygsfältet logik App Designer **kör**. Det här steget startar manuellt logikappen.

3. Utlösa din logikapp genom att skicka en HTTP POST-begäran till URL: en i HTTP-begäran utlösaren och ta meddelandet innehåll i din förfrågan. Att skicka begäran, kan du använda ett verktyg som [Postman](https://www.getpostman.com/apps). 

   För den här artikeln skickas begäran en IDoc-fil, som måste vara i XML-format och innehålla namnområdet för SAP-åtgärd som du använder, till exempel: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. När du skickar en HTTP-begäran kan vänta på svar från din logikapp.

> [!NOTE]
> Din logikapp kan timeout om alla steg som krävs för svaret inte slutförs inom den [tidsgränsen för begäran](./logic-apps-limits-and-config.md). Om det här tillståndet inträffar kan begäranden blockeras. För att hjälpa dig att diagnostisera problem, lär du dig hur du kan [kontrollera och övervaka dina logikappar](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Grattis, du har nu skapat en logikapp som kan kommunicera med din SAP-server. Nu när du har konfigurerat en SAP-anslutning för din logikapp kan du utforska andra tillgängliga SAP-åtgärder, till exempel BAPI och RFC.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om kopplingen enligt beskrivningen av den kopplingar Swagger-filer finns i dessa referensartiklar: 

* [SAP-programserver](/connectors/sapapplicationserver/)
* [SAP Message Server](/connectors/sapmessageserver/)

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Ansluta till lokalt system](../logic-apps/logic-apps-gateway-connection.md) från logikappar
* Lär dig hur du validera, transformera och andra meddelandeåtgärder med den [Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Lär dig mer om andra [Logic Apps kopplingar](../connectors/apis-list.md)
