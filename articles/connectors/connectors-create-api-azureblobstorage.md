---
title: Ansluta till Azure Blob Storage
description: Skapa och hantera blobbar i Azure-lagringskonton med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: eb943bfe36be10d1e95d569a5c1bf48563e909c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247363"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Skapa och hantera blobbar i Azure Blob Storage med hjälp av Azure Logic Apps

Den här artikeln visar hur du kan komma åt och hantera filer som lagras som blobbar i ditt Azure-lagringskonto inifrån en logikapp med Azure Blob Storage-anslutningsappen. På så sätt kan du skapa logikappar som automatiserar uppgifter och arbetsflöden för att hantera dina filer. Du kan till exempel skapa logikappar som skapar, hämtar, uppdaterar och tar bort filer i ditt lagringskonto.

Anta att du har ett verktyg som uppdateras på en Azure-webbplats. som fungerar som utlösare för din logikapp. När den här händelsen inträffar kan du låta logikappen uppdatera en fil i din blob-lagringsbehållare, vilket är en åtgärd i logikappen.

Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Anslutningsspecifik teknisk information finns i [referensen för Azure Blob Storage connector](https://docs.microsoft.com/connectors/azureblobconnector/).

> [!IMPORTANT]
> Logikappar kan inte komma åt lagringskonton som finns bakom brandväggar om de båda finns i samma region. Som en lösning kan du ha dina logikappar och lagringskonto i olika regioner. Mer information om hur du aktiverar åtkomst från Azure Logic Apps till lagringskonton bakom brandväggar finns i avsnittet [Åtkomstlagringskonton bakom brandväggar](#storage-firewalls) senare i det här avsnittet.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Begränsningar

* Som standard kan Azure Blob Storage-åtgärder läsa eller skriva filer som är *50 MB eller mindre*. Om du vill hantera filer som är större än 50 MB men upp till 1024 MB stöder Azure Blob Storage-åtgärder [meddelandesegmentering](../logic-apps/logic-apps-handle-large-messages.md). Åtgärden **Hämta blob-innehåll** använder implicit segmentering.

* Azure Blob Storage-utlösare stöder inte segmentering. När du begär filinnehåll väljer utlösare bara filer som är 50 MB eller mindre. Så här hämtar du filer som är större än 50 MB:

  * Använd en Azure Blob Storage-utlösare som returnerar filegenskaper, till exempel **När en blob läggs till eller ändras (endast egenskaper)**.

  * Följ utlösaren med azure blob Storage **Get blob-innehållsåtgärd,** som läser hela filen och implicit använder segmentering.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En [Azure-lagringskonto och lagringsbehållare](../storage/blobs/storage-quickstart-blobs-portal.md)

* Logikappen där du behöver åtkomst till ditt Azure blob storage-konto. Om du vill starta logikappen med en Azure Blob Storage-utlösare behöver du en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Lägga till blob-lagringsutlösare

I Azure Logic Apps måste varje logikapp starta med en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en viss händelse inträffar eller när ett visst villkor uppfylls. Varje gång utlösaren utlöses skapar Logic Apps-motorn en logikappinstans och börjar köra appens arbetsflöde.

Det här exemplet visar hur du kan starta ett logikapparbetsflöde med **utlösaren När en blob läggs till eller ändras (endast egenskaper)** när en blobs egenskaper läggs till eller uppdateras i lagringsbehållaren.

1. Skapa en tom logikapp i [Azure-portalen](https://portal.azure.com) eller Visual Studio som öppnar Logic App Designer. I det här exemplet används Azure-portalen.

2. I sökrutan anger du "azure blob" som filter. Välj den utlösare du vill använda i listan utlösare.

   I det här exemplet används den här utlösaren: **När en blob läggs till eller ändras (endast egenskaper)**

   ![Välj Azure Blob Storage-utlösare](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Om du uppmanas att ange anslutningsinformation [skapar du blob-lagringsanslutningen nu](#create-connection). Eller, om anslutningen redan finns, ange nödvändig information för utlösaren.

   I det här exemplet markerar du den behållare och mapp som du vill övervaka.

   1. Markera mappikonen i rutan **Behållare.**

   2. I mapplistan väljer du den högra **>** vinkelparentesen ( ) och bläddrar sedan tills du hittar och väljer önskad mapp.

      ![Välj lagringsmapp som ska användas med utlösare](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Välj intervall och frekvens för hur ofta du vill att utlösaren ska kontrollera mappen för ändringar.

4. När du är klar väljer du **Spara**i designerverktygsfältet .

5. Fortsätt nu att lägga till en eller flera åtgärder i logikappen för de uppgifter som du vill utföra med utlösarresultaten.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Lägg till blob-lagringsåtgärd

I Azure Logic Apps är en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. I det här exemplet börjar logikappen med [upprepningsutlösaren](../connectors/connectors-native-recurrence.md).

1. Öppna logikappen i Logic App Designer i [Azure-portalen](https://portal.azure.com) eller Visual Studio. I det här exemplet används Azure-portalen.

2. Välj **Nytt steg**under utlösaren eller åtgärden i Logic App Designer.

   ![Lägga till nytt steg i logikapparbetsflödet](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du musen över anslutningspilen. Välj plustecknet**+**( ) som visas och välj **Lägg till en åtgärd**.

3. I sökrutan anger du "azure blob" som filter. Välj den åtgärd du vill använda i åtgärdslistan.

   I det här exemplet används den här åtgärden: **Hämta blob-innehåll**

   ![Välj åtgärden Azure Blob Storage](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Om du uppmanas att ange anslutningsinformation [skapar du din Azure Blob Storage-anslutning nu](#create-connection).
Eller, om anslutningen redan finns, ange nödvändig information för åtgärden.

   Markera i det här exemplet den fil du vill använda.

   1. Välj mappikonen i rutan **Blob.**
  
      ![Välj lagringsmapp som ska användas med åtgärd](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Sök efter och välj den fil du vill använda baserat på blobens **ID-nummer.** Du hittar det här **ID-numret** i blobens metadata som returneras av den tidigare beskrivna blob-lagringsutlösaren.

5. När du är klar väljer du **Spara**i designerverktygsfältet .
Om du vill testa logikappen kontrollerar du att den markerade mappen innehåller en blob.

Det här exemplet hämtar bara innehållet för en blob. Om du vill visa innehållet lägger du till en annan åtgärd som skapar en fil med blobben med hjälp av en annan koppling. Lägg till exempel till en OneDrive-åtgärd som skapar en fil baserat på blob-innehållet.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Ansluta till lagringskonto

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. När du uppmanas att skapa anslutningen anger du den här informationen:

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Anslutningsnamn** | Ja | <*anslutningsnamn*> | Namnet som ska skapas för anslutningen |
   | **Lagringskonto** | Ja | <*lagring-konto*> | Välj ditt lagringskonto i listan. |
   ||||

   Ett exempel:

   ![Skapa Azure Blob storage-kontoanslutning](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. När du är klar väljer du **Skapa**

1. När du har skapat anslutningen fortsätter du med [Lägg till blob-lagringsutlösare](#add-trigger) eller [Lägg till blob-lagringsåtgärd](#add-action).

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här kopplingen, till exempel utlösare, åtgärder och begränsningar enligt beskrivningen i kopplingens Swagger-fil, finns på [kopplingens referenssida](https://docs.microsoft.com/connectors/azureblobconnector/).

> [!NOTE]
> För logikappar i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandegränserna](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Komma åt lagringskonton bakom brandväggar

Du kan lägga till nätverkssäkerhet i ett Azure-lagringskonto genom att begränsa åtkomsten med en [brandvägg och brandväggsregler](../storage/common/storage-network-security.md). Den här inställningen skapar dock en utmaning för Azure och andra Microsoft-tjänster som behöver åtkomst till lagringskontot. Lokal kommunikation i datacentret abstraherar de interna IP-adresserna, så du kan inte ställa in brandväggsregler med IP-begränsningar. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../storage/common/storage-network-security.md).

Här är olika alternativ för åtkomst till lagringskonton bakom brandväggar från Azure Logic Apps med hjälp av Azure Blob Storage connector eller andra lösningar:

* Azure Storage Blob-anslutningsapp

  * [Komma åt lagringskonton i andra regioner](#access-other-regions)
  * [Komma åt lagringskonton via ett betrott virtuellt nätverk](#access-trusted-virtual-network)

* Andra lösningar

  * [Komma åt lagringskonton som en betrodd tjänst med hanterade identiteter](#access-trusted-service)
  * [Få tillgång till lagringskonton via Azure API Management](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>Problem med att komma åt lagringskonton i samma region

Logikappar kan inte komma åt lagringskonton direkt bakom brandväggar när de båda är i samma region. Som en lösning placerar du logikapparna i en region som skiljer sig från ditt lagringskonto och ger åtkomst till de [utgående IP-adresserna för hanterade anslutningsappar i din region](../logic-apps/logic-apps-limits-and-config.md#outbound).

> [!NOTE]
> Den här lösningen gäller inte för Azure Table Storage-anslutningsappen och Azure Queue Storage-anslutningen. Använd i stället den inbyggda HTTP-utlösaren och åtgärderna för att komma åt tabelllagringen eller kölagringen.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Komma åt lagringskonton via ett betrott virtuellt nätverk

Du kan placera lagringskontot i ett virtuellt Azure-nätverk som du hanterar och sedan lägga till det virtuella nätverket i listan över betrodda virtuella nätverk. Om du vill att logikappen ska komma åt lagringskontot via ett [betrott virtuellt nätverk](../virtual-network/virtual-networks-overview.md)måste du distribuera logikappen till en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)som kan ansluta till resurser i ett virtuellt nätverk. Du kan sedan lägga till undernäten i ISE i den betrodda listan. Azure Storage-anslutningsappar, till exempel Blob Storage-anslutningen, kan komma åt lagringsbehållaren direkt. Den här inställningen är samma upplevelse som att använda tjänstslutpunkterna från en ISE.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Komma åt lagringskonton som en betrodd tjänst med hanterade identiteter

Om du vill ge Microsoft betrodda tjänster åtkomst till ett lagringskonto via en brandvägg kan du skapa ett undantag för det lagringskontot för dessa tjänster. Den här lösningen tillåter Azure-tjänster som stöder [hanterade identiteter för autentisering för](../active-directory/managed-identities-azure-resources/overview.md) åtkomst till lagringskonton bakom brandväggar som betrodda tjänster. För att en logikapp i globala Azure med flera innehavare ska komma åt dessa lagringskonton aktiverar du först stöd för [hanterad identitet](../logic-apps/create-managed-service-identity.md) i logikappen. Sedan använder du HTTP-åtgärden eller utlösaren i logikappen och [anger att deras autentiseringstyp ska använda logikappens hanterade identitet](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). I det här scenariot kan du *bara* använda HTTP-åtgärden eller utlösaren.

Så här ställer du in undantaget och det hanterade identitetsstödet:

1. Välj Brandväggar och virtuella nätverk under **Inställningar**på ditt **lagringskonto**. Under **Tillåt åtkomst från**väljer du alternativet Markerade **nätverk** så att de relaterade inställningarna visas.

1. Under **Undantag**väljer du **Tillåt betrodda Microsoft-tjänster för att komma åt det här lagringskontot**och välj sedan **Spara**.

   ![Välj undantag som tillåter Microsofts betrodda tjänster](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. Aktivera stöd för den [hanterade identiteten i logikappens](../logic-apps/create-managed-service-identity.md)inställningar .

1. I logikappens arbetsflöde lägger du till och konfigurerar HTTP-åtgärden eller utlösaren för att komma åt lagringskontot eller entiteten.

   > [!IMPORTANT]
   > För utgående HTTP-åtgärd eller utlösa anrop till Azure `x-ms-version` Storage-konton kontrollerar du att begäranden innehåller egenskapen och API-versionen för den åtgärd som du vill köra på lagringskontot. Mer information finns [i Autentisera åtkomst med hanterad identitet](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) och [versionshantering för Azure Storage-tjänster](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).

1. På den åtgärden [väljer du den hanterade identitet](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) som ska användas för autentisering.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Få tillgång till lagringskonton via Azure API Management

Om du använder en dedikerad nivå för [API Management](../api-management/api-management-key-concepts.md)kan du fronta Lagrings-API:et med hjälp av API-hantering och tillåta den senares IP-adresser via brandväggen. Lägg i princip till det virtuella Azure-nätverket som används av API Management i lagringskontots brandväggsinställning. Du kan sedan använda antingen API Management-åtgärden eller HTTP-åtgärden för att anropa Azure Storage-API:erna. Men om du väljer det här alternativet måste du hantera autentiseringsprocessen själv. Mer information finns i [Enkel företagsintegrationsarkitektur](https://aka.ms/aisarch).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
