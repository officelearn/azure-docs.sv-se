---
title: Ansluta till SAP-system – Azure Logic Apps
description: Få åtkomst till och hantera SAP-resurser genom att automatisera arbets flöden med Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 05/09/2019
tags: connectors
ms.openlocfilehash: 9e46c51ae06920bd57f272248f06020dfad380e7
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326714"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Ansluta till SAP-system från Azure Logic Apps

Den här artikeln visar hur du kan komma åt dina lokala SAP-resurser inifrån en Logic-app med hjälp av SAP-anslutningen. Anslutningen fungerar med SAP: s klassiska versioner som R/3 och ECC-system lokalt. Anslutningen möjliggör även integrering med SAP: s nyare HANA-baserade SAP-system, till exempel S/4 HANA, oavsett om de finns lokalt eller i molnet. SAP Connector stöder meddelande-eller data integrering till och från SAP NetWeaver-baserade system via mellanliggande dokument (IDoc), Business Application Programming Interface (BAPI) eller Remote Function Call (RFC).

SAP-anslutaren använder [SAP .net Connector-biblioteket (NCo)](https://support.sap.com/en/product/connectors/msnet.html) och tillhandahåller följande åtgärder:

* **Skicka till SAP**: Skicka IDoc över tRFC, ring upp BAPI-funktioner över RFC eller anropa RFC/tRFC i SAP-system.
* **Ta emot från SAP**: Ta emot IDoc över tRFC, anropar BAPI-funktioner över tRFC eller anropa RFC/tRFC i SAP-system.
* **Generera scheman**: Generera scheman för SAP-artefakter för IDoc, BAPI eller RFC.

För dessa åtgärder stöder SAP-anslutaren grundläggande autentisering genom användar namn och lösen ord. Anslutningen har även stöd för [Säker nätverkskommunikation (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). SNC kan användas för SAP NetWeaver enkel inloggning (SSO) eller för ytterligare säkerhetsfunktioner som tillhandahålls av en extern säkerhets produkt.

SAP-anslutningen integreras med lokala SAP-system via den [lokala](../logic-apps/logic-apps-gateway-connection.md)datagatewayen. I skicka-scenarier, till exempel när ett meddelande skickas från en Logic app till ett SAP-system, fungerar datagatewayen som en RFC-klient och vidarebefordrar de begär Anden som tas emot från Logic app till SAP. På samma sätt fungerar datagatewayen som en RFC-server som tar emot begär Anden från SAP i Receive-scenarier och vidarebefordrar dem till Logic-appen.

Den här artikeln visar hur du skapar exempel på Logic Apps som integreras med SAP och som täcker de tidigare beskrivna integrerings scenarierna.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa med i den här artikeln behöver du följande objekt:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration ännu kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* Den Logic-app från vilken du vill komma åt SAP-systemet och en utlösare som startar din Logic app-arbetsflöde. Om du inte har använt Logic Apps, se [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md) och [snabb start: Skapa din första Logic-](../logic-apps/quickstart-create-first-logic-app-workflow.md)app.

* Din [SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) -Programserver eller [SAP-meddelande Server](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* Hämta och installera den senaste [lokala](https://www.microsoft.com/download/details.aspx?id=53127) datagatewayen på en lokal dator. Se till att konfigurera din gateway i Azure Portal innan du fortsätter. Gatewayen hjälper dig att komma åt lokala data och resurser på ett säkert sätt. Mer information finns i [installera en lokal datagateway för Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Om du använder SNC med SSO ser du till att gatewayen körs som en användare som är mappad mot SAP-användaren. Om du vill ändra standard kontot väljer du **Ändra konto**och anger autentiseringsuppgifterna för användaren.

  ![Ändra Gateway-konto](./media/logic-apps-using-sap-connector/gateway-account.png)

* Om du aktiverar SNC med en extern säkerhets produkt kopierar du SNC-biblioteket eller-filerna på samma dator där gatewayen är installerad. Några exempel på SNC-produkter är [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), KERBEROS och NTLM.

* Hämta och installera det senaste SAP-klientcertifikatet, som för närvarande är [SAP Connector (NCo 3,0) för Microsoft .net 3.0.22.0 som kompileras med .NET Framework 4,0-Windows 64-bitars (x64)](https://softwaredownloads.sap.com/file/0020000001000932019), på samma dator som den lokala datagatewayen. Installera den här versionen eller senare av följande anledningar:

  * Tidigare SAP NCo-versioner kan bli död när fler än ett IDoc-meddelande skickas på samma gång. Det här tillståndet blockerar alla senare meddelanden som skickas till SAP-målet, vilket leder till timeout-fel i meddelandet.
  
  * Den lokala datagatewayen körs bara på 64-bitars system. Annars får du fel meddelandet "felaktig avbildning" eftersom värd tjänsten för data Gateway inte stöder 32-bitars sammansättningar.
  
  * Både data Gateway-värdservern och Microsoft SAP-adaptern använder .NET Framework 4,5. SAP-NCo för .NET Framework 4,0 fungerar med processer som använder .NET Runtime 4,0 till 4.7.1. SAP-NCo för .NET Framework 2,0 fungerar med processer som använder .NET Runtime 2,0 till 3,5, men fungerar inte längre med den senaste lokala datagatewayen.

* Meddelande innehåll som du kan skicka till din SAP-server, till exempel en IDoc-fil, måste vara i XML-format och innehålla namn området för den SAP-åtgärd som du vill använda.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>Skicka till SAP

I det här exemplet används en Logic-app som du kan utlösa med en HTTP-begäran. Logic-appen skickar en IDoc till en SAP-server och returnerar ett svar till den begär Ande som anropade Logic-appen. 

### <a name="add-an-http-request-trigger"></a>Lägg till en HTTP-begäran-utlösare

I Azure Logic Apps måste varje Logi Kap par starta med en [](../logic-apps/logic-apps-overview.md#logic-app-concepts)utlösare som utlöses när en enskild händelse inträffar eller när ett särskilt villkor uppfylls. Varje gång utlösaren utlöses skapar Logic Apps-motorn en Logic App-instans och börjar köra appens arbets flöde.

I det här exemplet skapar du en Logic-app med en slut punkt i Azure så att du kan skicka *http post-begäranden* till din Logic app. När din Logic app tar emot dessa HTTP-förfrågningar utlöses utlösaren och kör nästa steg i arbets flödet.

1. I [Azure Portal](https://portal.azure.com)skapar du en tom Logic-app som öppnar Logic Apps designer.

1. I rutan Sök anger du "http-begäran" som filter. Välj **när en HTTP-begäran tas emot**från listan utlösare. ****

   ![Lägg till utlösare för HTTP-begäran](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Spara din Logic app så att du kan skapa en slut punkts-URL för din Logic app. I verktygsfältet designer väljer du **Spara**.

   Slut punkts-URL: en visas nu i utlösaren, till exempel:

   ![Generera URL för slut punkt](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Lägg till en SAP-åtgärd

I Azure Logic Apps är en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) ett steg i arbets flödet som följer en utlösare eller en annan åtgärd. Om du inte har lagt till en utlösare i din Logic app och vill följa det här exemplet lägger du till utlösaren som [beskrivs i det här avsnittet](#add-trigger).

1. I Logic App Designer går du till utlösaren och väljer **nytt steg**.

   ![Välj "nytt steg"](./media/logic-apps-using-sap-connector/add-action.png)

1. I rutan Sök anger du "SAP" som filter. I listan **åtgärder** väljer **du skicka meddelande till SAP**.
  
   ![Välj SAP Send-åtgärd](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Eller i stället för att söka, väljer du fliken **Enterprise** och väljer SAP-åtgärden.

   ![Välj SAP skicka åtgärd från fliken Enterprise](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Om du uppmanas att ange anslutnings information skapar du din SAP-anslutning nu. Annars, om anslutningen redan finns, Fortsätt med nästa steg så att du kan konfigurera din SAP-åtgärd.

   **Skapa en lokal SAP-anslutning**

    1. Ange anslutnings informationen för din SAP-server. För egenskapen **data Gateway** väljer du den datagateway som du skapade i Azure Portal för din gateway-installation.

         - Om egenskapen **inloggnings typ** är inställd på **program Server**, krävs de här egenskaperna, som oftast visas som valfria,:

            ![Skapa SAP Application Server-anslutning](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

         - Om egenskapen **inloggnings typ** är inställd på **grupp**, krävs dessa egenskaper, som oftast visas som valfria,:

            ![Skapa anslutning till SAP Message Server](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

           Som standard används stark inmatning för att kontrol lera ogiltiga värden genom att utföra XML-verifiering mot schemat. Det här beteendet kan hjälpa dig att identifiera problem tidigare. Alternativet för **säker inmatning** är tillgängligt för bakåtkompatibilitet och kontrollerar sträng längden. Läs mer om [alternativet för säker inmatning](#safe-typing).

    1. När du är klar väljer du **skapa**.

       Logic Apps konfigurerar och testar anslutningen för att kontrol lera att anslutningen fungerar korrekt.

1. Nu hittar och väljer du en åtgärd från SAP-servern.

    1. I rutan **SAP-åtgärd** väljer du mappikonen. I listan fil söker du efter och väljer det SAP-meddelande som du vill använda. Använd pilarna för att navigera i listan.

       Det här exemplet väljer en IDoc med typen **order** .

       ![Sök och Välj åtgärden IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

       Om du inte hittar den åtgärd du vill ha kan du ange en sökväg manuellt, till exempel:

       ![Ange sökväg till IDoc-åtgärden manuellt](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

       > [!TIP]
       > Ange värdet för **SAP-åtgärd** via uttrycks redigeraren. På så sätt kan du använda samma åtgärd för olika meddelande typer.

       Mer information om IDoc-åtgärder finns i [meddelande scheman för iDOC-åtgärder](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

    1. Klicka i rutan **inmatat meddelande** så att listan med dynamiskt innehåll visas. Välj fältet **brödtext** under **när en http-begäran tas emot**från listan.

       Det här steget inkluderar bröd innehållet från din HTTP Request-utlösare och skickar utdata till SAP-servern.

       ![Välj fältet brödtext](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

       När du är klar ser din SAP-åtgärd ut som i det här exemplet:

       ![Slutför SAP-åtgärd](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Spara din logikapp. I verktygsfältet designer väljer du **Spara**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Lägg till en HTTP-svars åtgärd

Lägg nu till en svars åtgärd i din Logic Apps-arbetsflöde och inkludera utdata från SAP-åtgärden. På så sätt returnerar din Logic-app resultatet från din SAP-server till den ursprungliga begär Ande.

1. Välj **nytt steg**under SAP-åtgärden i Logic App Designer.

1. I rutan Sök anger du "Response" som filter. I listan **åtgärder** väljer du **svar**.

1. Klicka i rutan **brödtext** så att listan med dynamiskt innehåll visas. I listan, under **Skicka meddelande till SAP**, väljer du fältet **brödtext** .

   ![Slutför SAP-åtgärd](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Spara din logikapp.

### <a name="test-your-logic-app"></a>Testa din Logic app

1. Om din Logic app inte redan är aktive rad väljer du **Översikt**på din Logic app-meny. Välj **Aktivera**i verktygsfältet.

1. I verktygsfältet designer väljer du **Kör**. Det här steget startar din Logic app manuellt.

1. Utlös din Logic app genom att skicka en HTTP POST-begäran till URL: en i din HTTP Request-utlösare.
Inkludera ditt meddelande innehåll med din begäran. Du kan använda ett verktyg som Postman för att skicka begäran. [](https://www.getpostman.com/apps)

   I den här artikeln skickar begäran en IDoc-fil som måste vara i XML-format och innehålla namn området för den SAP-åtgärd som du använder, till exempel:

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. När du har skickat din HTTP-begäran väntar du på svaret från din Logic app.

   > [!NOTE]
   > Din Logi Kap par kan ta lång tid om alla steg som krävs för svaret inte slutförs inom [tids gränsen för begäran](./logic-apps-limits-and-config.md). Om det här tillståndet inträffar kan begär Anden blockeras. För att hjälpa dig att diagnostisera problem kan du läsa mer om hur du kan [kontrol lera och övervaka dina Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Nu har du skapat en logisk app som kan kommunicera med din SAP-server. Nu när du har konfigurerat en SAP-anslutning för din Logic app kan du utforska andra tillgängliga SAP-åtgärder, till exempel BAPI och RFC.

## <a name="receive-from-sap"></a>Ta emot från SAP

I det här exemplet används en Logic-app som utlöses när appen tar emot ett meddelande från ett SAP-system.

### <a name="add-an-sap-trigger"></a>Lägg till en SAP-utlösare

1. I Azure Portal skapar du en tom Logic-app som öppnar Logic Apps designer.

1. I rutan Sök anger du "SAP" som filter. Välj **när ett meddelande tas emot från SAP**i listan utlösare. ****

   ![Lägg till SAP-utlösare](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   Du kan också gå till fliken **Enterprise** och välja utlösaren:

   ![Lägg till SAP-utlösare från fliken Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Om du uppmanas att ange anslutnings information skapar du din SAP-anslutning nu. Om anslutningen redan finns fortsätter du med nästa steg så att du kan konfigurera din SAP-åtgärd.

   **Skapa en lokal SAP-anslutning**

   - Ange anslutnings informationen för din SAP-server. För egenskapen **data Gateway** väljer du den datagateway som du skapade i Azure Portal för din gateway-installation.

      - Om egenskapen **inloggnings typ** är inställd på **program Server**, krävs de här egenskaperna, som oftast visas som valfria,:

         ![Skapa SAP Application Server-anslutning](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      - Om egenskapen **inloggnings typ** är inställd på **grupp**, krävs dessa egenskaper, som oftast visas som valfria,:

          ![Skapa anslutning till SAP Message Server](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Som standard används stark inmatning för att kontrol lera ogiltiga värden genom att utföra XML-verifiering mot schemat. Det här beteendet kan hjälpa dig att identifiera problem tidigare. Alternativet för **säker inmatning** är tillgängligt för bakåtkompatibilitet och kontrollerar sträng längden. Läs mer om [alternativet för säker inmatning](#safe-typing).

1. Ange de parametrar som krävs baserat på din konfiguration av SAP-systemet.

   Du kan också ange en eller flera SAP-åtgärder. Den här listan med åtgärder anger de meddelanden som utlösaren tar emot från din SAP-server via datagatewayen. En tom lista anger att utlösaren tar emot alla meddelanden. Om listan innehåller fler än ett meddelande, tar utlösaren bara emot de meddelanden som anges i listan. Alla andra meddelanden som skickas från din SAP-server avvisas av gatewayen.

   Du kan välja en SAP-åtgärd från fil väljaren:

   ![Välj SAP-åtgärd](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Du kan också ange en åtgärd manuellt:

   ![Ange SAP-åtgärd manuellt](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   Här är ett exempel som visar hur åtgärden visas när du konfigurerar utlösaren för att ta emot mer än ett meddelande.

   ![Utlösare exempel](media/logic-apps-using-sap-connector/example-trigger.png)  

   Mer information om SAP-åtgärden finns i [meddelande scheman för iDOC-åtgärder](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Spara din Logic app så att du kan börja ta emot meddelanden från SAP-systemet.
I verktygsfältet designer väljer du **Spara**.

Din Logi Kap par är nu redo att ta emot meddelanden från SAP-systemet.

> [!NOTE]
> SAP-utlösaren är ingen avsöknings utlösare utan är en webhook-baserad utlösare i stället. Utlösaren anropas från gatewayen endast när det finns ett meddelande, så ingen avsökning krävs.

### <a name="test-your-logic-app"></a>Testa din Logic app

1. Skicka ett meddelande från SAP-systemet för att utlösa din Logic app.

1. På menyn Logic app väljer du **Översikt**. Granska **körnings historiken** för alla nya körningar för din Logic app.

1. Öppna den senaste körningen, som visar meddelandet som skickas från ditt SAP-system i avsnittet Utlös utmatningar.

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generera scheman för artefakter i SAP

I det här exemplet används en Logic-app som du kan utlösa med en HTTP-begäran. SAP-åtgärden skickar en begäran till ett SAP-system för att generera scheman för angivet IDoc och BAPI. Scheman som returneras i svaret överförs till ett integrations konto med hjälp av Azure Resource Manager anslutningen.

### <a name="add-an-http-request-trigger"></a>Lägg till en HTTP-begäran-utlösare

1. I Azure Portal skapar du en tom Logic-app som öppnar Logic Apps designer.

1. I rutan Sök anger du "http-begäran" som filter. Välj **när en HTTP-begäran tas emot**från listan utlösare. ****

   ![Lägg till utlösare för HTTP-begäran](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Spara din Logic app så att du kan skapa en slut punkts-URL för din Logic app.
I verktygsfältet designer väljer du **Spara**.

   Slut punkts-URL: en visas nu i utlösaren, till exempel:

   ![Generera URL för slut punkt](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Lägga till en SAP-åtgärd för att generera scheman

1. I Logic App Designer går du till utlösaren och väljer **nytt steg**.

   ![Välj "nytt steg"](./media/logic-apps-using-sap-connector/add-action.png)

1. I rutan Sök anger du "SAP" som filter. I listan **åtgärder** väljer du **Skapa scheman**.
  
   ![Välj SAP Send-åtgärd](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Alternativt kan du också välja fliken **Enterprise** och välja SAP-åtgärd.

   ![Välj SAP skicka åtgärd från fliken Enterprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Om du uppmanas att ange anslutnings information skapar du din SAP-anslutning nu. Om anslutningen redan finns fortsätter du med nästa steg så att du kan konfigurera din SAP-åtgärd.

   **Skapa en lokal SAP-anslutning**

   1. Ange anslutnings informationen för din SAP-server. För egenskapen **data Gateway** väljer du den datagateway som du skapade i Azure Portal för din gateway-installation.

      - Om egenskapen **inloggnings typ** är inställd på **program Server**, krävs de här egenskaperna, som oftast visas som valfria,:

        ![Skapa SAP Application Server-anslutning](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      - Om egenskapen **inloggnings typ** är inställd på **grupp**, krävs dessa egenskaper, som oftast visas som valfria,:

        ![Skapa anslutning till SAP Message Server](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Som standard används stark inmatning för att kontrol lera ogiltiga värden genom att utföra XML-verifiering mot schemat. Det här beteendet kan hjälpa dig att identifiera problem tidigare. Alternativet för **säker inmatning** är tillgängligt för bakåtkompatibilitet och kontrollerar sträng längden. Läs mer om [alternativet för säker inmatning](#safe-typing).

   1. När du är klar väljer du **skapa**. 
   
      Logic Apps konfigurerar och testar anslutningen för att kontrol lera att anslutningen fungerar korrekt.

1. Ange sökvägen till den artefakt som du vill skapa schemat för.

   Du kan välja SAP-åtgärden från fil väljaren:

   ![Välj SAP-åtgärd](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Du kan också ange åtgärden manuellt:

   ![Ange SAP-åtgärd manuellt](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Om du vill generera scheman för fler än en artefakt anger du SAP-åtgärds informationen för varje artefakt, till exempel:

   ![Välj Lägg till nytt objekt](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Visa två objekt](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Mer information om SAP-åtgärden finns i [meddelande scheman för iDOC-åtgärder](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Spara din logikapp. I verktygsfältet designer väljer du **Spara**.

### <a name="test-your-logic-app"></a>Testa din Logic app

1. I verktygsfältet designer väljer du **Kör** för att utlösa en körning för din Logic app.

1. Öppna körningen och kontrol lera utdata för åtgärden **generera scheman** .

   Utdata visar de genererade scheman för den angivna listan med meddelanden.

### <a name="upload-schemas-to-an-integration-account"></a>Ladda upp scheman till ett integrations konto

Du kan också hämta eller lagra de genererade schemana i-databaser, till exempel ett BLOB-, lagrings-eller integrations konto. Integrations konton ger en förstklassig upplevelse med andra XML-åtgärder, så det här exemplet visar hur du överför scheman till ett integrations konto för samma Logic-app med hjälp av Azure Resource Manager-anslutningen.

1. I Logic App Designer går du till utlösaren och väljer **nytt steg**.

1. I rutan Sök anger du "Resource Manager" som filter. Välj **skapa eller uppdatera en resurs**.

   ![Välj Azure Resource Manager åtgärd](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Ange information om åtgärden, inklusive Azure-prenumeration, Azure-resurs grupp och integrations konto. Om du vill lägga till SAP-tokens i fälten klickar du i rutorna för dessa fält och väljer från listan med dynamiskt innehåll som visas.

   1. Öppna listan **Lägg till ny parameter** och välj fälten **plats** och **Egenskaper** .

   1. Ange information om de nya fälten som visas i det här exemplet.

      ![Ange information om Azure Resource Manager åtgärd](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   Åtgärden **skapa schema** i SAP genererar scheman som en samling, så att design verktyget automatiskt lägger till en **för varje** loop till åtgärden. Här är ett exempel som visar hur den här åtgärden visas:

   ![Azure Resource Manager åtgärd med "för varje"-loop](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)  
   > [!NOTE]
   > Schemana använder Base64-kodat format. Om du vill överföra scheman till ett integrations konto måste de avkodas med hjälp `base64ToString()` av funktionen. Här är ett exempel som visar koden för `"properties"` -elementet:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Spara din logikapp. I verktygsfältet designer väljer du **Spara**.

### <a name="test-your-logic-app"></a>Testa din Logic app

1. I verktygsfältet designer väljer du **Kör** för att utlösa din Logic app manuellt.

1. Efter en lyckad körning går du till integrations kontot och kontrollerar att de genererade schemana finns.

## <a name="enable-secure-network-communications"></a>Aktivera säker nätverkskommunikation

Innan du börjar ska du kontrol lera att du uppfyller de tidigare [](#pre-reqs)angivna förutsättningarna:

* Den lokala datagatewayen är installerad på en dator som är i samma nätverk som ditt SAP-system.
* För SSO körs gatewayen som en användare som är mappad till en SAP-användare.
* SNC-biblioteket som tillhandahåller ytterligare säkerhetsfunktioner installeras på samma dator som data gatewayen. Några exempel är [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), KERBEROS och NTLM.

   Om du vill aktivera SNC för dina begär anden till eller från SAP-systemet markerar du kryss rutan **Använd SNC** i SAP-anslutningen och anger följande egenskaper:

   ![Konfigurera SAP-SNC i anslutning](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Egenskap | Beskrivning |
   |----------| ------------|
   | **Sökväg till SNC-bibliotek** | SNC-bibliotekets namn eller sökväg i förhållande till NCo-installations plats eller absolut sökväg. Exempel är `sapsnc.dll` eller `.\security\sapsnc.dll` eller `c:\security\sapsnc.dll`. |
   | **SNC SSO** | När du ansluter via SNC används SNC-identiteten vanligt vis för att autentisera anroparen. Ett annat alternativ är att åsidosätta så att information om användare och lösen ord kan användas för att autentisera anroparen, men linjen är fortfarande krypterad. |
   | **SNC mitt namn** | I de flesta fall kan den här egenskapen utelämnas. Den installerade SNC-lösningen känner vanligt vis till sitt eget SNC-namn. För lösningar som har stöd för flera identiteter kan du behöva ange vilken identitet som ska användas för det aktuella målet eller servern. |
   | **SNC partner namn** | Namnet på backend-SNC. |
   | **SNC skydds kvalitet** | Quality of service som ska användas för SNC-kommunikation för det specifika målet eller den här servern. Standardvärdet definieras av Server dels systemet. Det maximala värdet definieras av den säkerhets produkt som används för SNC. |
   |||

   > [!NOTE]
   > Ange inte miljövariablerna SNC_LIB och SNC_LIB_64 på den dator där du har data gatewayen och SNC-biblioteket. Om det är inställt, prioriteras de av SNC-bibliotekets värde som skickas via anslutningen.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Säker inmatning

När du skapar din SAP-anslutning används som standard stark inmatning för att kontrol lera ogiltiga värden genom att utföra XML-verifiering mot schemat. Det här beteendet kan hjälpa dig att identifiera problem tidigare. Alternativet för **säker inmatning** är tillgängligt för bakåtkompatibilitet och kontrollerar sträng längden. Om du väljer **säker inmatning**behandlas dats-typen och Tims-typen i SAP som strängar i stället för motsvarande XML-motsvarigheter, `xs:date` och `xs:time`där `xmlns:xs="http://www.w3.org/2001/XMLSchema"`. Säker inmatning påverkar beteendet för all schema generering, att skicka meddelandet för både "har skickats"-nytto lasten och "mottaget"-svaret och utlösaren. 

När stark inmatning används (**säker inmatning** är inte aktiverat) mappar schemat dats-och Tims-typerna till fler enkla XML-typer:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

När du skickar meddelanden med stark inmatning uppfyller DATS-och TIMS-svaret det matchande XML-typ formatet:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

När **säker inmatning** är aktiverat mappar schemat dats-och Tims-typerna till XML-sträng fält med längd begränsningar, till exempel:

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

När meddelanden skickas med **säker inmatning** aktive rad ser dats och Tims-svaret ut som i det här exemplet:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

Här är de kända problemen och begränsningarna för SAP-anslutningen:

* Endast ett enda skicka till SAP-anrop eller ett meddelande fungerar med tRFC. Det finns inte stöd för att genomföra flera tRFC-anrop i samma session.

* SAP-utlösaren stöder inte mottagning av batch-IDocs från SAP. Den här åtgärden kan leda till att RFC-anslutningsfel uppstår mellan SAP-systemet och datagatewayen.

* SAP-utlösaren stöder inte data Gateway-kluster. I vissa fall kan datagateway-noden som kommunicerar med SAP-systemet skilja sig från den aktiva noden, vilket resulterar i ett oväntat beteende. För sändnings scenarier stöds data Gateway-kluster.

* SAP-anslutaren stöder för närvarande inte SAP-router-strängar. Den lokala datagatewayen måste finnas i samma lokala nätverk som det SAP-system som du vill ansluta till.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/sap/).

## <a name="next-steps"></a>Nästa steg

* [Anslut till lokala system](../logic-apps/logic-apps-gateway-connection.md) från Azure Logic Apps.
* Lär dig hur du verifierar, transformerar och använder andra meddelande åtgärder med [Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md).
