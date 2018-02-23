---
title: "Självstudie i tillgänglighetsuppsättningar för virtuella Linux-datorer i Azure | Microsoft Docs"
description: "Läs mer om tillgänglighetsuppsättningar för virtuella Linux-datorer i Azure."
documentationcenter: 
services: virtual-machines-linux
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 10/05/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 504c4a666d1abd7a495d6759d62815f53f0b54fa
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-use-availability-sets"></a>Använda tillgänglighetsuppsättningar


I den här självstudien får du lära dig hur du ökar tillgängligheten och tillförlitligheten för dina VM-lösningar i Azure med en funktion som heter ”Tillgänglighetsuppsättningar”. Tillgänglighetsuppsättningarna ser till att de virtuella datorer som du distribuerar i Azure distribueras över flera isolerade maskinvarukluster. Detta innebär att endast en del av de virtuella datorerna påverkas om det skulle uppstå ett maskinvaru- eller programvarufel i Azure, och att din lösning fortfarande är tillgänglig och fungerar.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en tillgänglighetsuppsättning
> * Skapa en virtuell dator i en tillgänglighetsuppsättning
> * Kontrollera tillgängliga VM-storlekar


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver de här självstudierna att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="availability-set-overview"></a>Översikt över tillgänglighetsuppsättning

En tillgänglighetsuppsättning är en logisk grupperingsfunktion som du kan använda i Azure för att se till att VM-resurserna du placerar i Azure är isolerade från varandra när de distribueras i ett Azure-datacenter. Azure ser till att de virtuella datorer du placerar i en tillgänglighetsuppsättning körs över flera fysiska servrar, datarack, lagringsenheter och nätverksväxlar. Om det uppstår ett maskinvarufel eller Azure-programvarufel påverkas endast en del av dina virtuella datorer. Ditt program fungerar fortfarande och är tillgängligt för dina kunder. Tillgänglighetsuppsättningar är en viktig funktion när du vill skapa tillförlitliga molnlösningar.

Nu ska vi titta en typisk VM-baserad lösning där du kan ha fyra klientdelswebbservrar och använda två virtuella serverdelsdatorer som värd för en databas. När du använder Azure bör du definiera två tillgänglighetsuppsättningar innan du distribuerar dina virtuella datorer: en tillgänglighetsuppsättning för ”webb”-nivån och en tillgänglighetsuppsättning för ”databas”-nivån. När du skapar en ny virtuell dator kan du sedan ange tillgänglighetsuppsättningen som en parameter för kommandot ”az vm create”. Azure ser då automatiskt till att de virtuella datorer du skapar i tillgänglighetsuppsättningen är isolerade över flera fysiska maskinvaruresurser. Om det uppstår ett problem på den fysiska maskinvaran som din webbserver eller de virtuella databasdatorerna körs på fungerar fortfarande de andra instanserna av webbservern och de virtuella databasdatorerna eftersom de finns på en annan maskinvara.

Använd tillgänglighetsuppsättningar när du vill distribuera tillförlitliga VM-baserade lösningar i Azure.


## <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning

Du kan skapa en tillgänglighetsuppsättning med [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create). I det här exemplet anger vi antalet för både uppdaterings- och feldomäner till *2* för tillgänglighetsuppsättningen med namnet *myAvailabilitySet* i resursgruppen *myResourceGroupAvailability*.

Skapa en resursgrupp.

```azurecli-interactive 
az group create --name myResourceGroupAvailability --location eastus
```


```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Med tillgänglighetsuppsättningar kan du isolera resurser över feldomäner och uppdateringsdomäner. En **feldomän** representerar en isolerad resurssamling med server + nätverk + lagring. I föregående exempel anger vi att vår tillgänglighetsuppsättning ska distribueras över minst två feldomäner när våra virtuella datorer distribueras. Vi anger också att vår tillgänglighetsuppsättning ska distribueras över två **uppdateringsdomäner**.  Med två uppdateringsdomäner kommer Azures programuppdateringar till våra VM-resurser isoleras, vilket förhindrar att alla program som körs under vår VM uppdateras samtidigt.


## <a name="create-vms-inside-an-availability-set"></a>Skapa virtuella datorer i en tillgänglighetsuppsättning

De virtuella datorerna måste skapas i tillgänglighetsuppsättningen för att säkerställa att de distribueras i maskinvaran. Du kan inte lägga till en befintlig virtuell dator i en tillgänglighetsuppsättning efter att den har skapats. 

När du skapar en virtuell dator med hjälp av [az vm create](/cli/azure/vm#az_vm_create) anger du tillgänglighetsuppsättning med hjälp av parametern `--availability-set` som anger namnet på tillgänglighetsuppsättningen.

```azurecli-interactive 
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

Nu har vi två virtuella datorer i vår nya tillgänglighetsuppsättning. Eftersom de finns i samma tillgänglighetsuppsättning säkerställer Azure att de virtuella datorerna och deras resurser (inklusive datadiskar) distribueras över isolerad fysisk maskinvara. Den här distributionen garanterar mycket högre tillgänglighet för vår övergripande VM-lösning.

Om du tittar på tillgänglighetsuppsättningen i portalen (genom att gå till Resursgrupper > myResourceGroupAvailability > myAvailabilitySet), ser du hur de virtuella datorerna är distribuerade över två feldomäner och uppdateringsdomäner.

![Tillgänglighetsuppsättning i portalen](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Kontrollera tillgängliga VM-storlekar 

Du kan lägga till fler virtuella datorer i tillgänglighetsuppsättningen senare, men du måste veta vilka VM-storlekar som är tillgängliga för maskinvaran.  Använd [az vm availability-set list-sizes](/cli/azure/availability-set#az_availability_set_list_sizes) för att visa en lista över alla tillgängliga storlekar i maskinvaruklustret för tillgänglighetsuppsättningen.

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

