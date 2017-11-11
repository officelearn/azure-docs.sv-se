---
title: "Distribuera OpenShift behållare plattform i Azure | Microsoft Docs"
description: "Distribuera OpenShift behållare plattform i Azure."
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
ms.openlocfilehash: 159f30fc59a050b9a4ff983e8ac84e424104b484
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Distribuera OpenShift behållare plattform i Azure

Du kan använda flera metoder för att distribuera OpenShift behållare plattform i Azure:

- Du kan distribuera manuellt Azure nödvändiga infrastrukturkomponenter och följ sedan OpenShift behållare plattform [dokumentationen](https://docs.openshift.com/container-platform/3.6/welcome/index.html).
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-container-platform/) som förenklar distributionen av klustret OpenShift behållare plattform.
- Ett annat alternativ är att använda den [Azure Marketplace-erbjudande](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

En Red Hat-prenumeration krävs för alla alternativ. Under distributionen av är Red Hat Enterprise Linux-instansen registrerad på Red Hat-prenumeration och bifogas Pool-ID som innehåller rättigheter för OpenShift behållare plattform.
Kontrollera att du har ett giltigt Red Hat prenumeration Manager (RHSM) användarnamn, lösenord och Pool-ID. Den här informationen kan du kontrollera genom att logga in https://access.redhat.com.

## <a name="deploy-by-using-the-openshift-container-platform-resource-manager-template"></a>Distribuera med hjälp av OpenShift behållare plattform Resource Manager-mall

Om du vill distribuera med hjälp av Resource Manager-mallen använder du en fil med parametrar för att ange indataparametrarna. Om du vill anpassa distributionen-objekt som inte omfattas av använder indataparametrar duplicera GitHub-lagringsplatsen och ändra lämpliga objekt.

Några vanliga anpassningsalternativ inkludera, men är inte begränsade till:

- Virtuellt nätverk CIDR (variabel i azuredeploy.json)
- Skyddsmiljö VM-storlek (variabel i azuredeploy.json)
- Namnkonventioner (variabler i azuredeploy.json)
- OpenShift klustret egenskaper, ändras via hosts-filen (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Konfigurera parameterfilen

Använd den `appId` värde från huvudnamn för tjänsten som du skapade tidigare för den `aadClientId` parameter. 

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
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "true"
        },
        "enableCockpit": {
            "value": "false"
        },
        "rhsmUsernamePasswordOrActivationKey": {
            "value": "usernamepassword"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
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

Ersätta objekt inom hakparenteser med din specifika information.

### <a name="deploy-by-using-azure-cli"></a>Distribuera med hjälp av Azure CLI

> [!NOTE] 
> Kommandot kräver Azure CLI 2.0.8 eller senare. Du kan verifiera CLI-versionen med den `az --version` kommando. Om du vill uppdatera CLI-versionen finns [installera Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latesti).

I följande exempel distribuerar OpenShift kluster och alla relaterade resurser i en resursgrupp med namnet myResourceGroup, med ett distributionsnamn av myOpenShiftCluster. Mallen som refereras direkt från GitHub-lagringsplatsen och en lokal fil med namnet azuredeploy.parameters.json filen används-parametrar.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Distributionen tar minst 30 minuter att slutföra beroende på det totala antalet noder som har distribuerats. URL till konsolen OpenShift och DNS-namnet på OpenShift master utskrifter till terminal när distributionen är klar.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-by-using-the-openshift-container-platform-azure-marketplace-offer"></a>Distribuera med hjälp av OpenShift behållare plattformen Azure Marketplace-erbjudande

Det enklaste sättet att distribuera OpenShift behållare plattform i Azure är att använda den [Azure Marketplace-erbjudande](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Detta är det enklaste alternativet, men också har det begränsade möjligheter. Erbjudandet omfattar tre konfigurationsalternativ:

- **Liten**: distribuerar ett icke-hög tillgänglighet (HA)-kluster med en nod, infrastruktur för en nod, två noder för programmet och en skyddsmiljö nod. Alla noder är standard DS2v2 VM-storlekar. Det här klustret kräver 10 Totalt antal kärnor och är idealisk för småskalig testning.
- **Medel**: distribuerar ett kluster för hög tillgänglighet med tre överordnade noder, två infrastrukturnoder, fyra noder för programmet och en skyddsmiljö nod. Alla noder utom skyddsmiljö nod är standard DS3v2 VM-storlekar. Noden skyddsmiljön är en standard DS2v2. Det här klustret kräver 38 kärnor.
- **Stora**: distribuerar ett kluster för hög tillgänglighet med tre överordnade noder, två infrastrukturnoder, sex noder för programmet och en skyddsmiljö nod. Huvud- och infrastruktur-noderna är standard DS3v2 VM-storlekar. Programmet noderna är standard DS4v2 VM-storlekar och noden skyddsmiljö är en standard DS2v2. Det här klustret kräver 70 kärnor.

Konfigurationen av Azure Cloud Solution Provider är valfri för medelstora och stora klusterstorlekar. Liten klusterstorleken ger inte ett alternativ för att konfigurera Azure Cloud Solution Provider.

## <a name="connect-to-the-openshift-cluster"></a>Anslut till klustret OpenShift

När distributionen är klar, ansluta till konsolen OpenShift med din webbläsare med hjälp av den `OpenShift Console Uri`. Du kan också ansluta till OpenShift master med hjälp av följande kommando:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Rensa resurser

Använd den [ta bort grupp az](/cli/azure/group#delete) kommando för att ta bort resursgruppen, OpenShift kluster och alla relaterade resurser när de inte längre behövs.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distributionen](./openshift-post-deployment.md)
- [Felsöka OpenShift distribution i Azure](./openshift-troubleshooting.md)
- [Komma igång med OpenShift behållare plattform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
