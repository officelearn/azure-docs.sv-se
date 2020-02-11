---
title: Få underhålls meddelanden med CLI
description: Visa underhålls meddelanden för virtuella datorer som körs i Azure och starta självbetjänings underhållet med hjälp av Azure CLI.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 5750d7cc6219714849aaf3e47b23708b54eefab1
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77115721"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Hantering av planerade underhålls aviseringar med hjälp av Azure CLI

**Den här artikeln gäller för virtuella datorer som kör både Linux och Windows.**

Du kan använda CLI för att se när virtuella datorer är schemalagda för [Underhåll](maintenance-notifications.md). Information om planerat underhåll är tillgänglig från [AZ VM get-instance-View](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view).
 
Underhålls information returneras endast om underhållet är planerat. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Starta underhåll

Följande anrop startar underhåll på en virtuell dator om `IsCustomerInitiatedMaintenanceAllowed` har värdet true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Klassiska distributioner


Om du fortfarande har äldre virtuella datorer som har distribuerats med den klassiska distributions modellen kan du använda den klassiska Azure CLI-funktionen för att fråga efter virtuella datorer och starta underhåll.

Kontrol lera att du har rätt läge för att arbeta med den klassiska virtuella datorn genom att skriva:

```
azure config mode asm
```

Om du vill hämta underhålls statusen för en virtuell dator med namnet *myVM*, skriver du:

```
azure vm show myVM 
``` 

För att starta underhåll på den klassiska virtuella datorn med namnet *myVM* i tjänsten för *fjärrdistribution* och *distribution av* distribution skriver du:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Nästa steg

Du kan också hantera planerat underhåll med hjälp av [Azure PowerShell](maintenance-notifications-powershell.md) eller [portalen](maintenance-notifications-portal.md).
