---
title: Aktivera och inaktivera Azure Serial Console | Microsoft-dokument
description: Aktivera och inaktivera Azure Serial Console-tjänsten
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: e09e08f8ba36cf576bc27551254225adee3bb0fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451309"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Aktivera och inaktivera Azure Serial Console

Precis som alla andra resurser kan Azure Serial Console aktiveras och inaktiveras. Serial Console är aktiverat som standard för alla prenumerationer i globala Azure. Om du inaktiverar Serial Console inaktiveras tjänsten för hela prenumerationen. Om du inaktiverar eller aktiverar Serial Console igen för en prenumeration krävs åtkomst på deltagarnivå eller högre på prenumerationen.

Du kan också inaktivera seriell konsol för en enskild vm- eller virtuell datorskalauppsättningsinstans genom att inaktivera startdiagnostik. Du kommer att kräva åtkomst på deltagarnivå eller högre på både skalningsuppsättningen för virtuella datorer/virtuella datorer och ditt startdiagnostiklagringskonto.

## <a name="vm-level-disable"></a>Inaktivera VM-nivå
Seriekonsolen kan inaktiveras för en viss skala för virtuella datorer eller virtuella datorer som har angetts genom att inaktivera inställningen för startdiagnostik. Inaktivera startdiagnostik från Azure-portalen för att inaktivera seriell konsol för den virtuella datorn eller skalningsuppsättningen för den virtuella datorn. Om du använder seriell konsol på en skalningsuppsättning för virtuella datorer kontrollerar du att du uppgraderar de uppsättning instanserna för virtuell datorskala till den senaste modellen.


## <a name="subscription-level-enabledisable"></a>Aktivera/inaktivera prenumerationsnivå

> [!NOTE]
> Se till att du är i rätt moln (Azure Public Cloud, Azure US Government Cloud) innan du kör det här kommandot. Du kan `az cloud list` kontrollera med och `az cloud set -n <Name of cloud>`ställa in molnet med .

### <a name="azure-cli"></a>Azure CLI

Seriell konsol kan inaktiveras och återaktiveras för en hel prenumeration med hjälp av följande kommandon i Azure CLI (du kan använda "Prova"-knappen för att starta en instans av Azure Cloud Shell där du kan köra kommandona):

Om du vill inaktivera seriell konsol för en prenumeration använder du följande kommandon:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Om du vill aktivera seriekonsol för en prenumeration använder du följande kommandon:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Om du vill ha den aktuella aktiverade/inaktiverade statusen seriell konsol för en prenumeration använder du följande kommandon:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

Seriell konsol kan också aktiveras och inaktiveras med PowerShell.

Om du vill inaktivera seriell konsol för en prenumeration använder du följande kommandon:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Om du vill aktivera seriekonsol för en prenumeration använder du följande kommandon:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Nästa steg
* Läs mer om [virtuella Azure-konsol för virtuella Linux-datorer](./serial-console-linux.md)
* Läs mer om [virtuella Azure-seriekonsolen för Virtuella Datorer i Windows](./serial-console-windows.md)
* Lär dig mer om [energisparalternativ i Azure Serial Console](./serial-console-power-options.md)