---
title: Ansluta till Azure Blob Storage – Azure Logic Apps
description: Skapa och hantera blobbar i Azure Storage med Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 06/20/2019
tags: connectors
ms.openlocfilehash: ce59c238e50a1be6879b07e959b236f6181a8ce4
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703261"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Skapa och hantera blobbar i Azure Blob Storage med Azure Logic Apps

Den här artikeln visar hur du kan komma åt och hantera filer som lagras som blobbar i ditt Azure Storage-konto inifrån en Logic-app med Azure Blob Storage-anslutningen. På så sätt kan du skapa Logi Kap par som automatiserar uppgifter och arbets flöden för att hantera dina filer. Du kan till exempel skapa Logi Kap par som skapar, hämtar, uppdaterar och tar bort filer i ditt lagrings konto.

Anta att du har ett verktyg som uppdateras på en Azure-webbplats. Detta fungerar som utlösare för din Logic app. När den här händelsen inträffar kan du låta din Logic app uppdatera en fil i din Blob Storage-behållare, som är en åtgärd i din Logic app.

> [!NOTE]
>
> Logic Apps kan inte komma åt Azure Storage-konton som har [brand Väggs regler](../storage/common/storage-network-security.md) och finns i samma region. Logic Apps kan dock komma åt Azure Storage-konton som finns i en annan region eftersom en offentlig IP-adress används för att kommunicera mellan regioner. Se bara till att du tillåter [utgående IP-adresser för hanterade anslutningar i din region](../logic-apps/logic-apps-limits-and-config.md#outbound). Eller så kan du använda antingen fler avancerade alternativ här:
>
> * Skapa en [integrerings tjänst miljö](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)som kan ansluta till resurser i ett virtuellt Azure-nätverk.
>
> * Om du använder en dedikerad nivå för API Management kan du följa lagrings-API: et genom att använda API Management och tillåta de senares IP-adresser genom brand väggen. I princip lägger du till det virtuella Azure-nätverket som används av API Management i lagrings kontots brand Väggs inställning. Du kan sedan använda antingen åtgärden API Management eller HTTP-åtgärden för att anropa API: erna för Azure Storage. Men om du väljer det här alternativet måste du hantera autentiseringsprocessen själv. Mer information finns i [enkel arkitektur för företags integrering](https://aka.ms/aisarch).

Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabb start: Skapa din första Logic-](../logic-apps/quickstart-create-first-logic-app-workflow.md)app. Information om anslutningsspecifika teknisk information finns i referens för [Azure Blob Storage Connector](/connectors/azureblobconnector/).

## <a name="limits"></a>Begränsningar

* Som standard kan Azure Blob Storage-åtgärder läsa eller skriva filer som är *50 MB eller mindre*. För att hantera filer som är större än 50 MB men upp till 1024 MB, stöder Azure Blob Storage-åtgärder [meddelande segment](../logic-apps/logic-apps-handle-large-messages.md). Åtgärden **Hämta BLOB-innehåll** använder implicit segment.

* Azure Blob Storage-utlösare stöder inte segment. När du begär fil innehåll väljer utlösare endast filer som är 50 MB eller mindre. Följ det här mönstret om du vill hämta filer som är större än 50 MB:

  * Använd en Azure Blob Storage-utlösare som returnerar fil egenskaper, till exempel **när en BLOB läggs till eller ändras (endast egenskaper)** .

  * Följ utlösaren med åtgärden Azure Blob Storage **Hämta BLOB-innehåll** , som läser den fullständiga filen och som implicit använder segment.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [Azure Storage-konto och en lagrings behållare](../storage/blobs/storage-quickstart-blobs-portal.md)

* Den Logic-app där du behöver åtkomst till ditt Azure Blob Storage-konto. Om du vill starta din Logic-app med en Azure Blob Storage-utlösare behöver du en [Tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Lägg till Blob Storage-utlösare

I Azure Logic Apps måste varje Logi Kap par starta med en [](../logic-apps/logic-apps-overview.md#logic-app-concepts)utlösare som utlöses när en enskild händelse inträffar eller när ett särskilt villkor uppfylls. Varje gång utlösaren utlöses skapar Logic Apps-motorn en Logic App-instans och börjar köra appens arbets flöde.

Det här exemplet visar hur du kan starta ett Logic app-arbetsflöde med **när en BLOB läggs till eller ändras (endast egenskaper)** utlöses när en blobs egenskaper läggs till eller uppdateras i din lagrings behållare.

1. I [Azure Portal](https://portal.azure.com) eller Visual Studio skapar du en tom Logic-app som öppnar Logic App Designer. I det här exemplet används Azure Portal.

2. I rutan Sök anger du "Azure Blob" som filter. Välj den utlösare som du vill använda från listan utlösare.

   I det här exemplet används den här utlösaren: **När en BLOB läggs till eller ändras (endast egenskaper)**

   ![Välj utlösare](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Om du uppmanas att ange anslutnings information [skapar du en Blob Storage-anslutning nu](#create-connection). Eller, om anslutningen redan finns, anger du den information som krävs för utlösaren.

   I det här exemplet väljer du den behållare och mapp som du vill övervaka.

   1. I rutan **behållare** väljer du mappikonen.

   2. I mapplistan väljer du höger vinkel paren tes ( **>** ) och bläddra tills du hittar och väljer den mapp som du vill använda.

      ![Välj mapp](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Välj intervall och frekvens för hur ofta du vill att utlösaren ska söka efter ändringar i mappen.

4. När du är klar väljer du **Spara**i verktygsfältet designer.

5. Fortsätt nu att lägga till en eller flera åtgärder i din Logic app för de uppgifter som du vill utföra med utlösnings resultaten.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Lägg till Blob Storage-åtgärd

I Azure Logic Apps är en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) ett steg i arbets flödet som följer en utlösare eller en annan åtgärd. I det här exemplet börjar Logic-appen med [upprepnings utlösaren](../connectors/connectors-native-recurrence.md).

1. Öppna din Logic app i Logic App Designer i [Azure Portal](https://portal.azure.com) eller Visual Studio. I det här exemplet används Azure Portal.

2. I Logic App Designer går du till utlösaren eller åtgärden och väljer **nytt steg**.

   ![Lägga till en åtgärd](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du musen över den anslutande pilen. Välj plus tecknet ( **+** ) som visas och välj **Lägg till en åtgärd**.

3. I rutan Sök anger du "Azure Blob" som filter. Välj den åtgärd du vill använda i listan åtgärder.

   I det här exemplet används den här åtgärden: **Hämta BLOB-innehåll**

   ![Välj åtgärd](./media/connectors-create-api-azureblobstorage/azure-blob-action.png)

4. Om du uppmanas att ange anslutnings information [skapar du din Azure Blob Storage-anslutning nu](#create-connection).
Eller, om anslutningen redan finns, anger du den information som krävs för åtgärden.

   I det här exemplet väljer du den fil som du vill använda.

   1. Välj mappikonen från rutan **BLOB** .
  
      ![Välj mapp](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Sök efter och välj den fil som du vill använda baserat på blobens **ID-** nummer. Du kan hitta detta **ID-** nummer i blobens metadata som returneras av den tidigare beskrivna Blob Storage-utlösaren.

5. När du är klar väljer du **Spara**i verktygsfältet designer.
Om du vill testa din Logic-App kontrollerar du att den valda mappen innehåller en blob.

I det här exemplet hämtas endast innehållet för en blob. Om du vill visa innehållet lägger du till en annan åtgärd som skapar en fil med bloben med hjälp av en annan koppling. Lägg till exempel till en OneDrive-åtgärd som skapar en fil baserat på BLOB-innehållet.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Anslut till lagrings konto

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och gränser, som beskrivs i anslutnings programmets öppna API-fil (tidigare Swagger), finns på [kopplingens referens sida](/connectors/azureblobconnector/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
