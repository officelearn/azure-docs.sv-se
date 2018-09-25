---
title: Skapa och hantera virtuella datorer i DevTest Labs med Azure CLI | Microsoft Docs
description: Lär dig hur du använder Azure DevTest Labs för att skapa och hantera virtuella datorer med Azure CLI
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: ccf9b08856fcc652e3ad4b2b31587d43d7ef9cca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995966"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Skapa och hantera virtuella datorer med DevTest Labs med Azure CLI
Den här snabbstarten får du hjälp att skapa, starta, ansluta, uppdaterar och rensar en utvecklingsdator i labbet. 

Innan du börjar:

* Om ett labb inte har skapat instruktioner finns [här](devtest-lab-create-lab.md).

* [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Börja genom att köra az-inloggning för att skapa en anslutning till Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Skapa och verifiera den virtuella datorn 
Skapa en virtuell dator från en marketplace-avbildning med ssh autentisering.
```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```
> [!NOTE]
> Placera den **övningen resursgrupp** i parametern--resursgrupp.
>

Om du vill skapa en virtuell dator med hjälp av en formel, använder formeln parametern--i [az lab vm skapa](https://docs.microsoft.com/cli/azure/lab/vm#az-lab-vm-create).


Kontrollera att den virtuella datorn är tillgänglig.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Starta och ansluta till den virtuella datorn
Starta en virtuell dator.
```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```
> [!NOTE]
> Placera den **övningen resursgrupp** i parametern--resursgrupp.
>

Ansluta till en virtuell dator: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) eller [fjärrskrivbord](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Uppdatera den virtuella datorn
Tillämpa artefakter till en virtuell dator.
```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

Lista artefakter i laboratoriet.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>Stoppa och ta bort den virtuella datorn    
Stoppa en virtuell dator.
```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Ta bort en virtuell dator.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
