---
title: Så här inaktiverar du funktioner i Azure Functions
description: Lär dig hur du inaktiverar och aktiverar funktioner i Azure Functions.
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: fb8edf635856078655b8640ba0e1723fdd5e8a5a
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116138"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Så här inaktiverar du funktioner i Azure Functions

Den här artikeln förklarar hur du inaktiverar en funktion i Azure Functions. För att *inaktivera* en funktion innebär körningen att ignorera den automatiska utlösaren som definierats för funktionen. På så sätt kan du förhindra att en speciell funktion körs utan att stoppa hela Function-appen.

Det rekommenderade sättet att inaktivera en funktion är genom att använda en app-inställning i formatet `AzureWebJobs.<FUNCTION_NAME>.Disabled`. Du kan skapa och ändra den här program inställningen på flera olika sätt, inklusive genom att använda [Azure CLI](/cli/azure/) och från funktionens **Hantera** -flik i [Azure Portal](https://portal.azure.com). 

> [!NOTE]  
> När du inaktiverar en HTTP-utlöst funktion med hjälp av metoderna som beskrivs i den här artikeln, kan slut punkten fortfarande vara tillgänglig när den körs på den lokala datorn.  

## <a name="use-the-azure-cli"></a>Använda Azure CLI

I Azure CLI använder du kommandot [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) för att skapa och ändra appens inställning. Följande kommando inaktiverar en funktion med namnet `QueueTrigger` genom att skapa en app-inställning med namnet `AzureWebJobs.QueueTrigger.Disabled` ange den som `true`. 

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

## <a name="use-the-portal"></a>Använda portalen

Du kan också använda **funktions tillstånds** växeln på funktionens **Hantera** -flik. Växeln fungerar genom att skapa och ta bort `AzureWebJobs.<FUNCTION_NAME>.Disabled` app-inställningen.

![Funktions tillstånds växel](media/disable-function/function-state-switch.png)

## <a name="other-methods"></a>Andra metoder

Även om program inställnings metoden rekommenderas för alla språk och alla körnings versioner finns det flera andra sätt att inaktivera funktioner. Dessa metoder, som varierar beroende på språk-och körnings version, bevaras för bakåtkompatibilitet. 

### <a name="c-class-libraries"></a>C#klass bibliotek

I en klass biblioteks funktion kan du också använda attributet `Disable` för att förhindra att funktionen utlöses. Du kan använda attributet utan en konstruktor-parameter, som du ser i följande exempel:

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
> Attributet `Disabled` är det enda sättet att inaktivera en klass biblioteks funktion. Den genererade *Function. JSON* -filen för en klass biblioteks funktion är inte avsedd att redige ras direkt. Om du redigerar filen får du ingen påverkan på vad du gör med egenskapen `disabled`.
>
> Samma sak gäller för **funktions tillstånds** växeln på fliken **Hantera** , eftersom den fungerar genom att ändra *Function. JSON* -filen.
>
> Observera också att portalen kan indikera att funktionen är inaktive rad när den inte är det.

### <a name="functions-1x---scripting-languages"></a>Functions 1. x – skript språk

I version 1. x kan du också använda egenskapen `disabled` i filen *Function. JSON* för att se till att körnings miljön inte utlöser en funktion. Den här metoden fungerar bara för skript språk som C# skript och Java Script. Egenskapen `disabled` kan anges till `true` eller till namnet på en app-inställning:

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

I det andra exemplet inaktive ras funktionen när det finns en app-inställning med namnet IS_DISABLED och är inställd på `true` eller 1.

Du kan redigera filen i Azure Portal eller använda **funktions tillstånds** växeln på funktionens **Hantera** -flik. Portal växeln fungerar genom att ändra *Function. JSON* -filen.

![Funktions tillstånds växel](media/disable-function/function-state-switch.png)

## <a name="next-steps"></a>Nästa steg

Den här artikeln är att inaktivera automatiska utlösare. Mer information om utlösare finns i [utlösare och bindningar](functions-triggers-bindings.md).
