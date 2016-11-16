---
title: "Skapa en funktion för händelsebearbetning | Microsoft Docs"
description: "Använd Azure Functions och skapa en C#-funktion som körs baserat på en händelsetimer."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 84bd0373-65e2-4022-bcca-2b9cd9e696f5
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/25/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2381b04e32a6d65075dadf04f717f4946ba4d464


---
# <a name="create-an-event-processing-azure-function"></a>Skapa en Azure-funktion för händelsebearbetning
Azure Functions är en händelsedriven beräkna-på-begäran-upplevelse med vilken du kan skapa schemalagda eller utlösta kodenheter som implementeras i ett flertal olika programmeringsspråk. Läs mer om Azure Functions i [Översikt över Azure Functions](functions-overview.md).

Det här ämnet visar hur du skapar en ny funktion i C# som körs baserat på en händelsetimer för att lägga till meddelanden i en lagringskö. 

## <a name="prerequisites"></a>Krav
Innan du kan skapa en funktion måste du ha ett aktivt Azure-konto. Om du inte redan har ett Azure-konto, [finns kostnadsfria konton tillgängliga](https://azure.microsoft.com/free/).

## <a name="create-a-timertriggered-function-from-the-template"></a>Skapa en timerutlöst funktion från mallen
En funktionsapp är värd för körningen av dina funktioner i Azure. Innan du kan skapa en funktion måste du ha ett aktivt Azure-konto. Om du inte redan har ett Azure-konto, [finns kostnadsfria konton tillgängliga](https://azure.microsoft.com/free/). 

1. Gå till [Azure Functions-portalen](https://functions.azure.com/signin) och logga in med ditt Azure-konto.
2. Om du har en befintlig funktionsapp som du kan använda, kan du välja den från **Dina funktionsappar** och sedan klicka på **Öppna**. För att skapa en ny funktionsapp, skriver du in ett unikt **namn** för din nya funktionsapp eller accepterar det genererade namnet, väljer **Region** och klickar på **Skapa + Kom igång**. 
3. Klicka på **+ ny funktion** > **TimerTrigger - C#** > **Skapa** i funktionsappen. Detta skapar en funktion med ett standardnamn som körs i standardschemat en gång i minuten. 
   
    ![Skapa en ny timerutlöst funktion](./media/functions-create-an-event-processing-function/functions-create-new-timer-trigger.png)
4. I den nya funktionen klickar du på fliken **Integrera** > **Nya utdata** > **Azure Storage-kön** > **Välj**.
   
    ![Skapa en ny timerutlöst funktion](./media/functions-create-an-event-processing-function/functions-create-storage-queue-output-binding.png)
5. I **Azure Storage-köutdata** väljer du en befintlig **Lagringskontoanslutning** eller skapar en ny och klickar sedan på **Spara**. 
   
    ![Skapa en ny timerutlöst funktion](./media/functions-create-an-event-processing-function/functions-create-storage-queue-output-binding-2.png)
6. På fliken **Utveckla** ersätter du det befintliga C#-skriptet i fönstret **Kod** med följande kod:
    ```cs   
    using System;

    public static void Run(TimerInfo myTimer, out string outputQueueItem, TraceWriter log)
    {
        // Add a new scheduled message to the queue.
        outputQueueItem = $"Ping message added to the queue at: {DateTime.Now}.";

        // Also write the message to the logs.
        log.Info(outputQueueItem);
    }
    ```
   
    Den här koden lägger till ett nytt meddelande till kön med aktuellt datum och tid när funktionen körs.
7. Klicka på **Spara** och titta i fönstret **Loggar** för körning av nästa funktion.
8.  (Valfritt) Navigera till lagringskontot och verifiera att meddelanden läggs till i kön.
9. Gå tillbaka till fliken **Integrera** och ändra schemafältet till `0 0 * * * *`. Funktionen körs nu en gång i timmen. 

Detta är ett mycket förenklat exempel på en utgående bindning för både en timerutlösare och en lagringskö. Mer information finns i ämnena [Timerutlösare för Azure Functions](functions-bindings-timer.md) och [Azure Functions-utlösare och -bindningar för Azure Storage](functions-bindings-storage.md).

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Functions finns i dessa ämnen.

* [Azure Functions, info för utvecklare](functions-reference.md)  
  Info för programmerare om att koda funktioner och definiera utlösare och bindningar.
* [Testa Azure Functions](functions-test-a-function.md)  
  Beskriver olika verktyg och tekniker för att testa funktioner.
* [Så här skalar du Azure Functions](functions-scale.md)  
  Beskriver tillgängliga serviceplaner för Azure Functions, inklusive den dynamiska serviceplanen, och hur du väljer rätt plan.  

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->


