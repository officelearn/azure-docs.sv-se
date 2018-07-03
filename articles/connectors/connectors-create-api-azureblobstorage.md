---
title: Ansluta till Azure blob storage - Azure Logic Apps | Microsoft Docs
description: Skapa och hantera blobar i Azure storage med Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 49d08135dee4568d1a9d65ec2d22d17ee3bda2ea
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "35294687"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Skapa och hantera blobbar i Azure blob storage med Azure Logic Apps

Den här artikeln visar hur du kan komma åt och hantera filer som lagras som blobar i Azure storage-kontot från inuti en logikapp med Azure Blob Storage connector. På så sätt kan du skapa logikappar som automatiserar uppgifter och arbetsflöden för att hantera dina filer. Du kan till exempel skapa logikappar som att skapa, hämta, uppdatera och ta bort filer i ditt storage-konto.

Anta att du har ett verktyg som uppdateras på en Azure-webbplats. den fungerar som en utlösare för din logikapp. Om den här händelsen inträffar kan ha du logikappen vissa filen i din blob storage-behållare, vilket är en åtgärd i din logikapp. 

Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Snabbstart: skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Specifika teknisk information finns i den <a href="https://docs.microsoft.com/connectors/azureblobconnector/" target="blank">Azure Blob Storage-anslutning för referens</a>.

## <a name="prerequisites"></a>Förutsättningar

* En [Azure storage-konto och storage-behållare](../storage/blobs/storage-quickstart-blobs-portal.md)

* Logikappen där du behöver åtkomst till Azure blob storage-kontot. Om du vill starta logikappen med en Azure Blob Storage-utlösare, som du behöver en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Lägg till blob storage-utlösare

I Azure Logic Apps varje logikapp måste börja med en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en specifik händelse sker eller när ett specifikt villkor uppfylls. Varje gång utlösaren Logic Apps-motorn skapar en logikappinstans och börjar köras appens arbetsflöde.

Det här exemplet visar hur du kan starta en logikapparbetsflöde med den **Azure Blob Storage - när en blob läggs till eller ändras (enbart egenskaper)** utlösare när egenskaper för en blob hämtar läggs till eller uppdateras i lagringsbehållaren. 

1. I Azure portal eller Visual Studio, skapar du en tom logikapp som öppnas Logic App Designer. Det här exemplet används Azure-portalen.

2. I sökrutan anger du ”azure blob” som filter. Välj utlösaren som du vill använda från listan över utlösare.

   Det här exemplet används den här utlösaren: **Azure Blob Storage - när en blob läggs till eller ändras (enbart egenskaper)**

   ![Välj utlösare](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Om du uppmanas anslutningsinformation [skapar din blob storage-anslutning nu](#create-connection). Eller om anslutningen redan finns, ange nödvändig information för utlösaren.

   För det här exemplet väljer du behållaren och mappen som du vill övervaka.

   1. I den **behållare** väljer du mappikonen.

   2. I listan väljer du vinkelparentesen ( **>** ), och sedan bläddra tills du hittar och välj den mapp som du vill. 

      ![Välj mapp](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Välj intervall och frekvens för hur ofta du vill att utlösaren ska kontrollera mappen för ändringar.

4. När du är klar på verktygsfältet för appdesignern väljer **spara**.

5. Nu ska du fortsätta att lägga till en eller flera åtgärder i din logikapp för uppgifter som du vill utföra med utlösare resultaten.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Lägg till blob storage-åtgärd

I Azure Logic Apps, en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. I det här exemplet logikappen som börjar med den [upprepningsutlösare](../connectors/connectors-native-recurrence.md).

1. Öppna logikappen i Logic App Designer i Azure portal eller Visual Studio. Det här exemplet används Azure-portalen.

2. I Logic App Designer, utlösaren eller åtgärden, väljer **nytt steg** > **Lägg till en åtgärd**.

   ![Lägga till en åtgärd](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Flytta musen över den anslutande pilen för att lägga till en åtgärd mellan befintliga steg. 
   Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.

3. I sökrutan anger du ”azure blob” som filter. Välj vilken åtgärd du önska från åtgärdslistan över.

   Det här exemplet använder den här åtgärden: **Azure Blob Storage - Get-blobinnehåll**

   ![Välj åtgärd](./media/connectors-create-api-azureblobstorage/azure-blob-action.png) 

4. Om du uppmanas anslutningsinformation [skapa anslutningen Azure Blob Storage nu](#create-connection). Eller om anslutningen redan finns, ange informationen som krävs för åtgärden. 

   För det här exemplet väljer du den fil du vill.

   1. Från den **Blob** väljer du mappikonen.
  
      ![Välj mapp](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Hitta och välj den fil du vill utifrån blobens **Id** tal. Du hittar den **Id** numret i blobbmetadata som returneras av beskrevs tidigare blob storage-utlösare.

5. När du är klar på verktygsfältet för appdesignern väljer **spara**.
Testa din logikapp genom att se till att den valda mappen innehåller en blob.

Det här exemplet hämtar endast innehållet för en blob. Lägg till en annan åtgärd som skapar en fil med blobben med hjälp av en annan koppling för att visa innehållet. Lägg exempelvis till en OneDrive-åtgärd som skapar en fil baserat på blob-innehållet.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Ansluta till lagringskonto

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare och åtgärder gränser, enligt beskrivningen av kopplingens Swagger-fil, finns i den [anslutningsappens-referenssida](/connectors/azureblobconnector/). 

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
