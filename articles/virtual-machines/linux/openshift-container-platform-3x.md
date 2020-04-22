---
title: Distribuera OpenShift Container Platform 3.11 i Azure
description: Distribuera OpenShift Container Platform 3.11 i Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: 7d6cd4c6ce7991ae83f6f4a1dd6d8b86fe7eedbc
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757894"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Distribuera OpenShift Container Platform 3.11 i Azure

Du kan använda en av flera metoder för att distribuera OpenShift Container Platform 3.11 i Azure:

- Du kan distribuera nödvändiga Azure-infrastrukturkomponenter manuellt och sedan följa dokumentationen till [OpenShift Container Platform](https://docs.openshift.com/container-platform).
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-container-platform/) som förenklar distributionen av OpenShift Container Platform-klustret.
- Ett annat alternativ är att använda [Azure Marketplace-erbjudandet](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy).

För alla alternativ krävs en Red Hat-prenumeration. Under distributionen registreras Red Hat Enterprise Linux-instansen i Red Hat-prenumerationen och är kopplad till pool-ID:t som innehåller berättigandena för OpenShift Container Platform.
Kontrollera att du har ett giltigt användarnamn, lösenord och pool-ID (Red Hat Subscription Manager). Du kan använda en aktiveringsnyckel, organisations-ID och pool-ID. Du kan verifiera den här https://access.redhat.cominformationen genom att logga in på .


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Distribuera med hjälp av openshift-distributionsplattformen Resource Manager 3.11

### <a name="private-clusters"></a>Privata kluster

Distribuera privata OpenShift-kluster kräver mer än bara att inte ha en offentlig IP associerad till huvudbelastningsutjämnaren (webbkonsol) eller till infra belastningsutjämnaren (routern).  Ett privat kluster använder vanligtvis en anpassad DNS-server (inte standard-Azure DNS), ett anpassat domännamn (till exempel contoso.com) och fördefinierade virtuella nätverk.  För privata kluster måste du konfigurera det virtuella nätverket med alla lämpliga undernät och DNS-serverinställningar i förväg.  Använd sedan **befintligaMasterSubnetReference**, **existerandeInfraSubnetReference**, **existingCnsSubnetReference**och **existingNodeSubnetReference** för att ange det befintliga undernätet för användning av klustret.

Om privat master väljs (**masterClusterType**=private) måste en statisk privat IP-adress anges för **masterPrivateClusterIp**.  Den här IP-adressen tilldelas den främre delen av huvudbelastningsutjämnaren.  IP-adressen måste finnas inom CIDR för huvudundernätet och inte användas.  **masterClusterDnsType** måste vara inställd på "anpassad" och huvud-DNS-namnet måste anges för **masterClusterDns**.  DNS-namnet måste mappas till den statiska privata IP-adressen och kommer att användas för att komma åt konsolen på huvudnoderna.

Om privat router väljs **(routerClusterType**=privat) måste en statisk privat IP-adress anges för **routerPrivateClusterIp**.  Denna IP kommer att tilldelas den främre änden av infra belastningsutjämnaren.  IP-adressen måste ligga inom CIDR för infra-undernätet och inte användas.  **routingSubDomainType** måste vara inställt på "anpassad" och JOKERTECKNET DNS-namnet för routning måste anges för **routningSubDomain**.  

Om privata bakgrunder och privat router är markerade måste det anpassade domännamnet också anges för **domännamn**

Efter en lyckad distribution är Bastion-noden den enda noden med en offentlig IP som du kan ssh i.  Även om huvudnoderna är konfigurerade för offentlig åtkomst, är de inte exponerade för ssh-åtkomst.

Om du vill distribuera med resurshanteraren använder du en parameterfil för att ange indataparametrarna. Om du vill anpassa distributionen ytterligare förfalskar du GitHub-repoen och ändrar lämpliga objekt.

Några vanliga anpassningsalternativ är, men är inte begränsade till:

- Bastion VM-storlek (variabel i azuredeploy.json)
- Namngivningskonventioner (variabler i azuredeploy.json)
- OpenShift kluster detaljer, ändras via hosts-fil (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Konfigurera parameterfilen

OpenShift [Container Platform-mallen](https://github.com/Microsoft/openshift-container-platform) har flera grenar tillgängliga för olika versioner av OpenShift Container Platform.  Baserat på dina behov kan du distribuera direkt från repo eller så kan du förfalska repoerna och göra anpassade ändringar i mallarna eller skripten innan du distribuerar.

Använd `appId` värdet från tjänstens huvudnamn `aadClientId` som du skapade tidigare för parametern.

I följande exempel visas en parameterfil med namnet azuredeploy.parameters.json med alla nödvändiga indata.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
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
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
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
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
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
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Ersätt parametrarna med din specifika information.

Olika versioner kan ha olika parametrar så kontrollera de nödvändiga parametrarna för grenen du använder.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Parameters.json fil förklaras

| Egenskap | Beskrivning | Giltiga alternativ | Standardvärde |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL för artefakter (json, skript, etc.) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Azure-regionen för att distribuera resurser till |  |  |
| `masterVmSize` | Storleken på huvuddatorn. Välj bland en av de tillåtna vm-storlekarna i filen azuredeploy.json |  | Standard_E2s_v3 |
| `infraVmSize` | Storleken på den virtuella datorn för Infra. Välj bland en av de tillåtna vm-storlekarna i filen azuredeploy.json |  | Standard_D4s_v3 |
| `nodeVmSize` | Storleken på den virtuella datorn för appnod. Välj bland en av de tillåtna vm-storlekarna i filen azuredeploy.json |  | Standard_D4s_v3 |
| `cnsVmSize` | Storleken på CNS-noden (Container Native Storage) nod. Välj bland en av de tillåtna vm-storlekarna i filen azuredeploy.json |  | Standard_E4s_v3 |
| `osImageType` | Den RHEL-avbildning som ska användas. defaultgallery: On-Demand; marknadsplats: bild från tredje part | standardgallery <br> marketplace | standardgallery |
| `marketplaceOsImage` | Om `osImageType` är marknadsplats, ange sedan lämpliga värden för "utgivare", "erbjudande", "sku", "version" av marknadsplatsen erbjudandet. Den här parametern är en objekttyp |  |  |
| `storageKind` | Vilken typ av lagring som ska användas  | Hanterade<br> Ohanterad | Hanterade |
| `openshiftClusterPrefix` | Klusterprefix som används för att konfigurera värdnamn för alla noder.  Mellan 1 och 20 tecken |  | mykluster |
| `minoVersion` | Den delversion av OpenShift Container Platform 3.11 som ska distribueras |  | 69 |
| `masterInstanceCount` | Antal mastersnoder som ska distribueras | 1, 3, 5 | 3 |
| `infraInstanceCount` | Antal infranoder som ska distribueras | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Antal noder som ska distribueras | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Antal CNS-noder som ska distribueras | 3, 4 | 3 |
| `osDiskSize` | Storleken på OS-disken för den virtuella datorn (i GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Storleken på datadisken som ska kopplas till noder för Docker-volym (i GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Storlek på datadisken som ska kopplas till CNS-noder för användning av glusterf :a | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Administratörsanvändarnamn för både OS-inloggning (VM) och första OpenShift-användare |  | ocpadmin (ocpadmin) |
| `enableMetrics` | Aktivera mått. Mått kräver mer resurser så välj rätt storlek för Infra VM | true <br> false | false |
| `enableLogging` | Aktivera loggning. elasticsearch pod kräver 8 GB RAM så välj rätt storlek för Infra VM | true <br> false | false |
| `enableCNS` | Aktivera inbyggd lagring av behållare | true <br> false | false |
| `rhsmUsernameOrOrgId` | Användarnamn eller organisations-ID för Red Hat-prenumerationshanteraren |  |  |
| `rhsmPoolId` | Pool-ID för Red Hat-prenumerationshanteraren som innehåller dina OpenShift-berättiganden för beräkningsnoder |  |  |
| `rhsmBrokerPoolId` | Pool-ID för Red Hat-prenumerationshanteraren som innehåller dina OpenShift-berättiganden för bakgrunder och infranoder. Om du inte har olika pool-ID anger du samma pool-ID som rhsmPoolId |  |
| `sshPublicKey` | Kopiera din SSH Public Key här |  |  |
| `keyVaultSubscriptionId` | Prenumerations-ID för prenumerationen som innehåller Key Vault |  |  |
| `keyVaultResourceGroup` | Namnet på resursgruppen som innehåller Nyckelvalvet |  |  |
| `keyVaultName` | Namnet på det Nyckelvalv som du skapade |  |  |
| `enableAzure` | Aktivera Azure Cloud Provider | true <br> false | true |
| `aadClientId` | Azure Active Directory-klient-ID kallas även program-ID för tjänsthuvudnamn |  |  |
| `domainName` | Namn på det anpassade domännamn som ska användas (om tillämpligt). Ange till "ingen" om du inte distribuerar helt privat kluster |  | ingen |
| `masterClusterDnsType` | Domäntyp för OpenShift-webbkonsol. "standard" kommer att använda DNS-etikett master infra offentliga IP. "anpassade" kan du definiera ditt eget namn | standard <br> Anpassade | standard |
| `masterClusterDns` | Det anpassade DNS-namn som ska användas för att komma åt OpenShift-webbkonsolen om du har valt`masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Om den är inställd på `routingSubDomain` "nipio" kommer nip.io att användas.  Använd "anpassad" om du har en egen domän som du vill använda för routning | nipio (nipio) <br> Anpassade | nipio (nipio) |
| `routingSubDomain` | Jokertecknet DNS-namn som du vill använda för routning om du har valt "anpassad" för`routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Välj om du vill använda ett befintligt virtuellt nätverk eller skapa ett nytt virtuellt nätverk | Befintliga <br> ny | ny |
| `virtualNetworkResourceGroupName` | Namn på resursgruppen för det nya virtuella nätverket om du har valt "ny" för`virtualNetworkNewOrExisting` |  | resourceGroup().namn |
| `virtualNetworkName` | Namnet på det nya virtuella nätverket som ska skapas om du har valt "ny" för`virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Adressprefix för det nya virtuella nätverket |  | 10.0.0.0/14 |
| `masterSubnetName` | Namnet på huvudundernätet |  | mastersubnet (svenska) |
| `masterSubnetPrefix` | CIDR som används för huvudundernätet - måste vara en delmängd av addressPrefixet |  | 10.1.0.0/16 |
| `infraSubnetName` | Namnet på det infra undernätet |  | infrasubnet |
| `infraSubnetPrefix` | CIDR som används för infra-undernätet - måste vara en delmängd av adressenPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | Namnet på nodundernätet |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR som används för nodundernätet - måste vara en delmängd av addressPrefix |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Fullständig referens till befintligt undernät för huvudnoder. Behövs inte om du skapar nytt vNet/ Undernät |  |  |
| `existingInfraSubnetReference` | Fullständig referens till befintligt undernät för infranoder. Behövs inte om du skapar nytt vNet/ Undernät |  |  |
| `existingCnsSubnetReference` | Fullständig referens till befintligt undernät för CNS-noder. Behövs inte om du skapar nytt vNet/ Undernät |  |  |
| `existingNodeSubnetReference` | Fullständig referens till befintligt undernät för beräkningsnoder. Behövs inte om du skapar nytt vNet/ Undernät |  |  |
| `masterClusterType` | Ange om klustret använder privata eller offentliga huvudnoder. Om privat väljs exponeras inte huvudnoderna för Internet via en offentlig IP.If private is chosen, the master nodes won't be exposed to the Internet via a public IP. I stället används den privata IP-adress som anges i`masterPrivateClusterIp` | Offentliga <br> Privat | Offentliga |
| `masterPrivateClusterIp` | Om privata huvudnoder väljs måste en privat IP-adress anges för användning av den interna belastningsutjämnaren för huvudnoder. Den här statiska IP-adressen måste finnas inom CIDR-blocket för huvudundernätet och används inte redan. Om offentliga huvudnoder är markerade används inte det här värdet utan måste ändå anges |  | 10.1.0.200 |
| `routerClusterType` | Ange om klustret använder privata eller offentliga infranoder. Om privat väljs exponeras inte infranoderna för Internet via en offentlig IP. I stället används den privata IP-adress som anges i`routerPrivateClusterIp` | Offentliga <br> Privat | Offentliga |
| `routerPrivateClusterIp` | Om privata infra-noder väljs måste en privat IP-adress anges för användning av den interna belastningsutjämnaren för infra-noder. Denna statiska IP måste finnas inom CIDR-blocket för infra-undernätet och används inte redan. Om offentliga infra-noder väljs används inte det här värdet utan måste ändå anges |  | 10.2.0.200 |
| `routingCertType` | Använd anpassat certifikat för routningsdomän eller standardlicensen för självsignerade certifikat – följ instruktionerna i avsnittet **Anpassade certifikat** | självsignerade <br> Anpassade | självsignerade |
| `masterCertType` | Använd anpassat certifikat för huvuddomänen eller standardlicensen för självsignerade certifikat – följ instruktionerna i avsnittet **Anpassade certifikat** | självsignerade <br> Anpassade | självsignerade |

<br>

### <a name="deploy-using-azure-cli"></a>Distribuera med Azure CLI

> [!NOTE] 
> Följande kommando kräver Azure CLI 2.0.8 eller senare. Du kan verifiera CLI-versionen med `az --version` kommandot. Information om hur du uppdaterar CLI-versionen finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

I följande exempel distribueras OpenShift-klustret och alla relaterade resurser till en resursgrupp med namnet openshiftrg, med distributionsnamnet myOpenShiftCluster. Mallen refereras direkt från GitHub-repoen och en lokal parameterfil med namnet azuredeploy.parameters.json-fil används.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Distributionen tar minst 60 minuter att slutföra, baserat på det totala antalet noder som har distribuerats och de konfigurerade alternativen. Bastion DNS FQDN och URL för OpenShift-konsolen skrivs ut till terminalen när distributionen är klar.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Om du inte vill binda upp kommandoraden som väntar `--no-wait` på att distributionen ska slutföras lägger du till som ett av alternativen för gruppdistributionen. Utdata från distributionen kan hämtas från Azure-portalen i distributionsavsnittet för resursgruppen.

## <a name="connect-to-the-openshift-cluster"></a>Ansluta till OpenShift-klustret

När distributionen är klar hämtar du anslutningen från utdataavsnittet i distributionen. Anslut till OpenShift-konsolen med webbläsaren med hjälp av **Url:en för OpenShift-konsolen**. Du kan också SSH till Bastion värd. Följande är ett exempel där administratören användarnamn är clusteradmin och bastion offentliga IP DNS FQDN är bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Rensa resurser

Använd kommandot [az group delete](/cli/azure/group) för att ta bort resursgruppen, OpenShift-klustret och alla relaterade resurser när de inte längre behövs.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distribution](./openshift-container-platform-3x-post-deployment.md)
- [Felsöka OpenShift-distribution i Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Komma igång med OpenShift Container Platform](https://docs.openshift.com)
