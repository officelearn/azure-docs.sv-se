---
title: Ansluta till SAP-system
description: Få åtkomst till och hantera SAP-resurser genom att automatisera arbets flöden med Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/21/2020
tags: connectors
ms.openlocfilehash: a0f6af706a81db537b9ed66dc49996282c4dbbaa
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87833903"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Ansluta till SAP-system från Azure Logic Apps

> [!IMPORTANT]
> De tidigare SAP Application Server-och SAP Message Server-kopplingarna är föråldrade den 29 februari 2020. Den aktuella SAP-anslutningen konsoliderar dessa tidigare SAP-anslutningar så att du inte behöver ändra Anslutnings typ, är helt kompatibel med tidigare anslutningar, innehåller många ytterligare funktioner och fortsätter att använda SAP .net Connector-biblioteket (SAP NCo).
>
> För logi Kap par som använder de äldre anslutningarna, [migrera till den senaste anslutningen](#migrate) innan utfasnings datumet. Annars kommer de här Logic Apps att uppleva körnings problem och kommer inte att kunna skicka meddelanden till ditt SAP-system.

Den här artikeln visar hur du kan komma åt dina lokala SAP-resurser inifrån en Logic-app med hjälp av SAP-anslutningen. Anslutningen fungerar med SAP: s klassiska versioner som R/3 och ECC-system lokalt. Anslutningsappen möjliggör även integrering med SAP:s nyare HANA-baserade SAP-system som S/4 HANA, vare sig de finns lokalt eller i molnet. SAP-anslutningsappen har stöd för integrering av meddelanden eller data till och från SAP NetWeaver-baserade system via Intermediate Document (IDoc), Business Application Programming Interface (BAPI) eller Remote Function Call (RFC).

SAP-anslutaren använder [SAP .net Connector-biblioteket (NCo)](https://support.sap.com/en/product/connectors/msnet.html) och tillhandahåller följande åtgärder:

* **Skicka meddelande till SAP**: skicka iDOC över tRFC, ANROPAr BAPI-funktioner över RFC eller anropa RFC/TRFC i SAP-system.

* **När ett meddelande tas emot från SAP**: Receive iDOC över tRFC, anropar BAPI Functions över tRFC eller anropar RFC/TRFC i SAP-system.

* **Generera scheman**: generera scheman för SAP-artefakter för iDOC, BAPI eller RFC.

För dessa åtgärder stöder SAP-anslutaren grundläggande autentisering genom användar namn och lösen ord. Anslutningen har även stöd för [Säker nätverkskommunikation (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). SNC kan användas för SAP NetWeaver enkel inloggning (SSO) eller för ytterligare säkerhetsfunktioner som tillhandahålls av en extern säkerhets produkt.

Den här artikeln visar hur du skapar exempel på Logic Apps som integreras med SAP och som täcker de tidigare beskrivna integrerings scenarierna. För logi Kap par som använder äldre SAP-kopplingar visar den här artikeln hur du migrerar dina Logi Kap par till den senaste SAP-anslutningen.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa med i den här artikeln behöver du följande objekt:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration ännu kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* Den Logic-app från vilken du vill komma åt SAP-systemet och en utlösare som startar din Logic app-arbetsflöde. Om du inte har använt Logic Apps, se [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md) och [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Din [SAP-Programserver](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) eller [SAP-meddelande Server](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* Meddelande innehåll som du skickar till din SAP-server, till exempel en IDoc-fil, måste vara i XML-format och innehålla namn området för den SAP-åtgärd som du vill använda.

* Om du vill använda **när ett meddelande tas emot från SAP** -utlösaren, måste du också utföra dessa installations steg:
  
  > [!NOTE]
  > Den här utlösaren använder samma URI-plats för att både förnya och avbryta prenumerationen på en webhook-prenumeration. Vid förnyelse-åtgärden används HTTP- `PATCH` metoden, medan den avbrutna prenumerationen använder HTTP- `DELETE` metoden. Detta kan göra att en förnyelse åtgärd visas som en avställnings åtgärd i utlösarens historik, men åtgärden är fortfarande en förnyelse eftersom utlösaren använder `PATCH` som HTTP-metod `DELETE` .

  * Konfigurera dina säkerhets behörigheter för SAP Gateway med den här inställningen:

    `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

  * Konfigurera din säkerhets loggning för SAP Gateway, som hjälper dig att hitta Access Control List (ACL)-fel och inte är aktive rad som standard. Annars får du följande fel meddelande:

    `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    Mer information finns i hjälp avsnittet om SAP [Konfigurera Gateway-loggning](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm).

<a name="multi-tenant"></a>

### <a name="multi-tenant-azure-prerequisites"></a>Krav för Azure för flera innehavare

Dessa krav gäller när dina Logi Kap par körs i Azure med flera klienter och du vill använda den hanterade SAP-anslutningen, som inte körs internt i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Annars, om du använder en Premium-nivå ISE och vill använda den SAP-anslutning som körs internt i ISE, se [krav för integration service Environment (ISE)](#sap-ise).

Den hanterade (ej-ISE) SAP-kopplingen integreras med lokala SAP-system via den [lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md). När ett meddelande till exempel skickas från en Logic app till ett SAP-system fungerar datagatewayen som en RFC-klient och vidarebefordrar de begär Anden som tas emot från Logic app till SAP i scenarier för att skicka meddelanden. På samma sätt fungerar datagatewayen som en RFC-server som tar emot begär Anden från SAP och vidarebefordrar dem till Logic app i scenarier med mottagna meddelanden.

* [Hämta och installera den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) på den lokala datorn. Skapa sedan [en Azure gateway-resurs](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) för denna gateway i Azure Portal. Gatewayen hjälper dig att komma åt lokala data och resurser på ett säkert sätt.

  Vi rekommenderar att du använder en version som stöds av den lokala datagatewayen. Microsoft släpper en ny version varje månad. För närvarande stöder Microsoft de senaste sex versionerna. Om det uppstår ett problem med din gateway kan du försöka att [Uppgradera till den senaste versionen](https://aka.ms/on-premises-data-gateway-installer), som kan innehålla uppdateringar för att lösa problemet.

* [Hämta och installera det senaste SAP-klient biblioteket](#sap-client-library-prerequisites) på samma dator som den lokala datagatewayen.

<a name="sap-ise"></a>

### <a name="integration-service-environment-ise-prerequisites"></a>Krav för integrerings tjänst miljö (ISE)

Dessa krav gäller när dina Logi Kap par körs på en Premium-nivå (inte i [integrerings tjänst miljön](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)) och du vill använda den SAP-anslutning som körs internt i en ISE. En ISE ger åtkomst till resurser som skyddas av ett virtuellt Azure-nätverk och erbjuder andra ISE-ursprungliga anslutningar som låter Logic Apps direkt komma åt lokala resurser utan att använda lokal datagateway.

> [!NOTE]
> Även om SAP ISE-anslutaren är synlig i en Developer-nivå ISE kommer försök att installera anslutnings programmet att Miss lyckas.

1. Om du inte redan har ett Azure Storage konto och en BLOB-behållare, skapar du behållaren genom att antingen använda [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) eller [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

1. [Hämta och installera det senaste SAP-klient biblioteket](#sap-client-library-prerequisites) på den lokala datorn. Du bör ha följande Assembly-filer:

   * libicudecnumber.dll
   * rscp4n.dll
   * sapnco.dll
   * sapnco_utils.dll

1. Skapa en zip-fil som innehåller dessa sammansättningar och ladda upp det här paketet till BLOB-behållaren i Azure Storage.

1. I antingen Azure Portal eller Azure Storage Explorer bläddrar du till den behållare plats där du laddade upp zip-filen.

1. Kopiera URL: en för den platsen och se till att du inkluderar signaturen signatur för delad åtkomst (SAS).

   Annars kommer SAS-token inte att auktoriseras och distributionen för SAP ISE-anslutningen kommer att Miss förväntas.

1. Innan du kan använda SAP ISE-anslutaren måste du installera och distribuera anslutningen i din ISE.

   1. Leta upp och öppna din ISE i [Azure Portal](https://portal.azure.com).
   
   1. På menyn ISE väljer du **hanterade anslutningar**  >  **Lägg till**. I listan kopplingar letar du reda på och väljer **SAP**.
   
   1. I rutan för att **lägga till en ny hanterad koppling** i rutan **SAP-paket** klistrar du in URL: en för zip-filen som innehåller SAP-sammansättningarna. *Se till att du inkluderar SAS-token.*

   1. När du är färdig väljer du **Skapa**.

   Mer information finns i [Lägg till ISE-anslutningar](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment).

1. Om SAP-instansen och ISE finns i olika virtuella nätverk måste du också [peer-koppla dessa nätverk](../virtual-network/tutorial-connect-virtual-networks-portal.md) så att din ISES virtuella nätverk är anslutet till din SAP-instanss virtuella nätverk.

<a name="sap-client-library-prerequisites"></a>

### <a name="sap-client-library-prerequisites"></a>Krav för SAP-klient bibliotek

* Se till att du installerar den senaste versionen, [SAP Connector (NCo 3,0) för Microsoft .net 3.0.22.0 som kompilerats med .NET Framework 4,0-Windows 64-bit (x64)](https://softwaredownloads.sap.com/file/0020000001000932019). Tidigare versioner kan resultera i kompatibilitetsproblem. Mer information finns i [versioner av SAP-klient bibliotek](#sap-library-versions).

* Som standard placerar SAP-installationsprogrammet Assembly-filerna i standardmappen för installation. Du måste kopiera dessa Assembly-filer till en annan plats, baserat på ditt scenario enligt följande:

  * För logi Kap par som körs i en ISE följer du stegen som beskrivs i [integrerings tjänst miljöns krav](#sap-ise). För logi Kap par som körs i Azure med flera innehavare och använder den lokala datagatewayen kopierar du Assembly-filerna från standardmappen för installation till datagateway-installationsmappen. Om du stöter på problem med datagatewayen kan du läsa följande problem:

  * Du måste installera 64-bitars versionen för SAP-klient biblioteket eftersom data gatewayen bara körs på 64-bitars system. Annars får du fel meddelandet "felaktig avbildning" eftersom värd tjänsten för data Gateway inte stöder 32-bitars sammansättningar.

  * Om din SAP-anslutning Miss lyckas med fel meddelandet "kontrol lera konto information och/eller behörigheter och försök igen", kan Assembly-filerna ha fel plats. Se till att du har kopierat Assembly-filerna till installationsmappen för datagateway.

    Du kan felsöka genom att [använda logg visaren för .net-sammansättnings bindning](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer), vilket gör att du kan kontrol lera att Assembly-filerna finns på rätt plats. Alternativt kan du välja alternativet **Global Assembly Cache Registration** när du installerar SAP-klient biblioteket.

<a name="sap-library-versions"></a>

#### <a name="sap-client-library-versions"></a>Versioner av SAP-klient bibliotek

Tidigare SAP NCo-versioner kan bli död när fler än ett IDoc-meddelande skickas på samma gång. Det här tillståndet blockerar alla senare meddelanden som skickas till SAP-målet, vilket leder till timeout-fel i meddelandet.

Här följer relationerna mellan SAP-klient biblioteket, .NET Framework, .NET-körningen och gatewayen:

* Både Microsoft SAP-styrenheten och värd tjänsten för Gateway använder .NET Framework 4,5.

* SAP-NCo för .NET Framework 4,0 fungerar med processer som använder .NET Runtime 4,0 till 4.7.1.

* SAP-NCo för .NET Framework 2,0 fungerar med processer som använder .NET Runtime 2,0 till 3,5, men inte längre fungerar med den senaste gatewayen.

### <a name="secure-network-communications-prerequisites"></a>Krav för säker nätverks kommunikation

Om du använder den lokala datagatewayen med valfri säker nätverkskommunikation (SNC), som endast stöds i Azure med flera innehavare, måste du också konfigurera de här inställningarna:

* Om du använder SNC med enkel inloggning (SSO) ser du till att data gatewayen körs som en användare som är mappad mot SAP-användaren. Om du vill ändra standard kontot väljer du **Ändra konto**och anger autentiseringsuppgifterna för användaren.

  ![Ändra data Gateway-konto](./media/logic-apps-using-sap-connector/gateway-account.png)

* Om du aktiverar SNC med en extern säkerhets produkt kopierar du SNC-biblioteket eller-filerna på samma dator där datagatewayen är installerad. Några exempel på SNC-produkter är [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), KERBEROS och NTLM.

Mer information om hur du aktiverar SNC för data Gateway finns i [Aktivera säker nätverkskommunikation](#secure-network-communications).

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migrera till aktuell anslutning

Följ dessa steg om du vill migrera från en tidigare hanterad SAP-koppling (ej-ISE) till den aktuella hanterade SAP-anslutningen:

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
> När en Logi Kap par tar emot IDoc-paket från SAP, stöder inte [utlösaren](../connectors/connectors-native-reqres.md) "Plain" XML-scheman som genererats av SAP: s WE60 iDOC-dokumentation. Det finns dock stöd för XML-schemat "Plain" för scenarier som skickar meddelanden från Logic Apps *till* SAP. Du kan använda begär ande utlösare med SAP: s XML för IDoc, men inte med IDoc över RFC. Du kan också transformera XML-filen till det format som krävs. 

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

1. Om anslutningen redan finns fortsätter du med nästa steg så att du kan konfigurera din SAP-åtgärd. Men om du uppmanas att ange anslutnings information anger du informationen så att du kan skapa en anslutning till din lokala SAP-server.

   1. Ange ett namn för anslutningen.

   1. Följ dessa steg om du använder data gatewayen:
   
      1. I avsnittet **datagateway** , under **prenumeration**, väljer du först Azure-prenumerationen för den data gateway-resurs som du skapade i Azure Portal för din datagateway-installation.
   
      1. Under **anslutnings-Gateway**väljer du din data gateway-resurs i Azure.

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

      Mer information om IDoc-åtgärder finns i [meddelande scheman för iDOC-åtgärder](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Klicka i rutan **inmatat meddelande** så att listan med dynamiskt innehåll visas. Välj fältet **brödtext** under **när en http-begäran tas emot**från listan.

      Det här steget inkluderar bröd innehållet från din HTTP Request-utlösare och skickar utdata till SAP-servern.

      ![Välj egenskapen "brödtext" från utlösaren](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      När du är klar ser din SAP-åtgärd ut som i det här exemplet:

      ![Slutför SAP-åtgärden](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Lägg till en HTTP-svars åtgärd

Lägg nu till en svars åtgärd i din Logic Apps-arbetsflöde och inkludera utdata från SAP-åtgärden. På så sätt returnerar din Logic-app resultatet från din SAP-server till den ursprungliga begär Ande.

1. Välj **nytt steg**under SAP-åtgärden i Logic App Designer.

1. I rutan Sök anger `response` du som filter. I listan **åtgärder** väljer du **svar**.

1. Klicka i rutan **brödtext** så att listan med dynamiskt innehåll visas. I listan, under **Skicka meddelande till SAP**, väljer du fältet **brödtext** .

   ![Slutför SAP-åtgärd](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Spara logikappen.

#### <a name="add-rfc-request-response"></a>Lägg till RFC-begäran-svar

> [!NOTE]
> SAP-utlösaren tar emot IDocs över tRFC, som inte har en svars parameter genom design. 

Du måste skapa ett mönster för begäran och svar om du behöver ta emot svar genom att använda ett RPC (Remote Function Call) för att Logic Apps från SAP ABAP. Om du vill ta emot IDocs i din Logic app bör du först utföra en [http-begäran](../connectors/connectors-native-reqres.md#add-a-response-action) med status kod `200 OK` och inget innehåll. Det här rekommenderade steget Slutför SAP LUW Asynchronous Transfer över tRFC omedelbart, vilket gör att SAP CPIC-konversationen är tillgänglig igen. Du kan sedan lägga till ytterligare åtgärder i din Logic app för att bearbeta de mottagna IDoc utan att blockera ytterligare överföringar.

Om du vill implementera ett mönster för begäran och svar måste du först identifiera RFC-schemat med hjälp av [ `generate schema` kommandot](#generate-schemas-for-artifacts-in-sap). Det genererade schemat har två möjliga rotnoder: 

1. Noden begäran, som är det anrop som du får från SAP.
1. Noden svar, som är ditt svar tillbaka till SAP.

I följande exempel genereras ett mönster för begäran och svar från `STFC_CONNECTION` RFC-modulen. XML-begäran parsas för att extrahera ett Node-värde där SAP-begäranden `<ECHOTEXT>` . Svaret infogar den aktuella tidstämpeln som ett dynamiskt värde. Du får ett liknande svar när du skickar en `STFC_CONNECTION` RFC från en Logic app till SAP.

```http

<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>


```

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

   1. Följ dessa steg om du använder data gatewayen:

      1. I avsnittet **datagateway** , under **prenumeration**, väljer du först Azure-prenumerationen för den data gateway-resurs som du skapade i Azure Portal för din datagateway-installation.

      1. Under **anslutnings-Gateway**väljer du din data gateway-resurs i Azure.

   1. Fortsätt att ange information om anslutningen. För egenskapen **inloggnings typ** följer du stegen baserat på om egenskapen har angetts till **program Server** eller **grupp**:

      * För **program Server**krävs de här egenskaperna, som oftast visas som valfria,:

        ![Skapa SAP Application Server-anslutning](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * För **gruppen**är dessa egenskaper, som vanligt vis visas som valfria, obligatoriska:

        ![Skapa anslutning till SAP Message Server](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Som standard används stark inmatning för att kontrol lera ogiltiga värden genom att utföra XML-verifiering mot schemat. Det här beteendet kan hjälpa dig att identifiera problem tidigare. Alternativet för **säker inmatning** är tillgängligt för bakåtkompatibilitet och kontrollerar sträng längden. Läs mer om [alternativet för säker inmatning](#safe-typing).

   1. När du är klar väljer du **skapa**.

      Logic Apps konfigurerar och testar anslutningen för att kontrol lera att anslutningen fungerar korrekt.

1. Ange de [parametrar som krävs](#parameters) baserat på din konfiguration av SAP-systemet. 

   Du kan [filtrera meddelandena som du tar emot från SAP-servern genom att ange en lista över SAP-åtgärder](#filter-with-sap-actions).

   Du kan välja en SAP-åtgärd från fil väljaren:

   ![Lägg till SAP-åtgärd i Logic app](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Du kan också ange en åtgärd manuellt:

   ![Ange SAP-åtgärd manuellt](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Här är ett exempel som visar hur åtgärden visas när du konfigurerar utlösaren för att ta emot mer än ett meddelande.

   ![Utlösnings exempel som tar emot flera meddelanden](media/logic-apps-using-sap-connector/example-trigger.png)

   Mer information om SAP-åtgärden finns i [meddelande scheman för iDOC-åtgärder](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Spara din Logic app så att du kan börja ta emot meddelanden från SAP-systemet. I verktygsfältet designer väljer du **Spara**.

Din Logi Kap par är nu redo att ta emot meddelanden från SAP-systemet.

> [!NOTE]
> SAP-utlösaren är ingen avsöknings utlösare utan är en webhook-baserad utlösare i stället. Om du använder datagatewayen anropas utlösaren från datagatewayen endast när det finns ett meddelande, så ingen avsökning krävs.

<a name="parameters"></a>

#### <a name="parameters"></a>Parametrar

Tillsammans med enkla sträng-och siffer inmatningar accepterar SAP-kopplingen följande tabell parametrar ( `Type=ITAB` indata):

* Tabell riktnings parametrar, både indata och utdata, för äldre SAP-versioner.
* Ändra parametrar, som ersätter tabell riktnings parametrarna för nyare SAP-versioner.
* Parametrar för hierarkisk tabell

<a name="filter-with-sap-actions"></a>

#### <a name="filter-with-sap-actions"></a>Filtrera med SAP-åtgärder

Du kan välja att filtrera meddelanden som din Logic app tar emot från din SAP-server genom att tillhandahålla en lista, eller matris, med en eller flera SAP-åtgärder. Som standard är den här matrisen tom, vilket innebär att din Logic app tar emot alla meddelanden från din SAP-server utan att filtrera. 

När du ställer in mat ris filtret tar utlösaren bara emot meddelanden från de angivna SAP-åtgärds typerna och avvisar alla andra meddelanden från SAP-servern. Filtret påverkar dock inte om inmatningen av den mottagna nytto lasten är svag eller stark.

Alla SAP-åtgärds filtrering sker på SAP-kortets nivå för din lokala datagateway. Mer information finns i [Skicka test-idocs till Logic Apps från SAP](#send-idocs-from-sap).

Om du inte kan skicka IDoc-paket från SAP till din Logic Apps utlösare, se meddelandet transaktionell RFC (tRFC)-anrop för avvisning i dialog rutan SAP tRFC (T-Code SM58). I SAP-gränssnittet kan du få följande fel meddelanden, som klipps ut på grund av del Strängs gränserna i **text fältet status** .

* `The RequestContext on the IReplyChannel was closed without a reply being`: Oväntade fel inträffar när en catch-all-hanterare för kanalen avslutar kanalen på grund av ett fel och återkonstruerar kanalen för att bearbeta andra meddelanden.

  * [Lägg till en svars åtgärd](../connectors/connectors-native-reqres.md#add-a-response-action) som returnerar en status kod för att bekräfta att din Logic app tog emot iDOC `200 OK` . IDoc transporteras via tRFC, vilket inte tillåter en nytto last för svar.

  * Om du behöver avvisa IDoc i stället svarar du med HTTP-statuskod förutom `200 OK` så att SAP-kortet returnerar ett undantag tillbaka till SAP för din räkning. 

* `The segment or group definition E2EDK36001 was not found in the IDoc meta`: Förväntade fel inträffar med andra fel, till exempel att det inte gick att generera en IDoc XML-nyttolast eftersom dess segment inte släpps av SAP, så segment typen metadata som krävs för konvertering saknas. 

  * Kontakta ABAP-teknikern för ditt SAP-system om du vill att dessa segment ska lanseras av SAP.

<a name="find-extended-error-logs"></a>

## <a name="find-extended-error-logs"></a>Hitta utökade fel loggar

För fullständiga fel meddelanden kontrollerar du SAP-kortets utökade loggar. 

För lokala data Gateway-versioner från juni 2020 och senare kan du [Aktivera Gateway-loggar i appinställningar](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app).

För lokala data Gateway-versioner från april 2020 och tidigare inaktive ras loggar som standard. Följ dessa steg om du vill hämta utökade loggar:

1. I installationsmappen för din lokala datagateway öppnar du `Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config` filen. 

1. Ändra värdet från **falskt** till **Sant**för inställningen **SapExtendedTracing** .

1. Om du vill ha färre händelser ändrar du **SapTracingLevel** -värdet från **information** (standard) till **fel** eller **Varning**. Eller, för fler händelser, ändra **information** till **verbose**.

1. Spara konfigurationsfilen.

1. Starta om din data Gateway. Öppna den lokala datagatewayens installations program och gå till menyn **tjänst inställningar** . Under **starta om gatewayen väljer du** **starta om nu**.

1. Återskapa problemet.

1. Exportera dina gateway-loggar. I appen för data Gateway-installationsprogrammet går du till **diagnostik** -menyn. Under **Gateway-loggar**väljer du **Exportera loggar**. Dessa filer innehåller SAP-loggar sorterade efter datum. Beroende på logg storleken kan det finnas flera loggfiler för ett enda datum.

1. I konfigurations filen återställer du **SapExtendedTracing** -inställningen till **false**.

1. Starta om gateway-tjänsten.

### <a name="test-your-logic-app"></a>Testa din Logic app

1. Skicka ett meddelande från SAP-systemet för att utlösa din Logic app.

1. På menyn Logic app väljer du **Översikt**. Granska **körnings historiken** för alla nya körningar för din Logic app.

1. Öppna den senaste körningen, som visar meddelandet som skickas från ditt SAP-system i avsnittet Utlös utmatningar.

<a name="send-idocs-from-sap"></a>

### <a name="test-sending-idocs-from-sap"></a>Testa att skicka IDocs från SAP

Om du vill skicka IDocs från SAP till din Logic app behöver du följande minsta konfiguration:

> [!IMPORTANT]
> Använd bara de här stegen när du testar din SAP-konfiguration med din Logic app. Produktions miljöer kräver ytterligare konfiguration.

1. [Konfigurera ett RFC-mål i SAP](#create-rfc-destination)

1. [Skapa en ABAP-anslutning till ditt RFC-mål](#create-abap-connection)

1. [Skapa en mottagar port](#create-receiver-port)

1. [Skapa en avsändar port](#create-sender-port)

1. [Skapa en logisk system partner](#create-logical-system-partner)

1. [Skapa en partner profil](#create-partner-profiles)

1. [Testa att skicka meddelanden](#test-sending-messages)

#### <a name="create-rfc-destination"></a>Skapa en RFC-destination

1. Om du vill öppna **konfigurationen av inställningarna för RFC-anslutningar** går du till SAP-gränssnittet och använder **sm59** -transaktions kod (T-kod) med prefixet **/n** .

1. Välj **TCP/IP-anslutningar**  >  **skapa**.

1. Skapa ett nytt RFC-mål med följande inställningar:
    
    * Ange ett namn för ditt **RFC-mål**.
    
    * På fliken **tekniska inställningar** väljer du **registrerade Server program**för **aktiverings typ**. Ange ett värde för ditt **program-ID**. I SAP registreras din Logic Apps-utlösare genom att använda den här identifieraren.
    
    * På fliken **Unicode** , för **kommunikations typ med mål system**, väljer du **Unicode**.

1. Spara ändringarna.

1. Registrera ditt nya **program-ID** med Azure Logic Apps.

1. Om du vill testa anslutningen går du till SAP-gränssnittet och väljer **anslutnings test**under ditt nya **RFC-mål**.

#### <a name="create-abap-connection"></a>Skapa ABAP-anslutning

1. Om du vill öppna **konfigurationen av inställningarna för RFC-anslutningar** går du till SAP-gränssnittet och använder **sm59***-transaktions koden (T-Code) med kommandot **/n** .

1. Välj **ABAP-anslutningar**  >  **skapa**.

1. För **RFC-mål**anger du ID: t för [ditt test-SAP-system](#create-rfc-destination).

1. Spara ändringarna.

1. Om du vill testa anslutningen väljer du **anslutnings test**.

#### <a name="create-receiver-port"></a>Skapa mottagar port

1. Om du vill öppna **portarna i iDOC bearbetnings** inställningar, i SAP-gränssnittet, använder du **we21** -transaktions kod (T-kod) med prefixet **/n** .

1. Välj de **portar**som  >  **transaktions-RFC**  >  **skapa**.

1. I rutan inställningar som öppnas väljer du **eget port namn**. Ange ett **namn**för test porten. Spara ändringarna.

1. I inställningarna för den nya mottagar porten, för **RFC-mål**, anger du identifieraren för [ditt test-RFC-mål](#create-rfc-destination).

1. Spara ändringarna.

#### <a name="create-sender-port"></a>Skapa avsändar port

1.  Om du vill öppna **portarna i iDOC bearbetnings** inställningar, i SAP-gränssnittet, använder du **we21** -transaktions kod (T-kod) med prefixet **/n** .

1. Välj de **portar**som  >  **transaktions-RFC**  >  **skapa**.

1. I rutan inställningar som öppnas väljer du **eget port namn**. För test porten anger du ett **namn** som börjar med **SAP**. Alla avsändar port namn måste börja med bokstäverna **SAP**, till exempel **SAPTEST**. Spara ändringarna.

1. I inställningarna för den nya avsändar porten för RFC- **mål**anger du identifieraren för [din ABAP-anslutning](#create-abap-connection).

1. Spara ändringarna.

#### <a name="create-logical-system-partner"></a>Skapa logisk system partner

1. Öppna **vyn ändra visning av logiska system: översikts** inställningar i SAP-gränssnittet med **bd54** -transaktions kod (T-Code).

1. Godkänn varnings meddelandet som visas: varning **: tabellen är mellan klient**

1. Välj **nya poster**ovanför listan som visar dina befintliga logiska system.

1. För det nya logiska systemet anger du en **Log.System** -identifierare och en beskrivning av kort **namn** . Spara ändringarna.

1. När **meddelandet om Workbench** visas skapar du en ny begäran genom att ange en beskrivning eller om du redan har skapat en begäran, hoppa över det här steget.

1. När du har skapat Workbench-begäran länkar du den till begäran till tabellen uppdaterings förfrågan. Bekräfta att tabellen har uppdaterats genom att spara ändringarna.

#### <a name="create-partner-profiles"></a>Skapa partner profiler

För produktions miljöer måste du skapa två partner profiler. Den första profilen är för avsändaren, som är din organisation och SAP-system. Den andra profilen gäller för mottagaren, som är din Logic-app.

1. Om du vill öppna inställningarna för **partner profiler** , i SAP-gränssnittet, använder du **we20** -transaktions koden (T-Code) med kommandot **/n** .

1. Under **partner profiler**väljer du **partner typ LS**  >  **create**.

1. Skapa en ny partner profil med följande inställningar:

    * För **partner nr.** ange [ID för din logiska system partner](#create-logical-system-partner).

    * För **en del. Skriv**, ange **ls**.

    * För **agent**anger du identifieraren för det SAP-användarkonto som ska användas när du registrerar program identifierare för Azure Logic Apps eller andra icke-SAP-system.

1. Spara ändringarna. Om du inte har [skapat den logiska system partnern](#create-logical-system-partner)får du felet genom att **Ange ett giltigt partner nummer**.

1. I partner profilens inställningar, under **utgående parmtrs**, väljer du **Skapa utgående parameter**.

1. Skapa en ny utgående parameter med följande inställningar:

    * Ange din **meddelande typ**, till exempel **CREMAS**.

    * Ange [mottagarens Port-ID](#create-receiver-port).

    * Ange en IDoc storlek för **paketet. Storlek**. Eller om du vill [Skicka idocs en i taget från SAP](#receive-idoc-packets-from-sap)väljer du **passe iDOC omedelbart**.

1. Spara ändringarna.

#### <a name="test-sending-messages"></a>Testa att skicka meddelanden

1. Öppna **test verktyget för iDOC bearbetnings** inställningar i SAP-gränssnittet med **we19** -transaktions kod (T-kod) med prefixet **/n** .

1. Under **mall för test**väljer du **via meddelande typ**och anger din meddelande typ, till exempel **CREMAS**. Välj **Skapa**.

1. Bekräfta **vilken iDOC-typ?** meddelande genom att välja **Fortsätt**.

1. Välj noden **EDIDC** . Ange lämpliga värden för mottagaren och avsändar portarna. Välj **Fortsätt**.

1. Välj **standard utgående bearbetning**.

1. Om du vill starta utgående IDoc-bearbetning väljer du **Fortsätt**. När bearbetningen är klar visas **iDOC som skickas till SAP-systemet eller det externa program** meddelandet.

1.  Om du vill söka efter bearbetnings fel använder du **SM58** -transaktions kod (T-kod) med kommandot **/n** .

## <a name="receive-idoc-packets-from-sap"></a>Ta emot IDoc-paket från SAP

Du kan konfigurera SAP för att [Skicka idocs i paket](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html), som är batchar eller grupper med IDocs. För att ta emot IDoc-paket behöver SAP-anslutaren och särskilt utlösaren ingen extra konfiguration. Men för att bearbeta varje objekt i ett IDoc-paket när utlösaren tar emot paketet, krävs ytterligare steg för att dela paketet i enskilda IDocs.

Här är ett exempel som visar hur du extraherar enskilda IDocs från ett paket med hjälp av [ `xpath()` funktionen](./workflow-definition-language-functions-reference.md#xpath):

1. Innan du börjar måste du ha en Logic-app med en SAP-utlösare. Om du inte redan har den här Logic-appen följer du de föregående stegen i det här avsnittet för att [Konfigurera en Logic app med en SAP-utlösare](#receive-from-sap).

   Exempel:

   ![Lägg till SAP-utlösare i Logic app](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Hämta rot namn området från XML-IDoc som din Logic app tar emot från SAP. Om du vill extrahera det här namn området från XML-dokumentet lägger du till ett steg som skapar en lokal sträng variabel och lagrar namn området med hjälp av ett `xpath()` uttryck:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Hämta rot namn område från IDoc](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Om du vill extrahera en enskild IDoc lägger du till ett steg som skapar en mat ris variabel och lagrar IDoc-samlingen med hjälp av ett annat `xpath()` uttryck:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Hämta objekt mat ris](./media/logic-apps-using-sap-connector/get-array.png)

   Array-variabeln gör varje IDoc tillgänglig för din Logic app för att bearbeta en individuellt genom att räkna upp över samlingen. I det här exemplet överför Logic-appen varje IDoc till en SFTP-server med hjälp av en slinga:

   ![Skicka IDoc till SFTP-server](./media/logic-apps-using-sap-connector/loop-batch.png)

   Varje IDoc måste innehålla rot namn området, vilket är orsaken till att fil innehållet är omslutet i ett- `<Receive></Receive` element tillsammans med rot namn området innan du skickar iDOC till den underordnade appen eller SFTP-servern i det här fallet.

Du kan använda snabb starts mal len för det här mönstret genom att välja den här mallen i Logic App Designer när du skapar en ny Logic app.

![Välj app-mall för batch-logik](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generera scheman för artefakter i SAP

I det här exemplet används en Logic-app som du kan utlösa med en HTTP-begäran. För att generera scheman för den angivna IDoc och BAPI skickar SAP-åtgärden **generate schema** en begäran till ett SAP-system.

Denna SAP-åtgärd returnerar ett [XML-schema](#sample-xml-schemas), inte innehållet eller data i själva XML-dokumentet. Scheman som returneras i svaret överförs till ett integrations konto med hjälp av Azure Resource Manager anslutningen. Scheman innehåller följande delar:

* Begär ande meddelandets struktur. Använd den här informationen för att skapa en BAPI- `get` lista.
* Svars meddelandets struktur. Använd den här informationen för att parsa svaret. 

Om du vill skicka begär ande meddelandet använder du den allmänna SAP-åtgärden **Skicka meddelande till SAP**, eller målet för målets **BAPI** -åtgärd.

### <a name="sample-xml-schemas"></a>Exempel på XML-scheman

Om du lär dig hur du skapar ett XML-schema som ska användas för att skapa ett exempel dokument, se följande exempel. I de här exemplen visas hur du kan arbeta med många typer av nytto laster, inklusive:

* [RFC-begäranden](#xml-samples-for-rfc-requests)
* [BAPI-begäranden](#xml-samples-for-bapi-requests)
* [IDoc-begäranden](#xml-samples-for-idoc-requests)
* Data typer för enkla eller komplexa XML-scheman
* Tabell parametrar
* Valfria XML-beteenden

Du kan börja XML-schemat med en valfri XML-prolog. SAP Connector fungerar med eller utan XML-prolog.

```xml

<?xml version="1.0" encoding="utf-8">

```

#### <a name="xml-samples-for-rfc-requests"></a>XML-exempel för RFC-begäranden

Följande exempel är ett grundläggande RFC-anrop. RFC-namnet är `STFC_CONNECTION` . Den här begäran använder standard namn området `xmlns=` , men du kan tilldela och använda namn rymds Ali Aset, till exempel `xmmlns:exampleAlias=` . Namn områdets värde är namn området för alla RFC: er i SAP för Microsoft-tjänster. Det finns en enkel indataparameter i begäran `<REQUTEXT>` .

```xml

<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>

```

Följande exempel är ett RFC-anrop med en tabell parameter. Det här exempel anropet och dess grupp med test-RFC: er är tillgängliga som en del av alla SAP-system. Tabell parameterns namn är `TCPICDAT` . Tabell radens typ är `ABAPTEXT` och det här elementet upprepas för varje rad i tabellen. Det här exemplet innehåller en enskild rad som kallas `LINE` . Begär Anden med en tabell parameter kan innehålla valfritt antal fält, där talet är ett positivt heltal (*n*). 

```xml

<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <RESTART_QNAME>exampleQName</RESTART_QNAME>
    <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
    </TCPICDAT>
</STFC_WRITE_TO_TCPIC>

```

Följande exempel är ett RFC-anrop med en tabell parameter som har ett anonymt fält. Ett anonymt fält är när fältet inte har tilldelats något namn. Komplexa typer deklareras under en separat namnrymd där deklarationen anger en ny standard för den aktuella noden och alla dess underordnade element. Exemplet använder hex `x002F` -koden som ett escape-tecken för symbolen */* , eftersom den här symbolen är reserverad i SAP-fält namnet.

```xml

<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <IM_XML_TABLE>
    <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
      <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
    </RFC_XMLCNT>
  </IM_XML_TABLE>
</RFC_XML_TEST_1>

```

Följande exempel innehåller prefix för namn områden. Du kan deklarera alla prefix samtidigt, eller så kan du deklarera valfritt antal prefix som attribut för en nod. Namn områdes Ali Aset `ns0` för RFC används som rot och parametrar för Basic-typen.

> [!NOTE]
> komplexa typer deklareras under en annan namnrymd för RFC-typer med alias `ns3` i stället för det vanliga RFC-namnområdet med aliaset `ns0` .

```xml

<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
  <ns0:DELIMITER>0</ns0:DELIMITER>
  <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
  <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
  <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
  <ns0:FIELDS>
    <ns3:RFC_DB_FLD>
      <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
    </ns3:RFC_DB_FLD>
  </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>

```

#### <a name="xml-samples-for-bapi-requests"></a>XML-exempel för BAPI-begäranden

> [!TIP]
> Om du använder Logic Apps designer för att redigera din BAPI-begäran kan du använda följande Sök funktioner: 
> 
> * Välj ett objekt i designern för att se en nedrullningsbar meny med tillgängliga metoder.
> * Filtrera affärs objekt typer efter nyckelord med hjälp av den sökbara lista som tillhandahålls av BAPI API-anropet.

> [!NOTE]
> SAP gör affärs objekt tillgängliga för externa system genom att beskriva dem som svar på RFC `RPY_BOR_TREE_INIT` , som Logic Apps problem utan indatamängds filter. Logic Apps kontrollerar tabellen output `BOR_TREE` . `SHORT_TEXT`Fältet används för namn på affärs objekt. Affärs objekt som inte returneras av SAP i utdatatabellen är inte tillgängliga för Logic Apps.
> Om du använder anpassade affärs objekt måste du se till att publicera och släppa dessa affärs objekt i SAP. Annars listar inte SAP dina anpassade affärs objekt i utdatatabellen `BOR_TREE` . Du kan inte komma åt dina anpassade affärs objekt i Logic Apps förrän du exponerar affärs objekt från SAP. 

I följande exempel hämtas en lista över banker med hjälp av BAPI-metoden `GETLIST` . Det här exemplet innehåller affärs objekt för en bank `BUS1011` . 

```xml

<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_CTRY>US</BANK_CTRY>
  <MAX_ROWS>10</MAX_ROWS>
</GETLIST>

```

I följande exempel skapas ett bank objekt med hjälp av- `CREATE` metoden. I det här exemplet används samma affärs objekt som i föregående exempel `BUS1011` . När du använder `CREATE` metoden för att skapa en bank måste du se till att spara ändringarna eftersom den här metoden inte är tilldelad som standard.

> [!TIP]
> Se till att XML-dokumentet följer alla verifierings regler som kon figurer ATS i SAP-systemet. I det här exempel dokumentet måste exempelvis bank nyckeln ( `<BANK_KEY>` ) vara ett bank organisations nummer, även kallat ABA-nummer, i USA.

```xml

<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>

```

Följande exempel hämtar information om en bank som använder bankens Routing Number, värdet för `<BANK_KEY>` . 

```xml

<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>

```

#### <a name="xml-samples-for-idoc-requests"></a>XML-exempel för IDoc-begäranden

Om du vill generera ett oformaterat SAP IDoc XML-schema använder du **SAP-inloggnings** programmet och T-koden `WE-60` . Få åtkomst till SAP-dokumentationen via det grafiska användar gränssnittet och generera XML-scheman i XSD-format för dina IDoc-typer och-tillägg. En förklaring av allmänna SAP-format och nytto laster och deras inbyggda dialog rutor finns i [SAP-dokumentationen](https://help.sap.com/viewer/index).

I det här exemplet deklaras rotnoden och namn områdena. URI: n i exempel koden, `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send` deklarerar följande konfiguration:

* `/IDoc`är rot noteringen för alla IDocs
* `/3`är post typen version för gemensamma segment definitioner
* `/ORDERS05`är typen av IDoc
* `//`är ett tomt segment eftersom det inte finns något IDoc-tillägg
* `/700`är SAP-versionen
* `/Send`är åtgärden för att skicka informationen till SAP

```xml

<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
  <ns0:idocData>

```

Du kan upprepa `idocData` noden för att skicka en batch med idocs i ett enda anrop. I exemplet nedan finns det en kontroll post, `EDI_DC40` och flera data poster.

```xml

<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
<...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
<...>
  </ns0:idocData>

```

Följande exempel är ett exempel på en IDoc kontroll post, som använder prefixet `EDI_DC` . Du måste uppdatera värdena för att matcha din SAP-installations-och IDoc-typ. IDoc-klientens kod kan till exempel inte vara `800` . Kontakta ditt SAP-team för att se till att du använder rätt värden för din SAP-installation.

```xml

<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>

```

Följande exempel är en exempel data post med enkla segment. I det här exemplet används formatet SAP-datum. Starkt skrivna dokument kan använda interna XML-datum format, till exempel `2020-12-31 23:59:59` .

```xml

<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>

```

Följande exempel är en data post med grupperade segment. Posten innehåller en överordnad nod, `E2EDKT1002GRP` och flera underordnade noder, inklusive `E2EDKT1002` och `E2EDKT2001` . 

```xml

<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <NS2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>

```

Den rekommenderade metoden är att skapa en IDoc-identifierare för användning med tRFC. Du kan ställa in transaktions-ID: `tid` t med hjälp av [åtgärden skicka iDOC](/connectors/sap/#send-idoc) i SAP Connector-API: et.

Följande exempel är en alternativ metod för att ange transaktions-ID: t eller `tid` . I det här exemplet stängs den sista noden för data post segmentet och IDoc-datanoden. Sedan används GUID, `guid` som tRFC-ID för att identifiera dubbletter. 

```xml

    </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>

```

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

   1. I avsnittet **datagateway** , under **prenumeration**, väljer du först Azure-prenumerationen för den data gateway-resurs som du skapade i Azure Portal för din datagateway-installation. 
   
   1. Under **anslutnings-Gateway**väljer du din data gateway-resurs i Azure.

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

   Mer information om SAP-åtgärden finns i [meddelande scheman för iDOC-åtgärder](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

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

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

### <a name="test-your-logic-app"></a>Testa din Logic app

1. I verktygsfältet designer väljer du **Kör** för att utlösa din Logic app manuellt.

1. Efter en lyckad körning går du till integrations kontot och kontrollerar att de genererade schemana finns.

<a name="secure-network-communications"></a>

## <a name="enable-secure-network-communications"></a>Aktivera säker nätverkskommunikation

Innan du börjar ska du kontrol lera att du har uppfyllt de tidigare angivna [kraven](#pre-reqs), som endast tillämpas när du använder datagatewayen och dina Logi Kap par körs i Azure med flera innehavare:

* Kontrol lera att den lokala datagatewayen är installerad på en dator som är i samma nätverk som ditt SAP-system.

* För enkel inloggning (SSO) körs data gatewayen som en användare som är mappad till en SAP-användare.

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

### <a name="change-language-headers"></a>Ändra språk rubriker

När du ansluter till SAP från Logic Apps är standard språket för anslutningen engelska. Du kan ställa in språket för din anslutning med hjälp av [standard-HTTP `Accept-Language` -huvud](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) med dina inkommande begär Anden.

> [!TIP]
> De flesta webbläsare lägger till en `Accept-Language` rubrik baserat på användarens inställningar. Webbläsaren använder den här rubriken när du skapar en ny SAP-anslutning i Logic Apps designer. Om du inte vill skapa SAP-anslutningar i webbläsarens språk, uppdaterar du webbläsarens inställningar så att de använder det språk du föredrar, eller så skapar du en SAP-anslutning med Azure Resource Manager i stället för Logic Apps designer. 

Du kan till exempel skicka en begäran med `Accept-Language` sidhuvudet till din Logic-app genom att använda **http-begäran** utlösare. Alla åtgärder i din Logic app får rubriken. Sedan använder SAP de angivna språken i system meddelanden, t. ex. BAPI-felmeddelanden.

SAP-anslutnings parametrarna för en Logic app har ingen språk egenskap. Så om du använder `Accept-Language` rubriken kan du få följande fel meddelande: **kontrol lera konto information och/eller behörigheter och försök igen.** I det här fallet kontrollerar du SAP-komponentens fel loggar i stället. Felet inträffar faktiskt i SAP-komponenten som använder rubriken, så du kan få ett av följande fel meddelanden:

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`
* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>Bekräfta transaktionen explicit

När du skickar transaktioner till SAP från Logic Apps sker detta Exchange i två steg enligt beskrivningen i SAP-dokumentet, [TRANSAKTIONELLA RFC Server-program](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Som standard hanterar åtgärden **Skicka till SAP** både stegen för funktions överföringen och för transaktions bekräftelsen i ett enda anrop. Med SAP Connector får du möjlighet att koppla från de här stegen. Du kan skicka en IDoc och i stället för att automatiskt bekräfta transaktionen, kan du använda åtgärden explicit **Bekräfta transaktions-ID** .

Den här funktionen för att ta del av transaktions-ID-bekräftelsen är användbar när du inte vill duplicera transaktioner i SAP, till exempel i scenarier där fel kan uppstå på grund av orsaker till nätverks problem. Genom att bekräfta transaktions-ID: t separat slutförs transaktionen bara en tid i SAP-systemet.

Här är ett exempel som visar det här mönstret:

1. Skapa en tom Logic-app och Lägg till en HTTP-utlösare.

1. Lägg till åtgärden **Skicka iDOC** från SAP-anslutaren. Ange information om IDoc som du skickar till ditt SAP-system.

1. För att explicit bekräfta transaktions-ID: t i ett separat steg, i fältet **Bekräfta tid** , väljer du **Nej**. För det valfria fältet **transaktions-ID GUID** kan du antingen manuellt ange värdet eller låta kopplingen automatiskt generera och returnera denna GUID i svaret från åtgärden skicka iDOC.

   ![Skicka åtgärds egenskaper för IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. För att explicit bekräfta transaktions-ID: t, Lägg till åtgärden **Bekräfta transaktions-ID** och se till att [undvika att skicka dubbletter av idocs till SAP](#avoid-sending-duplicate-idocs). Klicka i rutan **transaktions-ID** så att listan med dynamiskt innehåll visas. Välj det **transaktions-ID-** värde som returneras från åtgärden **Skicka iDOC** i listan.

   ![Bekräfta transaktions-ID-åtgärd](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   När det här steget körs markeras den aktuella transaktionen som slutförd i båda ändar, på sidan SAP-koppling och på SAP system-sidan.

#### <a name="avoid-sending-duplicate-idocs"></a>Undvik att skicka dubbletter av IDocs

Om det uppstår ett problem med att duplicera IDocs skickas till SAP från din Logic app, följer du de här stegen för att skapa en sträng variabel som fungerar som IDoc-transaktions-ID. Genom att skapa denna transaktions identifierare kan du förhindra dubbla nätverks sändningar när det finns problem, till exempel tillfälliga avbrott, nätverks problem eller förlorade bekräftelser.

> [!NOTE]
> SAP-system glömmer ett transaktions-ID efter en angiven tid eller 24 timmar som standard. Därför Miss lyckas SAP aldrig att bekräfta en transaktions-ID om ID eller GUID är okänt.
> Om det inte går att bekräfta ett transaktions-ID indikerar det här felet att kommunikation med SAP-systemet misslyckades innan SAP kunde bekräfta bekräftelsen.

1. I Logic Apps designer lägger du till den åtgärd som **initierar variabeln** i din Logic app. 
1. Konfigurera följande inställningar i redigerings programmet för **variabeln åtgärd initiera**. Spara sedan ändringarna.
    1. I **namn**anger du ett namn för din variabel. Till exempel `IDOCtransferID`.
    2. I **typ**väljer du **sträng** som variabel typ.
    3. För **värde**väljer du text rutan **Ange start värde** för att öppna menyn för dynamiskt innehåll. Välj fliken **uttryck** . I listan med funktioner anger du funktionen `guid()` . Välj **OK** för att spara ändringarna. Fältet **värde** är nu inställt på `guid()` funktionen, vilket genererar ett GUID.
1. Efter åtgärden **initiera variabel** lägger du till åtgärden **Skicka iDOC**.
1. Konfigurera följande inställningar i redigerings programmet för åtgärden **Skicka iDOC**. Spara sedan ändringarna.
    1. För **iDOC-typ** väljer du meddelande typ och anger ditt meddelande för **iDOC meddelande**.
    1. För **SAP-version**väljer du din SAP-konfigurations värden.
    1. För **post typer version**väljer du din SAP-konfigurations värden.
    1. För **Bekräfta tid**väljer du **Nej**.
    1. Välj **Lägg till ny parameter lista**  >  **transaktions-ID GUID**. Välj text rutan för att öppna menyn med dynamiskt innehåll. Under fliken **variabler** väljer du namnet på variabeln som du skapade. Till exempel `IDOCtransferID`.
1. I namn listen för åtgärden **Skicka iDOC**väljer du **...**  >  **Inställningar**. För **principen för återförsök**väljer du **ingen**  >  **görs**.
1. När åtgärden har **skickat iDOC**lägger du till åtgärden **Bekräfta transaktions-ID**.
1. Konfigurera följande inställningar i redigerings programmet för åtgärd **Bekräfta transaktions-ID**. Spara sedan ändringarna.
    1. För **transaktions-ID**anger du namnet på variabeln igen. Till exempel `IDOCtransferID`.

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

Här följer de kända problemen och begränsningarna för den hanterade SAP-anslutningen (ej-ISE):

* SAP-utlösaren stöder inte data Gateway-kluster. I vissa fall kan datagateway-noden som kommunicerar med SAP-systemet skilja sig från den aktiva noden, vilket resulterar i ett oväntat beteende. För sändnings scenarier stöds data Gateway-kluster.

* SAP-anslutaren stöder för närvarande inte SAP-router-strängar. Den lokala datagatewayen måste finnas i samma lokala nätverk som det SAP-system som du vill ansluta till.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här anslutningen, till exempel utlösare, åtgärder och begränsningar som beskrivs av kopplingens Swagger-fil finns på [kopplingens referens sida](/connectors/sap/).

> [!NOTE]
> För logi Kap par i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandets gränser](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* [Anslut till lokala system](../logic-apps/logic-apps-gateway-connection.md) från Azure Logic Apps.
* Lär dig hur du verifierar, transformerar och använder andra meddelande åtgärder med [Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md).
