---
title: Så här inaktiverar du funktioner i Azure Functions
description: Lär dig hur du inaktiverar och aktiverar funktioner i Azure Functions 1. x och 2. x.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: 183056d01146194b2854a70df790802e1a0bb839
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782239"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Så här inaktiverar du funktioner i Azure Functions

Den här artikeln förklarar hur du inaktiverar en funktion i Azure Functions. För att *inaktivera* en funktion innebär körningen att ignorera den automatiska utlösaren som definierats för funktionen. Hur du gör detta beror på körnings versionen och programmeringsspråket:

* Functions 2. x:
  * Ett sätt för alla språk
  * Valfritt sätt för C# klass bibliotek
* Functions 1. x:
  * Skript språk
  * C#klass bibliotek

## <a name="functions-2x---all-languages"></a>Functions 2. x-alla språk

I functions 2. x inaktiverar du en funktion med hjälp av en app-inställning i `AzureWebJobs.<FUNCTION_NAME>.Disabled`formatet. Du kan skapa och ändra den här inställningen program mässigt med hjälp av Azure CLI. Du kan också göra detta från funktionens **Hantera** -flik i [Azure Portal](https://portal.azure.com). 

### <a name="azure-cli"></a>Azure CLI

I Azure CLI använder [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) du kommandot för att skapa och ändra appens inställning. Följande kommando inaktiverar en funktion som heter `QueueTrigger` genom att skapa en app-inställning med namnet `AzureWebJobs.QueueTrigger.Disabled` ange den `true`som. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Om du vill aktivera funktionen igen kör du samma kommando med värdet `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

### <a name="portal"></a>Portalen

Du kan också använda **funktions tillstånds** växeln på funktionens **Hantera** -flik. Växeln fungerar genom att du `AzureWebJobs.<FUNCTION_NAME>.Disabled` skapar och tar bort appens inställningar.

![Funktions tillstånds växel](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Funktioner 2. x- C# klass bibliotek

I ett klass bibliotek för funktioner 2. x rekommenderar vi att du använder den metod som fungerar för alla språk. Men om du vill kan du [använda attributet Disable som i functions 1. x](#functions-1x---c-class-libraries).

## <a name="functions-1x---scripting-languages"></a>Functions 1. x – skript språk

För skript språk som C# skript och Java Script använder `disabled` du egenskapen för *Function. JSON* -filen för att se till att körnings miljön inte utlöser en funktion. Den här egenskapen kan anges till `true` eller till namnet på en app-inställning:

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

I det andra exemplet inaktive ras funktionen när det finns en app-inställning med namnet IS_DISABLED och har värdet `true` eller 1.

Du kan redigera filen i Azure Portal eller använda **funktions tillstånds** växeln på funktionens **Hantera** -flik. Portal växeln fungerar genom att ändra *Function. JSON* -filen.

![Funktions tillstånds växel](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Funktioner 1. x- C# klass bibliotek

I ett klass bibliotek av funktioner 1. x använder du ett `Disable` -attribut för att förhindra att en funktion utlöses. Du kan använda attributet utan en konstruktor-parameter, som du ser i följande exempel:

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

Attributet utan en konstruktor-parameter kräver att du kompilerar om och distribuerar om projektet för att ändra funktionens inaktiverade tillstånd. Ett mer flexibelt sätt att använda attributet är att inkludera en konstruktor-parameter som refererar till en boolesk app-inställning, som visas i följande exempel:

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

Med den här metoden kan du aktivera och inaktivera funktionen genom att ändra appens inställning utan att kompilera om eller distribuera om. Om du ändrar en app-inställning startar Function-appen om, så att den inaktiverade tillstånds ändringen kan identifieras direkt.

> [!IMPORTANT]
> `Disabled` Attributet är det enda sättet att inaktivera en klass biblioteks funktion. Den genererade *Function. JSON* -filen för en klass biblioteks funktion är inte avsedd att redige ras direkt. Om du redigerar filen får du ingen påverkan på vad du `disabled` gör.
>
> Samma sak gäller för **funktions tillstånds** växeln på fliken **Hantera** , eftersom den fungerar genom att ändra *Function. JSON* -filen.
>
> Observera också att portalen kan indikera att funktionen är inaktive rad när den inte är det.

## <a name="next-steps"></a>Nästa steg

Den här artikeln är att inaktivera automatiska utlösare. Mer information om utlösare finns i utlösare [och bindningar](functions-triggers-bindings.md).
