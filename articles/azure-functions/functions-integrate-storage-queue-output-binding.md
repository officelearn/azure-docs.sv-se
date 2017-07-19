---
title: "Skapa en funktion i Azure som utlöses av kömeddelanden | Microsoft Docs"
description: "Använd Azure Functions för att skapa en funktion utan server som startas av meddelanden som skickas till en Azure Storage-kö."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: d1ddfbe9a0a0c7c7e0a060776938bd68a87e1ba5
ms.contentlocale: sv-se
ms.lasthandoff: 06/26/2017

---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Lägga till meddelanden i en Azure Storage-kö med Functions

I Azure Functions kan du använda indata- och utdatabindningar för att ansluta till data i en extern tjänst från din funktion på ett deklarativt sätt. I det här avsnittet får du lära dig hur du uppdaterar en befintlig funktion genom att lägga till en utdatabindning som skickar meddelanden till Azure Queue Storage.  

![Visa meddelande i loggarna.](./media/functions-integrate-storage-queue-output-binding/functions-integrate-storage-binding-in-portal.png)

## <a name="prerequisites"></a>Krav 

[!INCLUDE [Previous topics](../../includes/functions-quickstart-previous-topics.md)]

* Installera [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="add-binding"></a>Lägga till en utdatabindning
 
1. Expandera både din funktionsapp och din funktion.

2. Välj **Integrera** och **+ Nya utdata** och klicka sedan på **Azure Queue Storage** och på **Välj**.
    
    ![Lägg till en Queue Storage-utdatabindning i en funktion på Azure Portal.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding.png)

3. Använd inställningarna som anges i tabellen och välj **Spara**: 

    ![Lägg till en Queue Storage-utdatabindning i en funktion på Azure Portal.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding-2.png)

    | Inställning      |  Föreslaget värde   | Beskrivning                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Könamn**   | myqueue-items    | Namnet på kön som ska anslutas till i ditt Storage-konto. |
    | **Lagringskontoanslutning** | AzureWebJobStorage | Du kan antingen använda den lagringskontoanslutning som redan används i funktionsappen eller skapa en ny.  |
    | **Meddelandeparameternamn** | outQueueItem | Namnet på utdatabindningsparametern. | 

Nu när du har definierat en utdatabindning måste du uppdatera koden så att bindningen används när meddelanden läggs till i en kö.  

## <a name="update-the-function-code"></a>Uppdatera funktionskoden

1. Välj din funktion så att funktionskoden visas i redigeraren. 

2. För en C#-funktion uppdaterar du funktionsdefinitionen på följande sätt för att lägga till lagringsbindningsparametern **outQueueItem**. Hoppa över det här steget om du använder en JavaScript-funktion.

    ```cs   
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, 
        ICollector<string> outQueueItem, TraceWriter log)
    {
        ....
    }
    ```

3. Lägg till följande kod i funktionen precis innan metoden returneras. Använd lämpligt kodstycke för det språk som du använder för funktionen.

    ```javascript
    context.bindings.outQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ```cs
    outQueueItem.Add("Name passed to the function: " + name);     
    ```

4. Välj **Spara** för att spara ändringarna.

Värdet som skickades till HTTP-utlösaren ingår i ett meddelande som har lagts till i kön.
 
## <a name="test-the-function"></a>Testa funktionen 

1. Välj **Kör** när kodändringarna har sparats. 

    ![Lägg till en Queue Storage-utdatabindning i en funktion på Azure Portal.](./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png)

2. Kontrollera i loggfilerna att funktionen har slutförts utan fel. En ny kö med namnet **outqueue** skapas i ditt lagringskonto av funktionskörningen när utdatabindningen används för första gången.

Därefter kan du ansluta till ditt lagringskonto och kontrollera den nya kön och meddelandet som du lade till. 

## <a name="connect-to-the-queue"></a>Ansluta till kön

Hoppa över de första tre stegen om du redan har installerat Lagringsutforskaren och anslutit den till ditt lagringskonto.    

1. Välj **Integrera** och ny bindning av utdata för **Azure Queue-storage** och expandera **Dokumentation**. Kopiera både **kontonamnet** och **kontonyckeln**. Du använder dessa autentiseringsuppgifter för att ansluta till lagringskontot.
 
    ![Hämta autentiseringsuppgifterna för att ansluta till lagringskontot.](./media/functions-integrate-storage-queue-output-binding/function-get-storage-account-credentials.png)

2. Kör verktyget [Microsoft Azure Storage Explorer](http://storageexplorer.com/), klicka på anslutningsikonen till vänster, välj **Use a storage account name and key** (Använd ett kontonamn och en kontonyckel för lagringskontot) och klicka på **Nästa**.

    ![Kör verktyget Storage Account Explorer.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-1.png)
    
3. Klistra in **kontonamn** och **kontonyckel** från steg 1 till motsvarande fält och välj sedan **Nästa** och **Anslut**. 
  
    ![Klistra in autentiseringsuppgifter för lagringskontot och anslut.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-2.png)

4. Expandera det anslutna lagringskontot, högerklicka på **Köer** och kontrollera att det finns en kö med namnet **myqueue-items**. Du bör även se ett meddelande som redan finns i kön.  
 
    ![Skapa en lagringskö.](./media/functions-integrate-storage-queue-output-binding/function-queue-storage-output-view-queue.png)
 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har lagt till en utdatabindning i en befintlig funktion. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Mer information om bindning till Queue Storage finns i [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md) (Azure Functions-lagringsköbindningar). 




