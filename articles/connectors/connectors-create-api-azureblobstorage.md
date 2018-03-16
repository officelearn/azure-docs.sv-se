---
title: "Lägg till Azure blob storage Connector i dina Logic Apps | Microsoft Docs"
description: "Komma igång och konfigurera Azure blob storage-kopplingen i en logikapp"
services: 
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: b5dc3f75-6bea-420b-b250-183668d2848d
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/02/2017
ms.author: estfan; ladocs
ms.openlocfilehash: 7aaff2ac78201c4484105c6cacc5f0fef19ca7b5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="use-the-azure-blob-storage-connector-in-a-logic-app"></a>Använda Azure blob storage-kopplingen i en logikapp
Använda Azure Blob storage anslutningen för att ladda upp, uppdatera, hämta och ta bort blobbar i ditt lagringskonto alla inom en logikapp.  

Med Azure blob storage kan du:

* Skapa ditt arbetsflöde genom att ladda upp nya projekt eller hämta filer som nyligen har uppdaterats.
* Använd åtgärder för att hämta filens metadata, ta bort en fil och kopiera filer. Till exempel när ett verktyg uppdateras i en Azure-webbplats (en utlösare), sedan uppdatera en fil i blob storage (en åtgärd). 

Det här avsnittet visar hur du använder blob storage-kopplingen i en logikapp.

Läs mer om Logic Apps i [vad är logic apps](../logic-apps/logic-apps-overview.md) och [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-azure-blob-storage"></a>Ansluta till Azure blob storage
Innan din logikapp får åtkomst till alla tjänster måste du först skapa en *anslutning* till tjänsten. En anslutning kan du ansluta en logikapp och en annan tjänst. Till exempel för att ansluta till ett lagringskonto måste du först skapa en blob-lagring *anslutning*. Ange de autentiseringsuppgifter som du vanligtvis använder för att få åtkomst till tjänsten som du ansluter till om du vill skapa en anslutning. Så med Azure storage kan du ange autentiseringsuppgifterna till ditt lagringskonto för att skapa anslutningen. 

#### <a name="create-the-connection"></a>Skapa anslutningen
> [!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="use-a-trigger"></a>Använda en utlösare
Den här anslutningen har inte utlösare. Använd andra utlösare för att starta logikapp, till exempel en upprepning utlösare, en HTTP-Webhook-utlösare, utlösare som är tillgängliga med övriga kopplingar och mycket mer. [Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) innehåller ett exempel.

## <a name="use-an-action"></a>Använda en åtgärd
En åtgärd är en åtgärd som utförs av arbetsflödet som definierats i en logikapp.

1. Klicka på plustecknet. Du ser flera alternativ: **lägga till en åtgärd**, **Lägg till ett villkor**, eller en av de **mer** alternativ.
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. Välj **lägga till en åtgärd**.
3. Skriv ”blob” om du vill hämta en lista över alla tillgängliga åtgärder i textrutan.
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png) 
4. I vårt exempel väljer **AzureBlob - Get filens metadata med sökvägen**. Om det finns redan en anslutning, väljer du den **...** (Visa Väljaren) för att välja en fil.
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    Om du uppmanas att ange anslutningsinformation anger du information för att skapa anslutningen. [Skapa anslutningen](connectors-create-api-azureblobstorage.md#create-the-connection) i det här avsnittet beskriver dessa egenskaper. 
   
   > [!NOTE]
   > I det här exemplet kan vi hämta metadata för en fil. Lägg till en annan åtgärd som skapar en ny fil med en annan koppling om du vill se metadata. Till exempel lägga till en OneDrive-åtgärd som skapar en ny ”test” fil baserat på metadata. 


5. **Spara** dina ändringar (övre vänstra hörnet i verktygsfältet). Din logikapp sparas och aktiveras automatiskt.

> [!TIP]
> [Lagringsutforskaren](http://storageexplorer.com/) är ett bra verktyg för att hantera flera lagringskonton.

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/azureblobconnector/). 

## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Utforska andra tillgängliga kopplingar i Logic Apps på vår [API: er listan](apis-list.md).

