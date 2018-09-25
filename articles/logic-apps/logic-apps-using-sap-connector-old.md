---
title: Ansluta till SAP-system – Azure Logic Apps | Microsoft Docs
description: Så här att komma åt och hantera SAP-resurser genom att automatisera arbetsflöden med Azure Logic Apps
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
ms.openlocfilehash: 086aa846e278ddf2d64eca97e781463f73b868d0
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47058023"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Ansluta till SAP-system från Azure Logic Apps

> [!NOTE]
> Den här SAP-anslutningsappen upphör att gälla snart. Vi har släppt nya och mer avancerade SAP-anslutningsappen och det rekommenderas att välja eller flytta till den [nya SAP-anslutningsappen](./logic-apps-using-sap-connector.md).
>  

Den här artikeln visar hur du kan komma åt dina SAP-resurser i en logikapp med SAP-programservern och SAP Message Server-anslutningar. På så sätt kan du automatisera uppgifter, processer och arbetsflöden som hanterar dina SAP-data och resurser genom att skapa logikappar.

Det här exemplet används en logikapp som du kan utlösa med en HTTP-begäran. Logikappen skickar ett mellanliggande dokument (IDoc) till en SAP-server och returnerar ett svar till den begärande som kallas logikappen.
De aktuella SAP-kopplingarna har åtgärder, men inte utlösare, så det här exemplet används den [HTTP-begäran-utlösaren](../connectors/connectors-native-reqres.md) som det första steget i att logic app-arbetsflöde. Se dessa referensartiklar SAP specifika teknisk information: 

* <a href="https://docs.microsoft.com/connectors/sapapplicationserver/" target="blank">SAP-programservern-anslutningsappen</a>
* <a href="https://docs.microsoft.com/connectors/sapmessageserver/" target="blank">SAP Message Server-anslutningsappen</a>

Om du inte har en Azure-prenumeration än, <a href="https://azure.microsoft.com/free/" target="_blank">registrera dig för ett kostnadsfritt konto</a>.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här artikeln behöver du följande objekt:

* Logikapp från där du vill komma åt dina SAP-system och en utlösare som startar logikappens arbetsflöde. SAP-anslutningsappar innehåller för närvarande endast åtgärder. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Snabbstart: skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Din <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">SAP-programservern</a> eller <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP Message Server</a>

* Ladda ned och installera senast [lokal datagateway](https://www.microsoft.com/download/details.aspx?id=53127) på en lokal dator. Kontrollera att du konfigurerar din gateway i Azure-portalen innan du fortsätter. Gatewayen hjälper dig att på ett säkert sätt få åtkomst till data och resurser som är lokalt. Mer information finns i [installera den lokala datagatewayen för Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Ladda ned och installera det senaste SAP-klientbiblioteket, som för närvarande är <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">SAP Connector (NCo) 3.0.20.0 för Microsoft .NET Framework 4.0 och Windows 64-bitars (x64)</a>, på samma dator som den lokala datagatewayen. Installera den här versionen eller senare av följande skäl:

  * Tidigare versioner av SAP NCo kan bli ett dödläge när mer än en IDoc-meddelanden skickas på samma gång. 
  Det här tillståndet blockerar alla senare meddelanden som skickas till SAP-mål, orsakar meddelanden uppnår en tidsgräns.

  * Den lokala datagatewayen körs bara på 64-bitars system. 
  I annat fall får du felet ”felaktig avbildning” eftersom tjänsten data gateway har inte stöd för 32-bitars sammansättningar.

  * Använda .NET Framework 4.5 både tjänsten data gateway och Microsoft SAP-Adapter. SAP-NCo för .NET Framework 4.0 fungerar med processer som använder .NET-runtime 4.0 4.7.1. 
  SAP-NCo för .NET Framework 2.0 fungerar med processer som använder .NET-runtime 2.0 3.5 och inte längre fungerar med den senaste lokala datagatewayen.

* Meddelandeinnehåll som du kan skicka till din SAP-server, t.ex ett exempel IDoc-fil. Det här innehållet måste vara i XML-format och innehålla namnområdet för SAP-åtgärd som du vill använda.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>Lägg till HTTP-begäran-utlösare

I Azure Logic Apps varje logikapp måste börja med en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en specifik händelse sker eller när ett specifikt villkor uppfylls. Varje gång utlösaren Logic Apps-motorn skapar en logikappinstans och börjar köras appens arbetsflöde.

I det här exemplet skapar du en logikapp med en slutpunkt i Azure så att du kan skicka *HTTP POST-begäranden* i logikappen. När logikappen tar emot dessa HTTP-begäranden, utlöses utlösaren och kör nästa steg i arbetsflödet.

1. Skapa en tom logikapp som öppnas Logic App Designer i Azure-portalen. 

2. Ange ”http-begäran” i sökrutan som filter. Välj den här utlösaren från listan över utlösare: **begäran – när en HTTP-begäran tas emot**

   ![Lägg till HTTP-begäran-utlösare](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. Spara din logikapp nu så att du kan generera en slutpunkts-URL för din logikapp.
Välj **Spara** i designerverktygsfältet. 

   Slutpunkten URL visas nu i utlösaren, till exempel:

   ![Skapa en Webbadress för slutpunkt](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>Lägg till SAP-åtgärd

I Azure Logic Apps, en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. Om du har inte lagt till en utlösare i logikappen ännu och vill följa det här exemplet [Lägg till utlösare som beskrivs i det här avsnittet](#add-trigger).

1. I Logikappdesignern under utlösaren väljer **nytt steg** > **Lägg till en åtgärd**.

   ![Lägga till en åtgärd](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. I sökrutan anger du ”sap-server” som filter. Välj åtgärden för din SAP-server från åtgärdslistan över: 

   * **SAP-programservern - skicka till SAP**
   * **SAP-Server för meddelande – Skicka till SAP**

   Det här exemplet använder den här åtgärden: **SAP-programservern - skicka till SAP**

   ![Välj ”SAP-programservern” eller ”SAP Message Server”](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. Skapa nu din SAP-anslutning om du uppmanas att ange anslutningsinformation. I annat fall om anslutningen redan finns, Fortsätt med nästa steg så att du kan ställa in din SAP-åtgärd. 

   **Skapa en lokal SAP-anslutning**

   1. För **gatewayer**väljer **Anslut via lokal datagateway** så att den lokala anslutningsegenskaper visas.

   2. Ange anslutningsinformation för SAP-server. 
   För den **gateway** egenskapen, Välj den datagateway som du skapade i Azure-portalen för din gatewayinstallationen, till exempel:

      **SAP-programservern**

      ![Skapa serveranslutning för SAP-program](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **SAP Message Server**

      ![Skapa SAP meddelande serveranslutning](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. När du är klar väljer du **Skapa**.

      Logic Apps konfigurerar och testar anslutningen, se till att anslutningen fungerar korrekt.

4. Nu hitta och välja en åtgärd från din SAP-server. 

   1. I den **SAP åtgärd** väljer du mappikonen. 
   Hitta och välj den åtgärd som du vill använda från listan. 

      Det här exemplet väljer den **IDOC** kategori för IDoc-åtgärden. 

      ![Hitta och välj IDoc-åtgärden](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      Om du inte hittar den åtgärd som du vill kan ange du manuellt en sökväg, till exempel:

      ![Manuellt ange sökvägen till IDoc-åtgärd](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      Läs mer om IDoc-åtgärder, [meddelande scheman för IDOC-åtgärder](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

   2. Klicka i den **indatameddelande** så att den dynamiska innehållslistan visas. 
   I listan under **när en HTTP-begäran tas emot**väljer den **brödtext** fält. 

      Det här steget omfattar brödtext i från din HTTP-begäran-utlösare och skickar som skickas till din SAP-server.

      ![Välj fältet ”Body”](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      När du är klar ut din SAP-åtgärd som det här exemplet:

      ![Fullständig SAP-åtgärd](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>Lägg till åtgärd för HTTP-svar

Lägg till en svarsåtgärd logikappens arbetsflöde och innehåller utdata från SAP-åtgärd. På så sätt kan logikappen returnerar resultatet från SAP-server till förfrågningskällan. 

1. I Logic App Designer, SAP-åtgärd väljer **nytt steg** > **Lägg till en åtgärd**.

2. I sökrutan anger du ”svar” som filter. Välj den här åtgärden från åtgärdslistan över: **begäran – svar**

3. Klicka i den **brödtext** så att den dynamiska innehållslistan visas. Från listan under **skicka till SAP**väljer den **brödtext** fält. 

   ![Fullständig SAP-åtgärd](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. Spara din logikapp. 

## <a name="test-your-logic-app"></a>Testa din logikapp

1. Om logikappen inte är redan aktiverat på logikappmenyn, Välj **översikt**. I verktygsfältet, välja **aktivera**. 

2. Välj Logic App Designer-verktygsfältet **kör**. Det här steget startar logikappen manuellt.

3. Utlösa logikappen genom att skicka en HTTP POST-begäran till URL: en i din HTTP-begäran-utlösare och innehåller meddelandet innehåll med din begäran. Skicka begäran, kan du använda ett verktyg som [Postman](https://www.getpostman.com/apps). 

   I den här artikeln skickar begäran en IDoc-fil, som måste vara i XML-format och innehålla namnområdet för SAP-åtgärd som du använder, till exempel: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. När du skickar HTTP-begäran, vänta på svar från din logikapp.

> [!NOTE]
> Din logikapp kan timeout om alla steg som krävs för svaret inte slutförs inom den [tidsgränsen för begäran](./logic-apps-limits-and-config.md). Om det här tillståndet inträffar kan blockeras begäranden. För att hjälpa dig att diagnostisera problem, lär du dig hur du kan [kontrollera och övervaka dina logikappar](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Grattis, nu har du skapat en logikapp som kan kommunicera med dina SAP-server. Nu när du har konfigurerat en anslutning för SAP för din logikapp, kan du utforska andra tillgängliga SAP-åtgärder, till exempel BAPI och RFC.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om anslutningen som beskrivs av de kopplingar Swagger-filerna finns i dessa referensartiklar: 

* [SAP-programservern](/connectors/sapapplicationserver/)
* [SAP Message Server](/connectors/sapmessageserver/)

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Ansluta till lokala system](../logic-apps/logic-apps-gateway-connection.md) från logikappar
* Lär dig att verifiera, omvandla och andra meddelandeåtgärder med den [Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
