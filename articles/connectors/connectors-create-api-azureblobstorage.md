---
title: Ansluta till Azure blob storage - Azure Logic Apps | Microsoft Docs
description: Skapa och hantera blobbar i Azure-lagring med Azure Logikappar
author: ecfan
manager: cfowler
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 15d737cd85f70717bfdf15dfb3d179f977b63c72
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "34723440"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Skapa och hantera blobbar i Azure blob storage med Azure Logikappar

Den här artikeln visar hur du kan komma åt och hantera filer som lagras som blobar i ditt Azure storage-konto i en logikapp med Azure Blob Storage-anslutningen. På så sätt kan du skapa logikappar som automatiserar uppgifter och arbetsflöden för att hantera dina filer. Du kan till exempel skapa logikappar som skapa, hämta, uppdatera och ta bort filer i ditt lagringskonto.

Anta att du har ett verktyg som uppdateras på en Azure-webbplats. den fungerar som utlösare för din logikapp. Den här händelsen inträffar, kan du ha logikappen vissa filen i din blobblagringsbehållare, vilket är en åtgärd i din logikapp. 

Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. Om du har använt logikappar granska [vad är Azure Logikappar](../logic-apps/logic-apps-overview.md) och [Snabbstart: skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Connector-specifik teknisk information finns i <a href="https://docs.microsoft.com/connectors/azureblobconnector/" target="blank">Azure Blob Storage connector referens</a>.

## <a name="prerequisites"></a>Förutsättningar

* En [Azure storage-konto och lagringsbehållare](../storage/blobs/storage-quickstart-blobs-portal.md)

* Logikappen där du behöver åtkomst till Azure blob storage-konto. Om du vill starta din logikapp med en Azure Blob Storage-utlösare, behöver du en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Lägga till blob storage-utlösare

I Azure Logic Apps varje logikapp måste börja med en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en viss händelse inträffar eller när ett specifikt villkor uppfylls. Varje gång utlösaren-utlöses Logic Apps motorn skapar en logik app-instansen och börjar köras appens arbetsflöde.

Det här exemplet illustrerar hur du kan starta ett arbetsflöde med logik app med den **Azure Blob Storage - när en blob har lagts till eller ändras (Egenskaper)** utlösare när en blob-egenskaper hämtar läggs till eller uppdateras i storage-behållare. 

1. Skapa en tom logikapp, vilket öppnar logik App Designer i Azure-portalen eller Visual Studio. Det här exemplet använder Azure-portalen.

2. I sökrutan anger du ”azure blob” som filter. Välj utlösare som du vill använda från listan över utlösare.

   Det här exemplet används den här utlösaren: **Azure Blob Storage - när en blob har lagts till eller ändras (Egenskaper)**

   ![Välj utlösare](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Om du uppmanas anslutningsinformation [skapa blob storage anslutningen nu](#create-connection). Eller, om det finns redan en anslutning, ange nödvändig information för utlösaren.

   Det här exemplet väljer du behållaren och mappen som du vill övervaka.

   1. I den **behållare** väljer du mappikonen.

   2. I listan väljer du vinkelparentesen ( **>** ), och Bläddra tills du hittar och välj den mapp som du vill använda. 

      ![Välj mapp](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Välj intervall och frekvens för hur ofta du vill att utlösaren ska kontrollera mappen för ändringar.

4. När du är klar i verktygsfältet designer väljer **spara**.

5. Fortsätt att lägga till en eller flera åtgärder i din logikapp för de aktiviteter som du vill utföra med Utlösare resultat.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Lägg till blob storage-åtgärd

I Azure Logikappar en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. I det här exemplet logikappen som börjar med den [upprepning utlösaren](../connectors/connectors-native-recurrence.md).

1. Öppna logikappen i logik App Designer i Azure-portalen eller Visual Studio. Det här exemplet använder Azure-portalen.

2. I logik App Designer under utlösare eller åtgärd, Välj **nytt steg** > **lägga till en åtgärd**.

   ![Lägga till en åtgärd](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Om du vill lägga till en åtgärd mellan befintliga steg, håller du muspekaren över anslutande pilen. 
   Välj på plustecknet (**+**) som visas och sedan välja **lägga till en åtgärd**.

3. I sökrutan anger du ”azure blob” som filter. Välj den åtgärd som du vill använda från listan över åtgärder.

   Det här exemplet använder den här åtgärden: **Azure Blob Storage - Get-blobinnehåll**

   ![Välj åtgärd](./media/connectors-create-api-azureblobstorage/azure-blob-action.png) 

4. Om du uppmanas anslutningsinformation [skapar anslutningen Azure Blob Storage nu](#create-connection). Eller, om det finns redan en anslutning, ange nödvändig information för åtgärden. 

   Det här exemplet väljer du den fil du vill.

   1. Från den **Blob** väljer du mappikonen.
  
      ![Välj mapp](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Hitta och välja den fil du vill baserat på blobben **Id** nummer. Du hittar det **Id** nummer i det blob-metadata som returneras av tidigare beskrivs blob storage-utlösare.

5. När du är klar i verktygsfältet designer väljer **spara**.
Kontrollera att den valda mappen innehåller en blob för att testa logikappen.

Det här exemplet endast hämtar innehållet för en blob. Lägg till en annan åtgärd som skapar en fil med blobben med hjälp av en annan koppling för att visa innehållet. Till exempel lägga till en OneDrive-åtgärd som skapar en fil utifrån blob-innehåll.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Ansluta till storage-konto

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare och åtgärder som gränser, enligt beskrivningen av kopplingens Swagger-filen finns den [kopplingens referenssida](/connectors/azureblobconnector/). 

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps kopplingar](../connectors/apis-list.md)
