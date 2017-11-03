---
title: "Tillgänglighetsuppsättningar självstudier för Linux virtuella datorer i Azure | Microsoft Docs"
description: "Läs mer om Tillgänglighetsuppsättningarna för Linux virtuella datorer i Azure."
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
ms.openlocfilehash: e7780a29f6633b444608d96012fabe67b9b6d924
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-availability-sets"></a>Hur du använder tillgänglighetsuppsättningar


I kursen får lära du att öka tillgängligheten och tillförlitligheten hos dina virtuella lösningar på Azure med hjälp av en funktion som kallas Tillgänglighetsuppsättningar. Tillgänglighetsuppsättningar se till att de virtuella datorerna som du distribuerar i Azure är fördelade på flera isolerade maskinvara kluster. Om du gör detta säkerställer att endast en delmängd av dina virtuella datorer som påverkas om ett maskinvaru- eller fel i Azure händer, och att din lösning är tillgänglig och fungerar.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en tillgänglighetsuppsättning
> * Skapa en virtuell dator i en tillgänglighetsuppsättning
> * Kontrollera tillgängliga storlekar på VM


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="availability-set-overview"></a>Översikt över tillgänglighetsuppsättning

En Tillgänglighetsuppsättning är en logisk gruppering funktion som du kan använda i Azure för att säkerställa att VM-resurser som du placerar i den isolerade från varandra när de distribueras i ett Azure-datacenter. Azure säkerställer att de virtuella datorerna som du placerar i en Tillgänglighetsuppsättning körs över flera fysiska servrar, beräkna rack, lagringsenheter och nätverksväxlar. Om ett maskinvaru- eller Azure programvarufel inträffar bara en del av dina virtuella datorer som påverkas och tillämpningsprogrammet övergripande förblir upp och fortsätter att vara tillgängliga för dina kunder. Tillgänglighetsuppsättningar är en viktig funktion när du vill skapa tillförlitliga molnlösningar.

Nu ska vi titta en typisk VM-baserad lösning där du kan ha 4 frontend-webbservrar och använda 2 backend-VMs som värd för en databas. Med Azure, skulle du vill definiera två tillgänglighetsuppsättningar innan du distribuerar dina virtuella datorer: en tillgänglighetsuppsättning för skiktet ”web” och en tillgänglighetsuppsättning för skiktet ”databas”. När du skapar en ny virtuell dator som du kan ange tillgänglighetsuppsättning som en parameter till virtuellt datorn az skapar kommando och Azure automatiskt ser till att de virtuella datorerna som du skapar inom separat uppsättningen tillgängliga över flera fysiska maskinvaruresurser. Om den fysiska maskinvara som din webbserver eller databasen Server virtuella datorer körs på ett problem har uppstått, vet du att andra instanser av webbservern och databasen virtuella datorer fortsätter att köras eftersom de finns på olika typer av maskinvara.

Använd Tillgänglighetsuppsättningar när du vill distribuera tillförlitliga VM-baserade lösningar i Azure.


## <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning

Du kan skapa en tillgänglighetsuppsättning med [az vm tillgänglighetsuppsättning skapa](/cli/azure/vm/availability-set#create). I detta exempel vi in både antalet domäner för uppdatering och fel på *2* för tillgänglighetsuppsättning namngivna *myAvailabilitySet* i den *myResourceGroupAvailability* resursgruppen.

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

Tillgänglighetsuppsättningar kan du isolera resurser över feldomäner och uppdatera domäner. En **feldomän** representerar isolerade mängd server + nätverk + lagring resurser. I föregående exempel anger vi vill våra tillgänglighetsuppsättning ska distribueras under minst två feldomäner när våra virtuella datorer distribueras. Vi också ange att vi vårt tillgänglighetsuppsättning distribuerade mellan två **uppdatera domäner**.  Två update domäner Se till att när Azure utför programuppdateringar våra VM resurser isolerade hindrar alla program som körs under våra VM uppdateras samtidigt.


## <a name="create-vms-inside-an-availability-set"></a>Skapa virtuella datorer i en tillgänglighetsuppsättning

Virtuella datorer måste skapas inom tillgänglighetsuppsättning för att kontrollera att de distribueras på rätt sätt i maskinvaran. Du kan inte lägga till en befintlig virtuell dator till en tillgänglighetsuppsättning när den har skapats. 

När du skapar en virtuell dator med hjälp av [az vm skapa](/cli/azure/vm#create) du ange tillgänglighetsuppsättning med hjälp av den `--availability-set` parametern för att ange namnet på tillgänglighetsuppsättningen.

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

Nu har vi två virtuella datorer i vår nya tillgänglighetsuppsättningen. Eftersom de finns i samma tillgänglighetsuppsättning säkerställer Azure att de virtuella datorerna och deras resurser (inklusive datadiskar) är fördelade på isolerade fysisk maskinvara. Den här distributionen garanterar mycket högre tillgänglighet av vår övergripande VM-lösningen.

Om du tittar på tillgänglighetsuppsättning i portalen genom att gå till resursgrupper > myResourceGroupAvailability > myAvailabilitySet, bör du se hur de virtuella datorerna är fördelade på 2 felet och uppdatera domäner.

![Tillgänglighetsuppsättning i portalen](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Sök efter tillgängliga storlekar på VM 

Du kan lägga till flera virtuella datorer tillgänglighetsuppsättning senare, men du behöver veta vilka VM-storlekar är tillgängliga på maskinvaran.  Använd [az vm-tillgänglighetsuppsättning lista över-storlekar](/cli/azure/availability-set#list-sizes) att lista alla tillgängliga storlekar på maskinvaran kluster för tillgänglighetsuppsättningen.

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
> * Kontrollera tillgängliga storlekar på VM

Gå vidare till nästa kurs vill veta mer om virtuella datorer.

> [!div class="nextstepaction"]
> [Skapa en VM-skalningsuppsättning](tutorial-create-vmss.md)

