---
title: Anslut till SAP-system
description: Komma åt och hantera SAP-resurser genom att automatisera arbetsflöden med Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/30/2019
tags: connectors
ms.openlocfilehash: 39ab222f64d964e95b16e043c9cdeccd8170ace3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651023"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Ansluta till SAP-system från Azure Logic Apps

> [!IMPORTANT]
> De tidigare SAP Application Server- och SAP Message Server-kopplingarna är inaktuella den 29 februari 2020. Den aktuella SAP-anslutningen konsoliderar dessa tidigare SAP-kopplingar så att du inte behöver ändra anslutningstypen, är helt kompatibel med tidigare kopplingar, ger många ytterligare funktioner och fortsätter att använda SAP .Net-anslutningsbiblioteket ( SAP NCo).
>
> För logikappar som använder de äldre kopplingarna migrerar du [till den senaste kopplingen](#migrate) före utfasningsdatumet. Annars kommer dessa logikappar att uppleva körningsfel och kan inte skicka meddelanden till ditt SAP-system.

Den här artikeln visar hur du kan komma åt dina lokala SAP-resurser inifrån en logikapp med hjälp av SAP-kopplingen. Kontakten fungerar med SAP:s klassiska utgåvor som R/3 och ECC-system lokalt. Anslutningen möjliggör också integrering med SAP:s nyare HANA-baserade SAP-system, till exempel S/4 HANA, oavsett om de finns lokalt eller i molnet. SAP-anslutningen stöder meddelande- eller dataintegration till och från SAP NetWeaver-baserade system via Intermediate Document (IDoc), Business Application Programming Interface (BAPI) eller REMOTE Function Call (RFC).

SAP-anslutningen använder [NCo-biblioteket (SAP .NET Connector)](https://support.sap.com/en/product/connectors/msnet.html) och tillhandahåller följande åtgärder:

* **Skicka meddelande till SAP**: Skicka IDoc över tRFC, anropa BAPI-funktioner via RFC eller anropa RFC/tRFC i SAP-system.
* **När ett meddelande tas emot från SAP:** Ta emot IDoc över tRFC anropar du BAPI-funktioner över tRFC eller anropar RFC/tRFC i SAP-system.
* **Generera scheman:** Generera scheman för SAP-artefakter för IDoc, BAPI eller RFC.

För dessa åtgärder stöder SAP-anslutningen grundläggande autentisering via användarnamn och lösenord. Anslutningen stöder även [Secure Network Communications (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). SNC kan användas för SAP NetWeaver enkel inloggning (SSO) eller för ytterligare säkerhetsfunktioner som tillhandahålls av en extern säkerhetsprodukt.

SAP-anslutningen integreras med lokala SAP-system via den [lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md). I skicka scenarier, till exempel när ett meddelande skickas från en logik app till ett SAP-system, fungerar datagateway som en RFC-klient och vidarebefordrar begäranden som tas emot från logikappen till SAP. I mottagningsscenarier fungerar datagatewayen också som en RFC-server som tar emot begäranden från SAP och vidarebefordrar dem till logikappen.

Den här artikeln visar hur du skapar exempel logikappar som integreras med SAP samtidigt som täcker de tidigare beskrivna integrationsscenarier. För logikappar som använder de äldre SAP-kopplingarna visar den här artikeln hur du migrerar logikapparna till den senaste SAP-anslutningen.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Krav

För att följa med i den här artikeln behöver du följande:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration ännu [registrerar du dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Logikappen där du vill komma åt ditt SAP-system och en utlösare som startar logikappens arbetsflöde. Om du inte har tidigare information om logikappar [Quickstart: Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Din [SAP-programserver](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) eller [SAP-meddelandeserver](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* Hämta och installera den senaste [lokala datagatewayen](https://www.microsoft.com/download/details.aspx?id=53127) på en lokal dator. Se till att du konfigurerar din gateway i Azure-portalen innan du fortsätter. Gatewayen hjälper dig att på ett säkert sätt komma åt lokala data och resurser. Mer information finns i [Installera en lokal datagateway för Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Om du använder SNC med SSO kontrollerar du att gatewayen körs som en användare som är mappad mot SAP-användaren. Om du vill ändra standardkontot väljer du **Ändra konto**och anger användaruppgifterna.

  ![Ändra gateway-konto](./media/logic-apps-using-sap-connector/gateway-account.png)

* Om du aktiverar SNC med en extern säkerhetsprodukt kopierar du SNC-biblioteket eller filerna på samma dator där gatewayen är installerad. Några exempel på SNC-produkter är [sapseculib,](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)Kerberos och NTLM.

* Hämta och installera det senaste SAP-klientbiblioteket, som för närvarande är [SAP Connector (NCo 3.0) för Microsoft .NET 3.0.22.0 som kompilerats med .NET Framework 4.0 - Windows 64-bitars (x64)](https://softwaredownloads.sap.com/file/0020000001000932019), på samma dator som den lokala datagatewayen. Installera den här versionen eller senare av följande skäl:

  * Tidigare SAP NCo-versioner kan bli låsta när mer än ett IDoc-meddelande skickas samtidigt. Det här villkoret blockerar alla senare meddelanden som skickas till SAP-målet, vilket gör att meddelandena time out.
  
  * Den lokala datagatewayen körs endast på 64-bitarssystem. Annars får du ett felmeddelande om "felaktig avbildning" eftersom värdtjänsten för datagateway inte stöder 32-bitars sammansättningar.
  
  * Både värdtjänsten för datagateway och Microsoft SAP-adaptern använder .NET Framework 4.5. SAP NCo för .NET Framework 4.0 fungerar med processer som använder .NET runtime 4.0 till 4.7.1. SAP NCo för .NET Framework 2.0 fungerar med processer som använder .NET runtime 2.0 till 3.5, men fungerar inte längre med den senaste lokala datagatewayen.

* Meddelandeinnehåll som du kan skicka till DIN SAP-server, till exempel en exempel-IDoc-fil, måste vara i XML-format och innehålla namnområdet för den SAP-åtgärd som du vill använda.

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migrera till aktuell koppling

1. Om du inte redan har gjort det uppdaterar du den [lokala datagatewayen](https://www.microsoft.com/download/details.aspx?id=53127) så att du har den senaste versionen. Mer information finns i [Installera en lokal datagateway för Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

1. I logikappen som använder den äldre SAP-kopplingen tar du bort åtgärden **Skicka till SAP.**

1. Lägg till skicka meddelandet **till SAP från** den senaste SAP-anslutningen. Innan du kan använda den här åtgärden återskapar du anslutningen till SAP-systemet.

1. När du är klar sparar du logikappen.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Skicka meddelande till SAP

I det här exemplet används en logikapp som du kan utlösa med en HTTP-begäran. Logikappen skickar en IDoc till en SAP-server och returnerar ett svar till beställaren som anropade logikappen.

### <a name="add-an-http-request-trigger"></a>Lägga till en HTTP-begärandeutlösare

I Azure Logic Apps måste varje logikapp starta med en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en viss händelse inträffar eller när ett visst villkor uppfylls. Varje gång utlösaren utlöses skapar Logic Apps-motorn en logikappinstans och börjar köra appens arbetsflöde.

I det här exemplet skapar du en logikapp med en slutpunkt i Azure så att du kan skicka *HTTP POST-begäranden* till logikappen. När logikappen tar emot dessa HTTP-begäranden utlöses utlösaren och kör nästa steg i arbetsflödet.

1. Skapa en tom logikapp i [Azure-portalen](https://portal.azure.com)som öppnar Logic App Designer.

1. Ange som filter `http request` i sökrutan. Välj **När en HTTP-begäran tas emot i**listan **Utlösare** .

   ![Lägga till utlösare för HTTP-begäran](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Spara nu logikappen så att du kan skapa en slutpunkts-URL för logikappen. Välj **Spara**i designerverktygsfältet .

   Slutpunkts-URL:en visas nu i utlösaren, till exempel:

   ![Generera URL för slutpunkt](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Lägga till en SAP-åtgärd

I Azure Logic Apps är en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. Om du inte har lagt till en utlösare i logikappen ännu och vill följa det här exemplet lägger du till [utlösaren som beskrivs i det här avsnittet](#add-trigger).

1. Välj **Nytt steg**under utlösaren i Logic App Designer.

   ![Lägga till nytt steg i logikappen](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Ange som filter `sap` i sökrutan. Välj Skicka meddelande **till SAP**i listan **Åtgärder** .
  
   ![Välj åtgärden Skicka meddelande till SAP](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Du kan också välja fliken **Företag** och välja SAP-åtgärden.

   ![Välj åtgärden Skicka meddelande till SAP på fliken Företag](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Om anslutningen redan finns fortsätter du med nästa steg så att du kan konfigurera SAP-åtgärden. Om du uppmanas att ange anslutningsinformation anger du informationen så att du kan skapa en anslutning till den lokala SAP-servern nu.

   1. Ange ett namn för anslutningen.

   1. I avsnittet **Datagateway** under **Prenumeration**väljer du först Azure-prenumerationen för gatewayresursen som du skapade i Azure-portalen för gateway-installationen. 
   
   1. Välj gateway-resurs under **Anslutningsgateway.**

   1. Fortsätt att tillhandahålla information om anslutningen. För egenskapen **Logon Type** följer du steget baserat på om egenskapen är inställd på **Application Server** eller **Group:**
   
      * För **Application Server**krävs dessa egenskaper, som vanligtvis verkar valfria:

        ![Skapa ANSLUTNING TILL SAP-programserver](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * För **Grupp**krävs dessa egenskaper, som vanligtvis verkar valfria:

        ![Skapa ANSLUTNING TILL SAP-meddelandeserver](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Som standard används stark skrivning för att söka efter ogiltiga värden genom att utföra XML-validering mot schemat. Detta kan hjälpa dig att identifiera problem tidigare. Alternativet **Säker skrivning** är tillgängligt för bakåtkompatibilitet och kontrollerar bara stränglängden. Läs mer om [alternativet Säker typning](#safe-typing).

   1. När du är klar väljer du **Skapa**.

      Logic Apps konfigurerar och testar anslutningen för att säkerställa att anslutningen fungerar som den ska.

1. Nu hitta och välj en åtgärd från din SAP-server.

   1. Markera mappikonen i rutan **SAP-åtgärd.** Leta reda på och välj det SAP-meddelande som du vill använda i fillistan. Om du vill navigera i listan använder du pilarna.

      I det här exemplet markeras en IDoc med typen **Order.**

      ![Sök efter och välj IDoc-åtgärd](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Om du inte hittar den åtgärd du vill ha kan du manuellt ange en sökväg, till exempel:

      ![Ange manuellt sökväg till IDoc-åtgärd](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Ange värdet för **SAP-åtgärd** via uttrycksredigeraren. På så sätt kan du använda samma åtgärd för olika meddelandetyper.

      Mer information om IDoc-åtgärder finns i [Meddelandescheman för IDOC-åtgärder](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Klicka i rutan **Indatameddelande** så att listan med dynamiskt innehåll visas. Välj fältet **Brödtext** i listan under **När en HTTP-begäran tas emot.**

      Det här steget innehåller brödtextinnehållet från http-begärandeutlösaren och skickar utdata till SAP-servern.

      ![Välj egenskapen "Brödtext" från utlösaren](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      När du är klar ser SAP-åtgärden ut så här:

      ![Slutför SAP-åtgärden](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet .

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Lägga till en HTTP-svarsåtgärd

Lägg nu till en svarsåtgärd i logikappens arbetsflöde och inkludera utdata från SAP-åtgärden. På så sätt returnerar logikappen resultaten från DIN SAP-server till den ursprungliga beställaren.

1. Välj **Nytt steg**under SAP-åtgärden i Logic App Designer.

1. Ange som filter `response` i sökrutan. Välj **Svar**i listan **Åtgärder** .

1. Klicka i rutan **Brödtext** så att listan med dynamiskt innehåll visas. Välj fältet **Brödtext** **i**listan.

   ![Fullständig SAP-åtgärd](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Spara din logikapp.

### <a name="test-your-logic-app"></a>Testa logikappen

1. Om logikappen inte redan är aktiverad väljer du **Översikt**på logikappmenyn . Välj **Aktivera**i verktygsfältet .

1. Välj **Kör**i designerverktygsfältet . Det här steget startar logikappen manuellt.

1. Utlösa logikappen genom att skicka en HTTP POST-begäran till URL:en i http-utlösaren.
Inkludera ditt meddelandeinnehåll med din begäran. Om du vill skicka begäran kan du använda ett verktyg som [Postman](https://www.getpostman.com/apps).

   I den här artikeln skickar begäran en IDoc-fil, som måste vara i XML-format och innehålla namnområdet för den SAP-åtgärd som du använder, till exempel:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. När du har skickat din HTTP-begäran väntar du på svaret från logikappen.

   > [!NOTE]
   > Logikappen kan ta timeout om alla steg som krävs för svaret inte slutförs inom [tidsgränsen för begäran](./logic-apps-limits-and-config.md). Om detta tillstånd inträffar kan begäranden blockeras. Lär dig hur du kan [kontrollera och övervaka dina logikappar.](../logic-apps/monitor-logic-apps.md)

Du har nu skapat en logikapp som kan kommunicera med DIN SAP-server. Nu när du har konfigurerat en SAP-anslutning för logikappen kan du utforska andra tillgängliga SAP-åtgärder, till exempel BAPI och RFC.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Ta emot meddelande från SAP

I det här exemplet används en logikapp som utlöses när appen tar emot ett meddelande från ett SAP-system.

### <a name="add-an-sap-trigger"></a>Lägga till en SAP-utlösare

1. Skapa en tom logikapp i Azure-portalen som öppnar Logic App Designer.

1. Ange som filter `sap` i sökrutan. Välj **När ett meddelande tas emot från SAP**i listan **Utlösare** .

   ![Lägg till SAP-utlösare](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Du kan också välja fliken **Företag** och sedan välja utlösaren:

   ![Lägg till SAP-utlösare från fliken Företag](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Om anslutningen redan finns fortsätter du med nästa steg så att du kan konfigurera SAP-åtgärden. Om du uppmanas att ange anslutningsinformation anger du informationen så att du kan skapa en anslutning till den lokala SAP-servern nu.

   1. Ange ett namn för anslutningen.

   1. I avsnittet **Datagateway** under **Prenumeration**väljer du först Azure-prenumerationen för gatewayresursen som du skapade i Azure-portalen för gateway-installationen. 

   1. Välj gateway-resurs under **Anslutningsgateway.**

   1. Fortsätt att tillhandahålla information om anslutningen. För egenskapen **Logon Type** följer du steget baserat på om egenskapen är inställd på **Application Server** eller **Group:**

      * För **Application Server**krävs dessa egenskaper, som vanligtvis verkar valfria:

        ![Skapa ANSLUTNING TILL SAP-programserver](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * För **Grupp**krävs dessa egenskaper, som vanligtvis verkar valfria:

        ![Skapa ANSLUTNING TILL SAP-meddelandeserver](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Som standard används stark skrivning för att söka efter ogiltiga värden genom att utföra XML-validering mot schemat. Detta kan hjälpa dig att identifiera problem tidigare. Alternativet **Säker skrivning** är tillgängligt för bakåtkompatibilitet och kontrollerar bara stränglängden. Läs mer om [alternativet Säker typning](#safe-typing).

   1. När du är klar väljer du **Skapa**.

      Logic Apps konfigurerar och testar anslutningen för att säkerställa att anslutningen fungerar som den ska.

1. Ange de parametrar som krävs baserat på din SAP-systemkonfiguration.

   Du kan också ange en eller flera SAP-åtgärder. Den här listan med åtgärder anger de meddelanden som utlösaren tar emot från DIN SAP-server via datagatewayen. En tom lista anger att utlösaren tar emot alla meddelanden. Om listan innehåller fler än ett meddelande tar utlösaren bara emot de meddelanden som anges i listan. Alla andra meddelanden som skickas från SAP-servern avvisas av gatewayen.

   Du kan välja en SAP-åtgärd från filväljaren:

   ![Lägg till SAP-åtgärd i logikappen](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Du kan också manuellt ange en åtgärd:

   ![Ange SAP-åtgärd manuellt](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Här är ett exempel som visar hur åtgärden visas när du ställer in utlösaren för att ta emot mer än ett meddelande.

   ![Utlösa exempel som tar emot flera meddelanden](media/logic-apps-using-sap-connector/example-trigger.png)

   Mer information om SAP-åtgärden finns i [Meddelandescheman för IDOC-åtgärder](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Spara nu logikappen så att du kan börja ta emot meddelanden från ditt SAP-system. Välj **Spara**i designerverktygsfältet .

Logikappen är nu redo att ta emot meddelanden från ditt SAP-system.

> [!NOTE]
> SAP-utlösaren är inte en avsökningsutlösare utan är en webhook-baserad utlösare i stället. Utlösaren anropas från gatewayen endast när ett meddelande finns, så ingen avsökning är nödvändig.

### <a name="test-your-logic-app"></a>Testa logikappen

1. Om du vill utlösa logikappen skickar du ett meddelande från SAP-systemet.

1. På logikappmenyn väljer du **Översikt**. Granska **historiken Körs** för alla nya körningar för logikappen.

1. Öppna den senaste körningen, som visar meddelandet som skickas från SAP-systemet i avsnittet utlösarutdata.

## <a name="receive-idoc-packets-from-sap"></a>Ta emot IDOC-paket från SAP

Du kan ställa in SAP för att [skicka IDOCs i paket](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html), som är batchar eller grupper av ID-enheter. För att ta emot IDOC-paket behöver SAP-anslutningen, och särskilt utlösaren, inte extra konfiguration. Men för att bearbeta varje objekt i ett IDOC-paket efter att utlösaren tar emot paketet krävs ytterligare steg för att dela upp paketet i enskilda IDOC:er.

Här är ett exempel som visar hur du extraherar enskilda IDOCs från ett paket med hjälp av [ `xpath()` funktionen:](./workflow-definition-language-functions-reference.md#xpath)

1. Innan du börjar behöver du en logikapp med en SAP-utlösare. Om du inte redan har den här logikappen följer du föregående steg i det här avsnittet för att [konfigurera en logikapp med en SAP-utlösare](#receive-from-sap).

   Ett exempel:

   ![Lägg till SAP-utlösare i logikapp](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Hämta rotnamnområdet från XML IDOC som logikappen tar emot från SAP. Om du vill extrahera det här namnområdet från XML-dokumentet lägger du till `xpath()` ett steg som skapar en lokal strängvariabel och lagrar namnområdet med ett uttryck:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Hämta rotnamnområde från IDOC](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Om du vill extrahera en enskild IDOC lägger du till ett steg `xpath()` som skapar en matrisvariabel och lagrar IDOC-samlingen med ett annat uttryck:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Hämta matris med objekt](./media/logic-apps-using-sap-connector/get-array.png)

   Matrisvariabeln gör varje IDOC tillgängligt för logikappen att bearbetas individuellt genom att räkna upp samlingen. I det här exemplet överför logikappen varje IDOC till en SFTP-server med hjälp av en loop:

   ![Skicka IDOC till SFTP-server](./media/logic-apps-using-sap-connector/loop-batch.png)

   Varje IDOC måste innehålla rotnamnområdet, vilket är anledningen till `<Receive></Receive` att filinnehållet är insvept i ett element tillsammans med rotnamnområdet innan IDOC skickas till nedströmsappen eller SFTP-servern i det här fallet.

Du kan använda snabbstartsmallen för det här mönstret genom att välja den här mallen i Logic App Designer när du skapar en ny logikapp.

![Välj mall för batchlogikapp](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generera scheman för artefakter i SAP

I det här exemplet används en logikapp som du kan utlösa med en HTTP-begäran. SAP-åtgärden skickar en begäran till ett SAP-system för att generera scheman för angiven IDoc och BAPI. Scheman som returneras i svaret överförs till ett integrationskonto med hjälp av Azure Resource Manager-kopplingen.

### <a name="add-an-http-request-trigger"></a>Lägga till en HTTP-begärandeutlösare

1. Skapa en tom logikapp i Azure-portalen som öppnar Logic App Designer.

1. Ange som filter `http request` i sökrutan. Välj **När en HTTP-begäran tas emot i**listan **Utlösare** .

   ![Lägga till utlösare för HTTP-begäran](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Spara nu logikappen så att du kan skapa en slutpunkts-URL för logikappen.
Välj **Spara**i designerverktygsfältet .

   Slutpunkts-URL:en visas nu i utlösaren, till exempel:

   ![Generera URL för slutpunkt](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Lägga till en SAP-åtgärd för att generera scheman

1. Välj **Nytt steg**under utlösaren i Logic App Designer.

   ![Lägga till nytt steg i logikappen](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Ange som filter `sap` i sökrutan. Välj **Generera scheman**i listan **Åtgärder** .
  
   ![Lägg till åtgärden Generera scheman i logikappen](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Du kan också välja fliken **Företag** och välja SAP-åtgärden.

   ![Välj SAP-skicka åtgärd från fliken Företag](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Om anslutningen redan finns fortsätter du med nästa steg så att du kan konfigurera SAP-åtgärden. Om du uppmanas att ange anslutningsinformation anger du informationen så att du kan skapa en anslutning till den lokala SAP-servern nu.

   1. Ange ett namn för anslutningen.

   1. I avsnittet **Datagateway** under **Prenumeration**väljer du först Azure-prenumerationen för gatewayresursen som du skapade i Azure-portalen för gateway-installationen. 
   
   1. Välj gateway-resurs under **Anslutningsgateway.**

   1. Fortsätt att tillhandahålla information om anslutningen. För egenskapen **Logon Type** följer du steget baserat på om egenskapen är inställd på **Application Server** eller **Group:**
   
      * För **Application Server**krävs dessa egenskaper, som vanligtvis verkar valfria:

        ![Skapa ANSLUTNING TILL SAP-programserver](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * För **Grupp**krävs dessa egenskaper, som vanligtvis verkar valfria:

        ![Skapa ANSLUTNING TILL SAP-meddelandeserver](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Som standard används stark skrivning för att söka efter ogiltiga värden genom att utföra XML-validering mot schemat. Detta kan hjälpa dig att identifiera problem tidigare. Alternativet **Säker skrivning** är tillgängligt för bakåtkompatibilitet och kontrollerar bara stränglängden. Läs mer om [alternativet Säker typning](#safe-typing).

   1. När du är klar väljer du **Skapa**.

      Logic Apps konfigurerar och testar anslutningen för att säkerställa att anslutningen fungerar som den ska.

1. Ange sökvägen till den artefakt som du vill generera schemat för.

   Du kan välja SAP-åtgärden från filväljaren:

   ![Välj SAP-åtgärd](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Du kan också ange åtgärden manuellt:

   ![Ange SAP-åtgärd manuellt](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Om du vill generera scheman för mer än en artefakt anger du SAP-åtgärdsinformation för varje artefakt, till exempel:

   ![Välj Lägg till nytt objekt](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Visa två objekt](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Mer information om SAP-åtgärden finns i [Meddelandescheman för IDOC-åtgärder](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet .

### <a name="test-your-logic-app"></a>Testa logikappen

1. Välj **Kör** i designerverktygsfältet om du vill utlösa en körning för logikappen.

1. Öppna körningen och kontrollera utdata för åtgärden **Generera scheman.**

   Utdata visar de genererade schemana för den angivna listan med meddelanden.

### <a name="upload-schemas-to-an-integration-account"></a>Ladda upp scheman till ett integrationskonto

Du kan också hämta eller lagra genererade scheman i databaser, till exempel ett blob-, lagrings- eller integrationskonto. Integrationskonton ger en förstklassig upplevelse med andra XML-åtgärder, så det här exemplet visar hur du laddar upp scheman till ett integrationskonto för samma logikapp med hjälp av Azure Resource Manager-kopplingen.

1. Välj **Nytt steg**under utlösaren i Logic App Designer.

1. Ange som filter `Resource Manager` i sökrutan. Välj **Skapa eller uppdatera en resurs**.

   ![Välj åtgärden Azure Resource Manager](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Ange information om åtgärden, inklusive din Azure-prenumeration, Azure-resursgrupp och integrationskonto. Om du vill lägga till SAP-token i fälten klickar du i rutorna för dessa fält och väljer från listan med dynamiskt innehåll som visas.

   1. Öppna listan **Lägg till ny parameter** och välj fälten **Plats** och **Egenskaper.**

   1. Ange information om de nya fälten som visas i det här exemplet.

      ![Ange information för Azure Resource Manager-åtgärd](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   Sap **Generera scheman** åtgärd genererar scheman som en samling, så designern lägger automatiskt till en **för varje** slinga till åtgärden. Här är ett exempel som visar hur den här åtgärden visas:

   ![Azure Resource Manager-åtgärd med "för varje" loop](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > Scheman använder base64-kodat format. Om du vill överföra scheman till ett integrationskonto `base64ToString()` måste de avkodas med hjälp av funktionen. Här är ett exempel som visar `"properties"` koden för elementet:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet .

### <a name="test-your-logic-app"></a>Testa logikappen

1. Välj **Kör** i verktygsfältet Designer för att manuellt utlösa logikappen.

1. Efter en lyckad körning går du till integrationskontot och kontrollerar att de genererade schemana finns.

## <a name="enable-secure-network-communications"></a>Aktivera säker nätverkskommunikation

Innan du börjar, se till att du uppfyllde de tidigare listade [förutsättningarna:](#pre-reqs)

* Den lokala datagatewayen installeras på en dator som finns i samma nätverk som DITT SAP-system.
* För SSO körs gatewayen som en användare som är mappad till en SAP-användare.
* SNC-biblioteket som tillhandahåller ytterligare säkerhetsfunktioner installeras på samma dator som datagatewayen. Några exempel är [sapseculib,](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)Kerberos och NTLM.

   Om du vill aktivera SNC för dina begäranden till eller från SAP-systemet markerar du kryssrutan **Använd SNC** i SAP-anslutningen och anger följande egenskaper:

   ![Konfigurera SAP SNC i anslutning](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Egenskap | Beskrivning |
   |----------| ------------|
   | **SNC-biblioteksväg** | SNC-bibliotekets namn eller sökväg i förhållande till NCo-installationsplatsen eller den absoluta sökvägen. Exempel `sapsnc.dll` är `.\security\sapsnc.dll` `c:\security\sapsnc.dll`eller eller . |
   | **SNC SSO** | När du ansluter via SNC används SNC-identiteten vanligtvis för att autentisera anroparen. Ett annat alternativ är att åsidosätta så att användar- och lösenordsinformation kan användas för att autentisera den som ringer, men linjen är fortfarande krypterad. |
   | **SNC Mitt namn** | I de flesta fall kan den här egenskapen utelämnas. Den installerade SNC-lösningen vet vanligtvis sitt eget SNC-namn. Endast för lösningar som stöder flera identiteter kan du behöva ange vilken identitet som ska användas för det här målet eller servern. |
   | **SNC-partnernamn** | Namnet på backend-SNC. |
   | **SNC-skyddets kvalitet** | Kvaliteten på den tjänst som ska användas för SNC-kommunikation av just den här destinationen eller servern. Standardvärdet definieras av backend-systemet. Det maximala värdet definieras av säkerhetsprodukten som används för SNC. |
   |||

   > [!NOTE]
   > Ange inte miljövariabler SNC_LIB och SNC_LIB_64 på datorn där du har datagatewayen och SNC-biblioteket. Om den är inställd har de företräde framför SNC-biblioteksvärdet som skickas genom kopplingen.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Säker maskinskrivning

När du skapar DIN SAP-anslutning används som standard stark skrivning för att söka efter ogiltiga värden genom att utföra XML-validering mot schemat. Detta kan hjälpa dig att identifiera problem tidigare. Alternativet **Säker skrivning** är tillgängligt för bakåtkompatibilitet och kontrollerar bara stränglängden. Om du väljer **Säker typning**behandlas typen DATS-typ och TIMS i SAP som `xs:date` `xs:time`strängar `xmlns:xs="http://www.w3.org/2001/XMLSchema"`i stället för som sina XML-motsvarigheter och , där . Säker skrivning påverkar beteendet för alla schemagenerering, skicka meddelandet för både "skickat" nyttolast och "tagits emot" svar och utlösaren. 

När stark skrivning används **(Säker typning** är inte aktiverat) mappar schemat typerna DATS och TIMS till enklare XML-typer:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

När du skickar meddelanden med stark skrivning följer DATS- och TIMS-svaret det matchande XML-typformatet:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

När **Säker typning** är aktiverad mappar schemat typerna DATS och TIMS till XML-strängfält med endast längdbegränsningar, till exempel:

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

När meddelanden skickas med **säker skrivning** aktiverad ser DATS- och TIMS-svaret ut så här:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Avancerade scenarier

### <a name="confirm-transaction-explicitly"></a>Bekräfta transaktionen uttryckligen

När du skickar transaktioner till SAP från Logic Apps sker det här utbytet i två steg enligt beskrivningen i SAP-dokumentet [Transactional RFC Server Programs](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Som standard hanterar åtgärden **Skicka till SAP** både stegen för funktionsöverföringen och för transaktionsbekräftelsen i ett enda anrop. SAP-kontakten ger dig möjlighet att frikoppla dessa steg. Du kan skicka ett IDOC och i stället för att automatiskt bekräfta transaktionen kan du använda den explicita **bekräfta transaktions-ID-åtgärden.**

Den här funktionen för att frikoppla transaktions-ID-bekräftelsen är användbar när du inte vill duplicera transaktioner i SAP, till exempel i scenarier där fel kan inträffa på grund av orsaker som nätverksproblem. Genom att bekräfta transaktions-ID separat slutförs transaktionen endast en gång i ditt SAP-system.

Här är ett exempel som visar det här mönstret:

1. Skapa en tom logikapp och lägg till en HTTP-utlösare.

1. Lägg till åtgärden **Skicka IDOC från SAP-kopplingen.** Ange information för IDOC som du skickar till ditt SAP-system.

1. Om du uttryckligen vill bekräfta transaktions-ID:n i ett separat steg väljer du **Nej**i fältet **Bekräfta TID.** För det valfria **GUID-fältet för transaktions-ID** kan du antingen manuellt ange värdet eller låta kopplingen automatiskt generera och returnera det här GUID-värdet i svaret från send IDOC-åtgärden.

   ![Skicka IDOC-åtgärdsegenskaper](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Om du uttryckligen vill bekräfta transaktions-ID:n lägger du till åtgärden **Bekräfta transaktions-ID.** Klicka i rutan **Transaktions-ID** så att listan med dynamiskt innehåll visas. Välj det **transaktions-ID-värde** som returneras från åtgärden **Skicka IDOC** i listan.

   ![Bekräfta åtgärd för transaktions-ID](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   När det här steget körs markeras den aktuella transaktionen i båda ändar, på SAP-anslutningssidan och på SAP-systemsidan.

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

Här är de problem och begränsningar som för närvarande är kända för SAP-anslutningen:

* SAP-utlösaren stöder inte datagatewaykluster. I vissa redundansfall kan datagatewaynoden som kommunicerar med SAP-systemet skilja sig från den aktiva noden, vilket resulterar i oväntat beteende. För skicka scenarier stöds datagatewaykluster.

* SAP-anslutningen stöder för närvarande inte SAP-routersträngar. Den lokala datagatewayen måste finnas på samma LAN som det SAP-system som du vill ansluta.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här kopplingen, till exempel utlösare, åtgärder och begränsningar enligt beskrivningen i kopplingens Swagger-fil, finns på [kopplingens referenssida](https://docs.microsoft.com/connectors/sap/).

> [!NOTE]
> För logikappar i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandegränserna](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* [Anslut till lokala system](../logic-apps/logic-apps-gateway-connection.md) från Azure Logic Apps.
* Lär dig hur du validerar, transformerar och använder andra meddelandeåtgärder med [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Läs mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md).
