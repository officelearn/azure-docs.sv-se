---
title: Distribuera behållar grupp till Azure Virtual Network
description: Lär dig hur du distribuerar behållar grupper till ett nytt eller befintligt virtuellt Azure-nätverk.
ms.topic: article
ms.date: 12/17/2019
ms.author: danlep
ms.openlocfilehash: 9c9f1d114ea3883a947fb454d5958c1479bd4a4e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442263"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Distribuera behållar instanser i ett virtuellt Azure-nätverk

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) ger säker, privat nätverk för dina Azure-resurser och lokala resurser. Genom att distribuera behållar grupper till ett virtuellt Azure-nätverk kan dina behållare kommunicera säkert med andra resurser i det virtuella nätverket.

Behållar grupper som distribueras till ett virtuellt Azure-nätverk möjliggör scenarier som:

* Direkt kommunikation mellan behållar grupper i samma undernät
* Skicka [uppgiftsbaserade](container-instances-restart-policy.md) arbets belastnings resultat från container instances till en databas i det virtuella nätverket
* Hämta innehåll för behållar instanser från en [tjänst slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) i det virtuella nätverket
* Container kommunikation med virtuella datorer i det virtuella nätverket
* Container kommunikation med lokala resurser via en [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Den här funktionen är för närvarande en för hands version och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).


## <a name="virtual-network-deployment-limitations"></a>Distributions begränsningar för virtuella nätverk

Vissa begränsningar gäller när du distribuerar behållar grupper till ett virtuellt nätverk.

* Under nätet kan inte innehålla några andra resurs typer för att distribuera behållar grupper till ett undernät. Ta bort alla befintliga resurser från ett befintligt undernät innan du distribuerar behållar grupper till den, eller skapa ett nytt undernät.
* Du kan inte använda en [hanterad identitet](container-instances-managed-identity.md) i en behållar grupp som distribuerats till ett virtuellt nätverk.
* På grund av de ytterligare nätverks resurser som berörs är distributionen av en behållar grupp till ett virtuellt nätverk normalt lite långsammare än att distribuera en standard behållar instans.

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

När den här funktionen är i för hands version gäller följande begränsningar när du distribuerar behållar grupper till ett virtuellt nätverk. 

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

Gränserna för container resurser kan skilja sig från gränserna för instanser som inte är av en arbets behållare i dessa regioner. För närvarande stöds endast Linux-behållare för den här funktionen. Windows-stöd planeras.

### <a name="unsupported-networking-scenarios"></a>Nätverks scenarier som inte stöds 

* **Azure Load Balancer** -att placera ett Azure Load Balancer framför container instances i en nätverksansluten behållar grupp stöds inte
* **Peering av virtuella nätverk** – VNet-peering fungerar inte för ACI om nätverket som ACI VNet är peer-koppla till använder ett offentligt IP-utrymme. Det peer-nätverket behöver ett RFC1918 privat IP-utrymme för att peer koppling ska fungera. Dessutom kan du för närvarande bara peer-ditt VNet till ett annat VNet
* **Routning av virtuella nätverks trafik** – kund vägar kan inte konfigureras runt offentliga IP-adresser. Vägar kan konfigureras inom det privata IP-utrymmet för det delegerade under nätet där ACI-resurserna distribueras 
* **Nätverks säkerhets grupper** -utgående säkerhets regler i NSG: er som tillämpas på ett undernät som har delegerats till Azure Container instances gäller för närvarande inte 
* **Offentliga IP-eller DNS-etiketter** – behållar grupper som distribueras till ett virtuellt nätverk har för närvarande inte stöd för att exponera behållare direkt till Internet med en offentlig IP-adress eller ett fullständigt kvalificerat domän namn
* **Intern namn matchning** -namn matchning för Azure-resurser i det virtuella nätverket via den interna Azure DNS stöds inte

**Borttagning av nätverks resurser** kräver [ytterligare steg](#delete-network-resources) när du har distribuerat behållar grupper till det virtuella nätverket.

## <a name="required-network-resources"></a>Nödvändiga nätverks resurser

Det finns tre Azure Virtual Network-resurser som krävs för att distribuera behållar grupper till ett virtuellt nätverk: det [virtuella nätverket](#virtual-network) , ett [delegerat undernät](#subnet-delegated) i det virtuella nätverket och en [nätverks profil](#network-profile). 

### <a name="virtual-network"></a>Virtuellt nätverk

Ett virtuellt nätverk definierar det adress utrymme som du skapar ett eller flera undernät i. Sedan distribuerar du Azure-resurser (t. ex. behållar grupper) till under näten i det virtuella nätverket.

### <a name="subnet-delegated"></a>Undernät (delegerat)

Undernät segmentera det virtuella nätverket i separata adressutrymmen som kan användas av Azure-resurserna du placerar i dem. Du skapar ett eller flera undernät i ett virtuellt nätverk.

Det undernät som du använder för container grupper får bara innehålla behållar grupper. När du först distribuerar en behållar grupp till ett undernät delegerar Azure det under nätet till Azure Container Instances. Under nätet kan endast användas för container grupper när det har delegerats. Om du försöker distribuera andra resurser än container grupper till ett delegerat undernät, Miss lyckas åtgärden.

### <a name="network-profile"></a>Nätverks profil

En nätverks profil är en mall för nätverks konfiguration för Azure-resurser. Den anger vissa nätverks egenskaper för resursen, till exempel det undernät som den ska distribueras till. När du först använder kommandot [AZ container Create][az-container-create] för att distribuera en behållar grupp till ett undernät (och därför ett virtuellt nätverk), skapar Azure en nätverks profil åt dig. Du kan sedan använda nätverks profilen för framtida distributioner till under nätet. 

Om du vill använda en Resource Manager-mall, YAML-fil eller en programmerings metod för att distribuera en behållar grupp till ett undernät måste du ange det fullständiga Resource Manager-resurs-ID: t för en nätverks profil. Du kan använda en profil som tidigare skapats med [AZ container Create][az-container-create]eller skapa en profil med hjälp av en Resource Manager-mall (se [mall-exempel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) och [referens](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Om du vill hämta ID för en profil som skapats tidigare använder du kommandot [AZ Network Profile List][az-network-profile-list] . 

I följande diagram har flera behållar grupper distribuerats till ett undernät delegerade till Azure Container Instances. När du har distribuerat en behållar grupp till ett undernät kan du distribuera ytterligare behållar grupper till den genom att ange samma nätverks profil.

![Behållar grupper inom ett virtuellt nätverk][aci-vnet-01]

## <a name="deployment-scenarios"></a>Distributionsscenarier

Du kan använda [AZ container Create][az-container-create] för att distribuera behållar grupper till ett nytt virtuellt nätverk och tillåta Azure att skapa nödvändiga nätverks resurser åt dig, eller distribuera till ett befintligt virtuellt nätverk. 

### <a name="new-virtual-network"></a>Nytt virtuellt nätverk

Om du vill distribuera till ett nytt virtuellt nätverk och låta Azure Skapa nätverks resurserna automatiskt, anger du följande när du kör [AZ container Create][az-container-create]:

* Virtuellt nätverksnamn
* Adressprefix för virtuellt nätverk i CIDR-format
* Namn på undernät
* Undernätsprefixets i CIDR-format

Prefixen för det virtuella nätverket och under nätet anger adress utrymmen för det virtuella nätverket respektive undernät. Dessa värden representeras i CIDR-notation (Classless Inter-Domain routing), till exempel `10.0.0.0/16`. Mer information om hur du arbetar med undernät finns i [lägga till, ändra eller ta bort ett virtuellt nätverks under nät](../virtual-network/virtual-network-manage-subnet.md).

När du har distribuerat din första behållar grupp med den här metoden kan du distribuera till samma undernät genom att ange det virtuella nätverket och under nät namnen, eller nätverks profilen som Azure skapar automatiskt åt dig. Eftersom Azure delegerar under nätet till Azure Container Instances kan du *bara* distribuera behållar grupper till under nätet.

### <a name="existing-virtual-network"></a>Befintligt virtuellt nätverk

Så här distribuerar du en behållar grupp till ett befintligt virtuellt nätverk:

1. Skapa ett undernät i det befintliga virtuella nätverket eller Töm ett befintligt undernät för *alla* andra resurser
1. Distribuera en behållar grupp med [AZ container Create][az-container-create] och ange något av följande:
   * Namn på virtuellt nätverk och undernät
   * Resurs-ID för virtuellt nätverk och resurs-ID för under nätet som tillåter användning av ett virtuellt nätverk från en annan resurs grupp
   * Namn eller ID för nätverks profil, som du kan hämta med [AZ Network Profile List][az-network-profile-list]

När du har distribuerat din första behållar grupp till ett befintligt undernät delegerar Azure det under nätet till Azure Container Instances. Du kan inte längre distribuera andra resurser än container grupper till det under nätet.

## <a name="deployment-examples"></a>Distributions exempel

I följande avsnitt beskrivs hur du distribuerar behållar grupper till ett virtuellt nätverk med Azure CLI. Kommando exemplen är formaterade för **bash** -gränssnittet. Om du föredrar ett annat gränssnitt, till exempel PowerShell eller kommando tolken, justerar du rad fortsättnings tecknen efter behov.

### <a name="deploy-to-a-new-virtual-network"></a>Distribuera till ett nytt virtuellt nätverk

Först distribuerar du en behållar grupp och anger parametrarna för ett nytt virtuellt nätverk och undernät. När du anger dessa parametrar skapar Azure det virtuella nätverket och under nätet, delegerar under nätet till Azure Container instances och skapar även en nätverks profil. När resurserna har skapats distribueras behållar gruppen till under nätet.

Kör följande [AZ container Create][az-container-create] -kommando som anger inställningar för ett nytt virtuellt nätverk och undernät. Du måste ange namnet på en resurs grupp som har skapats i en region som [har stöd](#preview-limitations) för behållar grupper i ett virtuellt nätverk. Det här kommandot distribuerar den offentliga Microsoft [ACI-HelloWorld-][aci-helloworld] behållaren som kör en liten Node. js-webbserver som betjänar en statisk webb sida. I nästa avsnitt ska du distribuera en andra behållar grupp till samma undernät och testa kommunikationen mellan de två behållar instanserna.

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

När du distribuerar till ett nytt virtuellt nätverk med hjälp av den här metoden kan distributionen ta några minuter medan nätverks resurserna skapas. Efter den första distributionen slutförs ytterligare distributioner av container grupper snabbare.

### <a name="deploy-to-existing-virtual-network"></a>Distribuera till befintligt virtuellt nätverk

Nu när du har distribuerat en behållar grupp till ett nytt virtuellt nätverk, distribuerar du en andra behållar grupp till samma undernät och verifierar kommunikationen mellan de två behållar instanserna.

Börja med att hämta IP-adressen för den första behållar gruppen som du har distribuerat, *AppContainer*:

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

Utdata ska visa IP-adressen för behållar gruppen i det privata under nätet:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Ange `CONTAINER_GROUP_IP` till den IP-adress som du hämtade med kommandot `az container show` och kör följande `az container create`-kommando. Den här andra behållaren, *commchecker*, kör en Alpine Linux-baserad avbildning och kör `wget` mot den första behållar gruppens privata UNDERNÄTs IP-adress.

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

När den här andra behållar distributionen har slutförts hämtar du dess loggar så att du kan se resultatet av den `wget` kommandot som körs:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Om den andra behållaren har kommunicerat med det första ska utdata likna följande:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

Logg resultatet ska visa att `wget` kunde ansluta till och hämta index filen från den första behållaren med dess privata IP-adress på det lokala under nätet. Nätverks trafiken mellan de två behållar grupperna fanns kvar i det virtuella nätverket.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Distribuera till befintligt virtuellt nätverk – YAML

Du kan också distribuera en behållar grupp till ett befintligt virtuellt nätverk med hjälp av en YAML-fil. Om du vill distribuera till ett undernät i ett virtuellt nätverk anger du flera ytterligare egenskaper i YAML:

* `ipAddress`: inställningarna för IP-adress för behållar gruppen.
  * `ports`: portarna som ska öppnas, om det finns några.
  * `protocol`: protokollet (TCP eller UDP) för den öppnade porten.
* `networkProfile`: anger nätverks inställningar som det virtuella nätverket och under nätet för en Azure-resurs.
  * `id`: det fullständiga Resource Manager-resurs-ID: t för `networkProfile`.

Om du vill distribuera en behållar grupp till ett virtuellt nätverk med en YAML-fil måste du först hämta nätverks profilens ID. Kör kommandot [AZ Network Profile List][az-network-profile-list] och ange namnet på den resurs grupp som innehåller ditt virtuella nätverk och delegerade undernät.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

Kommandots utdata visar det fullständiga resurs-ID: t för nätverks profilen:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

När du har nätverks profil-ID: t kopierar du följande YAML till en ny fil med namnet *VNet-Deploy-ACI. yaml*. Ersätt `id`-värdet med det ID som du precis hämtade under `networkProfile`och spara sedan filen. Den här YAML skapar en behållar grupp med namnet *appcontaineryaml* i ditt virtuella nätverk.

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
    id: /subscriptions/<Subscription ID>/resourceGroups/container/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Distribuera behållar gruppen med kommandot [AZ container Create][az-container-create] , och ange fil namnet yaml för parametern `--file`:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

När distributionen har slutförts kör du kommandot [AZ container show][az-container-show] för att visa dess status:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="delete-container-instances"></a>Ta bort behållar instanser

När du är klar med de behållar instanser som du har skapat tar du bort dem med följande kommandon:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Ta bort nätverks resurser


> [!NOTE]
> Om du får ett fel meddelande när du försöker ta bort nätverks profilen får 2-3 dagar för plattformen att automatiskt minimera problemet och försöka ta bort den igen. Om du fortfarande har problem med att ta bort nätverks profilen [öppnar du en support reqest.](https://azure.microsoft.com/support/create-ticket/)

Den inledande för hands versionen av den här funktionen kräver flera ytterligare kommandon för att ta bort de nätverks resurser som du skapade tidigare. Om du använde exempel kommandona i föregående avsnitt i den här artikeln för att skapa ditt virtuella nätverk och undernät kan du använda följande skript för att ta bort dessa nätverks resurser.

Ange `RES_GROUP` variabeln till namnet på den resurs grupp som innehåller det virtuella nätverk och undernät som ska tas bort innan du kör skriptet. Uppdatera namnet på det virtuella nätverket om du inte använde `aci-vnet` namnet som du föreslog tidigare. Skriptet är formaterat för bash-gränssnittet. Om du föredrar ett annat gränssnitt, t. ex. PowerShell eller kommando tolken, måste du justera variabel tilldelning och-åtkomst på motsvarande sätt.

> [!WARNING]
> Det här skriptet tar bort resurser! Den tar bort det virtuella nätverket och alla undernät som det innehåller. Se till att du inte längre behöver *någon* av resurserna i det virtuella nätverket, inklusive eventuella undernät som den innehåller, innan du kör skriptet. **De här resurserna kan inte återställas när de har**tagits bort.

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Nästa steg

Om du vill distribuera ett nytt virtuellt nätverk, undernät, nätverks profil och behållar grupp med hjälp av en Resource Manager-mall, se [skapa en Azure Container Group med VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

Flera virtuella nätverks resurser och funktioner beskrivs i den här artikeln, men i korthet. Azure Virtual Network-dokumentationen omfattar följande ämnen:

* [Virtuellt nätverk](../virtual-network/manage-virtual-network.md)
* [Undernät](../virtual-network/virtual-network-manage-subnet.md)
* [Serviceslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

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
