---
title: Data Science Virtual Machine-pooler – Azure | Microsoft Docs
description: Distribuera pooler med virtuella datorer för datavetenskap som en delad resurs för ett team
keywords: djupinlärning, AI, verktyg för datavetenskap, virtuell dator för datavetenskap, geospatial analys, tdsp
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: acae59922f5a46f059e19db6865491f5186139f7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103412"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Skapa en delad pool av virtuella datorer för datavetenskap

Den här artikeln beskrivs hur du kan skapa en delad pool av virtuella datorer för datavetenskap (Dsvm) för ett team att använda. Fördelarna med att använda en delad pool är bättre resursutnyttjande, hjälp till delning och samarbete och effektivare hanteringen av DSVM resurser. 

Du kan använda många metoder och tekniker för att skapa en pool med Dsvm. Den här artikeln handlar om pooler för batchbearbetning och interaktiva virtuella datorer.

## <a name="batch-processing-pool"></a>Batchbearbetning pool
Om du vill konfigurera en pool med Dsvm främst för att köra jobb i en batch offline, kan du använda den [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) eller [Azure Batch](https://docs.microsoft.com/azure/batch/) service. Den här artikeln fokuserar på Azure Batch AI.

Ubuntu-versionen av DSVM stöds som en av avbildningarna i Azure Batch AI. I Azure CLI eller SDK för Python, där du skapar Azure Batch AI-kluster kan du ange den `image` parametern och ange den till `UbuntuDSVM`. Du kan välja vilken typ av bearbetning av noder som du vill: GPU-baserade instanser jämfört med enbart-CPU-instanser, antal processorer och minne för en [brett urval av VM-instanser](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) tillgängliga på Azure. 

När du använder Ubuntu DSVM-avbildningen i Batch AI med GPU-baserad noder är förinstallerade alla nödvändiga GPU-drivrutiner och ramverk för djupinlärning. Förinstallationen sparar ganska lång tid i förbereda batch-noder. I själva verket om du utvecklar på en Ubuntu DSVM interaktivt du kommer att märka att Batch AI-noderna är exakt samma installationen och konfigurationen av miljön. 

Vanligtvis när du skapar ett Batch AI-kluster kan skapa du också en filresurs som är monterad på alla noder. Filresursen används för indata och utdata för data, samt lagring av batch-jobb kod/skript. 

När du skapar ett Batch AI-kluster kan använda du samma CLI eller SDK för Python-skicka jobb som ska köras. Du betalar för den tid som används för att köra batch-jobb. 

Mer information finns i:
* Steg för steg för att använda [Azure CLI](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) att hantera Batch AI
* Steg för steg för att använda [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) att hantera Batch AI
* [Batch AI-recept](https://github.com/Azure/BatchAI) som visar hur du använder olika AI och djupinlärning ramverk med Batch AI

## <a name="interactive-vm-pool"></a>Interaktiv VM-pool

En pool med interaktiva virtuella datorer som delas av hela AI/data science teamet tillåter användare att logga in på en tillgänglig instans av DSVM i stället för att en dedikerad instans för varje uppsättning användare. Den här konfigurationen hjälper med bättre tillgänglighet och effektivare användning av resurser. 

Den teknik som används för att skapa en interaktiv VM-pool [Azure VM-skalningsuppsättningar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Du kan använda skalningsuppsättningar för att skapa och hantera en uppsättning identiska, belastningsutjämning och automatisk skalning virtuella datorer. 

Användaren loggar in på den huvudsakliga pool IP- eller DNS-adress. Skalan in automatiskt vägar sessionen för en tillgänglig DSVM i skalningsuppsättningen. Eftersom användarna vill ha en liknande miljön, oavsett den virtuella datorn de loggat in, alla instanser av den virtuella datorn i skalningsuppsättningen montera en delad nätverksenhet, t.ex. en Azure Files-resurs eller en NFS-resurs. Användarens delad arbetsyta sparas normalt på arkivet delad fil är monterad på var och en av instanserna. 

Du hittar en exempelmall för Azure Resource Manager som skapar en skalningsuppsättning med Ubuntu DSVM instanser på [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Ett exempel på den [parameterfilen](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) för Azure Resource Manager-mallen är på samma plats. 

Du kan skapa en skalningsuppsättning från Azure Resource Manager-mallen genom att ange värden för parameterfil i Azure CLI. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
I föregående kommandon förutsätter att du har:
* En kopia av parameterfilen med de angivna värdena för din instans av skalningsuppsättningen.
* Antalet Virtuella datorinstanser.
* Dela länkar till Azure Files.
* Autentiseringsuppgifter för det lagringskonto som kommer att monteras på varje virtuell dator. 

Parameterfilen refereras lokalt i kommandona. Du kan även skicka parametrar eller fråga efter dem i ditt skript.  

I den föregående mallen gör det möjligt för en SSH och JupyterHub port från klientdelen skalningsuppsättningen till backend-poolen med Ubuntu Dsvm. Som en användare kan logga du bara in på den virtuella datorn på SSH eller JupyterHub på normalt sätt. Eftersom VM-instanser kan skalas upp eller ned dynamiskt, några tillstånd måste finnas delar sparas i den monterade Azure Files. Du kan använda samma metod för att skapa en pool med Windows-Dsvm. 

Den [skript som monterar Azure Files-resurs](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) är också tillgängligt i Azure DataScienceVM lagringsplatsen i GitHub. Skriptet monterar Azure Files-resurs på angiven monteringspunkten i parameterfilen. Skriptet skapar även mjuka länkar till den monterade enheten i den första användarens arbetskatalog. En användarspecifik notebook-katalog i Azure Files-resurs är Mjuk länkad till den `$HOME/notebooks/remote` katalogen så att användare kan komma åt, köra och spara sina Jupyter-anteckningsböcker. Du kan använda samma regler när du skapar fler användare på den virtuella datorn så att den pekar varje användares Jupyter arbetsyta till Azure Files-resursen. 

VM scale sets för stöd för autoskalning. Du kan ange regler för när du vill skapa ytterligare instanser och när du vill skala ned instanser. Du kan exempelvis skala till noll instanser och spara på molnet maskinvarukostnader för användning när de virtuella datorerna inte används alls. Sidor i dokumentationen för skalningsuppsättningar för virtuella datorer ger detaljerade anvisningar för [autoskalning](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera en gemensam identitet](dsvm-common-identity.md)
* [På ett säkert sätt lagra autentiseringsuppgifter för att komma åt resurser i molnet](dsvm-secure-access-keys.md)















