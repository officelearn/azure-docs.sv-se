---
title: Aktivera och inaktivera Azures serie konsol | Microsoft Docs
description: Så här aktiverar och inaktiverar du tjänsten Azure Serial Console
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
ms.openlocfilehash: 1c1fe208c77142351a786fa636896e64a8a467d7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129658"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Aktivera och inaktivera Azures serie konsol

Precis som med andra resurser kan Azures serie konsol aktive ras och inaktive ras. Serie konsolen är aktive rad som standard för alla prenumerationer i Global Azure. Vid inaktive ring av serie konsolen inaktive ras tjänsten för hela prenumerationen för närvarande. Om du inaktiverar eller återaktiverar en serie konsol för en prenumeration krävs åtkomst till deltagar nivå eller högre i prenumerationen.

Du kan också inaktivera en serie konsol för en enskild virtuell dator eller en virtuell dators skalnings uppsättnings instans genom att inaktivera startdiagnostik. Du måste ha åtkomst till deltagar nivå eller mer på både virtuell dator/virtuell dators skalnings uppsättning och ditt lagrings konto för startdiagnostik.

## <a name="vm-level-disable"></a>Inaktivera för VM-nivå
Serie konsolen kan inaktive ras för en angiven virtuell dator eller en virtuell dators skalnings uppsättning genom att inaktivera inställningen för startdiagnostik. Inaktivera startdiagnostik från Azure Portal för att inaktivera serie konsolen för den virtuella datorn eller skalnings uppsättningen för den virtuella datorn. Om du använder en seriell konsol på en skalnings uppsättning för virtuella datorer måste du uppgradera de virtuella datorernas skalnings uppsättnings instanser till den senaste modellen.


## <a name="subscription-level-disable"></a>Prenumerationsnivå inaktivera

### <a name="azure-cli"></a>Azure CLI

Seriell konsol kan inaktive ras och aktive ras för en hel prenumeration med hjälp av följande kommandon i Azure CLI:

Om du vill inaktivera en serie konsol för en prenumeration använder du följande kommandon:
```azurecli-interactive
subscriptionId=$(az account show -o=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default"
```

Om du vill aktivera en prenumerations serie konsol använder du följande kommandon:
```azurecli-interactive
subscriptionId=$(az account show -o=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default"
```

Använd följande kommandon för att hämta den aktuella aktiverade/inaktiverade statusen för en prenumerations serie konsol:
```azurecli-interactive
subscriptionId=$(az account show -o=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" -o=json | jq .properties
```

### <a name="powershell"></a>PowerShell

Seriell konsol kan också aktive ras och inaktive ras med PowerShell.

Om du vill inaktivera en serie konsol för en prenumeration använder du följande kommandon:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Om du vill aktivera en prenumerations serie konsol använder du följande kommandon:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Azures serie konsol för virtuella Linux-datorer](./serial-console-linux.md)
* Lär dig mer om [Azures serie konsol för virtuella Windows-datorer](./serial-console-windows.md)
* Lär dig mer om [energispar funktioner i Azures serie konsol](./serial-console-power-options.md)