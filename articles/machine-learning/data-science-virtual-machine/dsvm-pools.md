---
title: Delade pooler
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig hur du skapar & distribuerar en delad pool av virtuella data science-datorer (DSVM) som en delad resurs för ett team.
keywords: djupinlärning, AI, datavetenskapsverktyg, virtuell datavetenskap, geospatial analys, teamdatavetenskapsprocess
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: cc0efc0a076ddc3fc9425999f1e38b4a32dec7a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477348"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Skapa en delad pool med virtuella datavetenskapsdatorer

I den här artikeln får du lära dig hur du skapar en delad pool med virtuella data science-datorer (DSVMs) för ett team. Fördelarna med att använda en delad pool är bättre resursutnyttjande, enklare delning och samarbete samt effektivare hantering av DSVM-resurser.

Du kan använda många metoder och tekniker för att skapa en pool med DSVMs. Den här artikeln fokuserar på pooler för interaktiva virtuella datorer . En alternativ hanterad beräkningsinfrastruktur är Azure Machine Learning Compute. Mer information finns i [Konfigurera beräkningsmål](../how-to-set-up-training-targets.md#amlcompute).

## <a name="interactive-vm-pool"></a>Interaktiv VM-pool

En pool med interaktiva virtuella datorer som delas av hela AI/data science-teamet tillåter användare att logga in på en tillgänglig instans av DSVM i stället för att ha en dedikerad instans för varje uppsättning användare. Den här inställningen möjliggör bättre tillgänglighet och effektivare resursutnyttjande.

Du använder [Azure virtual machine scale set-teknik](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) för att skapa en interaktiv VM-pool. Du kan använda skalningsuppsättningar för att skapa och hantera en grupp med identiska, belastningsbalanserade och automatiskskala virtuella datorer.

Användaren loggar in på huvudpoolens IP- eller DNS-adress. Skalningsuppsättningen dirigerar automatiskt sessionen till en tillgänglig DSVM i skalningsuppsättningen. Eftersom användarna vill ha en konsekvent och välbekant miljö oavsett den virtuella datorn som de loggar in på, monterar alla instanser av den virtuella datorn i skalningsuppsättningen en delad nätverksenhet, till exempel en Azure Files share eller en NFS-resurs (Network File System). Användarens delade arbetsyta lagras normalt i det delade filarkivet som är monterat på var och en av instanserna.

Du hittar ett exempel på En Azure Resource Manager-mall som skapar en skalningsuppsättning med Ubuntu DSVM-instanser på [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Du hittar ett exempel på [parameterfilen](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) för Azure Resource Manager-mallen på samma plats.

Du kan skapa skalningsuppsättningen från Azure Resource Manager-mallen genom att ange värden för parameterfilen i Azure CLI:

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

De föregående kommandona förutsätter att du har:

* En kopia av parameterfilen med de värden som angetts för din instans av skalningsuppsättningen.
* Antalet VM-instanser.
* Pekare till Azure-filresursen.
* Autentiseringsuppgifter för lagringskontot som ska monteras på varje virtuell dator.

Parameterfilen refereras lokalt i kommandona. Du kan också skicka parametrar infogade eller fråga efter dem i skriptet.  

Med den föregående mallen kan SSH- och JupyterHub-porten från frontend-skalan vara inställd på backend-poolen för Ubuntu DSVMs. Som användare loggar du in på den virtuella datorn på ett säkert skal (SSH) eller på JupyterHub på normalt sätt. Eftersom VM-instanserna kan skalas upp eller ned dynamiskt måste alla tillstånd sparas i den monterade Azure Files-resursen. Du kan använda samma metod för att skapa en pool med Windows DSVMs.

[Skriptet som monterar Azure Files-resursen](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) är också tillgängligt i Azure DataScienceVM-databasen i GitHub. Skriptet monterar Azure Files-resursen vid den angivna monteringspunkten i parameterfilen. Skriptet skapar också mjuka länkar till den monterade enheten i den första användarens arbetskatalog. En användarspecifik anteckningsbokskatalog i Azure Files-resursen `$HOME/notebooks/remote` är mjuklänkad till katalogen så att användarna kan komma åt, köra och spara sina Jupyter-anteckningsböcker. Du kan använda samma konvention när du skapar ytterligare användare på den virtuella datorn för att peka varje användares Jupyter-arbetsyta till Azure Files-resursen.

Skala för virtuella datorer stöder automatisk skalning. Du kan ange regler om när du ska skapa ytterligare instanser och när instanser ska skalas ned. Du kan till exempel skala ned till noll instanser för att spara på molnmaskinvaruanvändningskostnader när de virtuella datorerna inte används alls. Dokumentationssidorna för skalningsuppsättningar för virtuella datorer innehåller detaljerade steg för [automatisk skalning](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera en gemensam identitet](dsvm-common-identity.md)
* [Lagra autentiseringsuppgifter för att komma åt molnresurser på ett säkert sätt](dsvm-secure-access-keys.md)
