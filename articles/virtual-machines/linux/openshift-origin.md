---
title: Distribuera OpenShift ursprung i Azure | Microsoft Docs
description: Distribuera OpenShift ursprung i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: d593b011ce4d0998ef07bc257d3c26f1286e3b0e
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-openshift-origin-in-azure"></a>Distribuera OpenShift ursprung i Azure

Du kan använda något av två sätt att distribuera OpenShift ursprung i Azure:

- Du kan distribuera alla nödvändiga Azure infrastrukturkomponenter manuellt och följ sedan OpenShift ursprung [dokumentationen](https://docs.openshift.org/3.6/welcome/index.html).
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-origin) som förenklar distributionen av klustret OpenShift ursprung.

## <a name="deploy-by-using-the-openshift-origin-template"></a>Distribuera med hjälp av mallen OpenShift ursprung

Använd den `appId` värde från tjänstens huvudnamn som du skapade tidigare för den `aadClientId` parameter.

I följande exempel skapas en parameterfil som heter azuredeploy.parameters.json med alla indata.

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
> Kommandot kräver Azure CLI 2.0.8 eller senare. Du kan verifiera CLI-versionen med den `az --version` kommando. Om du vill uppdatera CLI-versionen finns [installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

I följande exempel distribuerar OpenShift kluster och alla relaterade resurser i en resursgrupp med namnet myResourceGroup, med ett distributionsnamn av myOpenShiftCluster. Mallen refereras direkt från GitHub-lagringsplatsen med hjälp av en lokal parameterfil som heter azuredeploy.parameters.json.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Distributionen tar minst 25 minuter att slutföra beroende på det totala antalet noder som har distribuerats. URL till konsolen OpenShift och DNS-namnet på OpenShift master utskrifter till terminal när distributionen är klar.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-openshift-cluster"></a>Anslut till klustret OpenShift

När distributionen är klar, ansluta till konsolen OpenShift med din webbläsare med hjälp av den `OpenShift Console Uri`. Du kan också ansluta till OpenShift master med hjälp av följande kommando:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Rensa resurser

Använd den [ta bort grupp az](/cli/azure/group#delete) kommando för att ta bort resursgruppen, OpenShift kluster och alla relaterade resurser när de inte längre behövs.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distributionen](./openshift-post-deployment.md)
- [Felsöka OpenShift distribution](./openshift-troubleshooting.md)
- [Komma igång med OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)
