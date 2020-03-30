---
title: Distribuera behållargrupp till virtuella Azure-nätverk
description: Lär dig hur du distribuerar behållargrupper till ett nytt eller befintligt virtuellt Azure-nätverk.
ms.topic: article
ms.date: 01/06/2020
ms.author: danlep
ms.openlocfilehash: 318576e9b5c5b32bbc993ea16494c938b74bd2f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77200069"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Distribuera behållarinstanser till ett virtuellt Azure-nätverk

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) tillhandahåller säkra, privata nätverk för dina Azure- och lokala resurser. Genom att distribuera behållargrupper till ett virtuellt Azure-nätverk kan dina behållare kommunicera säkert med andra resurser i det virtuella nätverket.

Behållargrupper som distribueras till ett virtuellt Azure-nätverk möjliggör scenarier som:

* Direkt kommunikation mellan behållargrupper i samma undernät
* Skicka [uppgiftsbaserad](container-instances-restart-policy.md) arbetsbelastningsutdata från behållarinstanser till en databas i det virtuella nätverket
* Hämta innehåll för behållarinstanser från en [tjänstslutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) i det virtuella nätverket
* Containerkommunikation med virtuella datorer i det virtuella nätverket
* Containerkommunikation med lokala resurser via en [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Distributioner av behållargrupp till ett virtuellt nätverk är i allmänhet endast tillgängliga för produktionsarbetsbelastningar i följande regioner: **Östra USA, Södra centrala USA och Västra US 2**. I andra regioner där funktionen är tillgänglig förhandsgranskas för närvarande virtuella nätverksdistributioner, med allmän tillgänglighet planerad inom en snar framtid. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. 


## <a name="virtual-network-deployment-limitations"></a>Begränsningar för distribution av virtuella nätverk

Vissa begränsningar gäller när du distribuerar behållargrupper till ett virtuellt nätverk.

* Om du vill distribuera behållargrupper till ett undernät kan undernätet inte innehålla några andra resurstyper. Ta bort alla befintliga resurser från ett befintligt undernät innan du distribuerar behållargrupper till det, eller skapa ett nytt undernät.
* Du kan inte använda en [hanterad identitet](container-instances-managed-identity.md) i en behållargrupp som distribueras till ett virtuellt nätverk.
* Du kan inte aktivera en [liveness-avsökning](container-instances-liveness-probe.md) eller [beredskapsavsökning](container-instances-readiness-probe.md) i en behållargrupp som distribueras till ett virtuellt nätverk.
* På grund av de ytterligare nätverksresurser som ingår är det vanligtvis långsammare att distribuera en behållargrupp till ett virtuellt nätverk än att distribuera en standardbehållareinstans.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

Begränsningar för behållarresurser kan skilja sig från gränser för icke-nätverksbaserade behållarinstanser i dessa regioner. För närvarande stöds endast Linux-behållare för den här funktionen. Windows-stöd planeras.

### <a name="unsupported-networking-scenarios"></a>Scenarier som inte stöds av nätverk 

* **Azure Load Balancer** - Det går inte att placera en Azure Load Balancer framför behållarinstanser i en nätverksbaserad behållargrupp
* **Virtuell nätverks peering**
  * VNet-peering fungerar inte för ACI om nätverket som ACI VNet peering använder ett offentligt IP-utrymme. Det peered-nätverket behöver ett privat IP-utrymme för RFC 1918 för att VNet-peering ska fungera. 
  * Du kan bara peer ditt virtuella nätverk till ett annat VNet
* **Routning av virtuell nätverkstrafik** – Anpassade vägar kan inte ställas in runt offentliga IP-adresser. Vägar kan ställas in inom det privata IP-utrymmet i det delegerade undernätet där ACI-resurserna distribueras 
* **Nätverkssäkerhetsgrupper** – Utgående säkerhetsregler i NSG:er som tillämpas på ett undernät som delegerats till Azure Container Instances tillämpas för närvarande inte 
* **Offentlig IP- eller DNS-etikett** - Behållargrupper som distribueras till ett virtuellt nätverk stöder för närvarande inte att exponera behållare direkt till Internet med en offentlig IP-adress eller ett fullständigt kvalificerat domännamn
* **Intern namnmatchning** – Namnmatchning för Azure-resurser i det virtuella nätverket via den interna Azure DNS stöds inte

**Borttagning av nätverksresurser** kräver [ytterligare steg](#delete-network-resources) när du har distribuerat behållargrupper till det virtuella nätverket.

## <a name="required-network-resources"></a>Nödvändiga nätverksresurser

Det finns tre Azure Virtual Network-resurser som krävs för att distribuera behållargrupper till ett virtuellt nätverk: själva [det virtuella nätverket,](#virtual-network) ett [delegerat undernät](#subnet-delegated) i det virtuella nätverket och en [nätverksprofil](#network-profile). 

### <a name="virtual-network"></a>Virtuellt nätverk

Ett virtuellt nätverk definierar det adressutrymme där du skapar ett eller flera undernät. Du distribuerar sedan Azure-resurser (som behållargrupper) till undernäten i det virtuella nätverket.

### <a name="subnet-delegated"></a>Undernät (delegerat)

Undernät segmenterar det virtuella nätverket i separata adressutrymmen som kan kan hanteras av de Azure-resurser som du placerar i dem. Du skapar ett eller flera undernät i ett virtuellt nätverk.

Undernätet som du använder för behållargrupper får bara innehålla behållargrupper. När du först distribuerar en behållargrupp till ett undernät delegerar Azure det undernätet till Azure Container Instances. När undernätet har delegerats kan det endast användas för behållargrupper. Om du försöker distribuera andra resurser än behållargrupper till ett delegerat undernät misslyckas åtgärden.

### <a name="network-profile"></a>Nätverksprofil

En nätverksprofil är en nätverkskonfigurationsmall för Azure-resurser. Den anger vissa nätverksegenskaper för resursen, till exempel det undernät som den ska distribueras till. När du först använder kommandot [az container create][az-container-create] för att distribuera en behållargrupp till ett undernät (och därmed ett virtuellt nätverk) skapar Azure en nätverksprofil åt dig. Du kan sedan använda nätverksprofilen för framtida distributioner till undernätet. 

Om du vill använda en Resource Manager-mall, YAML-fil eller en programmatisk metod för att distribuera en behållargrupp till ett undernät måste du ange det fullständiga resurshanterarens resurs-ID för en nätverksprofil. Du kan använda en profil som tidigare skapats med [az-behållarskap][az-container-create]eller skapa en profil med hjälp av en Resource Manager-mall (se [mallexempel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) och [referens](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Om du vill hämta ID:et för en tidigare skapad profil använder du kommandot [az-nätverksprofillista.][az-network-profile-list] 

I följande diagram har flera behållargrupper distribuerats till ett undernät som delegerats till Azure Container Instances. När du har distribuerat en behållargrupp till ett undernät kan du distribuera ytterligare behållargrupper till den genom att ange samma nätverksprofil.

![Behållargrupper i ett virtuellt nätverk][aci-vnet-01]

## <a name="deployment-scenarios"></a>Distributionsscenarier

Du kan använda [az container create][az-container-create] för att distribuera behållargrupper till ett nytt virtuellt nätverk och tillåta Azure att skapa de nödvändiga nätverksresurserna för dig eller distribuera till ett befintligt virtuellt nätverk. 

### <a name="new-virtual-network"></a>Nytt virtuellt nätverk

Om du vill distribuera till ett nytt virtuellt nätverk och låta Azure skapa nätverksresurserna för dig automatiskt anger du följande när du kör [az-behållarskap:][az-container-create]

* Virtuellt nätverksnamn
* Prefix för virtuell nätverksadress i CIDR-format
* Namn på undernät
* Prefix för undernätsadress i CIDR-format

Prefixen för virtuellt nätverk och undernätsadress anger adressutrymmena för det virtuella nätverket respektive undernätet. Dessa värden representeras i CIDR-notation (Classless Inter-Domain `10.0.0.0/16`Routing), till exempel . Mer information om hur du arbetar med undernät finns i [Lägga till, ändra eller ta bort ett virtuellt nätverksundernät](../virtual-network/virtual-network-manage-subnet.md).

När du har distribuerat din första behållargrupp med den här metoden kan du distribuera till samma undernät genom att ange det virtuella nätverket och undernätsnamnen eller nätverksprofilen som Azure skapar automatiskt åt dig. Eftersom Azure delegerar undernätet till Azure Container Instances kan du *distribuera endast* behållargrupper till undernätet.

### <a name="existing-virtual-network"></a>Befintligt virtuellt nätverk

Så här distribuerar du en behållargrupp till ett befintligt virtuellt nätverk:

1. Skapa ett undernät i ditt befintliga virtuella nätverk, använd ett befintligt undernät där en behållargrupp redan har distribuerats eller använd ett befintligt undernät som tömts på *alla* andra resurser
1. Distribuera en behållargrupp med [az-behållarskap][az-container-create] och ange något av följande:
   * Namn på virtuellt nätverk och undernätsnamn
   * Resurs-ID för virtuellt nätverk och resurs-ID för virtuella nätverk, vilket gör det möjligt att använda ett virtuellt nätverk från en annan resursgrupp
   * Nätverksprofilnamn eller ID, som du kan hämta med hjälp av [az-nätverksprofillista][az-network-profile-list]

När du har distribuerat din första behållargrupp till ett befintligt undernät delegerar Azure det undernätet till Azure Container Instances. Du kan inte längre distribuera andra resurser än behållargrupper till det undernätet.

## <a name="deployment-examples"></a>Exempel på distribution

I följande avsnitt beskrivs hur du distribuerar behållargrupper till ett virtuellt nätverk med Azure CLI. Kommandoexemplen är formaterade för **Bash** bash-skalet. Om du föredrar ett annat skal, till exempel PowerShell eller Kommandotolk, justerar du radfortsättningstecken i enlighet med detta.

### <a name="deploy-to-a-new-virtual-network"></a>Distribuera till ett nytt virtuellt nätverk

Distribuera först en behållargrupp och ange parametrarna för ett nytt virtuellt nätverk och undernät. När du anger dessa parametrar skapar Azure det virtuella nätverket och undernätet, delegerar undernätet till Azure Container-instanser och skapar även en nätverksprofil. När dessa resurser har skapats distribueras behållargruppen till undernätet.

Kör följande kommando [för az-behållarskapande][az-container-create] som anger inställningar för ett nytt virtuellt nätverk och undernät. Du måste ange namnet på en resursgrupp som har skapats i en region där distributioner av behållargrupper i ett virtuellt nätverk är [tillgängliga](#virtual-network-deployment-limitations). Det här kommandot distribuerar den offentliga Microsoft [aci-helloworld-behållaren][aci-helloworld] som kör en liten Node.js-webbserver som betjänar en statisk webbsida. I nästa avsnitt ska du distribuera en andra behållargrupp till samma undernät och testa kommunikationen mellan de två behållarinstanserna.

```azurecli
az container create \
    --name appcontainer \
    --resource-group myResourceGroup \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --vnet aci-vnet \
    --vnet-address-prefix 10.0.0.0/16 \
    --subnet aci-subnet \
    --subnet-address-prefix 10.0.0.0/24
```

När du distribuerar till ett nytt virtuellt nätverk med den här metoden kan distributionen ta några minuter medan nätverksresurserna skapas. Efter den första distributionen slutförs ytterligare distributioner av behållargrupper snabbare.

### <a name="deploy-to-existing-virtual-network"></a>Distribuera till befintligt virtuellt nätverk

Nu när du har distribuerat en behållargrupp till ett nytt virtuellt nätverk distribuerar du en andra behållargrupp till samma undernät och verifierar kommunikationen mellan de två behållarinstanserna.

Hämta först IP-adressen för den första behållargruppen som du har distribuerat, *appcontainer:*

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

Utdata ska visa IP-adressen för behållargruppen i det privata undernätet:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Ställ nu `CONTAINER_GROUP_IP` in på den IP `az container show` du hämtade med `az container create` kommandot och kör följande kommando. Den här andra behållaren, *commchecker*, kör `wget` en alpin Linux-baserad avbildning och körs mot den första behållargruppens privata IP-adress för undernät.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

az container create \
    --resource-group myResourceGroup \
    --name commchecker \
    --image alpine:3.5 \
    --command-line "wget $CONTAINER_GROUP_IP" \
    --restart-policy never \
    --vnet aci-vnet \
    --subnet aci-subnet
```

När den andra behållardistributionen har slutförts hämtar du `wget` dess loggar så att du kan se utdata för det kommando som den körde:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Om den andra behållaren kommunicerade med den första, bör utdata liknas vid:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

Loggutdata ska `wget` visa att indexfilen kunde ansluta och hämtas från den första behållaren med hjälp av sin privata IP-adress i det lokala undernätet. Nätverkstrafiken mellan de två behållargrupperna fanns kvar i det virtuella nätverket.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Distribuera till befintligt virtuellt nätverk - YAML

Du kan också distribuera en behållargrupp till ett befintligt virtuellt nätverk med hjälp av en YAML-fil, en Resource Manager-mall eller en annan programmatisk metod, till exempel med Python SDK. Om du vill distribuera till ett undernät i ett virtuellt nätverk anger du flera ytterligare egenskaper i YAML:

* `ipAddress`: IP-adressinställningarna för behållargruppen.
  * `ports`: Portarna för att öppna, om sådana finns.
  * `protocol`: Protokollet (TCP eller UDP) för den öppnade porten.
* `networkProfile`: Anger nätverksinställningar som det virtuella nätverket och undernätet för en Azure-resurs.
  * `id`: Resurs-ID:et `networkProfile`för hela Resurshanteraren för .

Om du vill distribuera en behållargrupp till ett virtuellt nätverk med en YAML-fil måste du först hämta nätverksprofilens ID. Kör kommandot [az-nätverksprofillista][az-network-profile-list] och ange namnet på resursgruppen som innehåller det virtuella nätverket och det delegerade undernätet.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

Utdata från kommandot visar nätverksprofilens fullständiga resurs-ID:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

När du har nätverksprofil-ID kopierar du följande YAML till en ny fil med namnet *vnet-deploy-aci.yaml*. Under `networkProfile`ersätter `id` du värdet med ID som du just hämtade och sparar sedan filen. Den här YAML skapar en behållargrupp med namnet *appcontaineryaml* i det virtuella nätverket.

```YAML
apiVersion: '2018-09-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Distribuera behållargruppen med kommandot [az container create][az-container-create] och ange `--file` YAML-filnamnet för parametern:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

När distributionen har slutförts kör du kommandot [az container show][az-container-show] för att visa dess status:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="delete-container-instances"></a>Ta bort behållarinstanser

När du är klar med bearbetningen med behållarinstanserna som du har skapat tar du bort dem med följande kommandon:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Ta bort nätverksresurser

Den här funktionen kräver för närvarande flera ytterligare kommandon för att ta bort nätverksresurserna som du skapade tidigare. Om du använde exempelkommandona i föregående avsnitt i den här artikeln för att skapa det virtuella nätverket och undernätet kan du använda följande skript för att ta bort dessa nätverksresurser. Skriptet förutsätter att resursgruppen innehåller ett enda virtuellt nätverk med en enda nätverksprofil.

Innan du kör skriptet `RES_GROUP` anger du variabeln till namnet på resursgruppen som innehåller det virtuella nätverket och undernätet som ska tas bort. Uppdatera namnet på det virtuella nätverket om `aci-vnet` du inte använde det namn som föreslogs tidigare. Skriptet är formaterat för Bash-skalet. Om du föredrar ett annat skal, till exempel PowerShell eller Kommandotolk, måste du justera variabel tilldelning och accessors därefter.

> [!WARNING]
> Det här skriptet tar bort resurser! Det tar bort det virtuella nätverket och alla undernät som det innehåller. Se till att du inte längre behöver *någon* av resurserna i det virtuella nätverket, inklusive eventuella undernät som det innehåller, innan du kör skriptet. När dessa resurser har tagits bort **kan de inte kan återuppkås.**

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar ett nytt virtuellt nätverk, undernät, en nätverksprofil och en behållargrupp med hjälp av en Resource Manager-mall finns i [Skapa en Azure-behållargrupp med VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

Flera virtuella nätverksresurser och funktioner diskuterades i den här artikeln, men kort. Dokumentationen till Azure Virtual Network beskriver följande avsnitt i stor utsträckning:

* [Virtuellt nätverk](../virtual-network/manage-virtual-network.md)
* [Undernät](../virtual-network/virtual-network-manage-subnet.md)
* [Tjänstslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute (ort)](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
