---
title: Distribuera OpenShift Container Platform i Azure | Microsoft Docs
description: Distribuera OpenShift Container Platform i Azure.
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
ms.date: 02/04/2018
ms.author: haroldw
ms.openlocfilehash: aaf8ea70254e0edb66c6b88e6303c91b26de2e9b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453566"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Distribuera OpenShift Container Platform i Azure

Du kan använda en eller flera metoder för att distribuera OpenShift Container Platform i Azure:

- Du kan manuellt distribuera nödvändiga Azure infrastrukturkomponenter och följ sedan de [OpenShift Container Platform dokumentation](https://docs.openshift.com/container-platform).
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-container-platform/) som förenklar distributionen av klustret OpenShift Container Platform.
- Ett annat alternativ är att använda den [Azure Marketplace-erbjudande](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

En Red Hat-prenumeration krävs för alla alternativ. Under distributionen, är Red Hat Enterprise Linux-instans registrerad på Red Hat-prenumeration och bifogas Pool-ID som innehåller rättigheter för OpenShift Container Platform.
Kontrollera att du har en giltig Red Hat prenumeration Manager (RHSM) användarnamn, lösenord och Pool-ID. Du kan använda en aktiveringsnyckeln och Org ID Pool-ID. Du kan kontrollera den här informationen genom att logga in till https://access.redhat.com.

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Distribuera med OpenShift Container Platform Resource Manager-mall

För att distribuera med hjälp av Resource Manager-mall kan använda du en parameterfil för att ange indataparametrarna. För att ytterligare anpassa distributionen, Förgrena GitHub-lagringsplatsen och ändra lämpliga objekt.

Vissa vanliga anpassningsalternativ inkludera, men inte begränsat till:

- Skyddsmiljö VM-storlek (variabel i azuredeploy.json)
- Namngivningskonventioner (variabler i azuredeploy.json)
- OpenShift egenskaper för klustret, du ändrar värdfilen (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Konfigurera parameterfilen

Den [mall för OpenShift Container Platform](https://github.com/Microsoft/openshift-container-platform) har flera grenar som är tillgängliga för olika versioner av OpenShift Container Platform.  Utifrån dina behov kan du distribuera direkt från lagringsplatsen eller Förgrena lagringsplatsen och ändra anpassade mallar eller skript innan du distribuerar.

Använd den `appId` värdet för tjänstens huvudnamn som du skapade tidigare för den `aadClientId` parametern.

I följande exempel visas en parameterfil som heter azuredeploy.parameters.json med alla nödvändiga indata.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
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
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
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
        "rhsmBrokerPoolId": {
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
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "routing.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "openshiftvnet"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "mastersubnet"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "infrasubnet"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "nodesubnet"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/masterinfrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.201"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        },
        "proxySettings": {
            "value": "none"
        },
        "httpProxyEntry": {
            "value": "none"
        },
        "httpsProxyEntry": {
            "value": "none"
        },
        "noProxyEntry": {
            "value": "none"
        }
    }
}
```

Ersätt parametrarna med din specifika information.

Olika versioner kan ha olika parametrar, så kontrollera de nödvändiga parametrarna för den gren som du använder.

### <a name="deploy-using-azure-cli"></a>Distribuera med hjälp av Azure CLI

> [!NOTE] 
> Kommandot kräver Azure CLI 2.0.8 eller senare. Du kan kontrollera CLI-versionen med den `az --version` kommando. Om du vill uppdatera CLI-version, se [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

I följande exempel distribuerar OpenShift-klustret och alla relaterade resurser i en resursgrupp med namnet openshiftrg, med ett distributionsnamn av myOpenShiftCluster. Mallen refereras direkt från GitHub-lagringsplatsen och en lokal parametrar fil med namnet azuredeploy.parameters.json filen används.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Distributionen tar minst 30 minuter att slutföra, baserat på det totala antalet noder som har distribuerats och alternativ som har konfigurerats. Skyddsmiljö DNS FQDN- och URL: en för OpenShift konsolen skriver ut till terminalen när distributionen är klar.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Om du inte vill sysselsätta kommandoraden att vänta tills distributionen är slutförd, lägga till `--no-wait` som ett alternativ för gruppdistributionen av. Utdata från distributionen kan hämtas från Azure-portalen under distributionen för resursgruppen.
 
## <a name="deploy-using-the-openshift-container-platform-azure-marketplace-offer"></a>Distribuera med OpenShift Container Platform Azure Marketplace-erbjudande

Det enklaste sättet att distribuera OpenShift Container Platform på Azure är att använda den [Azure Marketplace-erbjudande](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Detta är det enklaste alternativet, men det kan också har begränsade funktioner för anpassning. Marketplace-erbjudandet innehåller följande konfigurationsalternativ:

- **Master noder**: Tre (3) Master-noder med konfigurerbara instans skriver.
- **Infra noder**: Tre (3) Infra noder med konfigurerbara instans skriver.
- **Noder**: Antalet noder kan konfigureras (mellan 2 och 9) samt vilken instanstyp.
- **Disktyp**: Hanterade diskar används.
- **Nätverk**: Stöd för nya eller befintliga nätverk samt anpassade CIDR-intervall.
- **CNS**: CNS kan aktiveras.
- **Mått**: Mått kan aktiveras.
- **Loggning**: Du kan aktivera loggning.
- **Azure Cloud Provider**: Du kan aktivera.

## <a name="connect-to-the-openshift-cluster"></a>Anslut till klustret för OpenShift

När distributionen är klar kan du hämta anslutningen från utdataavsnittet i distributionen. Ansluta till konsolen OpenShift med din webbläsare med hjälp av den `OpenShift Console URL`. Du kan också SSH till Skyddsmiljö-värd. Följande är ett exempel där administratörsanvändarnamnet är clusteradmin och den offentliga IP för skyddsmiljö DNS FQDN är bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Rensa resurser

Använd den [az group delete](/cli/azure/group) att ta bort resursgruppen, OpenShift klustret och alla relaterade resurser när de inte längre behövs.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distribution](./openshift-post-deployment.md)
- [Felsöka OpenShift-distribution i Azure](./openshift-troubleshooting.md)
- [Komma igång med OpenShift Container Platform](https://docs.openshift.com/container-platform)

### <a name="documentation-contributors"></a>Bidragsgivarna för dokumentationen

Vi vill Vincent Power (vincepower) och Alfred Sin (asinn826) för sina bidrag till att den här dokumentationen hålla dig uppdaterad!
