---
title: Distribuera OpenShift Container Platform i Azure | Microsoft Docs
description: Distribuera OpenShift Container Platform i Azure.
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
ms.openlocfilehash: a275df4567053149688694315ff24ac1ad7f711f
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43186922"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Distribuera OpenShift Container Platform i Azure

Du kan använda en eller flera metoder för att distribuera OpenShift Container Platform i Azure:

- Du kan manuellt distribuera nödvändiga Azure infrastrukturkomponenter och följ sedan OpenShift Container Platform [dokumentation](https://docs.openshift.com/container-platform/3.10/welcome/index.html).
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-container-platform/) som förenklar distributionen av klustret OpenShift Container Platform.
- Ett annat alternativ är att använda den [Azure Marketplace-erbjudande](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

En Red Hat-prenumeration krävs för alla alternativ. Under distributionen, är Red Hat Enterprise Linux-instans registrerad på Red Hat-prenumeration och bifogas Pool-ID som innehåller rättigheter för OpenShift Container Platform.
Kontrollera att du har ett giltigt Red Hat prenumeration Manager (RHSM)-användarnamn, lösenord och Pool-ID. Du kan kontrollera den här informationen genom att logga in till https://access.redhat.com.

## <a name="deploy-by-using-the-openshift-container-platform-resource-manager-template"></a>Distribuera med hjälp av OpenShift Container Platform Resource Manager-mall

För att distribuera med hjälp av Resource Manager-mall kan använda du en parameterfil för att ange indataparametrarna som. Om du vill anpassa distributionen-objekt som inte omfattas av med indataparametrar Förgrena GitHub-lagringsplatsen och ändra lämpliga objekt.

Vissa vanliga anpassningsalternativ inkludera, men är inte begränsade till:

- Virtuellt nätverk CIDR (variabel i azuredeploy.json)
- Skyddsmiljö VM-storlek (variabel i azuredeploy.json)
- Namngivningskonventioner (variabler i azuredeploy.json)
- OpenShift egenskaper för klustret, du ändrar värdfilen (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Konfigurera parameterfilen

Använd den `appId` värdet för tjänstens huvudnamn som du skapade tidigare för den `aadClientId` parametern. 

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
> Kommandot kräver Azure CLI 2.0.8 eller senare. Du kan kontrollera CLI-versionen med den `az --version` kommando. Om du vill uppdatera CLI-version, se [installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

I följande exempel distribuerar OpenShift-klustret och alla relaterade resurser i en resursgrupp som heter myResourceGroup, med ett distributionsnamn av myOpenShiftCluster. Mallen refereras direkt från GitHub-lagringsplatsen och en lokal parametrar fil med namnet azuredeploy.parameters.json filen används.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Distributionen tar minst 30 minuter att slutföra beroende på det totala antalet noder som har distribuerats. URL till konsolen OpenShift och DNS-namnet på skriver ut master OpenShift till terminalen när distributionen är klar.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-by-using-the-openshift-container-platform-azure-marketplace-offer"></a>Distribuera med hjälp av OpenShift Container Platform Azure Marketplace-erbjudande

Det enklaste sättet att distribuera OpenShift Container Platform på Azure är att använda den [Azure Marketplace-erbjudande](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Detta är det enklaste alternativet, men det kan också har begränsade funktioner för anpassning. Erbjudandet omfattar tre konfigurationsalternativ:

- **Små**: distribuerar ett kluster för och hög tillgänglighet (HA) med en huvudnod, en infrastruktur-nod, två noder i programmet och en skyddsmiljö-nod. Alla noder är standard DS2v2 VM-storlekar. Det här klustret kräver 10 Totalt antal kärnor och är perfekt för småskalig testning.
- **Medel**: distribuerar ett kluster med hög tillgänglighet med tre huvudnoder, två infrastrukturnoder, fyra programnoder och en skyddsmiljö-nod. Alla noder utom noden skyddsmiljö är standard DS3v2 VM-storlekar. Skyddsmiljö-nod är en standard DS2v2. Det här klustret kräver 38 kärnor.
- **Stora**: distribuerar ett kluster med hög tillgänglighet med tre huvudnoder, två infrastrukturnoder, sex programnoder krävs och en skyddsmiljö-nod. Huvud- och infrastruktur-noder är standard DS3v2 VM-storlekar. Programnoder är standard DS4v2 VM-storlekar och skyddsmiljö-nod är en standard DS2v2. Det här klustret kräver 70 kärnor.

Konfiguration av Azure Cloud Solution Provider är valfritt för medelstora och stora klusterstorlekar. Små klusterstorleken ger inte ett alternativ för att konfigurera Azure Cloud Solution Provider.

## <a name="connect-to-the-openshift-cluster"></a>Anslut till klustret för OpenShift

När distributionen är klar ansluter du till konsolen OpenShift med din webbläsare med hjälp av den `OpenShift Console Uri`. Du kan också ansluta till huvudservern OpenShift med hjälp av följande kommando:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Rensa resurser

Använd den [az group delete](/cli/azure/group#az_group_delete) att ta bort resursgruppen, OpenShift klustret och alla relaterade resurser när de inte längre behövs.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distribution](./openshift-post-deployment.md)
- [Felsöka OpenShift-distribution i Azure](./openshift-troubleshooting.md)
- [Komma igång med OpenShift Container Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
