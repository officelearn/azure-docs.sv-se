---
title: Begränsa åtkomsten till ett Azure Container Registry från ett virtuellt nätverk
description: Tillåt endast åtkomst till ett Azure Container Registry från resurser i ett virtuellt Azure-nätverk eller från offentliga IP-adressintervall.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: 3050a52da4d39657bd7b2fb38e235b9bd418faf4
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619880"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Begränsa åtkomsten till ett Azure Container Registry med hjälp av ett virtuellt Azure-nätverk eller brand Väggs regler

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) ger säker, privat nätverk för dina Azure-resurser och lokala resurser. Genom att begränsa åtkomsten till ditt privata Azure Container Registry från ett virtuellt Azure-nätverk ser du till att endast resurser i det virtuella nätverket har åtkomst till registret. Du kan också konfigurera brand Väggs regler för att tillåta att registret endast kommer åt från vissa IP-adresser för olika scenarier med olika platser.

Den här artikeln visar två scenarier för att konfigurera regler för inkommande nätverks åtkomst i ett behållar register: från en virtuell dator som distribueras i ett virtuellt nätverk eller från en virtuell DATORs offentliga IP-adress.

> [!IMPORTANT]
> Den här funktionen är för närvarande en för hands version och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).
>

Om du i stället behöver konfigurera åtkomst regler för resurser för att nå ett behållar register från bakom en brand vägg, se [Konfigurera regler för åtkomst till ett Azure Container Registry bakom en brand vägg](container-registry-firewall-access-rules.md).


## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Endast ett **Premium** container Registry kan konfigureras med nätverks åtkomst regler. Information om register tjänst nivåer finns i [Azure Container Registry SKU: er](container-registry-skus.md). 

* Endast ett [Azure Kubernetes service-](../aks/intro-kubernetes.md) kluster eller en virtuell Azure- [dator](../virtual-machines/linux/overview.md) kan användas som en värd för att komma åt ett behållar register i ett virtuellt nätverk. *Andra Azure-tjänster, inklusive Azure Container Instances, stöds inte för närvarande.*

* [ACR tasks](container-registry-tasks-overview.md) -åtgärder stöds inte för närvarande i ett behållar register som används i ett virtuellt nätverk.

* Varje register stöder högst 100 virtuella nätverks regler.

## <a name="prerequisites"></a>Förutsättningar

* För att kunna använda Azure CLI-stegen i den här artikeln krävs Azure CLI version 2.0.58 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

* Om du inte redan har ett behållar register måste du skapa ett (Premium-SKU krävs) och skicka en `hello-world` exempel avbildning, till exempel från Docker Hub. Använd till exempel [Azure Portal][quickstart-portal] eller [Azure CLI][quickstart-cli] för att skapa ett register. 

* Om du vill begränsa åtkomsten till registret med ett virtuellt nätverk i en annan Azure-prenumeration måste du registrera resurs leverantören för Azure Container Registry i den prenumerationen. Exempel:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>Om nätverks regler för ett behållar register

Ett Azure Container Registry som standard accepterar anslutningar via Internet från värdar i ett nätverk. Med ett virtuellt nätverk kan du bara tillåta Azure-resurser, till exempel ett AKS-kluster eller en virtuell Azure-dator för säker åtkomst till registret, utan att korsa en nätverks gräns. Du kan också konfigurera nätverks brand Väggs regler för att vitlista angivna offentliga IP-adressintervall. 

Om du vill begränsa åtkomsten till ett register måste du först ändra standard åtgärden för registret så att det nekar alla nätverks anslutningar. Lägg sedan till nätverks åtkomst regler. Klienter som beviljats åtkomst via nätverks reglerna måste fortsätta att [autentisera till behållar registret](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) och ha behörighet att komma åt data.

### <a name="service-endpoint-for-subnets"></a>Tjänst slut punkt för undernät

Om du vill tillåta åtkomst från ett undernät i ett virtuellt nätverk måste du lägga till en [tjänst slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) för Azure Container Registry tjänsten. 

Tjänster för flera innehavare, t. ex. Azure Container Registry, använder en enda uppsättning IP-adresser för alla kunder. En tjänst slut punkt tilldelar en slut punkt för att få åtkomst till ett register. Den här slut punkten ger trafiken en optimal väg till resursen över Azure stamnät nätverket. Identiteten för det virtuella nätverket och undernätet överförs även med varje begäran.

### <a name="firewall-rules"></a>Brandväggsregler

För IP-nätverks regler anger du tillåtna Internet adress intervall med CIDR-notering som *16.17.18.0/24* eller enskilda IP-adresser som *16.17.18.19*. IP-nätverks regler tillåts endast för *offentliga* Internet-IP-adresser. IP-adressintervall som är reserverade för privata nätverk (enligt definitionen i RFC 1918) tillåts inte i IP-regler.

## <a name="create-a-docker-enabled-virtual-machine"></a>Skapa en Docker-aktiverad virtuell dator

I den här artikeln använder du en Docker-aktiverad virtuell Ubuntu-dator för att få åtkomst till ett Azure Container Registry. Om du vill använda Azure Active Directory autentisering i registret installerar du även [Azure CLI][azure-cli] på den virtuella datorn. Hoppa över det här steget om du redan har en virtuell Azure-dator.

Du kan använda samma resurs grupp för din virtuella dator och behållar registret. Den här installationen fören klar rensningen, men är inte obligatorisk. Om du väljer att skapa en separat resurs grupp för den virtuella datorn och det virtuella nätverket kör du [AZ Group Create][az-group-create]. I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på *westcentralus* -platsen:

```azurecli
az group create --name myResourceGroup --location westus
```

Distribuera nu en standard virtuell Azure-Ubuntu med [AZ VM Create][az-vm-create]. I följande exempel skapas en virtuell dator med namnet *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När kommandot har slutförts noterar du det `publicIpAddress` som visas av Azure CLI. Använd den här adressen om du vill göra SSH-anslutningar till den virtuella datorn och eventuellt senare konfigurera brand Väggs regler.

### <a name="install-docker-on-the-vm"></a>Installera Docker på den virtuella datorn

När den virtuella datorn har körts skapar du en SSH-anslutning till den virtuella datorn. Ersätt *publicIpAddress* med den offentliga IP-adressen för den virtuella datorn.

```bash
ssh azureuser@publicIpAddress
```

Kör följande kommando för att installera Docker på den virtuella datorn Ubuntu:

```bash
sudo apt install docker.io -y
```

Efter installationen kör du följande kommando för att kontrol lera att Docker körs korrekt på den virtuella datorn:

```bash
sudo docker run -it hello-world
```

Utdata:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Installera Azure CLI

Följ stegen i [Installera Azure CLI med apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) för att installera Azure CLI på den virtuella Ubuntu-datorn. I den här artikeln kontrollerar du att du installerar version 2.0.58 eller senare.

Avsluta SSH-anslutningen.

## <a name="allow-access-from-a-virtual-network"></a>Tillåt åtkomst från ett virtuellt nätverk

I det här avsnittet konfigurerar du behållar registret för att tillåta åtkomst från ett undernät i ett virtuellt Azure-nätverk. Likvärdiga steg som använder Azure CLI och Azure Portal tillhandahålls.

### <a name="allow-access-from-a-virtual-network---cli"></a>Tillåt åtkomst från ett virtuellt nätverk – CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Lägga till en tjänst slut punkt i ett undernät

När du skapar en virtuell dator skapar Azure som standard ett virtuellt nätverk i samma resurs grupp. Namnet på det virtuella nätverket baseras på namnet på den virtuella datorn. Om du till exempel namnger din virtuella dator *myDockerVM*är det virtuella standard nätverks namnet *myDockerVMVNET*, med ett undernät som heter *myDockerVMSubnet*. Kontrol lera detta i Azure Portal eller genom att använda kommandot [AZ Network VNet List][az-network-vnet-list] :

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

Utdata:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Använd kommandot [AZ Network VNet Subnet Update][az-network-vnet-subnet-update] för att lägga till en tjänst slut punkt för **Microsoft. ContainerRegistry** i under nätet. Ersätt namnen på ditt virtuella nätverk och undernät i följande kommando:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Använd kommandot [AZ Network VNet Subnet show][az-network-vnet-subnet-show] för att hämta resurs-ID för under nätet. Du behöver detta i ett senare steg för att konfigurera en regel för nätverks åtkomst.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Utdata:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>Ändra standard nätverks åtkomst till registret

Som standard tillåter ett Azure Container Registry anslutningar från värdar i alla nätverk. Om du vill begränsa åtkomsten till ett valt nätverk ändrar du standard åtgärden till neka åtkomst. Ersätt namnet på registret i följande [AZ ACR Update][az-acr-update] -kommando:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Lägg till nätverks regel i registret

Använd kommandot [AZ ACR Network-Rule Add][az-acr-network-rule-add] för att lägga till en nätverks regel i registret som tillåter åtkomst från den virtuella datorns undernät. Ersätt namnet på behållar registret och resurs-ID: t för under nätet i följande kommando: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Fortsätt att [Verifiera åtkomst till registret](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Tillåt åtkomst från ett virtuellt nätverk – Portal

#### <a name="add-service-endpoint-to-subnet"></a>Lägg till tjänst slut punkt i undernät

När du skapar en virtuell dator skapar Azure som standard ett virtuellt nätverk i samma resurs grupp. Namnet på det virtuella nätverket baseras på namnet på den virtuella datorn. Om du till exempel namnger din virtuella dator *myDockerVM*är det virtuella standard nätverks namnet *myDockerVMVNET*, med ett undernät som heter *myDockerVMSubnet*.

Så här lägger du till en tjänst slut punkt för Azure Container Registry till ett undernät:

1. I rutan Sök högst upp i [Azure Portal][azure-portal]anger du *virtuella nätverk*. När **virtuella nätverk** visas i Sök resultaten väljer du det.
1. I listan över virtuella nätverk väljer du det virtuella nätverk där den virtuella datorn distribueras, till exempel *myDockerVMVNET*.
1. Under **Inställningar**väljer du **undernät**.
1. Välj under nätet där den virtuella datorn distribueras, till exempel *myDockerVMSubnet*.
1. Under **tjänst slut punkter**väljer du **Microsoft. ContainerRegistry**.
1. Välj **Spara**.

![Lägg till tjänst slut punkt i undernät][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Konfigurera nätverks åtkomst för registret

Som standard tillåter ett Azure Container Registry anslutningar från värdar i alla nätverk. Så här begränsar du åtkomsten till det virtuella nätverket:

1. I portalen navigerar du till behållar registret.
1. Under **Inställningar**väljer du **brand vägg och virtuella nätverk**.
1. Om du vill neka åtkomst som standard välja att tillåta åtkomst från **valda nätverk**. 
1. Välj **Lägg till befintligt virtuellt nätverk**och välj det virtuella nätverk och undernät som du har konfigurerat med en tjänst slut punkt. Välj **Lägg till**.
1. Välj **Spara**.

![Konfigurera virtuellt nätverk för container Registry][acr-vnet-portal]

Fortsätt att [Verifiera åtkomst till registret](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Tillåt åtkomst från en IP-adress

I det här avsnittet konfigurerar du behållar registret för att tillåta åtkomst från en speciell IP-adress eller ett särskilt intervall. Likvärdiga steg som använder Azure CLI och Azure Portal tillhandahålls.

### <a name="allow-access-from-an-ip-address---cli"></a>Tillåt åtkomst från en IP-adress – CLI

#### <a name="change-default-network-access-to-registry"></a>Ändra standard nätverks åtkomst till registret

Om du inte redan har gjort det uppdaterar du register konfigurationen för att neka åtkomst som standard. Ersätt namnet på registret i följande [AZ ACR Update][az-acr-update] -kommando:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Ta bort nätverks regel från registret

Om du tidigare har lagt till en nätverks regel för att tillåta åtkomst från den virtuella datorns undernät, tar du bort under nätets tjänst slut punkt och nätverks regeln. Ersätt behållar registrets namn och resurs-ID för under nätet som du hämtade i ett tidigare steg i kommandot [AZ ACR Network-Rule Remove][az-acr-network-rule-remove] : 

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

#### <a name="add-network-rule-to-registry"></a>Lägg till nätverks regel i registret

Använd kommandot [AZ ACR Network-Rule Add][az-acr-network-rule-add] för att lägga till en nätverks regel i registret som tillåter åtkomst från den virtuella DATORns IP-adress. Ersätt behållar register namnet och den offentliga IP-adressen för den virtuella datorn i följande kommando.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Fortsätt att [Verifiera åtkomst till registret](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Tillåt åtkomst från en IP-adress – Portal

#### <a name="remove-existing-network-rule-from-registry"></a>Ta bort den befintliga nätverks regeln från registret

Om du tidigare har lagt till en nätverks regel för att tillåta åtkomst från den virtuella datorns undernät, tar du bort den befintliga regeln. Hoppa över det här avsnittet om du vill ha åtkomst till registret från en annan virtuell dator.

* Uppdatera under näts inställningarna för att ta bort under nätets tjänst slut punkt för Azure Container Registry. 

  1. I [Azure Portal][azure-portal]navigerar du till det virtuella nätverk där den virtuella datorn har distribuerats.
  1. Under **Inställningar**väljer du **undernät**.
  1. Välj under nätet där den virtuella datorn ska distribueras.
  1. Ta bort kryss rutan för **Microsoft. ContainerRegistry**under **tjänst slut punkter**. 
  1. Välj **Spara**.

* Ta bort nätverks regeln som tillåter under nätet att komma åt registret.

  1. I portalen navigerar du till behållar registret.
  1. Under **Inställningar**väljer du **brand vägg och virtuella nätverk**.
  1. Under **virtuella nätverk**väljer du namnet på det virtuella nätverket och väljer sedan **ta bort**.
  1. Välj **Spara**.

#### <a name="add-network-rule-to-registry"></a>Lägg till nätverks regel i registret

1. I portalen navigerar du till behållar registret.
1. Under **Inställningar**väljer du **brand vägg och virtuella nätverk**.
1. Om du inte redan har gjort det väljer du att tillåta åtkomst från **valda nätverk**. 
1. Se till att inget nätverk är markerat under **virtuella nätverk**.
1. Under **brand vägg**anger du den offentliga IP-adressen för en virtuell dator. Eller ange ett adress intervall i CIDR-notation som innehåller den virtuella datorns IP-adress.
1. Välj **Spara**.

![Konfigurera brand Väggs regel för container Registry][acr-vnet-firewall-portal]

Fortsätt att [Verifiera åtkomst till registret](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Verifiera åtkomst till registret

Efter några minuter efter att konfigurationen uppdaterats, verifiera att den virtuella datorn har åtkomst till behållar registret. Skapa en SSH-anslutning till den virtuella datorn och kör kommandot [AZ ACR login][az-acr-login] för att logga in i registret. 

```bash
az acr login --name mycontainerregistry
```

Du kan utföra register åtgärder som att köra `docker pull` för att hämta en exempel avbildning från registret. Ersätt en bild och ett tagg värde som är lämpligt för ditt register, med prefixet för inloggnings Server namnet för registret (alla gemener):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker hämtar avbildningen till den virtuella datorn.

Det här exemplet visar att du kan komma åt den privata behållar registret via nätverks åtkomst regeln. Registret kan dock inte nås från en annan inloggnings värd som inte har någon konfigurerad nätverks åtkomst regel. Om du försöker logga in från en annan värd med `az acr login` hjälp av `docker login` kommandot eller kommandot ser utdata ut ungefär så här:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Återställ standard register åtkomst

Om du vill återställa registret för att tillåta åtkomst som standard, tar du bort eventuella nätverks regler som är konfigurerade. Ange sedan standard åtgärden för att tillåta åtkomst. Likvärdiga steg som använder Azure CLI och Azure Portal tillhandahålls.

### <a name="restore-default-registry-access---cli"></a>Återställ standard register åtkomst-CLI

#### <a name="remove-network-rules"></a>Ta bort nätverks regler

Om du vill se en lista över nätverks regler som har kon figurer ATS för registret kör du följande kommando för [AZ ACR Network-Rule List][az-acr-network-rule-list] :

```azurecli
az acr network-rule list--name mycontainerregistry 
```

För varje regel som har kon figurer ATS kör du kommandot [AZ ACR Network-Rule Remove Remove][az-acr-network-rule-remove] för att ta bort den. Exempel:

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

#### <a name="allow-access"></a>Tillåt åtkomst

Ersätt namnet på registret i följande [AZ ACR Update][az-acr-update] -kommando:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Återställa standard åtkomst till registret – portalen


1. I portalen navigerar du till ditt behållar register och väljer **brand vägg och virtuella nätverk**.
1. Under **virtuella nätverk**väljer du varje virtuellt nätverk och väljer sedan **ta bort**.
1. Under **brand vägg**väljer du varje adress intervall och väljer sedan ikonen Ta bort.
1. Under **Tillåt åtkomst från**väljer du **alla nätverk**. 
1. Välj **Spara**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat alla Azure-resurser i samma resurs grupp och inte längre behöver dem, kan du välja att ta bort resurserna med hjälp av ett enda [AZ Group Delete](/cli/azure/group) -kommando:

```azurecli
az group delete --name myResourceGroup
```

Om du vill rensa dina resurser i portalen navigerar du till resurs gruppen myResourceGroup. När resurs gruppen har lästs in klickar du på **ta bort resurs** grupp för att ta bort resurs gruppen och resurserna som lagras där.

## <a name="next-steps"></a>Nästa steg

Flera virtuella nätverks resurser och funktioner beskrivs i den här artikeln, men i korthet. Azure Virtual Network-dokumentationen omfattar följande ämnen:

* [Virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Undernät](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [Serviceslutpunkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

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
