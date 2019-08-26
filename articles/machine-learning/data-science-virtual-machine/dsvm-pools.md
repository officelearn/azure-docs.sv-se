---
title: Data Science Virtual Machine-pooler – Azure | Microsoft Docs
description: Distribuera pooler för Dsvm som en delad resurs för ett team
keywords: djupinlärning, AI, verktyg för datavetenskap, virtuell dator för datavetenskap, geospatial analys, tdsp
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: vijetaj
ms.openlocfilehash: 76a715838fa5eb4f806fccc98675ea5acdbe1822
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992022"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Skapa en delad pool av virtuella datorer för datavetenskap

Den här artikeln beskriver hur du skapar en delad pool av data vetenskaps Virtual Machines (Dsvm) för ett team. Fördelarna med att använda en delad pool är bättre resursutnyttjande, enklare delning och samarbete och effektivare hantering av DSVM-resurser.

Du kan använda många metoder och tekniker för att skapa en pool med Dsvm. Den här artikeln fokuserar på pooler för interaktiva virtuella datorer (VM). En alternativ hanterad beräknings infrastruktur är Azure Machine Learning beräkning. Mer information finns i [Konfigurera Compute-mål](../service/how-to-set-up-training-targets.md#amlcompute).

## <a name="interactive-vm-pool"></a>Interaktiv VM-pool

En pool med interaktiva virtuella datorer som delas av hela AI/data science teamet tillåter användare att logga in på en tillgänglig instans av DSVM i stället för att en dedikerad instans för varje uppsättning användare. Den här installationen ger bättre tillgänglighet och effektivare användning av resurser.

Du använder teknik för [skalnings uppsättningar för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) för att skapa en interaktiv VM-pool. Du kan använda skalningsuppsättningar för att skapa och hantera en uppsättning identiska, belastningsutjämning och automatisk skalning virtuella datorer.

Användaren loggar in på den huvudsakliga pool IP- eller DNS-adress. Skalan in automatiskt vägar sessionen för en tillgänglig DSVM i skalningsuppsättningen. Eftersom användarna vill ha en konsekvent och välbekant miljö oavsett vilken virtuell dator de loggar in på, kan alla instanser av den virtuella datorn i skalnings uppsättningen montera en delad nätverks enhet, till exempel en Azure Files resurs eller en NFS-resurs (Network File System). Användarens delad arbetsyta sparas normalt på arkivet delad fil är monterad på var och en av instanserna.

Du hittar en exempelmall för Azure Resource Manager som skapar en skalningsuppsättning med Ubuntu DSVM instanser på [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Du hittar ett exempel på [parameter filen](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) för Azure Resource Manager-mallen på samma plats.

Du kan skapa skalnings uppsättningen från Azure Resource Manager-mallen genom att ange värden för parameter filen i Azure CLI:

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

I den föregående mallen gör det möjligt för en SSH och JupyterHub port från klientdelen skalningsuppsättningen till backend-poolen med Ubuntu Dsvm. Som användare loggar du in på den virtuella datorn på ett säkert gränssnitt (SSH) eller på JupyterHub på vanligt sätt. Eftersom de virtuella dator instanserna kan skalas upp eller ned dynamiskt, måste alla tillstånd sparas i den monterade Azure Files resursen. Du kan använda samma metod för att skapa en pool med Windows-Dsvm.

Den [skript som monterar Azure Files-resurs](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) är också tillgängligt i Azure DataScienceVM lagringsplatsen i GitHub. Skriptet monterar Azure Files-resurs på angiven monteringspunkten i parameterfilen. Skriptet skapar även mjuka länkar till den monterade enheten i den första användarens arbetskatalog. En användarspecifik Notebook-katalog i Azure Files resursen är mjuk länkad till `$HOME/notebooks/remote` katalogen så att användarna kan komma åt, köra och spara sina Jupyter-anteckningsböcker. Du kan använda samma regler när du skapar fler användare på den virtuella datorn så att den pekar varje användares Jupyter arbetsyta till Azure Files-resursen.

VM scale sets för stöd för autoskalning. Du kan ange regler för när du vill skapa ytterligare instanser och när du ska skala ned instanser. Du kan exempelvis skala till noll instanser och spara på molnet maskinvarukostnader för användning när de virtuella datorerna inte används alls. Sidor i dokumentationen för skalningsuppsättningar för virtuella datorer ger detaljerade anvisningar för [autoskalning](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera en gemensam identitet](dsvm-common-identity.md)
* [På ett säkert sätt lagra autentiseringsuppgifter för att komma åt resurser i molnet](dsvm-secure-access-keys.md)















