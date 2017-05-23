---
title: "Skapa en funktion i Azure som utlöses av Blob Storage | Microsoft Docs"
description: "Använd Azure Functions till att skapa en serverfri funktion som anropas när objekt läggs till i Azure Blob Storage."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: c0d1271bc083688bbc72bd2556546c2f738e7345
ms.contentlocale: sv-se
ms.lasthandoff: 05/12/2017


---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Skapa en funktion som utlöses av Azure Blob Storage

Lär dig hur du skapar en funktion som utlöses när filer överförs till eller uppdateras i Azure Blob Storage.

![Visa meddelande i loggarna.](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Krav

Innan du kör exemplet måste du ha utfört följande:

- Hämta och installera [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Skapa en Azure Functions-app

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Funktionsappen skapades.](./media/functions-create-first-azure-function/function-app-create-success.png)

Därefter skapar du en funktion i den nya funktionsappen.

<a name="create-function"></a>

## <a name="create-a-blob-storage-triggered-function"></a>Skapa en funktion som utlöses av Blob Storage

Expandera funktionsappen, klicka på knappen **+** bredvid **Funktioner** och klicka på mallen **BlobTrigger** för önskat språk. Använd inställningarna som anges i tabellen och klicka sedan på **Skapa**.

![Skapa funktionen som utlöses av Blob Storage.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)

| Inställning | Föreslaget värde | Beskrivning |
|---|---|---|
| **Sökväg**   | mycontainer /{namn}    | Platsen i Blob Storage som övervakas. Filnamnet för bloben skickas i bindningen som parametern _namn_.  |
| **Lagringskontoanslutning** | AzureWebJobStorage | Du kan antingen använda den lagringskontoanslutning som redan används i funktionsappen eller skapa en ny.  |
| **Namnge din funktion** | Ett unikt namn i funktionsappen | Namnge funktionen som utlöses av kön. |

Anslut sedan till Azure Storage-kontot och skapa behållaren **mycontainer**.

## <a name="create-the-container"></a>Skapa behållaren

1. Klicka på **Integrera** i din funktion, expandera **Dokumentation** och kopiera både **kontonamnet** och **kontonyckeln**. Du använder dessa autentiseringsuppgifter för att ansluta till lagringskontot. Om du redan har anslutit ditt lagringskonto går du vidare till steg 4.

    ![Hämta autentiseringsuppgifterna för att ansluta till lagringskontot.](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

1. Kör verktyget [Microsoft Azure Storage Explorer](http://storageexplorer.com/), klicka på anslutningsikonen till vänster, välj **Use a storage account name and key** (Använd ett kontonamn och en kontonyckel för lagringskontot) och klicka på **Nästa**.

    ![Kör verktyget Storage Account Explorer.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)

1. Ange **kontonamnet** och **kontonyckeln** från steg 1, klicka på **Nästa** och sedan på **Anslut**. 

    ![Ange autentiseringsuppgifter för lagringskontot och anslut.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

1. Expandera det anslutna lagringskontot, högerklicka på **Blob containers** (Blobbehållare), klicka på **Create blob container** (Skapa blobbehållare), skriv `mycontainer` och tryck på Retur.

    ![Skapa en lagringskö.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

Nu när du har en blobbehållare kan du testa funktionen genom att ladda upp en fil till behållaren.

## <a name="test-the-function"></a>Testa funktionen

1. Gå till Azure Portal igen, bläddra till din funktion, expandera **Loggar** längst ned på sidan och se till att loggströmningen inte är pausad.

1. Expandera ditt lagringskonto, **Blob containers** (Blobbehållare) och **mycontainer** i Storage Explorer. Klicka på **Ladda upp** och sedan på **Ladda upp filer**.

    ![Ladda upp en fil till blobbehållaren.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

1. Klicka på fältet **Filer** i dialogrutan **Ladda upp filer**. Bläddra till en fil lokalt i datorn, till exempel en bildfil, markera den, klicka på **Öppna** och sedan på **Ladda upp**.

1. Gå tillbaka till funktionsloggarna och kontrollera att bloben har lästs.

   ![Visa meddelande i loggarna.](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > Om din funktionsapp körs med standardförbrukningsplanen kan det dröja flera minuter från det att blobben läggs till eller uppdateras och att funktionen utlöses. Om du behöver låg latens i dina blobutlösta funktioner bör du köra funktionsapparna med en App Service-plan.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har skapat en funktion som körs när en blob läggs till eller uppdateras i Blob Storage. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Mer information om Blob Storage-utlösare finns i [Azure Functions Blob storage bindings](functions-bindings-storage-blob.md) (Blob Storage-bindningar i Azure Functions).
