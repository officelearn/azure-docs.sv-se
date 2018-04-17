---
title: Hitta och ta bort ej anslutna Azure nätverkskort | Microsoft Docs
description: Hitta och ta bort Azure-nätverkskort som inte är kopplade till virtuella datorer med Azure CLI 2.0
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: iainfou
ms.openlocfilehash: c730866fe73305a37b37038699a7f729085a16aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Hitta och ta bort beräkningsfunktionen nätverksgränssnittet kort (NIC) för virtuella Azure-datorer
När du tar bort en virtuell dator (VM) i Azure, raderas inte nätverkskort (NIC) som standard. Om du skapar och ta bort flera virtuella datorer fortsätta oanvända nätverkskorten att använda de interna IP-adresser. När du skapar andra Virtuella nätverkskort kan kanske de inte att hämta ett IP-adresslån i undernätet adressutrymme. Den här artikeln visar hur du hittar och ta bort ej anslutna nätverkskort.

## <a name="find-and-delete-unattached-nics"></a>Hitta och ta bort ej anslutna nätverkskort

Den *virtualMachine* egenskap för ett nätverkskort sparar gruppen ID och resursen i det Virtuella nätverkskortet som är kopplad till. Följande skript loop genom alla nätverkskort i en prenumeration och kontrollerar om den *virtualMachine* -egenskapen är null. Om den här egenskapen är null, är nätverkskortet inte kopplad till en virtuell dator.

Om du vill visa alla ej anslutna nätverkskort, den har rekommenderar för första gången du kör skriptet med den *deleteUnattachedNics* variabeln *0*. Kör skriptet med att ta bort alla nätverkskort som när du har granskat listan utdata, *deleteUnattachedNics* till *1*.

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

Mer information om hur du skapar och hanterar virtuella nätverk i Azure finns [skapa och hantera Virtuella datornätverk](tutorial-virtual-network.md).
