---
title: Hitta och ta bort ej anslutna nätverkskort i Azure | Microsoft Docs
description: Hitta och ta bort Azure-nätverkskort som inte är kopplade till virtuella datorer med Azure CLI
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: dd4fcfe80818bd8e1e87851f4b5131aac73ceeb5
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671509"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Hitta och ta bort icke anslutna nätverksgränssnittet kort (NIC) för virtuella Azure-datorer
När du tar bort en virtuell dator (VM) i Azure, raderas inte nätverkskort (NIC) som standard. Om du skapar och ta bort flera virtuella datorer är oanvända nätverkskort att fortsätta att använda de interna IP-adresser. När du skapar andra VM-nätverkskort kan kanske de inte att hämta ett IP-adresslån i adressutrymmet för undernätet. Den här artikeln visar hur du hitta och ta bort ej anslutna nätverkskort.

## <a name="find-and-delete-unattached-nics"></a>Hitta och ta bort ej anslutna nätverkskort

Den *virtualMachine* egenskap för ett nätverkskort sparar gruppen ID och resurs för den virtuella datorn som nätverkskortet är kopplat till. Följande skript igenom alla nätverkskort i en prenumeration och kontrollerar om den *virtualMachine* -egenskapen är null. Om den här egenskapen är null, är nätverkskortet inte kopplat till en virtuell dator.

Om du vill visa alla ej anslutna nätverkskort, det har rekommenderar första kör skriptet med den *deleteUnattachedNics* variabeln *0*. Ta bort alla ej anslutna nätverkskort när du har läst bland listans utdata genom att köra skriptet med *deleteUnattachedNics* till *1*.

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skapar och hanterar virtuella nätverk i Azure finns i [skapa och hantera Virtuella datornätverk](tutorial-virtual-network.md).
