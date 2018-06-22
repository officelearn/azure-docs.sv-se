---
title: Data vetenskap virtuella pooler - Azure | Microsoft Docs
description: Distribuera pooler för datavetenskap virtuella datorer som en delad resurs för en grupp
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
ms.openlocfilehash: 0740ff7542d066442146b8e80e188ad5ba49a2b5
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309406"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Skapa en delad pool av datavetenskap virtuella datorer

Den här artikeln beskrivs hur du kan skapa en delad pool av datavetenskap virtuella datorerna (DSVMs) för en grupp. Fördelarna med att använda en delad pool är bättre resursutnyttjande, förenkling av delning och samarbete och mer effektiv hantering av DSVM resurser. 

Du kan använda många metoder och tekniker för att skapa en pool med DSVMs. Den här artikeln fokuserar på pooler för batch-bearbetning och interaktiva virtuella datorer.

## <a name="batch-processing-pool"></a>Batch-bearbetning pool
Om du vill konfigurera en pool av DSVMs huvudsakligen för att köra jobb i en batch offline, kan du använda den [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) eller [Azure Batch](https://docs.microsoft.com/azure/batch/) service. Den här artikeln fokuserar på Azure Batch AI.

Ubuntu-versionen av DSVM stöds som en av avbildningarna i Azure Batch AI. I Azure CLI eller Python SDK, där du skapar Azure Batch AI-kluster, kan du ange den `image` parametern och ange det till `UbuntuDSVM`. Du kan välja vilken typ av bearbetning noder: GPU-baserad instanser jämfört med endast CPU-instanser, antalet processorer och minne från en [brett urval av VM-instanser](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) tillgängliga på Azure. 

När du använder Ubuntu DSVM bilden i Batch AI med GPU-baserad noder förinstallerat alla nödvändiga drivrutiner för GPU och djup learning ramverk. Förinstallationen sparar mycket tid för att förbereda batch-noder. Faktum är om du utvecklar på en Ubuntu DSVM interaktivt märke till att batchen AI-noderna är exakt samma installationen och konfigurationen av miljön. 

Vanligtvis när du skapar en Batch AI-klustret kan skapa du också en filresurs som är monterade på alla noder. Filresursen används för indata och utdata för data samt lagring av batch-jobbet kod/skript. 

När du har skapat en Batch AI-klustret, kan du använda samma CLI eller Python SDK skicka jobb ska köras. Du betalar för den tid som används för att köra batchjobb. 

Mer information finns i:
* Steg för steg-genomgång av [Azure CLI](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) att hantera Batch AI
* Steg för steg-genomgång av [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) att hantera Batch AI
* [Batch-AI recept](https://github.com/Azure/BatchAI) som visar hur du använder olika AI och djup learning ramverk med Batch AI

## <a name="interactive-vm-pool"></a>Interaktiva VM-pool

En pool med interaktiva virtuella datorer som delas av hela AI/data vetenskap gruppen tillåter användare att logga in på en tillgänglig instans av DSVM i stället för att en dedikerad instans för varje användare. Den här installationen hjälper med bättre tillgänglighet och mer effektivt utnyttjande av resurser. 

Tekniken som används för att skapa en interaktiv VM-adresspool är [skalningsuppsättningar i virtuella Azure-datorn](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Du kan använda skaluppsättningar för att skapa och hantera en grupp med identiska, Utjämning av nätverksbelastning och autoskalning virtuella datorer. 

Användaren loggar in på den huvudsakliga pool IP- eller DNS-adress. Skala in automatiskt vägar sessionen till en tillgänglig DSVM i uppsättningen av skalan. Eftersom användarna vill ha en liknande miljö oavsett VM de logga in till, alla instanser av den virtuella datorn i skaluppsättning montera en delad nätverksenhet som en resurs i Azure-filer eller en NFS-resurs. Användarens delade arbetsytan sparas normalt på arkivet delad fil är monterad på alla instanser. 

Du hittar en exempelmall Azure Resource Manager som skapar en skala som anges med Ubuntu DSVM instanser på [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Ett exempel på den [parameterfilen](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) för Azure Resource Manager mallen är på samma plats. 

Du kan skapa skaluppsättningen från Azure Resource Manager-mall genom att ange värden för parameterfilen i Azure CLI. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
Kommandona ovan förutsätter att du har:
* En kopia av parameterfilen med de angivna värdena för din instans av skaluppsättning.
* Antal VM-instanser.
* Dela pekare till Azure-filer.
* Autentiseringsuppgifter för det lagringskonto som ska monteras på varje virtuell dator. 

Parameterfilen refererar till lokalt i kommandona. Du kan också skicka parametrar eller fråga efter dem i skriptet.  

Föregående mallen kan SSH och JupyterHub port från frontend skaluppsättningen till backend-poolen med Ubuntu DSVMs. Som användare kan logga du bara in på den virtuella datorn på SSH eller JupyterHub på normalt sätt. Eftersom VM-instanser kan skalas upp eller ned dynamiskt, någon status måste vara dela sparas i den monterade Azure Files. Du kan använda samma metod för att skapa en pool med Windows DSVMs. 

Den [skript som monterar resursen Azure filer](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) är också tillgänglig i Azure DataScienceVM lagringsplatsen i GitHub. Skriptet monterar resursen Azure-filer på den angivna monteringspunkten i parameterfilen. Skriptet skapar även mjuka länkar till den monterade enheten i det ursprungliga användarkontot hemkatalog. En användarspecifik anteckningsboken katalog i resursen Azure Files är Mjuk länkad till den `$HOME/notebooks/remote` katalogen så att användare kan komma åt, köra och spara sin Jupyter-anteckningsböcker. Du kan använda samma konvention när du skapar ytterligare användare på den virtuella datorn så att den pekar varje användares Jupyter arbetsytan till Azure Files resursen. 

Virtuella datorn anger support autoskalning. Du kan ange regler för när du vill skapa ytterligare instanser och när att skala ned instanser. Du kan exempelvis skala till noll instanser som du vill spara maskinvarukostnader molnet användning när de virtuella datorerna inte används på alla. Dokumentationen sidor i skalningsuppsättningar i virtuella datorer ger detaljerade anvisningar för [autoskalning](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera en gemensam identitet](dsvm-common-identity.md)
* [Lagra autentiseringsuppgifter för att få åtkomst till molnresurser](dsvm-secure-access-keys.md)















