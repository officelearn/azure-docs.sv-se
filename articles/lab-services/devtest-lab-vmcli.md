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
ms.openlocfilehash: 48a30ef86cdb10b540ffe1231294542ccff87255
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895638"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Skapa och hantera virtuella datorer med DevTest Labs med Azure CLI
Den här snabbstarten får du hjälp att skapa, starta, ansluta, uppdaterar och rensar en utvecklingsdator i labbet. 

Innan du börjar:

* Om ett labb inte har skapat instruktioner finns [här](devtest-lab-create-lab.md).

* [Installera Azure CLI](/cli/azure/install-azure-cli). Börja genom att köra az-inloggning för att skapa en anslutning till Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Skapa och verifiera den virtuella datorn 
Innan du kör DevTest Labs-relaterade kommandon, ange rätt kontext i Azure med hjälp av den `az account set` kommando:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Kommandot för att skapa en virtuell dator är: `az lab vm create`. Resursgrupp för labbet, labbnamn och namn på virtuell dator är alla nödvändiga. Resten av argumenten ändras beroende på vilken typ av virtuell dator.

Följande kommando skapar en Windows-baserad avbildning från Azure Marketplace. Namnet på avbildningen är samma som du vill se när du skapar en virtuell dator med Azure-portalen. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3’ --admin-username 'AdminUser' --admin-password 'Password1!'
```

Följande kommando skapar en virtuell dator baserat på en anpassad avbildning som är tillgängliga i laboratoriet:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Den **bildtyp** argumentet har ändrats från **galleriet** till **anpassade**. Namnet på bilden matchar det som visas om du skapar den virtuella datorn i Azure-portalen.

Följande kommando skapar en virtuell dator från en marketplace-avbildning med ssh autentisering:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Du kan också skapa virtuella datorer baserat på formler genom att ange den **bildtyp** parameter **formeln**. Om du vill välja ett specifikt virtuellt nätverk för den virtuella datorn kan använda den **vnet-name** och **undernät** parametrar. Mer information finns i [az lab vm skapa](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Kontrollera att den virtuella datorn är tillgänglig.
Använd den `az lab vm show` kommandot för att kontrollera att den virtuella datorn är tillgänglig innan du startar och ansluta till den. 

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
Följande exempelkommando startar en virtuell dator:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Anslut till en virtuell dator: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) eller [fjärrskrivbord](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Uppdatera den virtuella datorn
Kommandot i följande exempel gäller artefakter till en virtuell dator:

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
Kommandot i följande exempel stoppar en virtuell dator.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Ta bort en virtuell dator.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Nästa steg
Se följande innehåll: [Azure CLI-dokumentationen för Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 