---
title: "Skapa en funktion som ansluter till Azure-tjänster | Microsoft-dokument"
description: "Använd Azure Functions för att skapa ett serverlöst program som ansluter till andra Azure-tjänster."
services: functions
documentationcenter: dev-center-name
author: yochay
manager: manager-alias
editor: 
tags: 
keywords: "azure-funktioner, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur"
ms.assetid: ab86065d-6050-46c9-a336-1bfc1fa4b5a1
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/01/2017
ms.author: rachelap; glenga
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 4bea7f73871f8bfc755f30b9ef41a1960893152e
ms.lasthandoff: 04/18/2017


---
# <a name="use-azure-functions-to-create-a-function-that-connects-to-other-azure-services"></a>Använd Azure Functions för att skapa en funktion som ansluter till andra Azure-tjänster

I det här avsnittet får du lära dig att skapa en funktion i Azure Functions som lyssnar efter meddelanden i en Azure Storage-kö och kopierar meddelandena till en Azure Storage-tabell. En timerutlöst funktion används för att läsa in meddelanden i kön. En andra funktion läser från kön och skriver meddelanden till tabellen. Både kön och tabellen skapas av Azure Functions utifrån bindningsdefinitionerna. 

För att göra det ännu mer intressant är en funktion skriven i JavaScript och en i C#-skript. Detta visar hur en funktionsapp kan ha funktioner på olika språk. 

Det här scenariot visas i en [video på Channel 9](https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-an-Azure-Function-which-binds-to-an-Azure-service/player).

## <a name="create-a-function-that-writes-to-the-queue"></a>Skapa en funktion som skriver till kön

Innan du kan ansluta till en lagringskö måste du skapa en funktion som läser in meddelandekön. JavaScript-funktionen använder en timer som utlösare som skriver ett meddelande till kön var 10: e sekund. Om du inte redan har ett Azure-konto kan du läsa [Try Azure Functions](https://functions.azure.com/try) (Prova Azure Functions) eller [skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

1. Gå till Azure Portal och leta upp din funktionsapp.

2. Klicka på **Ny funktion** > **TimerTrigger-JavaScript**. 

3. Ge funktionen namnet **FunctionsBindingsDemo1**, ange ett cron-uttrycksvärde på `0/10 * * * * *` för **Schema** och klicka sedan på **Skapa**.
   
    ![Skapa en timerutlöst funktion](./media/functions-create-an-azure-connected-function/new-trigger-timer-function.png)

    Du har nu skapat en timerutlöst funktion som körs var 10:e sekund.

5. På fliken **Utveckla** klickar du på **Loggar** och visar aktiviteten i loggen. Du ser att en loggpost skrivs var 10: e sekund.
   
    ![Visa loggen för att kontrollera att funktionen fungerar](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-view-log.png)

## <a name="add-a-message-queue-output-binding"></a>Lägga till en meddelandekö utan utdatabindning

1. På fliken **Integrera** väljer du **Nya utdata**  >  **Azure Queue Storage**  >  **Välj** .

    ![Lägga till en utlösande timer-funktion](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab.png)

2. Ange `myQueueItem` i **Meddelandeparameternamn** och `functions-bindings` i **Könamn**, välj en befintlig **Anslutning till lagringskonto** eller klicka på **ny** för att skapa en anslutning till lagringskonto. Klicka sedan på **Spara**.  

    ![Skapa en utdatabindning till lagringskön](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab2.png)

1. På fliken **Utveckla** lägger du till följande kod till funktionen:
   
    ```javascript
   
    function myQueueItem() 
    {
        return {
            msg: "some message goes here",
            time: "time goes here"
        }
    }
   
    ```
2. Leta reda på instruktionen *if* omkring rad 9 i funktionen och infoga följande kod efter instruktionen.
   
    ```javascript
   
    var toBeQed = myQueueItem();
    toBeQed.time = timeStamp;
    context.bindings.myQueueItem = toBeQed;
   
    ```  
   
    Den här koden skapar en **myQueueItem** och ställer in dess **tid**-egenskap till aktuell timeStamp. Den lägger sedan till det nya köobjektet till kontextens **myQueueItem**-bindning.

3. Klicka på **Spara och kör**.

## <a name="view-storage-updates-by-using-storage-explorer"></a>Visa lagringsuppdateringar med Storage Explorer
Du kan kontrollera att funktionen fungerar genom att visa meddelanden i kön du har skapat.  Du kan ansluta lagringskön med Cloud Explorer i Visual Studio. Portalen gör det emellertid lätt att ansluta lagringskontot med Microsoft Azure Storage Explorer.

1. På fliken **Integrera** klickar du på din utdatabindningskö > **Dokumentation** och tar sedan fram anslutningssträngen för lagringskontot så att du kan kopiera värdet. Du använder värdet för att ansluta till lagringskontot.

    ![Hämta Azure Storage Explorer](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab3.png)


2. Om du inte redan har gjort det laddar du ned och installerar [Microsoft Azure Storage Explorer](http://storageexplorer.com). 
 
3. I Storage Explorer klickar du på ikonen för att ansluta till Azure Storage, klistrar in anslutningssträngen i fältet och slutför guiden.

    ![Lägga till en anslutning i Storage Explorer](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-storage-explorer.png)

4. Under **Local and attached** (Lokal och ansluten) expanderar du **Lagringskonton** > ditt lagringskonto > **Köer** > **functions-bindings** och kontrollerar att meddelanden skrivs till kön.

    ![Visa meddelanden i kön](./media/functions-create-an-azure-connected-function/functionsbindings-azure-storage-explorer.png)

    Om kön inte finns eller är tom finns det troligen ett problem med din funktionsbindning eller kod.

## <a name="create-a-function-that-reads-from-the-queue"></a>Skapa en funktion som läser från kön

Du kan skapa en annan funktion som läser från kön och skriver meddelanden permanent till en Azure Storage-tabell nu när du har meddelanden som läggs till i kön.

1. Klicka på **Ny funktion** > **QueueTrigger-CSharp**. 
 
2. Namnge funktionen `FunctionsBindingsDemo2`, skriv **functions-bindings** i fältet **Könamn**, välj ett befintligt lagringskonto eller skapa ett och klicka på **Skapa**.

    ![Lägg till en timer-funktion för utdatakö](./media/functions-create-an-azure-connected-function/function-demo2-new-function.png) 

3. (Valfritt) Du kan kontrollera att den nya funktionen fungerar genom att visa den nya kön i Storage Explorer som tidigare. Du kan också använda Cloud Explorer i Visual Studio.  

4. (Valfritt) Uppdatera kön **functions-bindings** och lägg märke till om några objekt har tagits bort från kön. Borttagningen sker på grund av att funktionen är bunden till kön **functions-bindings** som indatautlösare och funktionen läser kön. 
 
## <a name="add-a-table-output-binding"></a>Lägga till en utdatabindningstabell

1. I FunctionsBindingsDemo2 klickar du på **Integrera** > **	Nya utdata** > **Azure Table Storage** > **Välj**.

    ![Lägga till en bindning till en Azure Storage-tabell](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab.png) 

2. Skriv `functionbindings` som **Tabellnamn** och `myTable` som **Tabellparameternamn**, välj en **Lagringskontoanslutning** eller skapa en ny, och klicka på **Spara**.

    ![Konfigurera Storage-tabellbindning](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab2.png)
   
3. På fliken **Utveckla** ersätter du den befintliga funktionskoden med följande:
   
    ```cs
    
    using System;
    
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {    
        TableItem myItem = new TableItem
        {
            PartitionKey = "key",
            RowKey = Guid.NewGuid().ToString(),
            Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
            Msg = myQueueItem.Msg,
            OriginalTime = myQueueItem.Time    
        };
        
        // Add the item to the table binding collection.
        myTable.Add(myItem);
    
        log.Verbose($"C# Queue trigger function processed: {myItem.RowKey} | {myItem.Msg} | {myItem.Time}");
    }
    
    public class TableItem
    {
        public string PartitionKey {get; set;}
        public string RowKey {get; set;}
        public string Time {get; set;}
        public string Msg {get; set;}
        public string OriginalTime {get; set;}
    }
    
    public class QItem
    {
        public string Msg { get; set;}
        public string Time { get; set;}
    }
    ```
    Klassen **TableItem** visar en rad i lagringstabellen, och du lägger till objektet till `myTable` samlingen med **TableItem**-objekt. Du måste ställa in egenskaperna **PartitionKey** och **RowKey** för att kunna infoga i tabellen.

4. Klicka på **Spara**.  Slutligen kan du kontrollera att funktionerna fungerar genom att visa tabellen i Storage Explorer eller Visual Studio Cloud Explorer.

5. (Valfritt) I ditt lagringskonto i Storage Explorer expanderar du **Tabeller** > **functionsbindings** och kontrollerar att rader har lagts till i tabellen. Du kan göra samma sak i Cloud Explorer i Visual Studio.

    ![Vy över rader i tabellen](./media/functions-create-an-azure-connected-function/functionsbindings-azure-storage-explorer2.png)

    Om tabellen inte finns eller är tom finns det troligen ett problem med din funktionsbindning eller kod. 
 
[!INCLUDE [More binding information](../../includes/functions-bindings-next-steps.md)]

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Functions finns i följande ämnen:

* [Azure Functions, info för utvecklare](functions-reference.md)  
  Info för programmerare om att koda funktioner och definiera utlösare och bindningar.
* [Testa Azure Functions](functions-test-a-function.md)  
  Beskriver olika verktyg och tekniker för att testa funktioner.
* [Så här skalar du Azure Functions](functions-scale.md)  
  Beskriver tillgängliga serviceplaner för Azure Functions, inklusive värdplanen för förbrukning, och hur du väljer rätt plan. 

[!INCLUDE [Getting help note](../../includes/functions-get-help.md)]


