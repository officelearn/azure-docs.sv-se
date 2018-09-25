---
title: Distribuera OKD i Azure | Microsoft Docs
description: Distribuera OKD i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 75a02e61adf3e5477b9945afc778e867d5d9c88c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958143"
---
# <a name="deploy-okd-in-azure"></a>Distribuera OKD i Azure

Du kan använda något av två sätt att distribuera OKD (tidigare OpenShift Origin) i Azure:

- Du kan manuellt distribuera alla nödvändiga Azure infrastrukturkomponenter och följ sedan OKD [dokumentation](https://docs.okd.io/3.10/welcome/index.html).
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-origin) som förenklar distributionen av klustret OKD.

## <a name="deploy-by-using-the-okd-template"></a>Distribuera med hjälp av mallen OKD

Använd den `appId` värde från tjänstens huvudnamn som du skapade tidigare för den `aadClientId` parametern.

I följande exempel skapas en parameterfil som heter azuredeploy.parameters.json med alla nödvändiga indata.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
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

### <a name="deploy-by-using-azure-cli"></a>Distribuera med hjälp av Azure CLI


> [!NOTE] 
> Kommandot kräver Azure CLI.8 eller senare. Du kan kontrollera CLI-versionen med den `az --version` kommando. Om du vill uppdatera CLI-version, se [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

I följande exempel distribuerar OKD klustret och alla relaterade resurser i en resursgrupp som heter myResourceGroup, med ett distributionsnamn av myOpenShiftCluster. Mallen refereras direkt från GitHub-lagringsplatsen med hjälp av en lokal parameterfil som heter azuredeploy.parameters.json.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Distributionen tar minst 25 minuter att slutföra beroende på det totala antalet noder som har distribuerats. URL till konsolen OKD och DNS-namnet på skriver ut master OpenShift till terminalen när distributionen är klar.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-okd-cluster"></a>Anslut till klustret OKD

När distributionen är klar ansluter du till konsolen OKD med din webbläsare med hjälp av den `OpenShift Console Uri`. Du kan också ansluta till OKD Master-databasen med hjälp av följande kommando:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Rensa resurser

Använd den [az group delete](/cli/azure/group#az_group_delete) att ta bort resursgruppen, OpenShift klustret och alla relaterade resurser när de inte längre behövs.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distribution](./openshift-post-deployment.md)
- [Felsöka distribution för OpenShift](./openshift-troubleshooting.md)
- [Komma igång med OKD](https://docs.okd.io/latest/getting_started/index.html)
