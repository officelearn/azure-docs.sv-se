---
title: Ansluta till SAP-system
description: Få åtkomst till och hantera SAP-resurser genom att automatisera arbets flöden med Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/27/2020
tags: connectors
ms.openlocfilehash: 36e22fd92d937271a3859d03367e2a7ef80ef3d2
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118666"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Ansluta till SAP-system från Azure Logic Apps

> [!IMPORTANT]
> De tidigare SAP Application Server-och SAP Message Server-kopplingarna är föråldrade den 29 februari 2020. Den aktuella SAP-anslutningen konsoliderar dessa tidigare SAP-anslutningar så att du inte behöver ändra Anslutnings typ, är helt kompatibel med tidigare anslutningar, innehåller många ytterligare funktioner och fortsätter att använda SAP .net Connector-biblioteket (SAP NCo).
>
> För logi Kap par som använder de äldre anslutningarna, [migrera till den senaste anslutningen](#migrate) innan utfasnings datumet. Annars kommer de här Logic Apps att uppleva körnings problem och kommer inte att kunna skicka meddelanden till ditt SAP-system.

Den här artikeln visar hur du kan komma åt dina lokala SAP-resurser inifrån en Logic-app med hjälp av SAP-anslutningen. Anslutningen fungerar med SAP: s klassiska versioner som R/3 och ECC-system lokalt. Anslutningen möjliggör även integrering med SAP: s nyare HANA-baserade SAP-system, till exempel S/4 HANA, oavsett om de finns lokalt eller i molnet. SAP Connector stöder meddelande-eller data integrering till och från SAP NetWeaver-baserade system via mellanliggande dokument (IDoc), Business Application Programming Interface (BAPI) eller Remote Function Call (RFC).

SAP-anslutaren använder [SAP .net Connector-biblioteket (NCo)](https://support.sap.com/en/product/connectors/msnet.html) och tillhandahåller följande åtgärder:

* **Skicka meddelande till SAP**: skicka iDOC över tRFC, ANROPAr BAPI-funktioner över RFC eller anropa RFC/TRFC i SAP-system.
* **När ett meddelande tas emot från SAP**: Receive iDOC över tRFC, anropar BAPI Functions över tRFC eller anropar RFC/TRFC i SAP-system.
* **Generera scheman**: generera scheman för SAP-artefakter för iDOC, BAPI eller RFC.

För dessa åtgärder stöder SAP-anslutaren grundläggande autentisering genom användar namn och lösen ord. Anslutningen har även stöd för [Säker nätverkskommunikation (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). SNC kan användas för SAP NetWeaver enkel inloggning (SSO) eller för ytterligare säkerhetsfunktioner som tillhandahålls av en extern säkerhets produkt.

SAP-anslutningen integreras med lokala SAP-system via den [lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md). I skicka-scenarier, till exempel när ett meddelande skickas från en Logic app till ett SAP-system, fungerar datagatewayen som en RFC-klient och vidarebefordrar de begär Anden som tas emot från Logic app till SAP. På samma sätt fungerar datagatewayen som en RFC-server som tar emot begär Anden från SAP i Receive-scenarier och vidarebefordrar dem till Logic-appen.

Den här artikeln visar hur du skapar exempel på Logic Apps som integreras med SAP och som täcker de tidigare beskrivna integrerings scenarierna. För logi Kap par som använder äldre SAP-kopplingar visar den här artikeln hur du migrerar dina Logi Kap par till den senaste SAP-anslutningen.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa med i den här artikeln behöver du följande objekt:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration ännu kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* Den Logic-app från vilken du vill komma åt SAP-systemet och en utlösare som startar din Logic app-arbetsflöde. Om du inte har använt Logic Apps, se [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md) och [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Din [SAP-Programserver](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) eller [SAP-meddelande Server](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* [Hämta och installera den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) på den lokala datorn. Skapa sedan [en Azure gateway-resurs](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) för denna gateway i Azure Portal. Gatewayen hjälper dig att komma åt lokala data och resurser på ett säkert sätt. 

  * Vi rekommenderar att du använder en version som stöds av den lokala datagatewayen. Microsoft släpper en ny version varje månad. För närvarande stöder Microsoft de senaste sex versionerna. Om det uppstår ett problem med din gateway kan du försöka att [Uppgradera till den senaste versionen](https://aka.ms/on-premises-data-gateway-installer), som kan innehålla uppdateringar för att lösa problemet.

* [Hämta, installera och konfigurera det senaste SAP-klient biblioteket](#sap-client-library-prerequisites) på samma dator som den lokala datagatewayen.

* Meddelande innehåll som du kan skicka till din SAP-server, till exempel en IDoc-fil, måste vara i XML-format och innehålla namn området för den SAP-åtgärd som du vill använda.

### <a name="sap-client-library-prerequisites"></a>Krav för SAP-klient bibliotek

* Som standard placerar SAP-installationsprogrammet Assembly-filerna i standardmappen för installation. Kopiera Assembly-filerna från standardmappen för installationen till installationsmappen för gateway.

    * Om din SAP-anslutning Miss lyckas med fel meddelandet "kontrol lera konto information och/eller behörigheter och försök igen", kan Assembly-filerna ha fel plats. Se till att du har kopierat Assembly-filerna till installationsmappen för gateway. Använd sedan [logg visaren för .net-sammansättnings bindning för fel sökning](https://docs.microsoft.com/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer), vilket gör att du kan kontrol lera att filerna finns på rätt plats.

    * Alternativt kan du välja alternativet **Global Assembly Cache Registration** när du installerar SAP-klient biblioteket.

* Se till att installera den senaste versionen, [SAP Connector (NCo 3,0) för Microsoft .net 3.0.22.0 som kompilerats med .NET Framework 4,0-Windows 64-bit (x64)](https://softwaredownloads.sap.com/file/0020000001000932019), av följande anledningar:

    * Tidigare SAP NCo-versioner kan bli död när fler än ett IDoc-meddelande skickas på samma gång. Det här tillståndet blockerar alla senare meddelanden som skickas till SAP-målet, vilket leder till timeout-fel i meddelandet.
    * Den lokala datagatewayen körs bara på 64-bitars system. Annars får du fel meddelandet "felaktig avbildning" eftersom värd tjänsten för data Gateway inte stöder 32-bitars sammansättningar.

    * Både data Gateway-värdservern och Microsoft SAP-adaptern använder .NET Framework 4,5. SAP-NCo för .NET Framework 4,0 fungerar med processer som använder .NET Runtime 4,0 till 4.7.1. SAP-NCo för .NET Framework 2,0 fungerar med processer som använder .NET Runtime 2,0 till 3,5, men fungerar inte längre med den senaste lokala datagatewayen.

### <a name="snc-prerequisites"></a>SNC-krav

Konfigurera de här inställningarna om du använder SNC (valfritt):

* Om du använder SNC med SSO ser du till att gatewayen körs som en användare som är mappad mot SAP-användaren. Om du vill ändra standard kontot väljer du **Ändra konto**och anger autentiseringsuppgifterna för användaren.

  ![Ändra Gateway-konto](./media/logic-apps-using-sap-connector/gateway-account.png)

* Om du aktiverar SNC med en extern säkerhets produkt kopierar du SNC-biblioteket eller-filerna på samma dator där gatewayen är installerad. Några exempel på SNC-produkter är [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), KERBEROS och NTLM.

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migrera till aktuell anslutning

1. Om du inte redan har gjort det kan du uppdatera din [lokala datagateway](https://www.microsoft.com/download/details.aspx?id=53127) så att du har den senaste versionen. Mer information finns i [installera en lokal datagateway för Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

1. Ta bort åtgärden **Skicka till SAP** i den Logic-app som använder den äldre SAP-anslutningen.

1. Lägg till åtgärden **Skicka meddelande till SAP** från den senaste SAP-anslutningen. Innan du kan använda den här åtgärden måste du återskapa anslutningen till SAP-systemet.

1. När du är klar sparar du din Logic app.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Skicka meddelande till SAP

I det här exemplet används en Logic-app som du kan utlösa med en HTTP-begäran. Logic-appen skickar en IDoc till en SAP-server och returnerar ett svar till den begär Ande som anropade Logic-appen.

### <a name="add-an-http-request-trigger"></a>Lägg till en HTTP-begäran-utlösare

I Azure Logic Apps måste varje Logi Kap par starta med en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts)som utlöses när en enskild händelse inträffar eller när ett särskilt villkor uppfylls. Varje gång utlösaren utlöses skapar Logic Apps-motorn en Logic App-instans och börjar köra appens arbets flöde.

> [!NOTE]
> När en Logi Kap par tar emot IDoc-paket från SAP, stöder inte [utlösaren](https://docs.microsoft.com/azure/connectors/connectors-native-reqres) "Plain" XML-scheman som genererats av SAP: s WE60 iDOC-dokumentation. Det finns dock stöd för XML-schemat "Plain" för scenarier som skickar meddelanden från Logic Apps *till* SAP. Du kan använda begär ande utlösare med SAP: s XML för IDoc, men inte med IDoc över RFC. Du kan också transformera XML-filen till det format som krävs. 

I det här exemplet skapar du en Logic-app med en slut punkt i Azure så att du kan skicka *http post-begäranden* till din Logic app. När din Logic app tar emot dessa HTTP-förfrågningar utlöses utlösaren och kör nästa steg i arbets flödet.

1. I [Azure Portal](https://portal.azure.com)skapar du en tom Logic-app som öppnar Logic Apps designer.

1. I rutan Sök anger `http request` du som filter. Välj **när en HTTP-begäran tas emot**från listan **utlösare** .

   ![Lägg till utlösare för HTTP-begäran](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Spara din Logic app så att du kan skapa en slut punkts-URL för din Logic app. I verktygsfältet designer väljer du **Spara**.

   Slut punkts-URL: en visas nu i utlösaren, till exempel:

   ![Generera URL för slut punkt](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Lägg till en SAP-åtgärd

I Azure Logic Apps är en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) ett steg i arbets flödet som följer en utlösare eller en annan åtgärd. Om du inte har lagt till en utlösare i din Logic app och vill följa det här exemplet [lägger du till utlösaren som beskrivs i det här avsnittet](#add-trigger).

1. I Logic App Designer går du till utlösaren och väljer **nytt steg**.

   ![Lägg till nytt steg i Logic app](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. I rutan Sök anger `sap` du som filter. I listan **åtgärder** väljer **du skicka meddelande till SAP**.
  
   ![Välj åtgärden "Skicka meddelande till SAP"](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Alternativt kan du välja fliken **Enterprise** och välja SAP-åtgärd.

   ![Välj åtgärden "Skicka meddelande till SAP" från fliken Enterprise](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Om anslutningen redan finns fortsätter du med nästa steg så att du kan konfigurera din SAP-åtgärd. Men om du uppmanas att ange anslutnings information anger du informationen så att du kan skapa en anslutning till din lokala SAP-server nu.

   1. Ange ett namn för anslutningen.

   1. I avsnittet **datagateway** , under **prenumeration**, väljer du först Azure-prenumerationen för den gateway-resurs som du skapade i Azure Portal för gateway-installationen. 
   
   1. Under **anslutnings-Gateway**väljer du din gateway-resurs.

   1. Fortsätt att ange information om anslutningen. För egenskapen **inloggnings typ** följer du stegen baserat på om egenskapen har angetts till **program Server** eller **grupp**:
   
      * För **program Server**krävs de här egenskaperna, som oftast visas som valfria,:

        ![Skapa SAP Application Server-anslutning](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * För **gruppen**är dessa egenskaper, som vanligt vis visas som valfria, obligatoriska:

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

      ![Välj egenskapen "brödtext" från utlösaren](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      När du är klar ser din SAP-åtgärd ut som i det här exemplet:

      ![Slutför SAP-åtgärden](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Spara din logikapp. I verktygsfältet designer väljer du **Spara**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Lägg till en HTTP-svars åtgärd

Lägg nu till en svars åtgärd i din Logic Apps-arbetsflöde och inkludera utdata från SAP-åtgärden. På så sätt returnerar din Logic-app resultatet från din SAP-server till den ursprungliga begär Ande.

1. Välj **nytt steg**under SAP-åtgärden i Logic App Designer.

1. I rutan Sök anger `response` du som filter. I listan **åtgärder** väljer du **svar**.

1. Klicka i rutan **brödtext** så att listan med dynamiskt innehåll visas. I listan, under **Skicka meddelande till SAP**, väljer du fältet **brödtext** .

   ![Slutför SAP-åtgärd](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Spara din logikapp.

### <a name="test-your-logic-app"></a>Testa din Logic app

1. Om din Logic app inte redan är aktive rad väljer du **Översikt**på din Logic app-meny. Välj **Aktivera**i verktygsfältet.

1. I verktygsfältet designer väljer du **Kör**. Det här steget startar din Logic app manuellt.

1. Utlös din Logic app genom att skicka en HTTP POST-begäran till URL: en i din HTTP Request-utlösare.
Inkludera ditt meddelande innehåll med din begäran. Du kan använda ett verktyg som [Postman](https://www.getpostman.com/apps)för att skicka begäran.

   I den här artikeln skickar begäran en IDoc-fil som måste vara i XML-format och innehålla namn området för den SAP-åtgärd som du använder, till exempel:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. När du har skickat din HTTP-begäran väntar du på svaret från din Logic app.

   > [!NOTE]
   > Din Logi Kap par kan ta lång tid om alla steg som krävs för svaret inte slutförs inom [tids gränsen för begäran](./logic-apps-limits-and-config.md). Om det här tillståndet inträffar kan begär Anden blockeras. För att hjälpa dig att diagnostisera problem kan du läsa mer om hur du kan [kontrol lera och övervaka dina Logic Apps](../logic-apps/monitor-logic-apps.md).

Nu har du skapat en logisk app som kan kommunicera med din SAP-server. Nu när du har konfigurerat en SAP-anslutning för din Logic app kan du utforska andra tillgängliga SAP-åtgärder, till exempel BAPI och RFC.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Ta emot meddelande från SAP

I det här exemplet används en Logic-app som utlöses när appen tar emot ett meddelande från ett SAP-system.

### <a name="add-an-sap-trigger"></a>Lägg till en SAP-utlösare

1. I Azure Portal skapar du en tom Logic-app som öppnar Logic Apps designer.

1. I rutan Sök anger `sap` du som filter. Välj **när ett meddelande tas emot från SAP**i listan **utlösare** .

   ![Lägg till SAP-utlösare](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Alternativt kan du välja fliken **Enterprise** och sedan välja utlösaren:

   ![Lägg till SAP-utlösare från fliken Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Om anslutningen redan finns fortsätter du med nästa steg så att du kan konfigurera din SAP-åtgärd. Men om du uppmanas att ange anslutnings information anger du informationen så att du kan skapa en anslutning till din lokala SAP-server nu.

   1. Ange ett namn för anslutningen.

   1. I avsnittet **datagateway** , under **prenumeration**, väljer du först Azure-prenumerationen för den gateway-resurs som du skapade i Azure Portal för gateway-installationen. 

   1. Under **anslutnings-Gateway**väljer du din gateway-resurs.

   1. Fortsätt att ange information om anslutningen. För egenskapen **inloggnings typ** följer du stegen baserat på om egenskapen har angetts till **program Server** eller **grupp**:

      * För **program Server**krävs de här egenskaperna, som oftast visas som valfria,:

        ![Skapa SAP Application Server-anslutning](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * För **gruppen**är dessa egenskaper, som vanligt vis visas som valfria, obligatoriska:

        ![Skapa anslutning till SAP Message Server](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Som standard används stark inmatning för att kontrol lera ogiltiga värden genom att utföra XML-verifiering mot schemat. Det här beteendet kan hjälpa dig att identifiera problem tidigare. Alternativet för **säker inmatning** är tillgängligt för bakåtkompatibilitet och kontrollerar sträng längden. Läs mer om [alternativet för säker inmatning](#safe-typing).

   1. När du är klar väljer du **skapa**.

      Logic Apps konfigurerar och testar anslutningen för att kontrol lera att anslutningen fungerar korrekt.

1. Ange de [parametrar som krävs](#parameters) baserat på din konfiguration av SAP-systemet.

   Du kan också ange en eller flera SAP-åtgärder. Den här listan med åtgärder anger de meddelanden som utlösaren tar emot från din SAP-server via datagatewayen. En tom lista anger att utlösaren tar emot alla meddelanden. Om listan innehåller fler än ett meddelande, tar utlösaren bara emot de meddelanden som anges i listan. Alla andra meddelanden som skickas från din SAP-server avvisas av gatewayen.

   Du kan välja en SAP-åtgärd från fil väljaren:

   ![Lägg till SAP-åtgärd i Logic app](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Du kan också ange en åtgärd manuellt:

   ![Ange SAP-åtgärd manuellt](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Här är ett exempel som visar hur åtgärden visas när du konfigurerar utlösaren för att ta emot mer än ett meddelande.

   ![Utlösnings exempel som tar emot flera meddelanden](media/logic-apps-using-sap-connector/example-trigger.png)

   Mer information om SAP-åtgärden finns i [meddelande scheman för iDOC-åtgärder](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Spara din Logic app så att du kan börja ta emot meddelanden från SAP-systemet. I verktygsfältet designer väljer du **Spara**.

Din Logi Kap par är nu redo att ta emot meddelanden från SAP-systemet.

> [!NOTE]
> SAP-utlösaren är ingen avsöknings utlösare utan är en webhook-baserad utlösare i stället. Utlösaren anropas från gatewayen endast när det finns ett meddelande, så ingen avsökning krävs.

<a name="parameters"></a>

#### <a name="parameters"></a>Parametrar

Tillsammans med enkla sträng-och siffer inmatningar accepterar SAP-kopplingen följande tabell parametrar ( `Type=ITAB` indata):

* Tabell riktnings parametrar, både indata och utdata, för äldre SAP-versioner.
* Ändra parametrar, som ersätter tabell riktnings parametrarna för nyare SAP-versioner.
* Parametrar för hierarkisk tabell

### <a name="test-your-logic-app"></a>Testa din Logic app

1. Skicka ett meddelande från SAP-systemet för att utlösa din Logic app.

1. På menyn Logic app väljer du **Översikt**. Granska **körnings historiken** för alla nya körningar för din Logic app.

1. Öppna den senaste körningen, som visar meddelandet som skickas från ditt SAP-system i avsnittet Utlös utmatningar.

## <a name="receive-idoc-packets-from-sap"></a>Ta emot IDOC-paket från SAP

Du kan konfigurera SAP för att [Skicka IDOCs i paket](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html), som är batchar eller grupper med IDOCs. För att ta emot IDOC-paket behöver SAP-anslutaren och särskilt utlösaren ingen extra konfiguration. Men för att bearbeta varje objekt i ett IDOC-paket när utlösaren tar emot paketet, krävs ytterligare steg för att dela paketet i enskilda IDOCs.

Här är ett exempel som visar hur du extraherar enskilda IDOCs från ett paket med hjälp av [ `xpath()` funktionen](./workflow-definition-language-functions-reference.md#xpath):

1. Innan du börjar måste du ha en Logic-app med en SAP-utlösare. Om du inte redan har den här Logic-appen följer du de föregående stegen i det här avsnittet för att [Konfigurera en Logic app med en SAP-utlösare](#receive-from-sap).

   Ett exempel:

   ![Lägg till SAP-utlösare i Logic app](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Hämta rot namn området från XML-IDOC som din Logic app tar emot från SAP. Om du vill extrahera det här namn området från XML-dokumentet lägger du till ett steg som skapar en lokal sträng variabel och lagrar namn området med hjälp av ett `xpath()` uttryck:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Hämta rot namn område från IDOC](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Om du vill extrahera en enskild IDOC lägger du till ett steg som skapar en mat ris variabel och lagrar IDOC-samlingen med hjälp av ett annat `xpath()` uttryck:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Hämta objekt mat ris](./media/logic-apps-using-sap-connector/get-array.png)

   Array-variabeln gör varje IDOC tillgänglig för din Logic app för att bearbeta en individuellt genom att räkna upp över samlingen. I det här exemplet överför Logic-appen varje IDOC till en SFTP-server med hjälp av en slinga:

   ![Skicka IDOC till SFTP-server](./media/logic-apps-using-sap-connector/loop-batch.png)

   Varje IDOC måste innehålla rot namn området, vilket är orsaken till att fil innehållet är omslutet i ett- `<Receive></Receive` element tillsammans med rot namn området innan du skickar iDOC till den underordnade appen eller SFTP-servern i det här fallet.

Du kan använda snabb starts mal len för det här mönstret genom att välja den här mallen i Logic App Designer när du skapar en ny Logic app.

![Välj app-mall för batch-logik](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generera scheman för artefakter i SAP

I det här exemplet används en Logic-app som du kan utlösa med en HTTP-begäran. SAP-åtgärden skickar en begäran till ett SAP-system för att generera scheman för angivet IDoc och BAPI. Scheman som returneras i svaret överförs till ett integrations konto med hjälp av Azure Resource Manager anslutningen.

### <a name="add-an-http-request-trigger"></a>Lägg till en HTTP-begäran-utlösare

1. I Azure Portal skapar du en tom Logic-app som öppnar Logic Apps designer.

1. I rutan Sök anger `http request` du som filter. Välj **när en HTTP-begäran tas emot**från listan **utlösare** .

   ![Lägg till utlösare för HTTP-begäran](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Spara din Logic app så att du kan skapa en slut punkts-URL för din Logic app.
I verktygsfältet designer väljer du **Spara**.

   Slut punkts-URL: en visas nu i utlösaren, till exempel:

   ![Generera URL för slut punkt](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Lägga till en SAP-åtgärd för att generera scheman

1. I Logic App Designer går du till utlösaren och väljer **nytt steg**.

   ![Lägg till nytt steg i Logic app](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. I rutan Sök anger `sap` du som filter. I listan **åtgärder** väljer du **Skapa scheman**.
  
   ![Lägg till åtgärden "Skapa scheman" i Logic app](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Alternativt kan du välja fliken **Enterprise** och välja SAP-åtgärd.

   ![Välj SAP skicka åtgärd från fliken Enterprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Om anslutningen redan finns fortsätter du med nästa steg så att du kan konfigurera din SAP-åtgärd. Men om du uppmanas att ange anslutnings information anger du informationen så att du kan skapa en anslutning till din lokala SAP-server nu.

   1. Ange ett namn för anslutningen.

   1. I avsnittet **datagateway** , under **prenumeration**, väljer du först Azure-prenumerationen för den gateway-resurs som du skapade i Azure Portal för gateway-installationen. 
   
   1. Under **anslutnings-Gateway**väljer du din gateway-resurs.

   1. Fortsätt att ange information om anslutningen. För egenskapen **inloggnings typ** följer du stegen baserat på om egenskapen har angetts till **program Server** eller **grupp**:
   
      * För **program Server**krävs de här egenskaperna, som oftast visas som valfria,:

        ![Skapa SAP Application Server-anslutning](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * För **gruppen**är dessa egenskaper, som vanligt vis visas som valfria, obligatoriska:

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

1. I rutan Sök anger `Resource Manager` du som filter. Välj **skapa eller uppdatera en resurs**.

   ![Välj Azure Resource Manager åtgärd](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Ange information om åtgärden, inklusive Azure-prenumeration, Azure-resurs grupp och integrations konto. Om du vill lägga till SAP-tokens i fälten klickar du i rutorna för dessa fält och väljer från listan med dynamiskt innehåll som visas.

   1. Öppna listan **Lägg till ny parameter** och välj fälten **plats** och **Egenskaper** .

   1. Ange information om de nya fälten som visas i det här exemplet.

      ![Ange information om Azure Resource Manager åtgärd](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   Åtgärden **skapa schema** i SAP genererar scheman som en samling, så att design verktyget automatiskt lägger till en **för varje** loop till åtgärden. Här är ett exempel som visar hur den här åtgärden visas:

   ![Azure Resource Manager åtgärd med "för varje"-loop](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > Schemana använder Base64-kodat format. Om du vill överföra scheman till ett integrations konto måste de avkodas med hjälp av `base64ToString()` funktionen. Här är ett exempel som visar koden för- `"properties"` elementet:
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

Innan du börjar ska du kontrol lera att du uppfyller de tidigare angivna [förutsättningarna](#pre-reqs):

* Den lokala datagatewayen är installerad på en dator som är i samma nätverk som ditt SAP-system.
* För SSO körs gatewayen som en användare som är mappad till en SAP-användare.
* SNC-biblioteket som tillhandahåller ytterligare säkerhetsfunktioner installeras på samma dator som data gatewayen. Några exempel är [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), KERBEROS och NTLM.

   Om du vill aktivera SNC för dina begär anden till eller från SAP-systemet markerar du kryss rutan **Använd SNC** i SAP-anslutningen och anger följande egenskaper:

   ![Konfigurera SAP-SNC i anslutning](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Egenskap | Beskrivning |
   |----------| ------------|
   | **Sökväg till SNC-bibliotek** | SNC-bibliotekets namn eller sökväg i förhållande till NCo-installations plats eller absolut sökväg. Exempel är `sapsnc.dll` eller `.\security\sapsnc.dll` eller `c:\security\sapsnc.dll` . |
   | **SNC SSO** | När du ansluter via SNC används SNC-identiteten vanligt vis för att autentisera anroparen. Ett annat alternativ är att åsidosätta så att information om användare och lösen ord kan användas för att autentisera anroparen, men linjen är fortfarande krypterad. |
   | **SNC mitt namn** | I de flesta fall kan den här egenskapen utelämnas. Den installerade SNC-lösningen känner vanligt vis till sitt eget SNC-namn. För lösningar som har stöd för flera identiteter kan du behöva ange vilken identitet som ska användas för det aktuella målet eller servern. |
   | **SNC partner namn** | Namnet på backend-SNC. |
   | **SNC skydds kvalitet** | Quality of service som ska användas för SNC-kommunikation för det specifika målet eller den här servern. Standardvärdet definieras av Server dels systemet. Det maximala värdet definieras av den säkerhets produkt som används för SNC. |
   |||

   > [!NOTE]
   > Ange inte miljövariabler SNC_LIB och SNC_LIB_64 på den dator där du har data gatewayen och SNC-biblioteket. Om det är inställt, prioriteras de av SNC-bibliotekets värde som skickas via anslutningen.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Säker inmatning

När du skapar din SAP-anslutning används som standard stark inmatning för att kontrol lera ogiltiga värden genom att utföra XML-verifiering mot schemat. Det här beteendet kan hjälpa dig att identifiera problem tidigare. Alternativet för **säker inmatning** är tillgängligt för bakåtkompatibilitet och kontrollerar sträng längden. Om du väljer **säker inmatning**behandlas dats-typen och Tims-typen i SAP som strängar i stället för motsvarande XML-motsvarigheter, `xs:date` och `xs:time` där `xmlns:xs="http://www.w3.org/2001/XMLSchema"` . Säker inmatning påverkar beteendet för all schema generering, att skicka meddelandet för både "har skickats"-nytto lasten och "mottaget"-svaret och utlösaren. 

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

## <a name="advanced-scenarios"></a>Avancerade scenarier

### <a name="confirm-transaction-explicitly"></a>Bekräfta transaktionen explicit

När du skickar transaktioner till SAP från Logic Apps sker detta Exchange i två steg enligt beskrivningen i SAP-dokumentet, [TRANSAKTIONELLA RFC Server-program](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Som standard hanterar åtgärden **Skicka till SAP** både stegen för funktions överföringen och för transaktions bekräftelsen i ett enda anrop. Med SAP Connector får du möjlighet att koppla från de här stegen. Du kan skicka en IDOC och i stället för att automatiskt bekräfta transaktionen, kan du använda åtgärden explicit **Bekräfta transaktions-ID** .

Den här funktionen för att ta del av transaktions-ID-bekräftelsen är användbar när du inte vill duplicera transaktioner i SAP, till exempel i scenarier där fel kan uppstå på grund av orsaker till nätverks problem. Genom att bekräfta transaktions-ID: t separat slutförs transaktionen bara en tid i SAP-systemet.

Här är ett exempel som visar det här mönstret:

1. Skapa en tom Logic-app och Lägg till en HTTP-utlösare.

1. Lägg till åtgärden **Skicka iDOC** från SAP-anslutaren. Ange information om IDOC som du skickar till ditt SAP-system.

1. För att explicit bekräfta transaktions-ID: t i ett separat steg, i fältet **Bekräfta tid** , väljer du **Nej**. För det valfria fältet **transaktions-ID GUID** kan du antingen manuellt ange värdet eller låta kopplingen automatiskt generera och returnera denna GUID i svaret från åtgärden skicka iDOC.

   ![Skicka åtgärds egenskaper för IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. För att explicit bekräfta transaktions-ID: t lägger du till åtgärden **Bekräfta transaktions-ID** . Klicka i rutan **transaktions-ID** så att listan med dynamiskt innehåll visas. Välj det **transaktions-ID-** värde som returneras från åtgärden **Skicka iDOC** i listan.

   ![Bekräfta transaktions-ID-åtgärd](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   När det här steget körs markeras den aktuella transaktionen som slutförd i båda ändar, på sidan SAP-koppling och på SAP system-sidan.

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

Här är de kända problemen och begränsningarna för SAP-anslutningen:

* SAP-utlösaren stöder inte data Gateway-kluster. I vissa fall kan datagateway-noden som kommunicerar med SAP-systemet skilja sig från den aktiva noden, vilket resulterar i ett oväntat beteende. För sändnings scenarier stöds data Gateway-kluster.

* SAP-anslutaren stöder för närvarande inte SAP-router-strängar. Den lokala datagatewayen måste finnas i samma lokala nätverk som det SAP-system som du vill ansluta till.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här anslutningen, till exempel utlösare, åtgärder och begränsningar som beskrivs av kopplingens Swagger-fil finns på [kopplingens referens sida](https://docs.microsoft.com/connectors/sap/).

> [!NOTE]
> För logi Kap par i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandets gränser](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* [Anslut till lokala system](../logic-apps/logic-apps-gateway-connection.md) från Azure Logic Apps.
* Lär dig hur du verifierar, transformerar och använder andra meddelande åtgärder med [Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md).
