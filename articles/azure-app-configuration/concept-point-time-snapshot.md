---
title: Hämta nyckel/värde-par från en tidpunkt
titleSuffix: Azure App Configuration
description: Hämta gamla nyckel/värde-par med tidpunkts ögonblicks bilder i Azure App konfiguration
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77523670"
---
# <a name="point-in-time-snapshot"></a>Tidpunktsbaserad ögonblicksbild

Azure App-konfigurationen innehåller en post med ändringar som gjorts i nyckel/värde-par. Den här posten innehåller en tids linje med nyckel värdes ändringar. Du kan återskapa historiken för alla nyckel värden och ange det tidigare värdet under de senaste sju dagarna. Med hjälp av den här funktionen kan du "Time-Travel" bakåt och hämta ett gammalt nyckel värde. Du kan till exempel återställa de konfigurations inställningar som används före den senaste distributionen för att återställa programmet till den tidigare konfigurationen.

## <a name="key-value-retrieval"></a>Hämtning av nyckelvärde

Du kan använda Azure PowerShell för att hämta tidigare nyckel värden.  Använd `az appconfig revision list`och Lägg till lämpliga parametrar för att hämta de värden som krävs.  Ange Azure App konfigurations instans genom att antingen ange Store-namnet`--name {app-config-store-name}`() eller med hjälp av en anslutnings`--connection-string {your-connection-string}`sträng (). Begränsa utdata genom att ange en viss tidpunkt (`--datetime`) och genom att ange det maximala antalet objekt som ska returneras (`--top`).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Hämta alla registrerade ändringar till dina nyckel värden.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Hämta alla registrerade ändringar för nyckeln `environment` och etiketterna `test` och. `prod`

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Hämta alla registrerade ändringar i det hierarkiska nyckel `environment:prod`utrymmet.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Hämta alla registrerade ändringar för nyckeln `color` vid en viss tidpunkt.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Hämta de senaste 10 registrerade ändringarna till dina nyckel värden och returnera bara värdena för `key`, `label`och `last-modified` tidstämpelt.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ASP.NET Core-webbapp](./quickstart-aspnet-core-app.md)  
