---
title: Ansluta till SAP-system – Azure Logic Apps | Microsoft Docs
description: Så här att komma åt och hantera SAP-resurser genom att automatisera arbetsflöden med Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 09/14/2018
tags: connectors
ms.openlocfilehash: 1738f02d28a4eb9ff5cbb51c73bc50ddf3c9a68b
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231346"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Ansluta till SAP-system från Azure Logic Apps

Den här artikeln visar hur du kan komma åt dina lokala SAP-resurser från i en logikapp genom att använda anslutningstjänsten SAP ERP Central komponent (ECC). SAP ECC-anslutningsappen stöder meddelande eller data integration till och från SAP Netweaver-baserade system via mellanliggande dokumentet (IDoc) eller Business Application Programming Interface (BAPI) eller fjärransluten funktionen anropa (RFC).

SAP ECC-anslutningsappen använder den <a href="https://support.sap.com/en/product/connectors/msnet.html">SAP .net Connector (NCo)-biblioteket</a> och ger dessa åtgärder eller åtgärder:

- **Skicka till SAP**: skicka IDoc eller genom att anropa BAPI funktioner över tRFC i SAP-system.
- **Ta emot från SAP**: ta emot IDoc eller BAPI fungerar anrop över tRFC från SAP-system.
- **Generera scheman**: Generera scheman för SAP-artefakter för IDoc, BAPI eller RFC.

SAP-anslutningsappen kan integreras med en lokal SAP-system via den [lokal datagateway](https://www.microsoft.com/download/details.aspx?id=53127). I Skicka scenarier, till exempel när du skickar ett meddelande från Logikappar till ett SAP-system datagateway fungerar som en RFC-klient och vidarebefordrar begäranden som tas emot från Logikappar till SAP.
I Receive scenarier fungerar på samma sätt kan datagateway som en RFC-server som tar emot förfrågningar från SAP och vidarebefordrar till Logikappen. 

Den här artikeln visar hur du skapar exemplet logikappar som integreras med SAP samtidigt som täcker de tidigare beskrivna integrationsscenarier.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här artikeln behöver du följande objekt:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration än, <a href="https://azure.microsoft.com/free/" target="_blank">registrera dig för ett kostnadsfritt konto</a>.

* Logikapp från där du vill komma åt dina SAP-system och en utlösare som startar logikappens arbetsflöde. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Snabbstart: skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Din <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">SAP-programservern</a> eller <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP Message Server</a>

* Ladda ned och installera senast [lokal datagateway](https://www.microsoft.com/download/details.aspx?id=53127) på en lokal dator. Kontrollera att du konfigurerar din gateway i Azure-portalen innan du fortsätter. Gatewayen hjälper dig att på ett säkert sätt få åtkomst till data och resurser som är lokalt. Mer information finns i [installera den lokala datagatewayen för Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Ladda ned och installera det senaste SAP-klientbiblioteket, som för närvarande är <a href="https://softwaredownloads.sap.com/file/0020000001865512018" target="_blank">SAP Connector (NCo) 3.0.21.0 för Microsoft .NET Framework 4.0 och Windows 64-bitars (x64)</a>, på samma dator som den lokala datagatewayen. Installera den här versionen eller senare av följande skäl:

  * Tidigare versioner av SAP NCo kan bli ett dödläge när mer än en IDoc-meddelanden skickas samtidigt. 
  Det här tillståndet blockerar alla senare meddelanden som skickas till SAP-mål, orsakar meddelanden uppnår en tidsgräns.

  * Den lokala datagatewayen körs bara på 64-bitars system. 
  I annat fall får du felet ”felaktig avbildning” eftersom tjänsten data gateway har inte stöd för 32-bitars sammansättningar.

  * Använda .NET Framework 4.5 både tjänsten data gateway och Microsoft SAP-Adapter. SAP-NCo för .NET Framework 4.0 fungerar med processer som använder .NET-runtime 4.0 4.7.1. 
  SAP-NCo för .NET Framework 2.0 fungerar med processer som använder .NET-runtime 2.0 3.5 och inte längre fungerar med den senaste lokala datagatewayen.

* Meddelandeinnehåll som du kan skicka till din SAP-server, t.ex ett exempel IDoc-fil. Det här innehållet måste vara i XML-format och innehålla namnområdet för SAP-åtgärd som du vill använda.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>Skicka till SAP

Det här exemplet används en logikapp som du kan utlösa med en HTTP-begäran. Logikappen skickar ett mellanliggande dokument (IDoc) till en SAP-server och returnerar ett svar till den begärande som kallas logikappen. 

### <a name="add-http-request-trigger"></a>Lägg till HTTP-begäran-utlösare

I Azure Logic Apps varje logikapp måste börja med en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en specifik händelse sker eller när ett specifikt villkor uppfylls. Varje gång utlösaren Logic Apps-motorn skapar en logikappinstans och börjar köras appens arbetsflöde.

I det här exemplet skapar du en logikapp med en slutpunkt i Azure så att du kan skicka *HTTP POST-begäranden* i logikappen. När logikappen tar emot dessa HTTP-begäranden, utlöses utlösaren och kör nästa steg i arbetsflödet.

1. I den [Azure-portalen](https://portal.azure.com), skapa en tom logikapp som öppnas Logic App Designer. 

2. Ange ”http-begäran” i sökrutan som filter. Välj den här utlösaren från listan över utlösare: **begäran – när en HTTP-begäran tas emot**

   ![Lägg till HTTP-begäran-utlösare](./media/logic-apps-using-sap-connector/add-trigger.png)

3. Spara din logikapp nu så att du kan generera en slutpunkts-URL för din logikapp.
Välj **Spara** i designerverktygsfältet. 

   Slutpunkten URL visas nu i utlösaren, till exempel:

   ![Skapa en Webbadress för slutpunkt](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-sap-action"></a>Lägg till SAP-åtgärd

I Azure Logic Apps, en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. Om du har inte lagt till en utlösare i logikappen ännu och vill följa det här exemplet [Lägg till utlösare som beskrivs i det här avsnittet](#add-trigger).

1. I Logikappdesignern under utlösaren väljer **nytt steg** > **Lägg till en åtgärd**.

   ![Lägga till en åtgärd](./media/logic-apps-using-sap-connector/add-action.png) 

2. I sökrutan anger du ”sap” som filter. Välj den här åtgärden från åtgärdslistan över: **skickar ett meddelande till SAP**
  
   ![Välj åtgärden Skicka för SAP](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Alternativt kan i stället för att söka, Välj den **Enterprise** och sedan SAP-åtgärd.

   ![Välj åtgärden för SAP-skicka från Enterprise-fliken](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

3. Skapa nu din SAP-anslutning om du uppmanas att ange anslutningsinformation. I annat fall om anslutningen redan finns, Fortsätt med nästa steg så att du kan ställa in din SAP-åtgärd. 

   **Skapa en lokal SAP-anslutning**

   1. Ange anslutningsinformation för SAP-server. 
   För den **Datagateway** egenskapen, Välj den datagateway som du skapade i Azure-portalen för din gateway-installation.

      Om den **inloggningstyp** är inställd på **programserver**, dessa egenskaper, som normalt visas valfritt, krävs:

      ![Skapa serveranslutning för SAP-program](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      Om den **inloggningstyp** är inställd på **grupp**, dessa egenskaper, som normalt visas valfritt, krävs: 

      ![Skapa SAP meddelande serveranslutning](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. När du är klar väljer du **Skapa**. 
   
      Logic Apps konfigurerar och testar anslutningen, se till att anslutningen fungerar korrekt.

4. Nu hitta och välja en åtgärd från din SAP-server. 

   1. I den **SAP åtgärd** väljer du mappikonen. 
   Hitta och välja SAP-meddelandet som du vill använda från listan över filer. 
   Använd pilarna för att navigera i listan.

      Det här exemplet väljer en IDoc med **ordning** typen. 

      ![Hitta och välj IDoc-åtgärden](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Om du inte hittar den åtgärd som du vill kan ange du manuellt en sökväg, till exempel:

      ![Manuellt ange sökvägen till IDoc-åtgärd](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Ange värdet för SAP-åtgärd via uttrycksredigeraren. På så sätt kan du använda samma åtgärd för olika meddelandetyper.

      Läs mer om IDoc-åtgärder, [meddelande scheman för IDOC åtgärder](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   2. Klicka i den **indatameddelande** så att den dynamiska innehållslistan visas. 
   Från listan under **när en HTTP-begäran tas emot**väljer den **brödtext** fält. 

      Det här steget omfattar brödtext i från din HTTP-begäran-utlösare och skickar som skickas till din SAP-server.

      ![Välj fältet ”Body”](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      När du är klar ut din SAP-åtgärd som det här exemplet:

      ![Fullständig SAP-åtgärd](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

5. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

<a name="add-response"></a>

### <a name="add-http-response-action"></a>Lägg till åtgärd för HTTP-svar

Lägg till en svarsåtgärd logikappens arbetsflöde och innehåller utdata från SAP-åtgärd. På så sätt kan logikappen returnerar resultatet från SAP-server till förfrågningskällan. 

1. I Logic App Designer, SAP-åtgärd väljer **nytt steg** > **Lägg till en åtgärd**.

2. I sökrutan anger du ”svar” som filter. Välj den här åtgärden från åtgärdslistan över: **begäran – svar**

3. Klicka i den **brödtext** så att den dynamiska innehållslistan visas. Från listan under **skicka till SAP**väljer den **brödtext** fält. 

   ![Fullständig SAP-åtgärd](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

4. Spara din logikapp. 

### <a name="test-your-logic-app"></a>Testa din logikapp

1. Om logikappen inte är redan aktiverat på logikappmenyn, Välj **översikt**. I verktygsfältet, välja **aktivera**. 

2. Välj Logic App Designer-verktygsfältet **kör**. Det här steget startar logikappen manuellt.

3. Utlösa logikappen genom att skicka en HTTP POST-begäran till URL: en i din HTTP-begäran-utlösare och innehåller meddelandet innehåll med din begäran. Skicka begäran, kan du använda ett verktyg som [Postman](https://www.getpostman.com/apps). 

   I den här artikeln skickar begäran en IDoc-fil, som måste vara i XML-format och innehålla namnområdet för SAP-åtgärd som du använder, till exempel: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. När du skickar HTTP-begäran, vänta på svar från din logikapp.

   > [!NOTE]
   > Din logikapp kan timeout om alla steg som krävs för svaret inte slutförs inom den [tidsgränsen för begäran](./logic-apps-limits-and-config.md). Om det här tillståndet inträffar kan blockeras begäranden. För att hjälpa dig att diagnostisera problem, lär du dig hur du kan [kontrollera och övervaka dina logikappar](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Grattis, nu har du skapat en logikapp som kan kommunicera med dina SAP-server. Nu när du har konfigurerat en anslutning för SAP för din logikapp, kan du utforska andra tillgängliga SAP-åtgärder, till exempel BAPI och RFC.

## <a name="receive-from-sap"></a>Ta emot från SAP

Det här exemplet används en logikapp som utlöser när du tar emot ett meddelande från ett SAP-system. 

### <a name="add-sap-trigger"></a>Lägg till SAP-utlösare

1. Skapa en tom logikapp som öppnas Logic App Designer i Azure-portalen. 

2. I sökrutan anger du ”sap” som filter. Välj den här utlösaren från listan över utlösare: **när ett meddelande tas emot från SAP**

   ![Lägg till SAP-utlösare](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   Alternativt kan du gå till fliken Enterprise och välja utlösaren

   ![Lägg till SAP-utlösare från ent-fliken](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

3. Skapa nu din SAP-anslutning om du uppmanas att ange anslutningsinformation. I annat fall om anslutningen redan finns, Fortsätt med nästa steg så att du kan ställa in din SAP-åtgärd. 

   **Skapa en lokal SAP-anslutning**

   1. Ange anslutningsinformation för SAP-server. 
   För den **Datagateway** egenskapen, Välj den datagateway som du skapade i Azure-portalen för din gateway-installation.

      Om den **inloggningstyp** är inställd på **programserver**, dessa egenskaper, som normalt visas valfritt, krävs:

      ![Skapa serveranslutning för SAP-program](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      Om den **inloggningstyp** är inställd på **grupp**, dessa egenskaper, som normalt visas valfritt, krävs:

      ![Skapa SAP meddelande serveranslutning](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

4. Ange de obligatoriska parametrarna utifrån din konfiguration för SAP-system. 

   Du kan också ange en eller flera SAP-åtgärder. 
   Den här listan över åtgärder anger de meddelanden som utlösaren tar emot från din SAP-server via en datagateway. 
   En tom lista anger att utlösaren får alla meddelanden. 
   Om listan innehåller mer än ett meddelande, får utlösaren endast meddelanden som anges i listan. Andra meddelanden som skickas från din SAP-server avvisas av gatewayen.

   Du kan välja en SAP-åtgärd filväljaren:

   ![Välj åtgärd för SAP](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Eller manuellt ange en åtgärd:

   ![Ange manuellt SAP-åtgärd](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   Här är ett exempel som visar hur åtgärden visas när du konfigurerar utlösaren tar emot flera meddelanden.

   ![Exempel för utlösare](media/logic-apps-using-sap-connector/example-trigger.png)  

   Läs mer om åtgärder som SAP, [meddelande scheman för IDOC-åtgärder](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

5. Spara din logikapp nu så att du kan börja ta emot meddelanden från SAP-system.
Välj **Spara** i designerverktygsfältet. 

Logikappen är nu redo att ta emot meddelanden från SAP-system. 

> [!NOTE]
> SAP-utlösaren är inte en avsökning utlösare, men en webhook-baserade utlösare i stället. Utlösaren kallas från gatewayen endast när det finns ett meddelande, så inga avsökningen behövs. 

### <a name="test-your-logic-app"></a>Testa din logikapp

1. Skicka ett meddelande från SAP-system för att utlösa logikappen.

2. På logikappmenyn, väljer **översikt**, och granska de **Körningshistorik** för alla nya körningar för din logikapp. 

3. Öppna den senaste körningen, som visar meddelandet som skickas från din SAP-system i utlösaren outputs-avsnittet.

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generera scheman för artefakter i SAP

Det här exemplet används en logikapp som du kan utlösa med en HTTP-begäran. SAP-åtgärden skickar en begäran till en SAP-system för att skapa scheman för angivna mellanliggande dokumentet (IDoc) och BAPI. Scheman som returneras i svaret överförs till ett Integrationkonto med hjälp av Azure Resource Manager-anslutningen.

### <a name="add-http-request-trigger"></a>Lägg till HTTP-begäran-utlösare

1. Skapa en tom logikapp som öppnas Logic App Designer i Azure-portalen. 

2. Ange ”http-begäran” i sökrutan som filter. Välj den här utlösaren från listan över utlösare: **begäran – när en HTTP-begäran tas emot**

   ![Lägg till HTTP-begäran-utlösare](./media/logic-apps-using-sap-connector/add-trigger.png)

3. Spara din logikapp nu så att du kan generera en slutpunkts-URL för din logikapp.
Välj **Spara** i designerverktygsfältet. 

   Slutpunkten URL visas nu i utlösaren, till exempel:

   ![Skapa en Webbadress för slutpunkt](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-sap-action-to-generate-schemas"></a>Lägg till SAP-åtgärd för att generera scheman

1. I Logikappdesignern under utlösaren väljer **nytt steg** > **Lägg till en åtgärd**.

   ![Lägga till en åtgärd](./media/logic-apps-using-sap-connector/add-action.png) 

2. I sökrutan anger du ”sap” som filter. Välj den här åtgärden från åtgärdslistan över: **generera scheman**
  
   ![Välj åtgärden Skicka för SAP](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Alternativt kan du kan också välja den **Enterprise** och sedan SAP-åtgärd.

   ![Välj åtgärden för SAP-skicka från Enterprise-fliken](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

3. Skapa nu din SAP-anslutning om du uppmanas att ange anslutningsinformation. I annat fall om anslutningen redan finns, Fortsätt med nästa steg så att du kan ställa in din SAP-åtgärd. 

   **Skapa en lokal SAP-anslutning**

   1. Ange anslutningsinformation för SAP-server. 
   För den **Datagateway** egenskapen, Välj den datagateway som du skapade i Azure-portalen för din gateway-installation.

      Om den **inloggningstyp** är inställd på **programserver**, dessa egenskaper, som normalt visas valfritt, krävs:

      ![Skapa serveranslutning för SAP-program](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      Om den **inloggningstyp** är inställd på **grupp**, dessa egenskaper, som normalt visas valfritt, krävs:
   
      ![Skapa SAP meddelande serveranslutning](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. När du är klar väljer du **Skapa**. Logic Apps konfigurerar och testar anslutningen, se till att anslutningen fungerar korrekt.

4. Ange sökvägen till den artefakt som du vill skapa schemat.

   Du kan välja åtgärden SAP filväljaren:

   ![Välj åtgärd för SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Alternativt kan du kan ange åtgärden manuellt:

   ![Ange manuellt SAP-åtgärd](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png) 

   Om du vill generera scheman för flera artefakten, ger du åtgärdsinformation SAP för varje artefakt till exempel:

   ![Välj Lägg till nytt objekt](media/logic-apps-using-sap-connector/schema-generator-array-pick.png) 

   ![Visa två objekt](media/logic-apps-using-sap-connector/schema-generator-example.png) 

   Läs mer om de åtgärder som SAP, [meddelande scheman för IDOC åtgärder](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

5. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

### <a name="test-your-logic-app"></a>Testa din logikapp

1. I verktygsfältet för appdesignern väljer **kör** att utlösa en körning för din logikapp.

2. Öppna körningen och kontrollera utdata för den **generera schemat** åtgärd. 

   Utdata visar de genererade scheman för den angivna listan med meddelanden.

### <a name="upload-schemas-to-integration-account"></a>Ladda upp scheman till integrationskontot

Du kan också ladda ned eller lagra de genererade scheman i lagringsplatser, såsom en blob, lagring eller integrationskontot. Integrationskonton tillhandahålla en förstklassig upplevelse med andra XML-åtgärder, så det här exemplet visar hur du överför scheman till ett integrationkonto för samma logikapp med hjälp av Azure Resource Manager-anslutningen.

1. I Logic App Designer under utlösaren väljer **nytt steg** > **Lägg till en åtgärd**. Ange ”resource manager” i sökrutan som filter. Välj den här åtgärden: **skapa eller uppdatera en resurs**

   ![Välj Azure Resource Manager-åtgärd](media/logic-apps-using-sap-connector/select-arm-action.png) 

2. Ange information, inklusive din Azure-prenumeration, Azure-resursgrupp och integrationskontot. För andra fält, följer du exemplet nedan.

   ![Ange information om Azure Resource Manager-åtgärd](media/logic-apps-using-sap-connector/arm-action.png)

   SAP **generera scheman** genererar scheman som en samling, så designern lägger automatiskt till en **för varje** loop för åtgärden. 
   Här är ett exempel som visar hur den här åtgärden visas:

   ![Azure Resource Manager-åtgärd med ”för var och en” loop](media/logic-apps-using-sap-connector/arm-action-foreach.png)  

   > [!NOTE]
   > Scheman använda base64-kodat format. Om du vill ladda upp scheman till ett integrationkonto de att avkoda med hjälp av den `base64ToString()` funktion. Här är ett exempel som visar koden för den `"properties"` element:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

3. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

### <a name="test-your-logic-app"></a>Testa din logikapp

1. I verktygsfältet för appdesignern väljer **kör** för att utlösa logikappen manuellt.

2. Kör efter ett lyckat, gå till integrationskontot och kontrollera att de genererade scheman som genereras finns.

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

Här följer kända problem och begränsningar för SAP-anslutningen:

* SAP utlösaren does't stöder tar emot batch idoc: er från SAP. Den här åtgärden kan resultera i RFC anslutningsfel mellan din SAP-system och datagateway.

* SAP-utlösaren stöder inte data gateway-kluster. Ibland redundans data gateway-noden som kommunicerar med SAP-system kan skilja sig från den aktiva noden, vilket resulterar i oväntade resultat. Scenarier med skicka stöds data gateway-kluster.

* I Receive scenarier stöds en icke-null-svar returneras inte. En logikapp med en utlösare och en svarsåtgärd leder till oväntade resultat. 

* Endast en enda skicka till SAP-anrop eller meddelandet fungerar med tRFC. Business Application Programming Interface (BAPI) commit mönster, till exempel flera tRFC anrop i samma session, stöds inte.

* RFC: er med bifogade filer stöds inte för båda skicka SAP och generera scheman åtgärder.

* SAP-anslutningsappen stöder för närvarande inte SAP router strängar. Den lokala datagatewayen måste finnas i samma nätverk som SAP-system som du vill ansluta till.

* Konverteringen för saknade (null), tom, minsta och högsta värden för fälten DATS och TIMS SAP kan komma att ändras i senare uppdateringar för den lokala datagatewayen.

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Ansluta till lokala system](../logic-apps/logic-apps-gateway-connection.md) från logikappar
* Lär dig att verifiera, omvandla och andra meddelandeåtgärder med den [Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
