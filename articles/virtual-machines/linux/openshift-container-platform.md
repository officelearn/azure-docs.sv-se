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
ms.openlocfilehash: c91b7232b2f87e0b4b5e659126b96a6ef8b4202c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Distribuera OpenShift behållare plattform i Azure

Det finns flera sätt att distribuera OpenShift behållare plattform i Azure. Du kan distribuera alla nödvändiga Azure infrastrukturkomponenter manuellt och följ sedan OpenShift behållare plattform [dokumentationen](https://docs.openshift.com/container-platform/3.6/welcome/index.html).
Du kan också använda en befintlig Resource Manager-mall som förenklar distributionen av klustret OpenShift behållare plattform. När sådana mallen finns [här](https://github.com/Microsoft/openshift-container-platform/).

Ett annat alternativ är att använda den [Azure Marketplace-erbjudande](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

En Red Hat-prenumeration krävs för båda alternativen. Under distributionen av är RHEL-instansen registrerad med Red Hat-prenumeration och bifogas Pool-ID som innehåller rättigheter för OpenShift behållare plattform.
Se till att du har en giltig Red Hat prenumeration Manager Username, Password och Pool-ID (RHSM användarnamn, lösenord RHSM och Pool-ID). Du kan kontrollera informationen genom att logga in https://access.redhat.com.

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Distribuera med OpenShift behållare plattform Resource Manager-mall

Om du vill distribuera med hjälp av Resource Manager-mall, används en fil med parametrar för att ange alla indataparametrar. Om du vill anpassa distributionen-objekt som inte omfattas med indataparametrar, förgrening github-lagringsplatsen och ändra lämpliga objekt.

Några vanliga anpassningsalternativ inkluderar (men inte begränsat till):

- VNet CIDR [variabel i azuredeploy.json]
- Skyddsmiljö VM-storlek [variabel i azuredeploy.json]
- Namngivningskonventioner [variabler i azuredeploy.json]
- OpenShift klustret närmare information - ändra via värdfilen [deployOpenShift.sh]

### <a name="configure-parameters-file"></a>Konfigurera parameterfilen

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

Ersätt objekt omgiven av {} med ditt relevant information.

### <a name="deploy-using-azure-cli"></a>Distribuera med hjälp av Azure CLI

> [!NOTE] 
> Kommandot kräver Azure CLI 2.0.8 eller senare. Du kan verifiera az CLI version med den `az --version` kommando. Om du vill uppdatera CLI-versionen finns [installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

I följande exempel distribuerar OpenShift kluster och alla relaterade resurser i en resursgrupp med namnet myResourceGroup med ett distributionsnamn av myOpenShiftCluster. Mallen refereras direkt från github-lagringsplatsen och en fil med lokala parametrar med namnet **azuredeploy.parameters.json** filen används.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Distributionen tar minst 30 minuter att slutföra beroende på det totala antalet noder som har distribuerats. URL OpenShift konsolen och DNS-namnet för OpenShift master skrivs till terminalen när distributionen är klar.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-using-openshift-container-platform-marketplace-offer"></a>Distribuera med OpenShift behållare plattform marketplace-erbjudande

Det enklaste sättet att distribuera OpenShift behållare plattform i Azure är att använda den [Azure Marketplace-erbjudande](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Det här alternativet är det enklaste, men även har begränsade anpassningsmöjligheter. Erbjudandet omfattar tre konfigurationsalternativ:

- Liten: Distribuerar en icke-hög tillgänglighet kluster med en Master-nod, en infrastruktur-nod, två noder för programmet och en Skyddsmiljö nod. Alla noder är Standard DS2v2 VM-storlekar. Det här klustret kräver 10 Totalt antal kärnor och är perfekt för testning i liten skala.
- Medel: Distribuerar ett kluster för hög tillgänglighet med tre Master noder, två Infrastrukturnoder, fyra noder för programmet och en Skyddsmiljö nod. Alla noder utom i Skyddsmiljön är Standard DS3v2 VM-storlekar. Noden Skyddsmiljön är en Standard DS2v2. Det här klustret kräver 38 kärnor.
- Stora: Distribuerar en hög tillgänglighet kluster med tre Master noder, två Infrastrukturnoder, sex noder för programmet och en Skyddsmiljö nod. Huvud- och Infrastrukturnoder är Standard DS3v2 VM-storlekar, program-noderna är Standard DS4v2 VM-storlekar och noden Skyddsmiljön är en Standard DS2v2. Det här klustret kräver 70 kärnor.

Konfigurationen för providern för Azure-molnet är valfri för medelstora och stora klusterstorlekar. Liten klusterstorleken ger inte ett alternativ för att konfigurera Azure Cloud-providern.

## <a name="connect-to-the-openshift-cluster"></a>Anslut till klustret OpenShift

När distributionen är klar ansluter du till OpenShift konsolen med en webbläsare med hjälp av den `OpenShift Console Uri`. Du kan också ansluta till huvudservern OpenShift med följande kommando:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda den [ta bort grupp az](/cli/azure/group#delete) kommando för att ta bort resursgruppen, OpenShift kluster och alla relaterade resurser.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- [Uppgifter för efter distribution](./openshift-post-deployment.md)
- [Felsökning av OpenShift distribution](./openshift-troubleshooting.md)
- [Komma igång med OpenShift behållare plattform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
