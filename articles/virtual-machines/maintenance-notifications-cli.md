---
title: Få underhållsmeddelanden med CLI
description: Visa underhållsmeddelanden för virtuella datorer som körs i Azure och starta självbetjäningsunderhåll med hjälp av Azure CLI.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4ad57c1c71a51f948bd405a5487a1e27e36bfff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920900"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Hantera planerade underhållsmeddelanden med Hjälp av Azure CLI

**Den här artikeln gäller virtuella datorer som kör både Linux och Windows.**

Du kan använda CLI för att se när virtuella datorer är schemalagda för [underhåll](maintenance-notifications.md). Planerad underhållsinformation är tillgänglig från [az vm get-instance-view](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view).
 
Underhållsinformation returneras endast om underhåll planeras. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Starta underhåll

Följande anrop startar underhåll på `IsCustomerInitiatedMaintenanceAllowed` en virtuell dator om den är inställd på true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Klassiska distributioner

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Om du fortfarande har äldre virtuella datorer som har distribuerats med den klassiska distributionsmodellen kan du använda Azure classic CLI för att fråga efter virtuella datorer och initiera underhåll.

Kontrollera att du är i rätt läge för att arbeta med klassisk virtuell dator genom att skriva:

```
azure config mode asm
```

Om du vill hämta underhållsstatus för en virtuell dator med namnet *myVM*skriver du:

```
azure vm show myVM 
``` 

Om du vill starta underhållet på den klassiska virtuella datorn *myVM* i *myService-tjänsten* och *minDeployment-distributionen* skriver du:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Nästa steg

Du kan också hantera planerat underhåll med [Azure PowerShell](maintenance-notifications-powershell.md) eller [portalen](maintenance-notifications-portal.md).
