---
title: Skapa och hantera virtuella datorer i DevTest Labs med Azure CLI | Microsoft Docs
description: "Lär dig hur du använder Azure DevTest Labs för att skapa och hantera virtuella datorer med Azure CLI 2.0"
services: devtest-lab,virtual-machines
documentationcenter: na
author: lisawong19
manager: douge
editor: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: liwong
ms.openlocfilehash: a3af12ba0598d60b55b3714ae1690fe3e5b54a42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Skapa och hantera virtuella datorer med DevTest Labs med hjälp av Azure CLI
Den här snabbstartsguide vägleder dig genom att skapa, starta, ansluta, uppdaterar och rensar en utvecklingsdator i labbet. 

Innan du börjar:

* Om ett labb som inte har skapats anvisningar finns [här](devtest-lab-create-lab.md).

* [Installera CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Starta, köra az in för att skapa en anslutning med Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Skapa och kontrollera den virtuella datorn 
Skapa en virtuell dator från en marketplace-avbildning med ssh autentisering.
```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```
> [!NOTE]
> Placera den **resursgrupp för labbets** i--resursgrupp parametern.
>

Om du vill skapa en virtuell dator med en formel, Använd--formeln parametern i [az lab vm skapa](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create).


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

## <a name="start-and-connect-to-the-virtual-machine"></a>Starta och Anslut till den virtuella datorn
Starta en virtuell dator.
```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```
> [!NOTE]
> Placera den **resursgrupp för labbets** i--resursgrupp parametern.
>

Ansluta till en virtuell dator: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) eller [fjärrskrivbord](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Uppdatera den virtuella datorn
Tillämpa artefakter för en virtuell dator.
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

Lista artefakter i labbet.
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