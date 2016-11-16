---
title: "Skapa en Azure-funktion som binder till en Azure-tjänst | Microsoft Docs"
description: "Skapa en Azure-funktion, ett serverlöst program, som samverkar med andra Azure-tjänster."
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
ms.date: 10/25/2016
ms.author: rachelap@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9ffd199c9e3c621a808ade109ed044b6c9b689b7


---
# <a name="create-an-azure-function-which-binds-to-an-azure-service"></a>Skapa en Azure-funktion som binder till en Azure-tjänst
[!INCLUDE [Getting Started Note](../../includes/functions-getting-started.md)]

I den här korta videon lära du dig hur du skapar en Azure-funktion som lyssnar efter meddelanden i en Azure-kö och kopierar meddelandena till en Azure-blob.

## <a name="watch-the-video"></a>Titta på videon
>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-an-Azure-Function-which-binds-to-an-Azure-service/player]
>
>

## <a name="create-an-input-queue-trigger-function"></a>Skapa en utlösarfunktion för indatakö
Syftet med den här funktionen är att skriva ett meddelande till en kö var 10:e sekund. För att åstadkomma detta måste du skapa funktionen och meddelandeköer och lägga till kod för att skriva meddelanden till de nyligen skapade köerna.

1. Gå till Azure Portal och leta upp din Azure-funktionsapp.
2. Klicka på **Ny funktion** > **TimerTrigger - Node**. Namnge funktionen **FunctionsBindingsDemo1**
3. Ange värdet "0/10 * * * * *" för schemat. Det här värdet är i form av ett cron-uttryck. Detta schemalägger att timern ska köras var 10:e sekund.
4. Klicka på knappen **Skapa** för att skapa funktionen.
   
    ![Lägga till en utlösande timer-funktion](./media/functions-create-an-azure-connected-function/new-trigger-timer-function.png)
5. Kontrollera att funktionen fungerar genom att granska aktiviteten i loggen. Du kanske måste klicka på länken **Loggar** i det övre högra hörnet för att visa loggfönstret.
   
   ![Verifiera att funktionen fungerar genom att granska loggen](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-view-log.png)

### <a name="add-a-message-queue"></a>Lägg till en meddelandekö
1. Gå till fliken **Integrera**.
2. Välj **Ny utdata** > **Azure Storage-kö** > **Välj**.
3. Ange **myQueueItem** i textrutan **Meddelandets parameternamn**.
4. Välj ett lagringskonto eller klicka på **Nytt** för att skapa ett lagringskonto om du inte redan har ett.
5. Ange **funktions-bindningar** i textrutanden **Könamn**.
6. Klicka på **Spara**.  
   
   ![Lägga till en utlösande timer-funktion](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab.png)

### <a name="write-to-the-message-queue"></a>Skriva till meddelandekön
1. Gå tillbaka till fliken **Utveckla** och lägg till följande kod till funktionen efter den befintliga koden:
   
    ```javascript
   
    function myQueueItem() 
      {
        return {
        msg: "some message goes here",
        time: "time goes here"
      }
    }
   
    ```
2. Modifiera den befintliga funktionskoden för att anropa den kod som lades till i steg 1. Infoga följande kod runt rad 9 i funktionen, efter instruktionen *om*.
   
    ```javascript
   
    var toBeQed = myQueueItem();
    toBeQed.time = timeStamp;
    context.bindings.myQueue = toBeQed;
   
    ```
   
    Den här koden skapar en **myQueueItem** och ställer in dess **tid**-egenskap till aktuell timeStamp. Den lägger sedan till det nya köobjektet till kontextens myQueue-binnding.
3. Klicka på **Spara och kör**.
4. Kontrollera koden fungerar genom att granska kön i Visual Studio.
   
   * Öppna Visual Studio och gå till **Visa** > **Moln** **Explorer**.
   * Leta upp lagringskontot och kön **funktions bindningar** som du använde när du skapade myQueue-kön. Du bör se rader av loggdata. Du kan behöva logga in på Azure via Visual Studio.  

## <a name="create-an-output-queue-trigger-function"></a>Skapa en utlösarfunktion för utdatakö
1. Klicka på **Ny funktion** > **QueueTrigger - C#**. Namnge funktionen **FunctionsBindingsDemo2**. Observera att du kan blanda språk i samma funktionsapp (i detta fall Node och C#).
2. Ange **funktions-bindningar** i fältet **Könamn**."
3. Välj ett lagringskonto eller skapa ett nytt.
4. Klicka på **Skapa**
5. Kontrollera den nya funktionen fungerar genom att granska både funktionens logg och Visual Studio efter uppdateringar. Funktionens loggen visar att funktionen kör och att objekten togs bort från kön. Eftersom funktionen är bunden till **funktions-bindningarnas** utdatakö som en indatautlösare, visar en uppdatering av **funktions-bindningarnas** kö i Visual Studio att objekten är borta. De har tagits bort från kön.   
   
   ![Lägg till en timer-funktion för utdatakö](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab.png)   

### <a name="modify-the-queue-item-type-from-json-to-object"></a>Ändra köobjekttyp från JSON till objekt
1. Ersätt koden i **FunctionsBindingsDemo2** med följande kod:    
   
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
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.Msg} | {myQueueItem.Time}");
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
   
    Denna kod lägger till två klasser, **TableItem** och **QItem**, som används för att läsa och skriva till köer. Dessutom har funktionen **Kör** ändrats för att godkänna parametern **QItem** och **TraceWriter** i stället för en **sträng** och en **TraceWriter**. 
2. Klicka på knappen **Spara**.
3. Kontrollera koden fungerar genom att granska loggen. Observera att Azure Functions automatiskt serialiserar och deserialiserar objektet för dig, vilket gör det lätt att få åtkomst till kön på ett objektorienterat sätt för att skicka runt data. 

## <a name="store-messages-in-an-azure-table"></a>Spara meddelanden i en Azure-tabell
När du nu har köer som arbetar tillsammans är det dags att lägga till en Azure-tabell för permanent lagring av ködatan.

1. Gå till fliken **Integrera**.
2. Skapa en Azure Storage-tabell för utdata och ge den namnet **myTable**.
3. Svara **functionsbindings** på frågan "Till vilken tabell ska datan skrivas?".
4. Ändra inställningen för **PartitionKey** från **{projekt-id}** till **{partition}**.
5. Välj ett lagringskonto eller skapa ett nytt.
6. Klicka på **Spara**.
7. Gå till fliken **Utveckla**.
8. Skapa klassen **TableItem** som representerar en Azure-tabell och ändra körfunktionen för att godkänna det nyligen skapade objektet TableItem. Observera att du måste använda egenskaperna **PartitionKey** och **RowKey** för att det ska fungera.
   
    ```cs
   
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
   
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.RowKey} | {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
    ```
9. Klicka på **Spara**.
10. Kontrollera att koden fungerar genom att granska i funktionens loggar och i Visual Studio. För att kontrollera i Visual Studio använder du **Cloud Explorer** för att navigera till Azure-tabellen **functionbindings** och kontrollera att det finns rader i den.

[!INCLUDE [Getting Started Note](../../includes/functions-bindings-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->


