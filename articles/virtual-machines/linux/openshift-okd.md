---
title: Distribuera OKD i Azure
description: Distribuera OKD i Azure.
author: haroldwongms
manager: joraio
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: d7d251370aefdfadc0b77a67f6dad1be2dcb9e9a
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759446"
---
# <a name="deploy-okd-in-azure"></a>Distribuera OKD i Azure

Du kan använda ett av två sätt att distribuera OKD (tidigare OpenShift Origin) i Azure:

- Du kan distribuera alla nödvändiga Azure-infrastrukturkomponenter manuellt och sedan följa [OKD-dokumentationen](https://docs.okd.io).
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-origin) som förenklar distributionen av OKD-klustret.

## <a name="deploy-using-the-okd-template"></a>Distribuera med OKD-mallen

Om du vill distribuera med resurshanteraren använder du en parameterfil för att ange indataparametrarna. Om du vill anpassa distributionen ytterligare förfalskar du GitHub-repoen och ändrar lämpliga objekt.

Några vanliga anpassningsalternativ är, men är inte begränsade till:

- Bastion VM-storlek (variabel i azuredeploy.json)
- Namngivningskonventioner (variabler i azuredeploy.json)
- OpenShift kluster detaljer, ändras via hosts-fil (deployOpenShift.sh)

[OKD-mallen](https://github.com/Microsoft/openshift-origin) har flera grenar tillgängliga för olika versioner av OKD.  Baserat på dina behov kan du distribuera direkt från repo eller så kan du förfalska repoerna och göra anpassade ändringar innan du distribuerar.

Använd `appId` värdet från tjänstens huvudnamn som `aadClientId` du skapade tidigare för parametern.

Följande är ett exempel på en parameterfil med namnet azuredeploy.parameters.json med alla nödvändiga indata.

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

Olika versioner kan ha olika parametrar så kontrollera de nödvändiga parametrarna för grenen du använder.

### <a name="deploy-using-azure-cli"></a>Distribuera med Azure CLI


> [!NOTE] 
> Följande kommando kräver Azure CLI 2.0.8 eller senare. Du kan verifiera CLI-versionen med `az --version` kommandot. Information om hur du uppdaterar CLI-versionen finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

I följande exempel distribueras OKD-klustret och alla relaterade resurser till en resursgrupp med namnet openshiftrg, med distributionsnamnet myOpenShiftCluster. Mallen refereras direkt från GitHub-repo när du använder en lokal parameterfil med namnet azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Distributionen tar minst 30 minuter att slutföra, baserat på det totala antalet noder som distribueras. Url:en för OpenShift-konsolen och DNS-namnet på OpenShift-huvudskrivs till terminalen när distributionen är klar. Alternativt kan du visa utdataavsnittet i distributionen från Azure-portalen.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Om du inte vill binda upp kommandoraden som väntar `--no-wait` på att distributionen ska slutföras lägger du till som ett av alternativen för gruppdistributionen. Utdata från distributionen kan hämtas från Azure-portalen i distributionsavsnittet för resursgruppen.

## <a name="connect-to-the-okd-cluster"></a>Ansluta till OKD-klustret

När distributionen är klar ansluter du till OpenShift-konsolen med webbläsaren med `OpenShift Console Url`hjälp av . Alternativt kan du SSH till OKD-hanteraren. Följande är ett exempel som använder utdata från distributionen:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Rensa resurser

Använd kommandot [az group delete](/cli/azure/group) för att ta bort resursgruppen, OpenShift-klustret och alla relaterade resurser när de inte längre behövs.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distribution](./openshift-container-platform-3x-post-deployment.md)
- [Felsöka OpenShift-distribution](./openshift-container-platform-3x-troubleshooting.md)
- [Komma igång med OKD](https://docs.okd.io)
