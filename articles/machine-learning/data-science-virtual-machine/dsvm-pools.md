---
title: Datavetenskap virtuella pooler - Azure | Microsoft Docs
description: Distribuera pooler för datavetenskap VM som en delad resurs för team
keywords: Djup learning AI datavetenskap verktyg, datavetenskap virtuell dator, geospatiala analytics, team av vetenskapliga data
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: c7aab0435ecbd0aee57a15008ac0270159ec2eb3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837090"
---
# <a name="creating-a-shared-pool-of-data-science-virtual-machines"></a>Skapa en delad pool av datavetenskap virtuella datorer

Den här artikeln beskrivs hur en delad pool av Data vetenskap virtuella datorer (DSVM) kan skapas för användning av teamet. Fördelen med att använda en delad pool är bättre resursutnyttjande underlätta samarbeta och dela, så att IT-avdelningen att hantera DSVM resurser mer effektivt. 

Det finns många sätt och olika tekniker som kan användas för att skapa en pool med DSVM.  Följande är huvudscenarier:

* Poolen för batchbearbetning
* Poolen för interaktiva virtuella datorer

## <a name="batch-processing-pool"></a>Batchbearbetning Pool
Om du vill konfigurera en pool av DSVM huvudsakligen för att köra jobb i en batch offline så att du kan använda [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) service eller [Azure Batch](https://docs.microsoft.com/azure/batch/). 

### <a name="azure-batch-ai"></a>Azure Batch AI
Ubuntu-versionen av DSVM stöds som en av avbildningarna i Azure Batch AI. Du kan ange i Azure CLI eller Python SDK: N där du skapar klustret Azure Batch AI den ```image``` parametern och ange det till ```UbuntuDSVM```. Du kan välja vilken typ av bearbetning noder – GPU-baserad instanser vs CPU endast instanser, antal processorer, minne från en [brett urval av VM-instanser](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) tillgängliga på Azure. När du använder Ubuntu DSVM bilden i Batch AI med GPU-baserad noder är alla nödvändiga GPU-drivrutiner och djup learning ramverk är förinstallerade spara mycket tid för att förbereda batch-noder. Faktum är om du utvecklar på en Ubuntu DSVM interaktivt måste ser du att Batch AI-noderna är exakt samma installationen och konfigurationen av miljön. Vanligtvis när en Batch AI-klustret har skapats du också skapa en filresurs som är monterad på alla noder och används för indata och utdata för data samt lagra batch-Jobbkod / skript. 

När Batch AI-klustret har skapats kan använda du samma CLI eller Python SDK skicka jobb ska köras. Du betalar bara för den tid som används för att köra batchjobb. 

#### <a name="more-information"></a>Mer information
* Steg för steg-genomgång av [Azure CLI](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) för att hantera Batch AI
* Steg för steg-genomgång av [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) för att hantera Batch AI
* [Batch-AI recept](https://github.com/Azure/BatchAI) finns visar hur du använder olika AI/djup learning ramverk med Batch AI.

## <a name="interactive-vm-pool"></a>Interaktiva VM-pool

En pool med interaktiva DSVMs som delas av hela AI / datavetenskap team tillåter användare att logga in på en tillgänglig instans av DSVM i stället för att en dedikerad instans för varje användare. Detta hjälper med bättre tillgänglighet och mer effektivt utnyttjande av resurser. 

Den teknik som används för att skapa en interaktiv VM-adresspool är den [Azure Virtual Machine-Skalningsuppsättningar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) (VMSS), där du kan skapa och hantera en grupp med identiska, Utjämning av nätverksbelastning och autoskalning virtuella datorer. En användarloggar in den huvudsakliga pool IP- eller DNS-adress. Skala in automatiskt vägar sessionen till en tillgänglig DSVM i Scale Set. Eftersom användaren vill liknande miljö oavsett VM de loggar in, alla instanser av den virtuella datorn i Scale Set monterar en delad nätverksenhet som en Azure-filer eller en NFS-resurs. Användarens delade arbetsytan sparas normalt på arkivet delad fil är monterad på alla instanser. 

En exempelmall Azure Resource Manager som skapar en Skaluppsättning med Ubuntu DSVMs instanser kan hittas på den [github](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Ett exempel på Azure Resource Manager-mallen [parameterfilen](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) finns även på samma plats. 

Du kan skapa skaluppsättningen VM i Azure Resource Manager-mallen genom att ange lämpliga värden för parameterfilen med hjälp av Azure CLI. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
Kommandona ovan förutsätter att du har en kopia av parameterfilen med de angivna värdena för din instans av skaluppsättningen för virtuell dator, antal VM-instanser, pekare till din Azure-filer tillsammans med autentiseringsuppgifter för det lagringskonto som ska monteras på varje virtuell dator. Parameterfilen refererar till lokalt i kommandot ovan. Du kan överföra också parametrar eller fråga efter dem i skriptet.  

Mallen ovan kan SSH och Jupyterhub porten från klientdel VM Scale inställd på backend-pool med Ubuntu DSVMs.  Som en användare logga du bara in till en virtuell dator på SSH eller JupyterHub på vanligt sätt. Eftersom VM-instanser kan skalas upp eller ned dynamiskt, någon status måste vara dela sparas i den monterade Azure Files. Samma metod kan användas för att skapa en pool med Windows DSVMs. 

Den [skript som monterar Azure-filer](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) är också tillgängliga på Azure DataScienceVM Github. Förutom montera Azure-filerna på den angivna monteringspunkten i parameterfilen skapar också ytterligare mjuka länkar till den monterade enheten i det ursprungliga användarkontot arbetskatalog och en användarspecifik anteckningsboken katalog i Azure delade filer är mjuk länkad till ```$HOME/notebooks/remote``` directory vilket gör att användare att komma åt, köra och spara sin Jupyter-anteckningsböcker.  Samma konvention kan användas när du skapar ytterligare användare på den virtuella datorn så att den pekar till varje användares Jupyter arbetsyta till delade filer i Azure. 

Azure skalningsuppsättningarna VM stöd autoskalning där du kan ange regler på när du vill skapa ytterligare instanser och under vilka omständigheter för att skala ned instanser inklusive gör den till noll instanser för att spara på molnet maskinvarukostnader för användning när de virtuella datorerna inte används på alla . Dokumentationen sidor i skalningsuppsättningar ger detaljerade anvisningar för [Autoskala](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Nästa steg

* [Ställ in gemensam identitet](dsvm-common-identity.md)
* [Lagra autentiseringsuppgifter för att få åtkomst till molnresurser](dsvm-secure-access-keys.md)















