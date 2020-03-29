---
title: Hitta och ta bort obundna Azure-nätverkskort
description: Så här hittar och tar du bort Azure-nätverkskort som inte är kopplade till virtuella datorer med Azure CLI
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 8142b95ee666e205a8328eafd5930f1f386e49af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945125"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Hitta och ta bort obundna nätverkskort för virtuella Azure-datorer
När du tar bort en virtuell dator (VM) i Azure tas nätverkskorten inte bort som standard. Om du skapar och tar bort flera virtuella datorer fortsätter de oanvända nätverkskorten att använda de interna IP-adresslånen. När du skapar andra nätverkskort för virtuella datorer kanske de inte kan hämta ett IP-lån i undernätets adressutrymme. I den här artikeln visas hur du hittar och tar bort obundna nätverkskort.

## <a name="find-and-delete-unattached-nics"></a>Hitta och ta bort ej anslutna nätverkskort

Egenskapen *virtualMachine* för ett nätverkskort lagrar ID- och resursgruppen för den virtuella datorn som nätverkskortet är kopplat till. Följande skript loopar genom alla nätverkskort i en prenumeration och kontrollerar om egenskapen *virtualMachine* är null. Om den här egenskapen är null är nätverkskortet inte kopplat till en virtuell dator.

Om du vill visa alla obundna nätverkskort rekommenderar vi starkt att du först kör skriptet med variabeln *deleteUnattachedNics* till *0*. Om du vill ta bort alla obundna nätverkskort när du har granskat listutdata kör du skriptet med *deleteUnattachedNics* till *1*.

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

Mer information om hur du skapar och hanterar virtuella nätverk i Azure finns i [skapa och hantera VM-nätverk](tutorial-virtual-network.md).
