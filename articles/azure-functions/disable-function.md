---
title: Så här inaktiverar du funktioner i Azure Functions
description: Lär dig hur du inaktiverar och aktiverar funktioner i Azure Functions 1.x och 2.x.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: glenga
ms.openlocfilehash: ab9cf429a0af69db116fe910ab90b83d404afbb7
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44093642"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Så här inaktiverar du funktioner i Azure Functions

Den här artikeln förklarar hur du inaktiverar en funktion i Azure Functions. Att *inaktivera* en funktion som innebär att göra körningen ignorerar automatisk utlösare som har definierats för funktionen. På samma sätt som du som är beroende av runtime-versionen och programmeringsspråket:

* Functions 1.x
  * Skriptspråk
  * C#-klassbibliotek
* Functions 2.x
  * Ett sätt för alla språk
  * Valfritt sätt för C#-klassbibliotek

## <a name="functions-1x---scripting-languages"></a>Fungerar 1.x - skriptspråk

Skriptspråk som C#-skript och JavaScript, använder du den `disabled` egenskapen för den *function.json* fil som talar om körningen inte att utlösa en funktion. Den här egenskapen kan anges till `true` eller namnet på en app-inställning:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
eller 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

I det andra exemplet funktionen inaktiveras när det finns en appinställning som heter IS_DISABLED och är inställd på `true` eller 1.

Du kan redigera filen i Azure portal eller Använd den **funktionstillstånd** växla i funktionens **hantera** fliken. Växeln portal fungerar genom att ändra den *function.json* fil.

![Funktionen tillstånd switch](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Fungerar 1.x - C#-klassbibliotek

I en funktioner 1.x-klassbiblioteket, använder du en `Disable` attribut för att förhindra att en funktion som utlöses. Du kan använda attributet utan en konstruktorparametern som visas i följande exempel:

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Attributet utan en konstruktorparametern kräver att du kompilera om och omdistribuera projekt om du vill ändra funktionens inaktiverat tillstånd. Ett mer flexibelt sätt att använda attributet är att inkludera en konstruktorparametern som refererar till en boolesk appinställningen, som visas i följande exempel:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Den här metoden kan du aktivera och inaktivera funktionen genom att ändra appinställningen, utan att kompilera eller omdistribuera. Ändra en appinställning orsakar funktionsappen måste startas om, så att ändringen inaktiverat tillstånd känns omedelbart.

> [!IMPORTANT]
> Den `Disabled` attributet är det enda sättet att inaktivera funktionen för biblioteket en klass. Den genererade *function.json* -fil för en klass biblioteket funktion inte är avsedd att redigeras direkt. Om du redigerar filen, oavsett vad du gör att den `disabled` egenskapen har ingen effekt.
>
> Samma sak gäller den **fungera tillstånd** växla den **hantera** fliken eftersom det fungerar genom att ändra den *function.json* fil.
>
> Observera också att portalen får ange funktionen ska inaktiveras när det inte.



## <a name="functions-2x---all-languages"></a>Fungerar 2.x - alla språk

I funktioner 2.x du inaktiverar en funktion med hjälp av en appinställning. Till exempel att inaktivera en funktion med namnet `QueueTrigger`, du skapar en app-inställning med namnet `AzureWebJobs.QueueTrigger.Disabled`, och ge den värdet `true`. Om du vill aktivera funktionen inställd appinställningen `false`. Du kan också använda den **funktionstillstånd** växla i funktionens **hantera** fliken. Växeln fungerar genom att skapa och ta bort den `AzureWebJobs.<functionname>.Disabled` appinställningen.

![Funktionen tillstånd switch](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Fungerar 2.x - C#-klassbibliotek

I en funktioner 2.x-klassbiblioteket rekommenderar vi att du använder den metod som fungerar för alla språk. Men om du vill kan du kan [använda attributet inaktivera funktioner i 1.x](#functions-1x---c-class-libraries).

## <a name="next-steps"></a>Nästa steg

Den här artikeln handlar om att inaktivera automatisk utlösare. Mer information om utlösare finns i [utlösare och bindningar](functions-triggers-bindings.md).
