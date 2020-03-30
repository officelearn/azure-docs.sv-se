---
title: Begränsa åtkomsten med ett virtuellt nätverk
description: Tillåt åtkomst till ett Azure-behållarregister endast från resurser i ett virtuellt Azure-nätverk eller från offentliga IP-adressintervall.
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454343"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Begränsa åtkomsten till ett Azure-behållarregister med hjälp av en Azure-regler för virtuellt nätverk eller brandvägg

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) tillhandahåller säkra, privata nätverk för dina Azure- och lokala resurser. Genom att begränsa åtkomsten till ditt privata Azure-behållarregister från ett virtuellt Azure-nätverk ser du till att endast resurser i det virtuella nätverket får åtkomst till registret. För scenarier över flera lokaler kan du också konfigurera brandväggsregler så att registeråtkomst endast tillåts från specifika IP-adresser.

Den här artikeln visar två scenarier för att konfigurera regler för inkommande nätverksåtkomst i ett behållarregister: från en virtuell dator som distribueras i ett virtuellt nätverk eller från en virtuell dators offentliga IP-adress.

> [!IMPORTANT]
> Den här funktionen är för närvarande i förhandsversion och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).
>

Om du i stället behöver ställa in åtkomstregler för resurser för att nå ett behållarregister bakom en brandvägg läser [du Konfigurera regler för åtkomst till ett Azure-behållarregister bakom en brandvägg](container-registry-firewall-access-rules.md).


## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Endast ett **Premium-behållarregister** kan konfigureras med regler för nätverksåtkomst. Information om registertjänstnivåer finns i [Azure Container Registry SKU: er](container-registry-skus.md). 

* Endast ett [Azure Kubernetes Service-kluster](../aks/intro-kubernetes.md) eller [virtuell Azure-dator](../virtual-machines/linux/overview.md) kan användas som värd för att komma åt ett behållarregister i ett virtuellt nätverk. *Andra Azure-tjänster, inklusive Azure Container Instances stöds för närvarande inte.*

* [ACR-uppgifter](container-registry-tasks-overview.md) stöds för närvarande inte i ett behållarregister som används i ett virtuellt nätverk.

* Varje register stöder högst 100 virtuella nätverksregler.

## <a name="prerequisites"></a>Krav

* Om du vill använda Azure CLI-stegen i den här artikeln krävs Azure CLI version 2.0.58 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

* Om du inte redan har ett behållarregister skapar du ett (Premium SKU `hello-world` krävs) och skickar en exempelavbildning, till exempel från Docker Hub. Du kan till exempel använda [Azure-portalen][quickstart-portal] eller [Azure CLI][quickstart-cli] för att skapa ett register. 

* Om du vill begränsa registeråtkomst med hjälp av ett virtuellt nätverk i en annan Azure-prenumeration måste du registrera resursprovidern för Azure Container Registry i den prenumerationen. Ett exempel:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>Om nätverksregler för ett behållarregister

Ett Azure-behållarregister accepterar som standard anslutningar via internet från värdar i alla nätverk. Med ett virtuellt nätverk kan du bara tillåta Azure-resurser som ett AKS-kluster eller Azure VM för att komma åt registret på ett säkert sätt, utan att passera en nätverksgräns. Du kan också konfigurera regler för nätverksbrandvägg så att endast specifika offentliga IP-adressintervall för internet tillåts. 

Om du vill begränsa åtkomsten till ett register ändrar du först standardåtgärden för registret så att den nekar alla nätverksanslutningar. Lägg sedan till regler för nätverksåtkomst. Klienter som beviljas åtkomst via nätverksreglerna måste fortsätta att [autentisera till behållarregistret](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) och ha behörighet att komma åt data.

### <a name="service-endpoint-for-subnets"></a>Tjänstslutpunkt för undernät

Om du vill tillåta åtkomst från ett undernät i ett virtuellt nätverk måste du lägga till en [tjänstslutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) för Azure Container Registry-tjänsten. 

Tjänster med flera innehavare, till exempel Azure Container Registry, använder en enda uppsättning IP-adresser för alla kunder. En tjänstslutpunkt tilldelar en slutpunkt för att komma åt ett register. Den här slutpunkten ger trafiken en optimal väg till resursen via Azure-stamnätet. Identiteterna för det virtuella nätverket och undernätet överförs också med varje begäran.

### <a name="firewall-rules"></a>Brandväggsregler

För IP-nätverksregler, ange tillåtna internetadressintervall med CIDR-notation som *16.17.18.0/24* eller en enskild IP-adresser som *16.17.18.19*. IP-nätverksregler är endast tillåtna för *offentliga* IP-adresser på Internet. IP-adressintervall som reserverats för privata nätverk (enligt definitionen i RFC 1918) är inte tillåtna i IP-regler.

## <a name="create-a-docker-enabled-virtual-machine"></a>Skapa en Docker-aktiverad virtuell dator

I den här artikeln använder du en Docker-aktiverad Ubuntu-virtuell dator för att komma åt ett Azure-behållarregister. Om du vill använda Azure Active Directory-autentisering till registret installerar du även [Azure CLI][azure-cli] på den virtuella datorn. Om du redan har en virtuell Azure-dator hoppar du över det här skapandesteget.

Du kan använda samma resursgrupp för den virtuella datorn och behållarregistret. Den här inställningen förenklar rensningen i slutet men krävs inte. Om du väljer att skapa en separat resursgrupp för den virtuella datorn och det virtuella nätverket kör du [az group create][az-group-create]. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *platsen för westcentralus:*

```azurecli
az group create --name myResourceGroup --location westus
```

Distribuera nu en virtuell standarddator för Ubuntu Azure med [az vm create][az-vm-create]. I följande exempel skapas en virtuell dator med namnet *myDockerVM:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När kommandot är klart bör `publicIpAddress` du notera vad som visas av Azure CLI. Använd den här adressen om du vill skapa SSH-anslutningar till den virtuella datorn och eventuellt för senare installation av brandväggsregler.

### <a name="install-docker-on-the-vm"></a>Installera Docker på den virtuella datorn

När den virtuella datorn har körts gör du en SSH-anslutning till den virtuella datorn. Ersätt *publicIpAddress* med den offentliga IP-adressen för din virtuella dator.

```bash
ssh azureuser@publicIpAddress
```

Kör följande kommando för att installera Docker på den virtuella datorn i Ubuntu:

```bash
sudo apt install docker.io -y
```

Efter installationen kör du följande kommando för att kontrollera att Docker körs korrekt på den virtuella datorn:

```bash
sudo docker run -it hello-world
```

Resultat:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Installera Azure CLI

Följ stegen i [Installera Azure CLI med apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) för att installera Azure CLI på din virtuella Ubuntu-dator. I den här artikeln kontrollerar du att du installerar version 2.0.58 eller senare.

Avsluta SSH-anslutningen.

## <a name="allow-access-from-a-virtual-network"></a>Tillåt åtkomst från ett virtuellt nätverk

I det här avsnittet konfigurerar du behållarregistret så att åtkomst från ett undernät i ett virtuellt Azure-nätverk tillåts. Motsvarande steg med Azure CLI- och Azure-portalen tillhandahålls.

### <a name="allow-access-from-a-virtual-network---cli"></a>Tillåt åtkomst från ett virtuellt nätverk - CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Lägga till en tjänstslutpunkt i ett undernät

När du skapar en virtuell dator skapar Azure som standard ett virtuellt nätverk i samma resursgrupp. Namnet på det virtuella nätverket baseras på namnet på den virtuella datorn. Om du till exempel namnger den virtuella datorn *myDockerVM*är standardnamnet för virtuellt nätverk *myDockerVMVNET*, med ett undernät med namnet *myDockerVMSubnet*. Verifiera detta i Azure-portalen eller med kommandot [az network vnet list:][az-network-vnet-list]

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

Resultat:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Använd kommandot [az network vnet subnet update][az-network-vnet-subnet-update] för att lägga till en **Microsoft.ContainerRegistry-tjänstslutpunkt** i undernätet. Ersätt namnen på det virtuella nätverket och undernätet i följande kommando:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Använd kommandot [az network vnet-nät visa][az-network-vnet-subnet-show] för att hämta resurs-ID:et i undernätet. Du behöver detta i ett senare steg för att konfigurera en nätverksåtkomstregel.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Resultat:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>Ändra standardåtkomst för nätverk till registret

Som standard tillåter ett Azure-behållarregister anslutningar från värdar i alla nätverk. Om du vill begränsa åtkomsten till ett markerat nätverk ändrar du standardåtgärden för att neka åtkomst. Ersätt namnet på registret i följande [kommando för az acr-uppdatering:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Lägga till nätverksregel i registret

Använd kommandot [az acr network-rule add][az-acr-network-rule-add] för att lägga till en nätverksregel i registret som tillåter åtkomst från den virtuella datorns undernät. Ersätt behållarregistrets namn och resurs-ID för undernätet i följande kommando: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Fortsätt att [verifiera åtkomsten till registret](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Tillåt åtkomst från ett virtuellt nätverk - portal

#### <a name="add-service-endpoint-to-subnet"></a>Lägga till tjänstslutpunkt i undernätet

När du skapar en virtuell dator skapar Azure som standard ett virtuellt nätverk i samma resursgrupp. Namnet på det virtuella nätverket baseras på namnet på den virtuella datorn. Om du till exempel namnger den virtuella datorn *myDockerVM*är standardnamnet för virtuellt nätverk *myDockerVMVNET*, med ett undernät med namnet *myDockerVMSubnet*.

Så här lägger du till en tjänstslutpunkt för Azure Container Registry i ett undernät:

1. Ange *virtuella nätverk*i sökrutan högst upp i [Azure-portalen][azure-portal]. När **virtuella nätverk** visas i sökresultaten markerar du det.
1. Välj det virtuella nätverk där den virtuella datorn distribueras i listan över virtuella nätverk, till exempel *myDockerVMVNET*.
1. Under **Inställningar**väljer du **Undernät**.
1. Välj det undernät där den virtuella datorn distribueras, till exempel *myDockerVMSubnet*.
1. Under **Tjänstslutpunkter**väljer du **Microsoft.ContainerRegistry**.
1. Välj **Spara**.

![Lägga till tjänstslutpunkt i undernätet][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Konfigurera nätverksåtkomst för registret

Som standard tillåter ett Azure-behållarregister anslutningar från värdar i alla nätverk. Så här begränsar du åtkomsten till det virtuella nätverket:

1. Navigera till behållarregistret i portalen.
1. Under **Inställningar**väljer du **Brandvägg och virtuella nätverk**.
1. Om du vill neka åtkomst som standard väljer du att tillåta åtkomst från **valda nätverk**. 
1. Välj **Lägg till befintligt virtuellt nätverk**och välj det virtuella nätverk och undernät som du har konfigurerat med en tjänstslutpunkt. Välj **Lägg till**.
1. Välj **Spara**.

![Konfigurera virtuellt nätverk för behållarregister][acr-vnet-portal]

Fortsätt att [verifiera åtkomsten till registret](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Tillåt åtkomst från en IP-adress

I det här avsnittet konfigurerar du behållarregistret så att åtkomst tillåts från en viss IP-adress eller ett visst intervall. Motsvarande steg med Azure CLI- och Azure-portalen tillhandahålls.

### <a name="allow-access-from-an-ip-address---cli"></a>Tillåt åtkomst från en IP-adress - CLI

#### <a name="change-default-network-access-to-registry"></a>Ändra standardåtkomst för nätverk till registret

Om du inte redan har gjort det uppdaterar du registerkonfigurationen så att åtkomst nekas som standard. Ersätt namnet på registret i följande [kommando för az acr-uppdatering:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Ta bort nätverksregeln från registret

Om du tidigare har lagt till en nätverksregel för att tillåta åtkomst från den virtuella datorns undernät tar du bort undernätets tjänstslutpunkt och nätverksregeln. Ersätt behållarregistrets namn och resurs-ID för undernätet som du hämtade i ett tidigare steg i kommandot [az acr-nätverksregel:][az-acr-network-rule-remove] 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>Lägga till nätverksregel i registret

Använd kommandot [az acr network-rule add][az-acr-network-rule-add] för att lägga till en nätverksregel i registret som tillåter åtkomst från den virtuella datorns IP-adress. Ersätt behållarregistrets namn och den offentliga IP-adressen för den virtuella datorn i följande kommando.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Fortsätt att [verifiera åtkomsten till registret](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Tillåt åtkomst från en IP-adress - portal

#### <a name="remove-existing-network-rule-from-registry"></a>Ta bort befintlig nätverksregel från registret

Om du tidigare har lagt till en nätverksregel för att tillåta åtkomst från den virtuella datorns undernät tar du bort den befintliga regeln. Hoppa över det här avsnittet om du vill komma åt registret från en annan virtuell dator.

* Uppdatera inställningarna för undernät för att ta bort undernätets tjänstslutpunkt för Azure Container Registry. 

  1. I [Azure-portalen][azure-portal]navigerar du till det virtuella nätverket där den virtuella datorn distribueras.
  1. Under **Inställningar**väljer du **Undernät**.
  1. Välj det undernät där den virtuella datorn distribueras.
  1. Ta bort kryssrutan för **Microsoft.ContainerRegistry**under **Tjänstslutpunkter**. 
  1. Välj **Spara**.

* Ta bort nätverksregeln som gör att undernätet kan komma åt registret.

  1. Navigera till behållarregistret i portalen.
  1. Under **Inställningar**väljer du **Brandvägg och virtuella nätverk**.
  1. Under **Virtuella nätverk**väljer du namnet på det virtuella nätverket och väljer sedan Ta **bort**.
  1. Välj **Spara**.

#### <a name="add-network-rule-to-registry"></a>Lägga till nätverksregel i registret

1. Navigera till behållarregistret i portalen.
1. Under **Inställningar**väljer du **Brandvägg och virtuella nätverk**.
1. Om du inte redan har gjort det väljer du att tillåta åtkomst från **valda nätverk**. 
1. Under **Virtuella nätverk**kontrollerar du att inget nätverk är markerat.
1. Under **Brandvägg**anger du den offentliga IP-adressen för en virtuell dator. Du kan också ange ett adressintervall i CIDR-notation som innehåller den virtuella datorns IP-adress.
1. Välj **Spara**.

![Konfigurera brandväggsregel för behållarregistret][acr-vnet-firewall-portal]

Fortsätt att [verifiera åtkomsten till registret](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Verifiera åtkomst till registret

När du har väntat några minuter på att konfigurationen ska uppdatera kontrollerar du att den virtuella datorn kan komma åt behållarregistret. Gör en SSH-anslutning till din virtuella dator och kör kommandot [az acr-inloggning][az-acr-login] för att logga in på registret. 

```bash
az acr login --name mycontainerregistry
```

Du kan utföra registeråtgärder `docker pull` som att köra för att hämta en exempelavbildning från registret. Ersätt ett bild- och taggvärde som är lämpligt för registret, som föregås av registrets inloggningsservernamn (alla gemener):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker drar avbildningen till den virtuella datorn.

Det här exemplet visar att du kan komma åt det privata behållarregistret via nätverksåtkomstregeln. Registret kan dock inte nås från en annan inloggningsvärd som inte har en nätverksåtkomstregel konfigurerad. Om du försöker logga in `az acr login` från `docker login` en annan värd med kommandot eller kommandot liknar utdata följande:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Återställa standardregisteråtkomst

Om du vill återställa registret så att åtkomst tillåts som standard tar du bort alla nätverksregler som har konfigurerats. Ange sedan standardåtgärden för att tillåta åtkomst. Motsvarande steg med Azure CLI- och Azure-portalen tillhandahålls.

### <a name="restore-default-registry-access---cli"></a>Återställa standardregisteråtkomst - CLI

#### <a name="remove-network-rules"></a>Ta bort nätverksregler

Om du vill visa en lista över nätverksregler som konfigurerats för registret kör du följande kommando för en lista över nätverksregler för [az-regler:][az-acr-network-rule-list]

```azurecli
az acr network-rule list--name mycontainerregistry 
```

För varje regel som är konfigurerad kör du kommandot [az acr network-rule remove][az-acr-network-rule-remove] för att ta bort den. Ett exempel:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>Tillåt åkomst

Ersätt namnet på registret i följande [kommando för az acr-uppdatering:][az-acr-update]
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Återställa standardregisteråtkomst - portal


1. I portalen navigerar du till behållarregistret och väljer **Brandvägg och virtuella nätverk**.
1. Under **Virtuella nätverk**väljer du varje virtuellt nätverk och väljer sedan Ta **bort**.
1. Markera varje adressintervall under **Brandvägg**och välj sedan ikonen Ta bort.
1. Under **Tillåt åtkomst från**väljer du Alla **nätverk**. 
1. Välj **Spara**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat alla Azure-resurser i samma resursgrupp och inte längre behöver dem kan du också ta bort resurserna med hjälp av ett enda [az-gruppborttagningskommando:](/cli/azure/group)

```azurecli
az group delete --name myResourceGroup
```

Om du vill rensa dina resurser i portalen navigerar du till resursgruppen myResourceGroup. När resursgruppen har lästs in klickar du på **Ta bort resursgruppen** för att ta bort resursgruppen och de resurser som lagras där.

## <a name="next-steps"></a>Nästa steg

Flera virtuella nätverksresurser och funktioner diskuterades i den här artikeln, men kort. Dokumentationen till Azure Virtual Network beskriver följande avsnitt i stor utsträckning:

* [Virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Undernät](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [Tjänstslutpunkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
