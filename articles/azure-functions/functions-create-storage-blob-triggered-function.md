---
title: Skapa en funktion i Azure som utlöses av Blob Storage | Microsoft Docs
description: Använd Azure Functions till att skapa en serverfri funktion som anropas när objekt läggs till i Azure Blob Storage.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 10/01/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: f71db50678ad0f1b37e30a4120b90d47d9090942
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49113860"
---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Skapa en funktion som utlöses av Azure Blob Storage

Lär dig hur du skapar en funktion som utlöses när filer överförs till eller uppdateras i Azure Blob Storage.

![Visa meddelande i loggarna.](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

+ Hämta och installera [Microsoft Azure Storage Explorer](http://storageexplorer.com/).
+ En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-an-azure-function-app"></a>Skapa en Azure Functions-app

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Funktionsappen skapades.](./media/functions-create-first-azure-function/function-app-create-success.png)

Därefter skapar du en funktion i den nya funktionsappen.

<a name="create-function"></a>

## <a name="create-a-blob-storage-triggered-function"></a>Skapa en funktion som utlöses av Blob Storage

1. Expandera funktionsappen och klicka på knappen **+** bredvid **Funktioner**. Om det här är den första funktionen i din funktionsapp väljer du **I portalen** och sedan **Fortsätt**. Annars går du till steg tre.

   ![Sidan snabbstart för funktioner i Azure Portal](./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-portal.png)

1. Välj **Fler mallar** och sedan **Slutför och visa mallar**.

    ![Functions-snabbstart, välj fler mallar](./media/functions-create-storage-blob-triggered-function/add-first-function.png)

1. Skriv `blob` i sökfältet och välj sedan mallen **Blob-utlösare**.

1. Om du får uppmaningen väljer du **Installera** för att installera Azure Storage-tillägget för eventuella beroenden i funktionsappen. När installationen är klar väljer du **Fortsätt**.

    ![Installera bindningstillägg](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)

1. Använd inställningarna som anges i tabellen nedanför bilden.

    ![Skapa funktionen som utlöses av Blob Storage.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png)

    | Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|
    | **Namn** | Ett unikt namn i funktionsappen | Namnge funktionen som utlöses av blobben. |
    | **Sökväg**   | samples-workitems/{namn}    | Platsen i Blob Storage som övervakas. Filnamnet för bloben skickas i bindningen som parametern _namn_.  |
    | **Lagringskontoanslutning** | AzureWebJobsStorage | Du kan antingen använda den lagringskontoanslutning som redan används i funktionsappen eller skapa en ny.  |

1. Klicka på **Skapa** för att skapa den nya funktionen.

Anslut sedan till ditt Azure Storage-konto och skapa containern **samples-workitems**.

## <a name="create-the-container"></a>Skapa containern

1. Klicka på **Integrera** i din funktion, expandera **Dokumentation** och kopiera både **kontonamnet** och **kontonyckeln**. Du använder dessa autentiseringsuppgifter för att ansluta till lagringskontot. Om du redan har anslutit ditt lagringskonto går du vidare till steg 4.

    ![Hämta autentiseringsuppgifterna för att ansluta till lagringskontot.](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

1. Kör verktyget [Microsoft Azure Storage Explorer](http://storageexplorer.com/), klicka på anslutningsikonen till vänster, välj **Use a storage account name and key** (Använd ett kontonamn och en kontonyckel för lagringskontot) och klicka på **Nästa**.

    ![Kör verktyget Storage Account Explorer.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)

1. Ange **kontonamnet** och **kontonyckeln** från steg 1, klicka på **Nästa** och sedan på **Anslut**. 

    ![Ange autentiseringsuppgifter för lagringskontot och anslut.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

1. Expandera det anslutna lagringskontot, högerklicka på **Blob containers** (Blobbehållare), klicka på **Create blob container** (Skapa blobbehållare), skriv `samples-workitems` och tryck på Retur.

    ![Skapa en lagringskö.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

Nu när du har en blobcontainer kan du testa funktionen genom att ladda upp en fil till containern.

## <a name="test-the-function"></a>Testa funktionen

1. Gå till Azure Portal igen, bläddra till din funktion, expandera **Loggar** längst ned på sidan och se till att loggströmningen inte är pausad.

1. Expandera ditt lagringskonto, **Blob containers** (Blobbehållare) och **samples-workitems** i Storage Explorer. Klicka på **Ladda upp** och sedan på **Ladda upp filer**.

    ![Ladda upp en fil till blobcontainern.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

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
