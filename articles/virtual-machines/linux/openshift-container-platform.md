---
title: Distribuera OpenShift Container Platform i Azure | Microsoft Docs
description: Distribuera OpenShift Container Platform i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2019
ms.author: haroldw
ms.openlocfilehash: 296bc42313ef80425004d3c9b43c6792cbaf97f4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718255"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Distribuera OpenShift Container Platform i Azure

Du kan använda en eller flera metoder för att distribuera OpenShift Container Platform i Azure:

- Du kan manuellt distribuera nödvändiga Azure infrastrukturkomponenter och följ sedan de [OpenShift Container Platform dokumentation](https://docs.openshift.com/container-platform).
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-container-platform/) som förenklar distributionen av klustret OpenShift Container Platform.
- Ett annat alternativ är att använda den [Azure Marketplace-erbjudande](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

En Red Hat-prenumeration krävs för alla alternativ. Under distributionen, är Red Hat Enterprise Linux-instans registrerad på Red Hat-prenumeration och bifogas Pool-ID som innehåller rättigheter för OpenShift Container Platform.
Kontrollera att du har en giltig Red Hat prenumeration Manager (RHSM) användarnamn, lösenord och Pool-ID. Du kan använda en aktiveringsnyckeln och Org ID Pool-ID. Du kan kontrollera den här informationen genom att logga in till https://access.redhat.com.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Distribuera med OpenShift Container Platform Resource Manager-mall

### <a name="private-clusters"></a>Privata kluster

Distribuera privat OpenShift-kluster kräver mer än bara inte har en offentlig IP-adress som är associerat till master belastningsutjämnaren (webbkonsolen) eller till den infra belastningsutjämnare (router).  Privata klustret använder vanligtvis en anpassad DNS-server (inte standard Azure DNS), ett anpassat domännamn (t.ex contoso.com) och fördefinierade virtuella nätverk.  För privata kluster måste du konfigurera ditt virtuella nätverk med alla lämpliga undernät och DNS-serverinställningarna i förväg.  Använd sedan **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference**, och  **existingNodeSubnetReference** att ange det befintliga undernätet för användning av klustret.

Om privata är markerad (**masterClusterType**= privata), en statisk privat IP-adress måste anges för **masterPrivateClusterIp**.  Denna IP-adress tilldelas till klienten för master belastningsutjämnaren.  IP-Adressen måste vara inom CIDR för master-undernät och inte är i användning.  **masterClusterDnsType** måste anges till ”anpassad” och DNS-namn måste anges för hanteraren **masterClusterDns**.  DNS-namnet måste mappas till den statiska privata IP och används för att få åtkomst till konsolen på de överordnade noderna.

Om privat router väljs (**routerClusterType**= privata), en statisk privat IP-adress måste anges för **routerPrivateClusterIp**.  Denna IP-adress tilldelas till klientdelen av den infrastruktur belastningsutjämnare.  IP-Adressen måste vara inom CIDR för den infrastruktur undernät och inte används.  **routingSubDomainType** måste anges till ”anpassad” och DNS-namn med jokertecken för routning måste anges för **routingSubDomain**.  

Om privat huvudservrar och privata router väljs det anpassade domännamnet också måste anges för **domännamn**

Efter slutförd distribution ser är Skyddsmiljö-noden den enda nod med en offentlig IP-adress som du kan ssh till.  Även om de överordnade noderna har konfigurerats för offentlig åtkomst, de visas inte för ssh åtkomst.

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

Olika versioner kan ha olika parametrar, så kontrollera de nödvändiga parametrarna för den gren som du använder.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Parameters.JSON förklarad

| Egenskap  | Beskrivning | Giltiga alternativ | Standardvärde |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL: en för artefakter (json, skript osv.) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Distribuera resurser till Azure-region |  |  |
| `masterVmSize` | Storlek på virtuell huvuddator. Välj någon av de tillåtna storlekar som anges i filen azuredeploy.JSON |  | Standard_E2s_v3 |
| `infraVmSize` | Storleken på den Infra VM. Välj någon av de tillåtna storlekar som anges i filen azuredeploy.JSON |  | Standard_D4s_v3 |
| `nodeVmSize` | Storleken på Appnod VM. Välj någon av de tillåtna storlekar som anges i filen azuredeploy.JSON |  | Standard_D4s_v3 |
| `cnsVmSize` | Storleken på behållare inbyggda (CNS) Lagringsnoden VM. Välj någon av de tillåtna storlekar som anges i filen azuredeploy.JSON |  | Standard_E4s_v3 |
| `osImageType` | Avbildningen som RHEL ska användas. defaultgallery: På begäran; Marketplace: tredjeparts-bild | defaultgallery <br> marketplace | defaultgallery |
| `marketplaceOsImage` | Om `osImageType` är marketplace och sedan ange lämpliga värden för ”publisher”, ”erbjudande”, ”sku”, ”version” i marketplace-erbjudandet. Den här parametern är en objekttyp |  |  |
| `storageKind` | Lagringstyp som ska användas  | Hanterad<br> Ohanterade | Hanterad |
| `openshiftClusterPrefix` | Kluster-Prefix som används för att konfigurera värdnamnen för alla noder.  Mellan 1 och 20 tecken |  | Det här klustret. |
| `minoVersion` | Lägre version av OpenShift Container Platform 3.11 att distribuera |  | 69 |
| `masterInstanceCount` | Antalet huvudservrar noder att distribuera | 1, 3, 5 | 3 |
| `infraInstanceCount` | Antal infra noder att distribuera | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Antalet noder för att distribuera | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Antalet CNS noder att distribuera | 3, 4 | 3 |
| `osDiskSize` | Storleken på operativsystemdisken för den virtuella datorn (i GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Storleken på datadisk för att ansluta till noder för Docker volym (i GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Storleken på datadisken för att ansluta till CNS noder för användning med glusterfs (i GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Administratörens användarnamn för både OS (VM)-inloggning och en inledande OpenShift-användare |  | ocpadmin |
| `enableMetrics` | Aktivera mätvärden. Mått kräver mer resurser, så Välj rätt storlek för Infra VM | true <br> false | false |
| `enableLogging` | Aktivera loggning. elasticsearch pod kräver 8 GB RAM-minne, så Välj rätt storlek för Infra VM | true <br> false | false |
| `enableCNS` | Aktivera behållare intern lagring | true <br> false | false |
| `rhsmUsernameOrOrgId` | Red Hat prenumeration Manager användarnamn eller organisation-ID |  |  |
| `rhsmPoolId` | Red Hat prenumeration Manager poolens ID som innehåller dina OpenShift rättigheter för beräkningsnoder |  |  |
| `rhsmBrokerPoolId` | Red Hat prenumeration Manager Pool ID som innehåller dina OpenShift-rättigheter för huvudservrar och infrastruktur-noder. Om du inte har olika pooler ID: N, anger du samma pool-ID som 'rhsmPoolId' |  |
| `sshPublicKey` | Kopiera din offentlig SSH-nyckel här |  |  |
| `keyVaultSubscriptionId` | Prenumerations-ID för den prenumeration som innehåller Nyckelvalvet |  |  |
| `keyVaultResourceGroup` | Namnet på resursgruppen som innehåller Nyckelvalvet |  |  |
| `keyVaultName` | Namnet på det Nyckelvalv som du skapade |  |  |
| `enableAzure` | Aktivera Azure-molnet Provider | true <br> false | true |
| `aadClientId` | Azure Active Directory klient-ID även känt som program-ID för tjänstens huvudnamn |  |  |
| `domainName` | Namnet på det anpassade domännamnet (om tillämpligt). Inställt på ”none” om det inte distribuera fullständigt privata klustret |  | inga |
| `masterClusterDnsType` | Domäntyp för OpenShift webbkonsolen. ”standard” använder DNS-etikett för master infra offentlig IP-adress. ”anpassade” kan du definiera egna namn | standard <br> anpassad | standard |
| `masterClusterDns` | Det DNS-namnet du använder för att få åtkomst till webbkonsolen för OpenShift om du har valt 'custom' för `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Om inställd nipio, `routingSubDomain` använder nip.io.  Använda ”anpassad” om du har en egen domän som du vill använda för Routning | nipio <br> anpassad | nipio |
| `routingSubDomain` | Jokertecken DNS-namn som du vill använda för routning om du har valt 'custom' för `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Välj om du vill använda ett befintligt virtuellt nätverk eller skapa ett nytt virtuellt nätverk | befintliga <br> nytt | nytt |
| `virtualNetworkResourceGroupName` | Namnet på resursgruppen för det nya virtuella nätverket om du har valt ”nytt” för `virtualNetworkNewOrExisting` |  | resourceGroup().name |
| `virtualNetworkName` | Namnet på det nya virtuella nätverket för att skapa om du har valt ”nytt” för `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Adressprefix för det nya virtuella nätverket |  | 10.0.0.0/14 |
| `masterSubnetName` | Namnet på master undernätet |  | mastersubnet |
| `masterSubnetPrefix` | CIDR som används för master undernät - måste vara en delmängd av addressPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | Namnet på den infra undernät |  | infrasubnet |
| `infraSubnetPrefix` | CIDR som används för den infrastruktur undernät - måste vara en delmängd av addressPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | Namnet på undernätet för noden |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR som används för nod-undernät - måste vara en delmängd av addressPrefix |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Fullständig referens till befintliga undernät för överordnade noder. Behövs inte om du skapar nya vNet / undernät |  |  |
| `existingInfraSubnetReference` | Fullständig referens till befintliga undernätet för infrastruktur noder. Behövs inte om du skapar nya vNet / undernät |  |  |
| `existingCnsSubnetReference` | Fullständig referens till befintliga undernät för CNS noder. Behövs inte om du skapar nya vNet / undernät |  |  |
| `existingNodeSubnetReference` | Fullständig referens till befintligt undernät för beräkningsnoder. Behövs inte om du skapar nya vNet / undernät |  |  |
| `masterClusterType` | Ange om klustret använder privata eller offentliga överordnade noder. Om du väljer privat exponeras de överordnade noderna inte till Internet via en offentlig IP-adress. I stället använder den privata IP-Adressen som anges i den `masterPrivateClusterIp` | offentlig <br> privat | offentlig |
| `masterPrivateClusterIp` | Om privat överordnade noder har valts, måste sedan en privat IP-adress anges för användning av den interna belastningsutjämnaren för överordnade noder. Den här statisk IP-adress måste vara i CIDR-block för master-undernät och inte redan används. Om offentliga överordnade noder har valts, det här värdet används inte men fortfarande måste anges |  | 10.1.0.200 |
| `routerClusterType` | Ange om klustret använder privat eller offentlig infra noder. Om du väljer privat den infra noder inte exponeras till Internet via en offentlig IP-adress. I stället använder den privata IP-Adressen som anges i den `routerPrivateClusterIp` | offentlig <br> privat | offentlig |
| `routerPrivateClusterIp` | Om privat infra noder har valts och sedan en privat IP-adress måste anges för använda genom att den interna belastningsutjämnaren för infra noder. Den här statisk IP-adress måste vara i CIDR-block för master-undernät och inte redan används. Om offentliga infra noder har valts, det här värdet används inte men fortfarande måste anges |  | 10.2.0.200 |
| `routingCertType` | Använda anpassade certifikat för routningsdomän eller det självsignerade certifikatet för standard - följer du anvisningarna i **anpassade certifikat** avsnittet | selfsigned <br> anpassad | selfsigned |
| `masterCertType` | Använda anpassade certifikat för domänen eller det självsignerade certifikatet för standard - följer du anvisningarna i **anpassade certifikat** avsnittet | selfsigned <br> anpassad | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Distribuera med hjälp av Azure CLI

> [!NOTE] 
> Kommandot kräver Azure CLI 2.0.8 eller senare. Du kan kontrollera CLI-versionen med den `az --version` kommando. Om du vill uppdatera CLI-version, se [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

I följande exempel distribuerar OpenShift-klustret och alla relaterade resurser i en resursgrupp med namnet openshiftrg, med ett distributionsnamn av myOpenShiftCluster. Mallen refereras direkt från GitHub-lagringsplatsen och en lokal parametrar fil med namnet azuredeploy.parameters.json filen används.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Distributionen tar minst 60 minuter att slutföra, baserat på det totala antalet noder som har distribuerats och alternativ som har konfigurerats. Skyddsmiljö DNS FQDN- och URL: en för OpenShift konsolen skriver ut till terminalen när distributionen är klar.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Om du inte vill sysselsätta kommandoraden att vänta tills distributionen är slutförd, lägga till `--no-wait` som ett alternativ för gruppdistributionen av. Utdata från distributionen kan hämtas från Azure-portalen under distributionen för resursgruppen.

## <a name="connect-to-the-openshift-cluster"></a>Anslut till klustret för OpenShift

När distributionen är klar kan du hämta anslutningen från utdataavsnittet i distributionen. Ansluta till konsolen OpenShift med din webbläsare med hjälp av den **OpenShift-konsolens URL**. Du kan också SSH till Skyddsmiljö-värd. Följande är ett exempel där administratörsanvändarnamnet är clusteradmin och den offentliga IP för skyddsmiljö DNS FQDN är bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

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
