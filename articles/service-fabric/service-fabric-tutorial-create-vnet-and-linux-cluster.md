---
title: Skapa ett Linux Service Fabric-kluster i Azure | Microsoft Docs
description: "Lär dig att distribuera ett Linux Service Fabric-kluster till ett befintligt virtuellt nätverk i Azure med Azure CLI."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/22/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 3b09e676a26336d1ef1e744f9e45066c4815fe21
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Distribuera ett Service Fabric Linux-kluster till ett virtuellt Azure-nätverk
Den här självstudien är del ett i en serie. Du lär dig att distribuera ett Linux Service Fabric-kluster till ett [virtuellt nätverk i Azure (VNET)](../virtual-network/virtual-networks-overview.md) och [en nätverkssäkerhetsgrupp (NSG)](../virtual-network/virtual-networks-nsg.md) med Azure CLI och en mall. När du är klar körs ett kluster i molnet som du kan distribuera program till. Om du vill skapa ett Windows-kluster med PowerShell läser du informationen om att [skapa ett säkert Windows-kluster i Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett VNET i Azure med Azure CLI
> * Skapa ett säkert Service Fabric-kluster i Azure med hjälp av Azure CLI
> * Gör klustret säkert med ett X.509-certifikat
> * Anslut till klustret med Service Fabric CLI
> * Ta bort ett kluster

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett säkert kluster på Azure
> * [Skala in eller ut ett kluster](service-fabric-tutorial-scale-cluster.md)
> * [Uppgradera ett klusters körning](service-fabric-tutorial-upgrade-cluster.md)
> * [Distribuera API Management med Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar den här vägledningen:
- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Installera [Service Fabric CLI](service-fabric-cli.md)
- Installera [Azure CLI 2.0](/cli/azure/install-azure-cli)

Följande procedurer skapar ett Service Fabric-kluster med fem noder. Om du vill beräkna kostnaden för att köra ett Service Fabric-kluster i Azure använder du [Azures prissättningsberäknare](https://azure.microsoft.com/pricing/calculator/).

## <a name="key-concepts"></a>Viktiga begrepp
Ett [Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras till och hanteras från. Kluster kan skala till tusentals datorer. En dator eller virtuell dator som är en del av ett kluster kallas för en nod. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper, till exempel placeringsegenskaper.

En nodtyp definierar storleken, antalet och egenskaperna för en uppsättning virtuella datorer i klustret. Varje definierad nodtyp är konfigurerad som en [VM-skalningsuppsättning](/azure/virtual-machine-scale-sets/), en Azure-beräkningsresurs som du använder för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp kan sedan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. Nodtyper används för att definiera roller för en uppsättning klusternoder, till exempel en ”klientdel” eller ”serverdel”.  Klustret kan innehålla fler än en nodtyp, men den primära nodtypen måste innehålla minst fem virtuella datorer för produktionskluster (eller minst tre virtuella datorer för testkluster).  [Service Fabric-systemtjänster](service-fabric-technical-overview.md#system-services) placeras på noderna för den primära nodtypen.

Klustret skyddas av ett klustercertifikat. Ett klustercertifikat är ett X.509-certifikat som används för att skydda nod-till-nod-kommunikation och autentisera klusterhanteringsslutpunkter till en hanteringsklient.  Klustercertifikatet tillhandahåller också en SSL för API för HTTPS-hantering och för Service Fabric Explorer via HTTPS. Självsignerade certifikat är praktiska för testkluster.  För produktionskluster ska du använda ett certifikat från en certifikatutfärdare som klustrets certifikat.

Klustrets certifikat måste:

- innehålla en privat nyckel.
- vara skapat för nyckelutbyte, som går att exportera till en Personal Information Exchange-fil (.pfx).
- ha ett ämnesnamn som överensstämmer med domänen du använder för att få åtkomst till Service Fabric-klustret. Matchningen krävs för att tillhandahålla SSL för klustrets HTTPS-hanteringsslutpunkter och Service Fabric Explorer. Du kan inte hämta ett SSL-certifikat från en certifikatutfärdare (CA) för domänen .cloudapp.azure.com. Du måste skaffa ett anpassat domännamn för klustret. När du begär ett certifikat från en certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som du använder för klustret.

Azure Key Vault används för att hantera certifikat för Service Fabric-kluster i Azure.  När ett kluster distribueras i Azure hämtar Azure-resursprovidern som ansvarar för att skapa Service Fabric-kluster certifikat från Key Vault och installerar dem på klustrets virtuella datorer.

Den här självstudiekursen distribuerar ett kluster med fem noder i en enskild nodtyp. Vid distributionskluster till en produktionsmiljö är det emellertid viktigt med [kapacitetsplanering](service-fabric-cluster-capacity.md). Nedan ser du några saker att tänka på om en del av processen.

- Antalet noder och nodtyper som klustret behöver 
- Egenskaperna för varje nodtyp (exempelvis storlek, primär, mot internet och antal virtuella datorer)
- Klustrets egenskaper för tillförlitlighet och hållbarhet

## <a name="download-and-explore-the-template"></a>Ladda ned och titta närmare på mallen
Ladda ned följande mallfiler för Resource Manager:
- [vnet-linuxcluster.json][template]
- [vnet-linuxcluster.parameters.json][parameters]

[vnet-linuxcluster.json][template] distribuerar ett antal resurser, däribland följande.

### <a name="service-fabric-cluster"></a>Service Fabric-kluster
Ett Linux-kluster distribueras med följande egenskaper:
- en enkel nodtyp 
- fem noder i den primära nodtypen (kan konfigureras i mallparametrarna)
- OS: Ubuntu 16.04 LTS (kan konfigureras i mallparametrarna)
- skyddat certifikat (kan konfigureras i mallparametrarna)
- [DNS-tjänst](service-fabric-dnsservice.md) är aktiverad
- [Hållbarhetsnivå](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Brons (kan konfigureras i mallparametrarna)
- [Tillförlitlighetsnivå](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) Silver (kan konfigureras i mallparametrarna)
- klientanslutningsslutpunkt: 19000 (kan konfigureras i mallparametrarna)
- HTTP-gatewayslutpunkt: 19080 (kan konfigureras i mallparametrarna)

### <a name="azure-load-balancer"></a>Azure-belastningsutjämnare
En belastningsutjämnare distribueras som söker av och reglerar följande portar:
- klientanslutningsslutpunkt: 19000
- HTTP-gatewayslutpunkt: 19080 
- programport: 80
- programport: 443

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuellt nätverk, undernät och nätverkssäkerhetsgrupp
Namnen på det virtuella nätverket, undernätet och nätverkssäkerhetsgrupper deklareras i mallparametrarna.  Adressutrymmen för det virtuella nätverket och undernätet deklareras också i mallparametrarna:
- det virtuella nätverkets adressutrymme: 10.0.0.0/16
- Service Fabric-undernätsadressutrymme: 10.0.2.0/24

Följande regler för inkommande trafik är aktiverade i nätverkssäkerhetsgruppen. Du kan ändra portvärdena genom att ändra mallvariablerna.
- ClientConnectionEndpoint (TCP): 19000
- HttpGatewayEndpoint (HTTP/TCP): 19080
- SMB : 445
- Internodecommunication – 1025, 1026, 1027
- Tillfälligt portintervall – 49152 till 65534 (minst 256 portar behövs)
- Portar för programanvändning: 80 och 443
- Portintervall för program – 49152 till 65534 (används för tjänst-till-tjänst-kommunikation och öppnas inte i belastningsutjämnaren)
- Blockera alla andra portar

Om det behövs några andra programportar måste du justera resursen Microsoft.Network/loadBalancers och Microsoft.Network/networkSecurityGroups för att låta trafiken komma in.

## <a name="set-template-parameters"></a>Ställa in mallparametrar
Parameterfilen [vnet-cluster.parameters.json][parameters] deklarerar många värden som används för att distribuera klustret och associerade resurser. Några av parametrarna du kan behöva ändra för distributionen är:

|Parameter|Exempelvärde|Anteckningar|
|---|---||
|adminUserName|vmadmin| Administratörsanvändarnamn för virtuella datorer i klustret. |
|adminPassword|Password#1234| Administratörslösenord för virtuella datorer i klustret.|
|clusterName|mysfcluster123| Namnet på klustret. |
|location|southcentralus| Platsen på klustret. |
|certificateThumbprint|| <p>Värde bör vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil.</p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i certifikatets tumavtrycksvärde. Till exempel ”6190390162C988701DB5676EB81083EA608DCCF3”. </p>| 
|certificateUrlValue|| <p>Värde bör vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil.</p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i certifikatets webbadress. Till exempel ”https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346”.</p>|
|sourceVaultValue||<p>Värde bör vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil.</p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i källans nyckelvärde. Till exempel ”/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”.</p>|


<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Distribuera virtuella nätverk och kluster
Konfigurera därefter nätverkstopologi och distribuera Service Fabric-klustret. Resource Manager-mallen [vnet-linuxcluster.json][template] skapar ett virtuellt nätverk (VNET) och ett undernät och en nätverkssäkerhetsgrupp (NSG) för Service Fabric. Mallen distribuerar också ett kluster med certifikatsäkerhet aktiverat.  För produktionskluster ska du använda ett certifikat från en certifikatutfärdare som klustrets certifikat. Ett självsignerat certifikat kan användas för att skydda testkluster.

Följande skript använder kommandot [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) och mallen för att distribuera ett nytt kluster som skyddas med ett befintligt certifikat. Kommandot skapar även ett nytt nyckelvalv i Azure och laddar upp certifikatet.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"  
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file vnet-linuxcluster.json --parameter-file vnet-linuxcluster.parameters.json
```

## <a name="connect-to-the-secure-cluster"></a>Ansluta till det säkra klustret
Anslut till klustret med Service Fabric CLI `sfctl cluster select`-kommandot med din nyckel.  Använd enbart alternativet **--no-verify** för ett självsignerat certifikat.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Kontrollera att du är ansluten och att klustret är felfritt med kommandot `sfctl cluster health`.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Rensa resurser
De andra artiklarna i självstudieserien använder klustret du nyss skapade. Om du inte genast fortsätter till nästa artikel kanske du vill ta bort klustret för att undvika kostnader. Det enklaste sättet att ta bort klustret och alla de resurser det använder är att ta bort resursgruppen.

Logga in på Azure och välj det prenumerations-ID som du vill ta bort klustret för.  Du hittar ditt prenumerations-ID genom att logga in på [Azure Portal](http://portal.azure.com). Ta bort resursgruppen och alla klusterresurser med kommandot [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete).

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett VNET i Azure med Azure CLI
> * Skapa ett säkert Service Fabric-kluster i Azure med hjälp av Azure CLI
> * Gör klustret säkert med ett X.509-certifikat
> * Anslut till klustret med Service Fabric CLI
> * Ta bort ett kluster

Fortsätt sedan till följande självstudie för att lära dig att skala klustret.
> [!div class="nextstepaction"]
> [Skala ett kluster](service-fabric-tutorial-scale-cluster.md)


[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.parameters.json
