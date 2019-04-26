---
title: Självstudiekurs – Tillgänglighetsuppsättningar för virtuella Linux-datorer i Azure | Microsoft Docs
description: I den här självstudien får du lära dig hur du använder Azure CLI för att distribuera virtuella datorer med hög tillgänglighet i tillgänglighetsuppsättningar
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 7fd671c77b4c0897134457f5aacaabc11d568694
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60418717"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-the-azure-cli"></a>Självstudier: Skapa och distribuera virtuella datorer med hög tillgänglighet med Azure CLI

I den här självstudien får du lära dig hur du ökar tillgängligheten och tillförlitligheten för dina VM-lösningar i Azure med en funktion som heter ”Tillgänglighetsuppsättningar”. Tillgänglighetsuppsättningarna ser till att de virtuella datorer som du distribuerar i Azure distribueras över flera isolerade maskinvarukluster. Detta innebär att endast en del av de virtuella datorerna påverkas om det skulle uppstå ett maskinvaru- eller programvarufel i Azure, och att din lösning fortfarande är tillgänglig och fungerar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en tillgänglighetsuppsättning
> * Skapa en virtuell dator i en tillgänglighetsuppsättning
> * Kontrollera tillgängliga VM-storlekar

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.30 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="availability-set-overview"></a>Översikt över tillgänglighetsuppsättning

En tillgänglighetsuppsättning är en logisk grupperingsfunktion som du kan använda i Azure för att se till att VM-resurserna du placerar i Azure är isolerade från varandra när de distribueras i ett Azure-datacenter. Azure ser till att de virtuella datorer du placerar i en tillgänglighetsuppsättning körs över flera fysiska servrar, datarack, lagringsenheter och nätverksväxlar. Om det uppstår ett maskinvarufel eller Azure-programvarufel påverkas endast en del av dina virtuella datorer. Ditt program fungerar fortfarande och är tillgängligt för dina kunder. Tillgänglighetsuppsättningar är en viktig funktion när du vill skapa tillförlitliga molnlösningar.

Nu ska vi titta en typisk VM-baserad lösning där du kan ha fyra klientdelswebbservrar och använda två virtuella serverdelsdatorer som värd för en databas. När du använder Azure bör du definiera två tillgänglighetsuppsättningar innan du distribuerar dina virtuella datorer: en tillgänglighetsuppsättning för ”webb”-nivån och en tillgänglighetsuppsättning för ”databas”-nivån. När du skapar en ny virtuell dator kan du sedan ange tillgänglighetsuppsättningen som en parameter för kommandot ”az vm create”. Azure ser då automatiskt till att de virtuella datorer du skapar i tillgänglighetsuppsättningen är isolerade över flera fysiska maskinvaruresurser. Om det uppstår ett problem på den fysiska maskinvaran som din webbserver eller de virtuella databasdatorerna körs på fungerar fortfarande de andra instanserna av webbservern och de virtuella databasdatorerna eftersom de finns på en annan maskinvara.

Använd tillgänglighetsuppsättningar när du vill distribuera tillförlitliga VM-baserade lösningar i Azure.


## <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning

Du kan skapa en tillgänglighetsuppsättning med [az vm availability-set create](/cli/azure/vm/availability-set). I det här exemplet är antalet uppdaterings- och feldomäner satt till *2* för tillgänglighetsuppsättningen med namnet *myAvailabilitySet* i resursgruppen *myResourceGroupAvailability*.

Börja med att skapa en resursgrupp med [az group create](/cli/azure/group#az-group-create) och skapa sedan tillgänglighetsuppsättningen:

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Med tillgänglighetsuppsättningar kan du isolera resurser över feldomäner och uppdateringsdomäner. En **feldomän** representerar en isolerad resurssamling med server + nätverk + lagring. I föregående exempel fördelas tillgänglighetsuppsättningen över minst två feldomäner när de virtuella datorerna har distribuerats. Tillgänglighetsuppsättningen distribueras också mellan två **uppdateringsdomäner**. Med två uppdateringsdomäner kommer Azures programuppdateringar till VM-resurserna isoleras, vilket förhindrar att alla program som körs på den virtuella datorn uppdateras samtidigt.


## <a name="create-vms-inside-an-availability-set"></a>Skapa virtuella datorer i en tillgänglighetsuppsättning

De virtuella datorerna måste skapas i tillgänglighetsuppsättningen för att säkerställa att de distribueras i maskinvaran. En befintlig virtuell dator kan inte läggas till i en tillgänglighetsuppsättning när den har skapats.

När en virtuell dator skapas med [az vm create](/cli/azure/vm) använder du parametern `--availability-set` för att ange namnet på tillgänglighetsuppsättningen.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

Det finns nu två virtuella datorer i tillgänglighetsuppsättningen. Eftersom de finns i samma tillgänglighetsuppsättning säkerställer Azure att de virtuella datorerna och deras resurser (inklusive datadiskar) distribueras över isolerad fysisk maskinvara. Den här distributionen garanterar mycket högre tillgänglighet för den övergripande VM-lösningen.

Distributionen av tillgänglighetsuppsättningen kan visas i portalen genom att gå till Resursgrupper > myResourceGroupAvailability > myAvailabilitySet. Virtuella datorer distribueras via två fel- och uppdateringsdomäner, enligt följande exempel:

![Tillgänglighetsuppsättning i portalen](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Kontrollera tillgängliga VM-storlekar

Ytterligare virtuella datorer kan läggas till i tillgänglighetsuppsättningen senare, där VM-storlekar är tillgängliga i maskinvaran. Använd [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az-vm-availability-set-list-sizes) för att visa en lista över alla tillgängliga storlekar i maskinvaruklustret för tillgänglighetsuppsättningen:

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en tillgänglighetsuppsättning
> * Skapa en virtuell dator i en tillgänglighetsuppsättning
> * Kontrollera tillgängliga VM-storlekar

Gå vidare till nästa självstudie om du vill veta mer om VM-skalningsuppsättningar.

> [!div class="nextstepaction"]
> [Skapa en VM-skalningsuppsättning](tutorial-create-vmss.md)
