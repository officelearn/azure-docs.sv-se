---
title: "Skapa en funktion i Azure som utlöses av kömeddelanden | Microsoft Docs"
description: "Använd Azure Functions för att skapa en funktion utan server som startas av meddelanden som skickas till en Azure Storage-kö."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 93b3910ba2f95bb6f3228926a51f71f96ccd1e23
ms.contentlocale: sv-se
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Skapa en funktion som utlöses av Azure Queue Storage

Lär dig hur du skapar en funktion som utlöses när meddelanden skickas till en Azure Storage-kö.  

![Visa meddelande i loggarna.](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

Det bör ta mindre än fem minuter att slutföra alla steg i det här avsnittet.

## <a name="prerequisites"></a>Krav

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Du måste också hämta och installera [Microsoft Azure Storage Explorer](http://storageexplorer.com/). 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>Skapa en funktion som utlöses av en kö

Expandera din funktionsapp, klicka på knappen **+** bredvid **Funktioner** och klicka på **QueueTrigger**-mallen för önskat språk. Använd sedan inställningarna i tabellen och klicka på **Skapa**.

![Skapa funktionen som utlöses av lagringskön.](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)
    
| Inställning      |  Föreslaget värde   | Beskrivning                                        |
| ------------ |  ----------------- | -------------------------------------------------- |
| **Könamn**   | myqueue-items    | Namnet på den kö som ska anslutas till i ditt Storage-konto. |
| **Lagringskontoanslutning** | AzureWebJobStorage | Du kan antingen använda den lagringskontoanslutning som redan används i funktionsappen eller skapa en ny.  |
| **Namnge din funktion** | Ett unikt namn i funktionsappen | Namnge funktionen som utlöses av kön. |  

Sedan ansluter du till ditt Azure Storage-konto och skapar lagringskön **myqueue-items**.

## <a name="create-the-queue"></a>Skapa kön

1. Klicka på **Integrera** i din funktion, expandera **Dokumentation** och kopiera både **kontonamnet** och **kontonyckeln**. Du använder dessa autentiseringsuppgifter för att ansluta till lagringskontot. Om du redan har anslutit ditt lagringskonto går du vidare till steg 4.
 
    ![Hämta autentiseringsuppgifterna för att ansluta till lagringskontot.](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)v

2. Kör verktyget [Microsoft Azure Storage Explorer](http://storageexplorer.com/), klicka på anslutningsikonen till vänster, välj **Use a storage account name and key** (Använd ett kontonamn och en kontonyckel för lagringskontot) och klicka på **Nästa**.

    ![Kör verktyget Storage Account Explorer.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)
    
3. Ange **kontonamnet** och **kontonyckeln** från steg 1, klicka på **Nästa** och sedan på **Anslut**. 
  
    ![Ange autentiseringsuppgifter för lagringskontot och anslut.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

4. Expandera det anslutna lagringskontot, högerklicka på **Köer**, klicka på **Skapa kö**, skriv `myqueue-items` och tryck sedan på Retur.
 
    ![Skapa en lagringskö.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

Nu när du har en lagringskö kan du testa funktionen genom att lägga till ett meddelande i kön.  

## <a name="test-the-function"></a>Testa funktionen

1. Gå till Azure Portal igen, bläddra till din funktion, expandera **Loggar** längst ned på sidan och se till att loggströmningen inte är pausad.

2. I Lagringsutforskaren expanderar du ditt lagringskonto, **Köer** och **myqueue-items**. Klicka sedan på **Lägg till meddelande**. 

    ![Lägg till ett meddelande i kön.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

2. Skriv ditt "Hello World!"- meddelande i **Meddelandetext** och klicka på **OK**.
 
3. Vänta några sekunder och gå sedan tillbaka till dina funktionsloggar och kontrollera att det nya meddelandet har lästs från kön. 

    ![Visa meddelande i loggarna.](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

4. Gå tillbaka till Lagringsutforskaren, klicka på **Uppdatera** och kontrollera att meddelandet har bearbetats och inte längre finns i kön.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har skapat en funktion som körs när ett meddelande läggs till i en lagringskö. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Mer information om Queue Storage-utlösare finns i [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md) (Azure Functions-lagringsköbindningar). 




