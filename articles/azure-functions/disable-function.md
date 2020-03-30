---
title: Inaktivera funktioner i Azure Functions
description: Lär dig hur du inaktiverar och aktiverar funktioner i Azure Functions.
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: fb8edf635856078655b8640ba0e1723fdd5e8a5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77116138"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Inaktivera funktioner i Azure Functions

I den här artikeln beskrivs hur du inaktiverar en funktion i Azure Functions. Om du vill *inaktivera* en funktion innebär det att köra bort den automatiska utlösaren som har definierats för funktionen. På så sätt kan du förhindra att en viss funktion körs utan att stoppa hela funktionsappen.

Det rekommenderade sättet att inaktivera en funktion är `AzureWebJobs.<FUNCTION_NAME>.Disabled`att använda en appinställning i formatet . Du kan skapa och ändra den här programinställningen på flera olika sätt, bland annat genom att använda [Azure CLI](/cli/azure/) och från fliken **Hantera** i [Azure-portalen](https://portal.azure.com). 

> [!NOTE]  
> När du inaktiverar en HTTP-utlöst funktion med hjälp av de metoder som beskrivs i den här artikeln kan slutpunkten fortfarande vara tillgänglig när den körs på den lokala datorn.  

## <a name="use-the-azure-cli"></a>Använda Azure CLI

I Azure CLI använder [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) du kommandot för att skapa och ändra appinställningen. Följande kommando inaktiverar en `QueueTrigger` funktion som heter `AzureWebJobs.QueueTrigger.Disabled` genom `true`att skapa en appinställning med namnet set it till . 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Om du vill återaktivera funktionen kör du `false`samma kommando igen med värdet .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Använda portalen

Du kan också använda **funktionstillståndsknappen** på fliken **Hantera.** Växeln fungerar genom att `AzureWebJobs.<FUNCTION_NAME>.Disabled` skapa och ta bort appinställningen.

![Funktionstillståndsbrytare](media/disable-function/function-state-switch.png)

## <a name="other-methods"></a>Andra metoder

Även om programinställningsmetoden rekommenderas för alla språk och alla körningsversioner, finns det flera andra sätt att inaktivera funktioner. Dessa metoder, som varierar beroende på språk och runtime-version, bibehålls för bakåtkompatibilitet. 

### <a name="c-class-libraries"></a>C#-klassbibliotek

I en klassbiblioteksfunktion kan `Disable` du också använda attributet för att förhindra att funktionen utlöses. Du kan använda attributet utan en konstruktorparameter, som visas i följande exempel:

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

Attributet utan en konstruktorparameter kräver att du kompilerar om och distribuerar om projektet för att ändra funktionens inaktiverade tillstånd. Ett mer flexibelt sätt att använda attributet är att inkludera en konstruktorparameter som refererar till en boolesk appinställning, som visas i följande exempel:

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

Med den här metoden kan du aktivera och inaktivera funktionen genom att ändra appinställningen, utan att kompilera om eller distribuera om. Om du ändrar en appinställning startas funktionsappen om, så att ändringen av inaktiverat tillstånd känns igen omedelbart.

> [!IMPORTANT]
> Attributet `Disabled` är det enda sättet att inaktivera en klassbiblioteksfunktion. Den genererade *function.json-filen* för en klassbiblioteksfunktion är inte avsedd att redigeras direkt. Om du redigerar filen har `disabled` det du inte har någon effekt på det du gör med egenskapen.
>
> Detsamma gäller för **funktionstillståndsknappen** på fliken **Hantera,** eftersom den fungerar genom att ändra *filen function.json.*
>
> Observera också att portalen kan indikera att funktionen är inaktiverad när den inte är det.

### <a name="functions-1x---scripting-languages"></a>Funktioner 1.x - skriptspråk

I version 1.x kan du `disabled` också använda egenskapen för *filen function.json* för att tala om för körningen att inte utlösa en funktion. Den här metoden fungerar bara för skriptspråk som C#-skript och JavaScript. Egenskapen `disabled` kan ställas `true` in på eller namnet på en appinställning:

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

I det andra exemplet inaktiveras funktionen när det finns en appinställning som `true` heter IS_DISABLED och är inställd på eller 1.

Du kan redigera filen i Azure-portalen eller använda **funktionstillståndsknappen** på fliken **Hantera.** Portalväxeln fungerar genom att ändra *filen function.json.*

![Funktionstillståndsbrytare](media/disable-function/function-state-switch.png)

## <a name="next-steps"></a>Nästa steg

Den här artikeln handlar om att inaktivera automatiska utlösare. Mer information om utlösare finns i [Utlösare och bindningar](functions-triggers-bindings.md).
