---
title: Hämta nyckelvärdespar från en punkt i tid
titleSuffix: Azure App Configuration
description: Hämta gamla nyckel-värde-par med ögonblicksbilder av point-in-time i Azure App-konfiguration
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523670"
---
# <a name="point-in-time-snapshot"></a>Tidpunktsbaserad ögonblicksbild

Azure App Configuration upprätthåller en post med ändringar som gjorts i nyckel-värde-par. Den här posten innehåller en tidslinje med nyckelvärdesändringar. Du kan rekonstruera historiken för alla nyckelvärde och ange dess tidigare värde när som helst inom de senaste sju dagarna. Med den här funktionen kan du "tidsresa" bakåt och hämta ett gammalt nyckelvärde. Du kan till exempel återställa konfigurationsinställningar som användes före den senaste distributionen för att återställa programmet till den tidigare konfigurationen.

## <a name="key-value-retrieval"></a>Hämtning av nyckelvärde

Du kan använda Azure PowerShell för att hämta tidigare nyckelvärden.  Använd `az appconfig revision list`, lägga till lämpliga parametrar för att hämta de nödvändiga värdena.  Ange Azure App Configuration-instansen genom`--name {app-config-store-name}`att ange antingen butiksnamnet`--connection-string {your-connection-string}`( ) eller med hjälp av en anslutningssträng ( ). Begränsa utdata genom att ange en`--datetime`viss tidpunkt ( ) och genom`--top`att ange det maximala antalet artiklar som ska returneras ( ).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Hämta alla inspelade ändringar av dina nyckelvärden.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Hämta alla inspelade `environment` ändringar för `test` `prod`nyckeln och etiketterna och .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Hämta alla inspelade ändringar i `environment:prod`det hierarkiska nyckelutrymmet .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Hämta alla registrerade ändringar `color` för nyckeln vid en viss tidpunkt.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Hämta de senaste 10 registrerade ändringarna av dina `key`nyckelvärden och returnera endast värdena för , `label`och tidsstämpeln. `last-modified`

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ASP.NET Core-webbapp](./quickstart-aspnet-core-app.md)  
