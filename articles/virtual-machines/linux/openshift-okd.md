---
title: Distribuera OKD i Azure | Microsoft Docs
description: Distribuera OKD i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/02/2019
ms.author: haroldw
ms.openlocfilehash: 7db50007dd32c84a360eaec25bf860709272437b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542517"
---
# <a name="deploy-okd-in-azure"></a>Distribuera OKD i Azure

Du kan använda något av två sätt att distribuera OKD (tidigare OpenShift Origin) i Azure:

- Du kan manuellt distribuera alla nödvändiga Azure infrastrukturkomponenter och följ sedan de [OKD dokumentation](https://docs.okd.io).
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-origin) som förenklar distributionen av klustret OKD.

## <a name="deploy-using-the-okd-template"></a>Distribuera med hjälp av mallen OKD

För att distribuera med hjälp av Resource Manager-mall kan använda du en parameterfil för att ange indataparametrarna. För att ytterligare anpassa distributionen, Förgrena GitHub-lagringsplatsen och ändra lämpliga objekt.

Vissa vanliga anpassningsalternativ inkludera, men inte begränsat till:

- Skyddsmiljö VM-storlek (variabel i azuredeploy.json)
- Namngivningskonventioner (variabler i azuredeploy.json)
- OpenShift egenskaper för klustret, du ändrar värdfilen (deployOpenShift.sh)

Den [OKD mall](https://github.com/Microsoft/openshift-origin) har flera grenar som är tillgängliga för olika versioner av OKD.  Utifrån dina behov kan du distribuera direkt från lagringsplatsen eller så kan du Förgrena lagringsplatsen och gör anpassade ändringar innan du distribuerar.

Använd den `appId` värde från tjänstens huvudnamn som du skapade tidigare för den `aadClientId` parametern.

Följande är ett exempel på en parameterfil som heter azuredeploy.parameters.json med alla nödvändiga indata.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Ersätt parametrarna med din specifika information.

Olika versioner kan ha olika parametrar, så kontrollera att de nödvändiga parametrarna för den gren som du använder.

### <a name="deploy-using-azure-cli"></a>Distribuera med hjälp av Azure CLI


> [!NOTE] 
> Kommandot kräver Azure CLI 2.0.8 eller senare. Du kan kontrollera CLI-versionen med den `az --version` kommando. Om du vill uppdatera CLI-version, se [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

I följande exempel distribuerar OKD klustret och alla relaterade resurser i en resursgrupp med namnet openshiftrg, med ett distributionsnamn av myOpenShiftCluster. Mallen refereras direkt från GitHub-lagringsplatsen när du använder en lokal parameterfil som heter azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Distributionen tar minst 30 minuter att slutföra, baserat på det totala antalet noder som har distribuerats. URL till konsolen OpenShift och DNS-namnet på skriver ut master OpenShift till terminalen när distributionen är klar. Du kan också visa outputs-avsnittet för distributionen från Azure-portalen.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Om du inte vill sysselsätta kommandoraden att vänta tills distributionen är slutförd, lägga till `--no-wait` som ett alternativ för gruppdistributionen av. Utdata från distributionen kan hämtas från Azure-portalen under distributionen för resursgruppen.

## <a name="connect-to-the-okd-cluster"></a>Anslut till klustret OKD

När distributionen är klar ansluter du till konsolen OpenShift med din webbläsare med hjälp av den `OpenShift Console Url`. Du kan också SSH till OKD Master-databasen. Följande är ett exempel som använder utdata från distributionen:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Rensa resurser

Använd den [az group delete](/cli/azure/group) att ta bort resursgruppen, OpenShift klustret och alla relaterade resurser när de inte längre behövs.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distribution](./openshift-post-deployment.md)
- [Felsöka distribution för OpenShift](./openshift-troubleshooting.md)
- [Komma igång med OKD](https://docs.okd.io)
