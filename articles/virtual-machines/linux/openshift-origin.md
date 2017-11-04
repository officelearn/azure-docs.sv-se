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
ms.openlocfilehash: 1a40c4cc064b32aced7e976f40f6ed6a57e62204
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-openshift-origin-in-azure"></a>Distribuera OpenShift ursprung i Azure

Det finns flera sätt att distribuera OpenShift ursprung i Azure. Du kan distribuera alla nödvändiga Azure infrastrukturkomponenter manuellt och följ sedan OpenShift ursprung [dokumentationen](https://docs.openshift.org/3.6/welcome/index.html).
Du kan också använda en befintlig Resource Manager-mall som förenklar distributionen av klustret OpenShift ursprung. När sådana mallen finns [här](https://github.com/Microsoft/openshift-origin).

## <a name="deploy-using-the-openshift-origin-template"></a>Distribuera med hjälp av mallen OpenShift ursprung

Använd den `appId` värde från huvudnamn för tjänsten som du skapade tidigare för den `aadClientId` parameter.

I följande exempel skapas en parameterfil som heter **azuredeploy.parameters.json** med alla indata.

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

### <a name="deploy-using-azure-cli"></a>Distribuera med hjälp av Azure CLI


> [!NOTE] 
> Kommandot kräver Azure CLI 2.0.8 eller senare. Du kan verifiera az CLI version med den `az --version` kommando. Om du vill uppdatera CLI-versionen finns [installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

I följande exempel distribuerar OpenShift kluster och alla relaterade resurser i en resursgrupp med namnet myResourceGroup med ett distributionsnamn av myOpenShiftCluster. Mallen refereras direkt från github-lagringsplatsen och en fil med lokala parametrar med namnet **azuredeploy.parameters.json** filen används.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Distributionen tar minst 25 minuter att slutföra beroende på det totala antalet noder som har distribuerats. URL OpenShift konsolen och DNS-namnet för OpenShift master skrivs till terminalen när distributionen är klar.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-openshift-cluster"></a>Anslut till klustret OpenShift

När distributionen är klar ansluter du till OpenShift konsolen med en webbläsare med hjälp av den `OpenShift Console Uri`. Du kan också ansluta till huvudservern OpenShift med följande kommando:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda den [ta bort grupp az](/cli/azure/group#delete) kommando för att ta bort resursgruppen, OpenShift kluster och alla relaterade resurser.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- [Uppgifter för efter distribution](./openshift-post-deployment.md)
- [Felsökning av OpenShift distribution](./openshift-troubleshooting.md)
- [Komma igång med OpenShift ursprung](https://docs.openshift.org/latest/getting_started/index.html)
