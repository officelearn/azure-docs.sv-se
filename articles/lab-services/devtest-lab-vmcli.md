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
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 11ac4e10cbd116ed204a8a11274408f5a5a9b4d9
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183127"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Skapa och hantera virtuella datorer med DevTest Labs med Azure CLI
Den här snabb starten hjälper dig att skapa, starta, ansluta, uppdatera och rensa en utvecklings dator i labbet. 

Innan du börjar:

* Om ett labb inte har skapats kan du hitta instruktioner [här](devtest-lab-create-lab.md).

* [Installera Azure CLI](/cli/azure/install-azure-cli). Starta genom att köra AZ-inloggning för att skapa en anslutning till Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Skapa och verifiera den virtuella datorn 
Innan du kör DevTest Labs relaterade kommandon anger du lämplig Azure-kontext med hjälp `az account set` av kommandot:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Kommandot för att skapa en virtuell dator är: `az lab vm create`. Resurs gruppen för labb, labb namn och namn på virtuella datorer krävs. Resten av argumenten ändras beroende på vilken typ av virtuell dator du har.

Följande kommando skapar en Windows-baserad avbildning från Azures marknads plats. Namnet på bilden är detsamma som du ser när du skapade en virtuell dator med hjälp av Azure Portal. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Följande kommando skapar en virtuell dator baserat på en anpassad avbildning som är tillgänglig i labbet:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Argumentet **bild typ** har ändrats från **Galleri** till **anpassat**. Namnet på avbildningen matchar vad du ser om du skapade den virtuella datorn i Azure Portal.

Följande kommando skapar en virtuell dator från en Marketplace-avbildning med SSH-autentisering:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Du kan också skapa virtuella datorer baserat på formler genom att ange parametern för **bild typ** till **formel**. Om du behöver välja ett särskilt virtuellt nätverk för den virtuella datorn använder du parametrarna **VNet-Name** och **Subnet** . Mer information finns i [AZ Lab VM Create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Kontrol lera att den virtuella datorn är tillgänglig.
`az lab vm show` Använd kommandot för att kontrol lera att den virtuella datorn är tillgänglig innan du startar och ansluter till den. 

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
Följande exempel kommando startar en virtuell dator:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Anslut till en virtuell dator: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) eller [fjärr skrivbord](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Uppdatera den virtuella datorn
Följande exempel kommando använder artefakter på en virtuell dator:

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

Lista artefakter som är tillgängliga i labbet.
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
Följande exempel kommando stoppar en virtuell dator.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Ta bort en virtuell dator.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Nästa steg
Se följande innehåll: [Azure CLI-dokumentation för Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 
