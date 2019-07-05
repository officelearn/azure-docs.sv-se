---
title: Ansluta till SAP-system – Azure Logic Apps
description: Komma åt och hantera resurser som SAP genom att automatisera arbetsflöden med Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 05/09/2019
tags: connectors
ms.openlocfilehash: 8232bf90b4dc160583959345a257846aaabad690
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67458933"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Ansluta till SAP-system från Azure Logic Apps

Den här artikeln visar hur du kan komma åt dina lokala SAP-resurser från i en logikapp med hjälp av SAP-anslutningen. Anslutningen fungerar med SAP: s klassiska versioner, till exempel R/3 och ECC-system på plats. Anslutningen kan också integrering med SAP: s nyare SAP HANA-baserade system, till exempel s/4 HANA, oavsett om de är lokala eller i molnet. SAP-anslutningsappen stöder integration med meddelande- eller data till och från SAP NetWeaver-baserade system via mellanliggande dokumentet (IDoc), Business Application Programming Interface (BAPI) eller fjärransluten funktionen anropa (RFC).

SAP-anslutningsappen använder den [SAP .NET Connector (NCo)-biblioteket](https://support.sap.com/en/product/connectors/msnet.html) och ger dessa åtgärder eller åtgärder:

* **Skicka till SAP**: Skickar IDoc via tRFC, anropar BAPI funktioner över RFC eller anropa RFC/tRFC i SAP-system.
* **Ta emot från SAP**: Ta emot IDoc över tRFC, anropar BAPI funktioner via tRFC eller anropa RFC/tRFC i SAP-system.
* **Generera scheman**: Generera scheman för SAP-artefakter för IDoc, BAPI eller RFC.

SAP-anslutningsappen stöder grundläggande autentisering med användarnamn och lösenord för dessa åtgärder. Det stöder också anslutningen [Secure Network Communications (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). SNC kan användas för SAP NetWeaver enkel inloggning (SSO) eller för ytterligare säkerhetsfunktioner som tillhandahålls av en extern security-produkt.

SAP-anslutningsappen kan integreras med en lokal SAP-system via den [lokal datagateway](../logic-apps/logic-apps-gateway-connection.md). I Skicka scenarier, till exempel när ett meddelande skickas från en logikapp till ett SAP-system, den datagatewayen fungerar som en RFC-klient och vidarebefordrar att förfrågningarna togs emot från logikappen till SAP.
På samma sätt i ett scenarier, datagateway fungerar som en RFC-server som tar emot förfrågningar från SAP och vidarebefordrar dem till logikappen.

Den här artikeln visar hur du skapar exemplet logikappar som integreras med SAP samtidigt som täcker de tidigare beskrivna integrationsscenarier.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här artikeln behöver du följande objekt:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration än, [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).
* Logikapp från där du vill komma åt dina SAP-system och en utlösare som startar logikappens arbetsflöde. Om du inte har använt logikappar [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md) och [snabbstarten: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).
* Din [SAP-programservern](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) eller [SAP-meddelandeservern](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).
* Ladda ned och installera senast [lokal datagateway](https://www.microsoft.com/download/details.aspx?id=53127) på en lokal dator. Kontrollera att du konfigurerar din gateway i Azure-portalen innan du fortsätter. Gatewayen kan du på ett säkert sätt komma åt lokala data och resurser. Mer information finns i [installera en lokal datagateway för Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).
* Om du använder SNC med enkel inloggning kan du kontrollera att gatewayen körs som en användare som har mappats mot SAP-användare. För att ändra standardkontot väljer **ändra konto**, och ange autentiseringsuppgifter.

  ![Ändra gateway-konto](./media/logic-apps-using-sap-connector/gateway-account.png)

* Om du aktiverar SNC med en extern security-produkt, kopiera SNC-bibliotek eller filer på samma dator där gatewayen är installerad. Några exempel på SNC-produkter är [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos- och NTLM.
* Ladda ned och installera det senaste SAP-klientbiblioteket, som för närvarande är [SAP Connector (NCo) 3.0.21.0 för Microsoft .NET Framework 4.0 och Windows 64-bitars (x64)](https://softwaredownloads.sap.com/file/0020000001865512018), på samma dator som den lokala datagatewayen. Installera den här versionen eller senare av följande skäl:

  * Tidigare versioner av SAP NCo kan bli ett dödläge när mer än en IDoc-meddelande skickas samtidigt. Det här tillståndet blockerar alla senare meddelanden som skickas till SAP-mål, vilket gör att meddelanden uppnår en tidsgräns.
  * Den lokala datagatewayen körs bara på 64-bitars system. I annat fall får du felet ”felaktig avbildning” eftersom tjänsten data gateway har inte stöd för 32-bitars sammansättningar.
  * Använda .NET Framework 4.5 både tjänsten data gateway och Microsoft SAP-Adapter. SAP-NCo för .NET Framework 4.0 fungerar med processer som använder .NET-runtime 4.0 4.7.1. SAP-NCo för .NET Framework 2.0 fungerar med processer som använder .NET-runtime 2.0 till 3.5, men inte längre fungerar med den senaste lokala datagatewayen.

* Meddelandeinnehåll som du kan skicka till din SAP-server, till exempel en exempelfil IDoc, måste vara i XML-format och innehålla namnområdet för SAP-åtgärd som du vill använda.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>Skicka till SAP

Det här exemplet används en logikapp som du kan utlösa med en HTTP-begäran. Logikappen skickar en IDoc till en SAP-server och returnerar ett svar till den begärande som kallas logikappen. 

### <a name="add-an-http-request-trigger"></a>Lägg till en HTTP-begäran-utlösare

I Azure Logic Apps varje logikapp måste börja med en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en specifik händelse sker eller när ett specifikt villkor uppfylls. Varje gång utlösaren Logic Apps-motorn skapar en logikappinstans och börjar köras appens arbetsflöde.

I det här exemplet skapar du en logikapp med en slutpunkt i Azure så att du kan skicka *HTTP POST-begäranden* i logikappen. När logikappen tar emot dessa HTTP-begäranden, utlöses utlösaren och kör nästa steg i arbetsflödet.

1. I den [Azure-portalen](https://portal.azure.com), skapa en tom logikapp som öppnas Logic App Designer.

1. Ange ”http-begäran” i sökrutan som filter. Från den **utlösare** väljer **när en HTTP-begäran tas emot**.

   ![Lägg till HTTP-begäran-utlösare](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Spara din logikapp nu så att du kan generera en slutpunkts-URL för din logikapp. På verktygsfältet för appdesignern väljer **spara**.

   Slutpunkten URL visas nu i utlösaren, till exempel:

   ![Skapa en Webbadress för slutpunkt](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Lägg till en SAP-åtgärd

I Azure Logic Apps, en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. Om du har inte lagt till en utlösare i logikappen ännu och vill följa det här exemplet [Lägg till utlösare som beskrivs i det här avsnittet](#add-trigger).

1. I Logikappdesignern under utlösaren väljer **nytt steg**.

   ![Välj ”nytt steg”](./media/logic-apps-using-sap-connector/add-action.png)

1. I sökrutan anger du ”sap” som filter. Från den **åtgärder** väljer **skickar ett meddelande till SAP**.
  
   ![Välj åtgärden Skicka för SAP](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Eller i stället för att söka, Välj den **Enterprise** och sedan SAP-åtgärd.

   ![Välj åtgärden för SAP-skicka från Enterprise-fliken](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Skapa nu din SAP-anslutning om du uppmanas att ange anslutningsinformation. I annat fall om anslutningen redan finns, Fortsätt med nästa steg så att du kan ställa in din SAP-åtgärd.

   **Skapa en lokal SAP-anslutning**

    1. Ange anslutningsinformation för SAP-server. För den **Datagateway** egenskapen, Välj den datagateway som du skapade i Azure-portalen för din gateway-installation.

         - Om den **inloggningstyp** är inställd på **programserver**, dessa egenskaper, som normalt visas valfritt, krävs:

            ![Skapa serveranslutning för SAP-program](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

         - Om den **inloggningstyp** är inställd på **grupp**, dessa egenskaper, som normalt visas valfritt, krävs:

            ![Skapa SAP meddelande serveranslutning](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

           Som standard används starkt att skriva för att kontrollera ogiltiga värden genom att utföra XML-verifiering mot schemat. Det här beteendet kan hjälpa dig att identifiera problem tidigare. Den **säkert att skriva** alternativet är tillgängligt för bakåtkompatibilitet och kontrollerar endast stränglängden. Läs mer om den [säkert att skriva alternativet](#safe-typing).

    1. När du är klar väljer du **skapa**.

       Logic Apps konfigurerar och testar anslutningen till att anslutningen fungerar korrekt.

1. Nu hitta och välja en åtgärd från din SAP-server.

    1. I den **SAP åtgärd** väljer du mappikonen. Hitta och välja SAP-meddelandet som du vill använda från listan över filer. Använd pilarna för att navigera i listan.

       Det här exemplet väljer en IDoc med den **order** typen.

       ![Hitta och välj IDoc-åtgärden](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

       Om du inte hittar den åtgärd som du vill kan ange du manuellt en sökväg, till exempel:

       ![Manuellt ange sökvägen till IDoc-åtgärd](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

       > [!TIP]
       > Ange värdet för **SAP åtgärd** via uttrycksredigeraren. På så sätt kan du använda samma åtgärd för olika meddelandetyper.

       Läs mer om IDoc-åtgärder, [meddelande scheman för IDOC åtgärder](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

    1. Klicka i den **indatameddelande** så att den dynamiska innehållslistan visas. Från listan under **när en HTTP-begäran tas emot**väljer den **brödtext** fält.

       Det här steget omfattar brödtext från HTTP-begäran-utlösare och skickar som skickas till din SAP-server.

       ![Välj fältet ”Body”](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

       När du är klar ut din SAP-åtgärd som det här exemplet:

       ![Fullständig SAP-åtgärd](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Spara din logikapp. På verktygsfältet för appdesignern väljer **spara**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Lägg till en åtgärd för HTTP-svar

Lägg till en svarsåtgärd logikappens arbetsflöde och innehåller utdata från SAP-åtgärd. På så sätt kan logikappen returnerar resultatet från SAP-server till förfrågningskällan.

1. I Logic App Designer, SAP-åtgärd väljer **nytt steg**.

1. I sökrutan anger du ”svar” som filter. Från den **åtgärder** väljer **svar**.

1. Klicka i den **brödtext** så att den dynamiska innehållslistan visas. Från listan under **skickar ett meddelande till SAP**väljer den **brödtext** fält.

   ![Fullständig SAP-åtgärd](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Spara din logikapp.

### <a name="test-your-logic-app"></a>Testa din logikapp

1. Om logikappen inte är redan aktiverat på logikappmenyn, Välj **översikt**. I verktygsfältet väljer **aktivera**.

1. På verktygsfältet för appdesignern väljer **kör**. Det här steget startar logikappen manuellt.

1. Utlösa logikappen genom att skicka en HTTP POST-begäran till URL: en i HTTP-begäran-utlösaren.
Inkludera ditt innehåll med din begäran meddelande. Skicka begäran, kan du använda ett verktyg som [Postman](https://www.getpostman.com/apps).

   I den här artikeln skickar begäran en IDoc-fil, som måste vara i XML-format och innehålla namnområdet för SAP-åtgärd som du använder, till exempel:

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. När du skickar HTTP-begäran, vänta på svar från din logikapp.

   > [!NOTE]
   > Din logikapp kan timeout om alla steg som krävs för svaret inte slutförs inom den [tidsgränsen för begäran](./logic-apps-limits-and-config.md). Om det här tillståndet inträffar kan blockeras begäranden. För att hjälpa dig att diagnostisera problem, lär du dig hur du kan [kontrollera och övervaka dina logikappar](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Du har nu skapat en logikapp som kan kommunicera med din SAP-server. Nu när du har konfigurerat en anslutning för SAP för din logikapp, kan du utforska andra tillgängliga SAP-åtgärder, till exempel BAPI och RFC.

## <a name="receive-from-sap"></a>Ta emot från SAP

Det här exemplet används en logikapp som utlöser när appen tar emot ett meddelande från ett SAP-system.

### <a name="add-an-sap-trigger"></a>Lägg till en SAP-utlösare

1. Skapa en tom logikapp som öppnas Logic App Designer i Azure-portalen.

1. I sökrutan anger du ”sap” som filter. Från den **utlösare** väljer **när ett meddelande tas emot från SAP**.

   ![Lägg till SAP-utlösare](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   Eller, går du till den **Enterprise** och sedan utlösaren:

   ![Lägg till SAP-utlösare från Enterprise-fliken](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Skapa nu din SAP-anslutning om du uppmanas att ange anslutningsinformation. Om anslutningen redan finns, fortsätter du med nästa steg så att du kan ställa in din SAP-åtgärd.

   **Skapa en lokal SAP-anslutning**

   - Ange anslutningsinformation för SAP-server. För den **Datagateway** egenskapen, Välj den datagateway som du skapade i Azure-portalen för din gateway-installation.

      - Om den **inloggningstyp** är inställd på **programserver**, dessa egenskaper, som normalt visas valfritt, krävs:

         ![Skapa serveranslutning för SAP-program](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      - Om den **inloggningstyp** är inställd på **grupp**, dessa egenskaper, som normalt visas valfritt, krävs:

          ![Skapa SAP meddelande serveranslutning](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Som standard används starkt att skriva för att kontrollera ogiltiga värden genom att utföra XML-verifiering mot schemat. Det här beteendet kan hjälpa dig att identifiera problem tidigare. Den **säkert att skriva** alternativet är tillgängligt för bakåtkompatibilitet och kontrollerar endast stränglängden. Läs mer om den [säkert att skriva alternativet](#safe-typing).

1. Ange de obligatoriska parametrarna utifrån din konfiguration för SAP-system.

   Du kan också ange en eller flera SAP-åtgärder. Den här listan över åtgärder anger de meddelanden som utlösaren tar emot från din SAP-server via en datagateway. En tom lista anger att utlösaren får alla meddelanden. Om listan innehåller mer än ett meddelande, får utlösaren endast meddelanden som anges i listan. Andra meddelanden som skickas från din SAP-server avvisas av gatewayen.

   Du kan välja en SAP-åtgärd filväljaren:

   ![Välj åtgärd för SAP](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Eller manuellt ange en åtgärd:

   ![Ange manuellt SAP-åtgärd](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   Här är ett exempel som visar hur åtgärden visas när du konfigurerar utlösaren tar emot flera meddelanden.

   ![Exempel för utlösare](media/logic-apps-using-sap-connector/example-trigger.png)  

   Läs mer om åtgärder som SAP, [meddelande scheman för IDOC-åtgärder](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Spara din logikapp nu så att du kan börja ta emot meddelanden från SAP-system.
På verktygsfältet för appdesignern väljer **spara**.

Logikappen är nu redo att ta emot meddelanden från SAP-system.

> [!NOTE]
> SAP-utlösaren är inte en avsökning utlösare men i stället är en webhook-baserade utlösare. Utlösaren kallas från gatewayen endast när det finns ett meddelande, så inga avsökningen behövs.

### <a name="test-your-logic-app"></a>Testa din logikapp

1. Skicka ett meddelande från SAP-system för att utlösa logikappen.

1. På logikappmenyn, väljer **översikt**. Granska den **Körningshistorik** för alla nya körningar för din logikapp.

1. Öppna den senaste körningen, som visar meddelandet som skickas från din SAP-system i utlösaren outputs-avsnittet.

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generera scheman för artefakter i SAP

Det här exemplet används en logikapp som du kan utlösa med en HTTP-begäran. SAP-åtgärden skickar en begäran till en SAP-system för att skapa scheman för angivna IDoc och BAPI. Scheman som returneras i svaret överförs till ett integrationkonto med hjälp av Azure Resource Manager-anslutningen.

### <a name="add-an-http-request-trigger"></a>Lägg till en HTTP-begäran-utlösare

1. Skapa en tom logikapp som öppnas Logic App Designer i Azure-portalen.

1. Ange ”http-begäran” i sökrutan som filter. Från den **utlösare** väljer **när en HTTP-begäran tas emot**.

   ![Lägg till HTTP-begäran-utlösare](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Spara din logikapp nu så att du kan generera en slutpunkts-URL för din logikapp.
På verktygsfältet för appdesignern väljer **spara**.

   Slutpunkten URL visas nu i utlösaren, till exempel:

   ![Skapa en Webbadress för slutpunkt](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Lägg till en SAP-åtgärd för att generera scheman

1. I Logikappdesignern under utlösaren väljer **nytt steg**.

   ![Välj ”nytt steg”](./media/logic-apps-using-sap-connector/add-action.png)

1. I sökrutan anger du ”sap” som filter. Från den **åtgärder** väljer **generera scheman**.
  
   ![Välj åtgärden Skicka för SAP](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Eller, du kan också välja den **Enterprise** och sedan SAP-åtgärd.

   ![Välj åtgärden för SAP-skicka från Enterprise-fliken](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Skapa nu din SAP-anslutning om du uppmanas att ange anslutningsinformation. Om anslutningen redan finns, fortsätter du med nästa steg så att du kan ställa in din SAP-åtgärd.

   **Skapa en lokal SAP-anslutning**

    1. Ange anslutningsinformation för SAP-server. För den **Datagateway** egenskapen, Välj den datagateway som du skapade i Azure-portalen för din gateway-installation.

       - Om den **inloggningstyp** är inställd på **programserver**, dessa egenskaper, som normalt visas valfritt, krävs:

         ![Skapa serveranslutning för SAP-program](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

       - Om den **inloggningstyp** är inställd på **grupp**, dessa egenskaper, som normalt visas valfritt, krävs:

         ![Skapa SAP meddelande serveranslutning](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

        Som standard används starkt att skriva för att kontrollera ogiltiga värden genom att utföra XML-verifiering mot schemat. Det här beteendet kan hjälpa dig att identifiera problem tidigare. Den **säkert att skriva** alternativet är tillgängligt för bakåtkompatibilitet och kontrollerar endast stränglängden. Läs mer om den [säkert att skriva alternativet](#safe-typing).

    1. När du är klar väljer du **skapa**. 
   
       Logic Apps konfigurerar och testar anslutningen till att anslutningen fungerar korrekt.

1. Ange sökvägen till den artefakt som du vill skapa schemat.

   Du kan välja åtgärden SAP filväljaren:

   ![Välj åtgärd för SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Alternativt kan du kan ange åtgärden manuellt:

   ![Ange manuellt SAP-åtgärd](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Om du vill generera scheman för flera artefakten, ger du åtgärdsinformation SAP för varje artefakt till exempel:

   ![Välj Lägg till nytt objekt](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Visa två objekt](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Läs mer om åtgärder som SAP, [meddelande scheman för IDOC åtgärder](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Spara din logikapp. På verktygsfältet för appdesignern väljer **spara**.

### <a name="test-your-logic-app"></a>Testa din logikapp

1. På verktygsfältet för appdesignern väljer **kör** att utlösa en körning för din logikapp.

1. Öppna körningen och kontrollera utdata för den **generera scheman** åtgärd.

   Utdata visar de genererade scheman för den angivna listan med meddelanden.

### <a name="upload-schemas-to-an-integration-account"></a>Ladda upp scheman till ett integrationskonto

Du kan också ladda ned eller lagra de genererade scheman i lagringsplatser, såsom en blob, lagring eller integrationskontot. Integrationskonton tillhandahålla en förstklassig upplevelse med andra XML-åtgärder, så det här exemplet visar hur du överför scheman till ett integrationkonto för samma logikapp med hjälp av Azure Resource Manager-anslutningen.

1. I Logikappdesignern under utlösaren väljer **nytt steg**.

1. Ange ”Resource Manager” i sökrutan som filter. Välj **skapa eller uppdatera en resurs**.

   ![Välj Azure Resource Manager-åtgärd](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Ange information för åtgärden, inklusive din Azure-prenumeration, Azure-resursgrupp och integrationskontot. Klicka i rutorna för dessa fält för att lägga till SAP-tokens i fälten och välj från den dynamiska innehållslistan som visas.

    1. Öppna den **Lägg till ny parameter** och välj den **plats** och **egenskaper** fält.

    1. Ange information för de här nya fält som visas i det här exemplet.

       ![Ange information om Azure Resource Manager-åtgärd](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   SAP **generera scheman** genererar scheman som en samling, så designern lägger automatiskt till en **för varje** loop för åtgärden. Här är ett exempel som visar hur den här åtgärden visas:

   ![Azure Resource Manager-åtgärd med ”för var och en” loop](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)  

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

1. Spara din logikapp. På verktygsfältet för appdesignern väljer **spara**.

### <a name="test-your-logic-app"></a>Testa din logikapp

1. På verktygsfältet för appdesignern väljer **kör** för att utlösa logikappen manuellt.

1. Kör efter ett lyckat, gå till integrationskontot och kontrollera att de genererade scheman finns.

## <a name="enable-secure-network-communications"></a>Aktivera säker kommunikation

Innan du börjar bör du se till att du uppfyller de tidigare angivna [krav](#pre-reqs):

* Den lokala datagatewayen är installerad på en dator som finns i samma nätverk som din SAP-system.
* För enkel inloggning körs gatewayen som en användare som är mappad till en SAP-användare.
* SNC-bibliotek som innehåller funktioner för ökad säkerhet är installerad på samma dator som den datagatewayen. Några exempel är [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos- och NTLM.

Om du vill aktivera SNC för dina förfrågningar till eller från SAP-system, Välj den **Använd SNC** kryssrutan i SAP-anslutning och ange följande egenskaper:

   ![Konfigurera SAP SNC i anslutning](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Egenskap | Beskrivning |
   |----------| ------------|
   | **SNC-bibliotekssökväg** | SNC-Biblioteksnamn eller sökväg i förhållande till NCo installationsplats eller absolut sökväg. Exempel är `sapsnc.dll` eller `.\security\sapsnc.dll` eller `c:\security\sapsnc.dll`. |
   | **SNC SSO** | När du ansluter via SNC används vanligtvis SNC-identiteten för att autentisera anroparen. Ett annat alternativ är att åsidosätta så att information om användare och lösenord kan användas för att autentisera anroparen, men raden fortfarande är krypterad. |
   | **SNC mitt namn** | I de flesta fall kan du utelämna den här egenskapen. Den installera SNC-lösningen vet vanligtvis sin egen SNC-namn. Endast för lösningar som har stöd för flera identiteter kan behöva du ange identitet som ska användas för den här specifika mål eller en server. |
   | **SNC-Partnernamn** | Namnet på backend-SNC. |
   | **SNC kvaliteten på skydd** | Tjänstkvaliteten som ska användas för SNC-kommunikation i den här specifika mål eller en server. Standardvärdet definieras av backend-system. Det maximala värdet definieras av security-produkten används för SNC. |
   |||

   > [!NOTE]
   > Konfigurerar inte miljövariabler miljövariabel SNC_LIB och SNC_LIB_64 på datorn där du har den datagatewayen och SNC-bibliotek. Om ange, de har företräde framför SNC-bibliotek värdet som skickas via anslutningen.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Säkert att skriva

Som standard när du skapar din SAP-anslutning, används starkt att skriva för att kontrollera ogiltiga värden genom att utföra XML-verifiering mot schemat. Det här beteendet kan hjälpa dig att identifiera problem tidigare. Den **säkert att skriva** alternativet är tillgängligt för bakåtkompatibilitet och kontrollerar endast stränglängden. Om du väljer **säkert att skriva**, DATS typ och TIMS typ i SAP behandlas som strängar i stället för motsvarigheterna XML `xs:date` och `xs:time`, där `xmlns:xs="http://www.w3.org/2001/XMLSchema"`. Säkert att skriva påverkar beteendet för alla schema-generering, skicka meddelandet för nyttolasten ”skickats” och ”mottagits”-svar och utlösaren. 

När starkt att skriva används (**säkert att skriva** är inte aktiverad), schemat mappar DATS och TIMS-typer till enklare XML-typer:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

När du skickar meddelanden med stark skriva uppfyller DATS och TIMS svaret till formatet matchande XML-typ:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

När **säkert att skriva** är aktiverad, schemat mappar DATS och TIMS typer till XML-sträng fält med längd, till exempel:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

När meddelanden skickas med **säkert att skriva** aktiverad DATS och TIMS svaret ser ut som i följande exempel:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```


## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

Här följer kända problem och begränsningar för SAP-anslutningen:

* Endast en enda skicka till SAP-anrop eller meddelandet fungerar med tRFC. BAPI commit mönster, till exempel flera tRFC anrop i samma session, stöds inte.
* SAP-utlösaren stöder inte mottagande batch idoc: er från SAP. Den här åtgärden kan resultera i RFC anslutningsfel mellan din SAP-system och datagateway.
* SAP-utlösaren stöder inte data gateway-kluster. Ibland redundans data gateway-noden som kommunicerar med SAP-system kan skilja sig från den aktiva noden, vilket leder till oväntade resultat. Scenarier med skicka stöds data gateway-kluster.
* SAP-anslutningsappen stöder för närvarande inte SAP router strängar. Den lokala datagatewayen måste finnas i samma nätverk som SAP-system som du vill ansluta till.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska de [anslutningsappens-referenssida](/connectors/sap/).

## <a name="next-steps"></a>Nästa steg

* [Ansluta till lokala system](../logic-apps/logic-apps-gateway-connection.md) från Azure Logic Apps.
* Lär dig att verifiera, transformera och använda andra meddelandeåtgärder med den [Enterprise-Integrationspaketet](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md).
