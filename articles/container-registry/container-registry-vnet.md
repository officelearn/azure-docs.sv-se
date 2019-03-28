---
title: Distribuera ett Azure container registry till ett virtuellt nätverk
description: Tillåt åtkomst till ett Azure container registry endast från resurser i Azure-nätverk eller från offentlig IP-adressintervall.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/14/2019
ms.author: danlep
ms.openlocfilehash: 0a4d9f355a5cdc92bab4491c08677042c42986cb
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517937"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Begränsa åtkomsten till ett Azure container registry med Azure-nätverk eller brandväggsregler

[Azure-nätverk](../virtual-network/virtual-networks-overview.md) tillhandahåller säkra, privata nätverk för Azure och lokala resurser. Genom att distribuera privat Azure container registry till en Azure-nätverk, kan du se till att endast resurser i det virtuella nätverket åtkomst till registret. Du kan också konfigurera brandväggsregler för att tillåta registeråtkomst endast från specifika IP-adresser för scenarier med flera platser.

Den här artikeln visar två scenarier för att skapa regler för nätverksåtkomst för att begränsa åtkomsten till ett Azure container registry: från en virtuell dator distribueras i samma nätverk eller från en virtuell dators offentliga IP-adressen.

> [!IMPORTANT]
> Den här funktionen finns för närvarande i förhandsversion och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).
>

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Endast en **Premium** behållarregister kan konfigureras med regler för nätverksåtkomst. Information om tjänstnivåer för registret finns i [Azure Container Registry SKU: er](container-registry-skus.md). 

* Endast en [Azure Kubernetes Service](../aks/intro-kubernetes.md) kluster eller Azure [VM](../virtual-machines/linux/overview.md) kan användas som värd för åtkomst till ett behållarregister i ett virtuellt nätverk. *Andra Azure-tjänster, inklusive Azure Container Instances stöds inte för närvarande.*

* Varje register stöder högst 100 virtuella Nätverksregler.

## <a name="prerequisites"></a>Förutsättningar

* Du använder Azure CLI stegen i den här artikeln, Azure CLI version 2.0.58 eller senare krävs. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

* Om du inte redan har ett behållarregister, skapa en (Premium-SKU krävs) och skicka en exempelbild som `hello-world` från Docker Hub. Till exempel använda den [Azure-portalen] [ quickstart-portal] eller [Azure CLI] [ quickstart-cli] att skapa ett register. 

## <a name="about-network-rules-for-a-container-registry"></a>Om Nätverksregler för ett behållarregister

Ett Azure container registry som standard tar emot anslutningar via internet från värdar i alla nätverk. Du kan tillåta endast Azure-resurser, till exempel ett AKS-kluster eller virtuella Azure-datorn säker åtkomst till registret, utan att korsa en nätverksgräns med ett virtuellt nätverk. Du kan också konfigurera network-brandväggsregler godkänna specifika offentliga internet IP-adressintervall. 

Om du vill begränsa åtkomsten till ett register först ändra standardåtgärden för registret så att det nekar alla nätverksanslutningar. Sedan kan lägga till regler för nätverksåtkomst. Klienter beviljas åtkomst via Nätverksregler måste fortsätta att [autentisera till behållarregistret](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) och ha behörighet att komma åt data.

### <a name="service-endpoint-for-subnets"></a>Tjänstens slutpunkt för undernät

Om du vill tillåta åtkomst från ett undernät i ett virtuellt nätverk du behöver lägga till en [tjänstslutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) för Azure Container Registry-tjänsten. 

Tjänster med flera innehavare, använda t.ex. Azure Container Registry, en enda uppsättning IP-adresser för alla kunder. En tjänstslutpunkt tilldelar en slutpunkt för att få åtkomst till ett register. Den här slutpunkten får trafik en bästa vägen till resursen via Azures stamnätverk. Identiteten för det virtuella nätverket och undernätet överförs även med varje begäran.

### <a name="firewall-rules"></a>Brandväggsregler

För IP-Nätverksregler, anger du tillåtna internet-adressintervall i CIDR-format som *16.17.18.0/24* eller en enstaka IP-adresser som *16.17.18.19*. IP-Nätverksregler är bara tillåtna för *offentliga* internet IP-adresser. IP-adressintervall som reserverats för privata nätverk (som definieras i RFC 1918) är inte tillåtna i IP-regler.

## <a name="create-a-docker-enabled-virtual-machine"></a>Skapa en Docker-aktiverad virtuell dator

I den här artikeln använder du en Docker-aktiverade Ubuntu VM till en Azure container registry. För att använda Azure Active Directory-autentisering till registret måste också installera den [Azure CLI] [ azure-cli] på den virtuella datorn. Om du redan har en Azure virtuell dator kan du hoppa över det här steget av skapandet.

Du kan använda samma resursgrupp för den virtuella datorn och ditt behållarregister. Den här konfigurationen gör det enklare att rensa i slutet, men är inget krav. Om du vill skapa en separat resursgrupp för den virtuella datorn och virtuellt nätverk, köra [az gruppen skapa][az-group-create]. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *westcentralus* plats:

```azurecli
az group create --name myResourceGroup --location westus
```

Nu distribuera en standard Ubuntu Azure-dator med [az vm skapa][az-vm-create]. I följande exempel skapas en virtuell dator med namnet *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När kommandot har slutförts kan du ta del av den `publicIpAddress` visas av Azure CLI. Använd den här adressen för att skapa SSH-anslutningar till den virtuella datorn och eventuellt för senare installation av brandväggsregler.

### <a name="install-docker-on-the-vm"></a>Installera Docker på den virtuella datorn

När Virtuellt datorn körs, skapar du en SSH-anslutning till den virtuella datorn. Ersätt *publicIpAddress* med den offentliga IP-adressen för den virtuella datorn.

```bash
ssh azureuser@publicIpAddress
```

Kör följande kommando för att installera Docker på Ubuntu VM:

```bash
sudo apt install docker.io -y
```

Kör följande kommando för att verifiera att Docker körs korrekt på den virtuella datorn efter installationen:

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

Följ stegen i [installera Azure CLI med apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) att installera Azure CLI på Ubuntu-dator. Den här artikeln är att se till att du installerar version 2.0.58 eller senare.

Avsluta SSH-anslutningen.

## <a name="allow-access-from-a-virtual-network"></a>Tillåt åtkomst från ett virtuellt nätverk

I det här avsnittet konfigurerar du ditt behållarregister för att tillåta åtkomst från ett undernät i ett Azure-nätverk. Motsvarande steg med hjälp av Azure CLI och Azure-portalen finns.

### <a name="allow-access-from-a-virtual-network---cli"></a>Tillåta åtkomst från ett virtuellt nätverk – CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Lägg till en tjänstslutpunkt till ett undernät

När du skapar en virtuell dator i Azure som standard skapar ett virtuellt nätverk i samma resursgrupp. Namnet på det virtuella nätverket baseras på namnet på den virtuella datorn. Exempel: Om du namnger din virtuella dator *myDockerVM*, standardnamnet för virtuellt nätverk är *myDockerVMVNET*, med ett undernät med namnet *myDockerVMSubnet*. Kontrollera detta i Azure portal eller med hjälp av den [az network vnet list] [ az-network-vnet-list] kommando:

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

Använd den [az network vnet-undernät update] [ az-network-vnet-subnet-update] att lägga till en **Microsoft.ContainerRegistry** tjänstslutpunkt till undernätet. Ersätt namnen för virtuellt nätverk och undernät i följande kommando:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Använd den [az network vnet-undernät show] [ az-network-vnet-subnet-show] kommando för att hämta resurs-ID för undernätet. Du behöver detta i ett senare steg att konfigurera en regel för åtkomst.

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

#### <a name="change-default-network-access-to-registry"></a>Ändra standard-nätverksåtkomst till registret

Som standard tillåter anslutningar från värdar i alla nätverk i ett Azure container registry. Om du vill begränsa åtkomsten till ett markerat nätverk, ändrar du åtgärden för att neka åtkomst. Ersätt namnet på registret i följande [az acr update] [ az-acr-update] kommando:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Lägg till regel för registret

Använd den [az acr-nätverksregel lägga till] [ az-acr-network-rule-add] kommando för att lägga till en regel i registret som tillåter åtkomst från undernätet för den virtuella datorn. Ersätt container registry-namn och resurs-ID för undernätet i följande kommando: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Fortsätta att [Kontrollera åtkomst till registret](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Tillåta åtkomst från ett virtuellt nätverk – portal

#### <a name="add-service-endpoint-to-subnet"></a>Lägg till tjänstslutpunkt i undernät

När du skapar en virtuell dator i Azure som standard skapar ett virtuellt nätverk i samma resursgrupp. Namnet på det virtuella nätverket baseras på namnet på den virtuella datorn. Exempel: Om du namnger din virtuella dator *myDockerVM*, standardnamnet för virtuellt nätverk är *myDockerVMVNET*, med ett undernät med namnet *myDockerVMSubnet*.

Lägga till en tjänstslutpunkt för Azure Container Registry till ett undernät:

1. I sökrutan överst på den [Azure-portalen][azure-portal], ange *virtuella nätverk*. När **virtuella nätverk** visas i sökresultaten, markerar du den.
1. Från listan över virtuella nätverk, väljer du det virtuella nätverket där den virtuella datorn distribueras som *myDockerVMVNET*.
1. Under **inställningar**väljer **undernät**.
1. Välj det undernät där den virtuella datorn distribueras som *myDockerVMSubnet*.
1. Under **tjänstslutpunkter**väljer **Microsoft.ContainerRegistry**.
1. Välj **Spara**.

![Lägg till tjänstslutpunkt i undernät][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Konfigurera nätverksåtkomsten för registret

Som standard tillåter anslutningar från värdar i alla nätverk i ett Azure container registry. Begränsa åtkomsten till det virtuella nätverket:

1. Gå till behållarregistret i portalen.
1. Under **inställningar**väljer **brandvägg och virtuella nätverk**.
1. Om du vill neka åtkomst som standard välja att tillåta åtkomst från **valda nätverk**. 
1. Välj **Lägg till befintligt virtuellt nätverk**, och välj det virtuella nätverk och undernät som du konfigurerat med en tjänstslutpunkt. Välj **Lägg till**.
1. Välj **Spara**.

![Konfigurera ett virtuellt nätverk för container registry][acr-vnet-portal]

Fortsätta att [Kontrollera åtkomst till registret](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Tillåta åtkomst från en IP-adress

I det här avsnittet konfigurerar du ditt behållarregister för att tillåta åtkomst från ett undernät i ett Azure-nätverk. Motsvarande steg med hjälp av Azure CLI och Azure-portalen finns.

### <a name="allow-access-from-an-ip-address---cli"></a>Tillåta åtkomst från en IP-adress – CLI

#### <a name="change-default-network-access-to-registry"></a>Ändra standard-nätverksåtkomst till registret

Om du inte redan gjort det, uppdaterar du registerkonfigurationen för att neka åtkomst som standard. Ersätt namnet på registret i följande [az acr update] [ az-acr-update] kommando:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Ta bort regel från registret

Om du tidigare lade till en regel för att tillåta åtkomst från undernätet för den virtuella datorn bort undernätets tjänstslutpunkt och nätverk regeln. Ersätt namnet på den container registry och resurs-ID för det undernät som du hämtade i ett tidigare steg i den [az acr-nätverksregel ta bort] [ az-acr-network-rule-remove] kommando: 

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

#### <a name="add-network-rule-to-registry"></a>Lägg till regel för registret

Använd den [az acr-nätverksregel lägga till] [ az-acr-network-rule-add] kommando för att lägga till en regel i registret som tillåter åtkomst från den Virtuella datorns IP-adress. Ersätt namnet på den container registry och offentliga IP-adressen för den virtuella datorn i följande kommando.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Fortsätta att [Kontrollera åtkomst till registret](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Tillåta åtkomst från en IP-adress – portal

#### <a name="remove-existing-network-rule-from-registry"></a>Ta bort befintliga nätverksregel från registret

Ta bort den befintliga regeln om du tidigare lade till en regel för att tillåta åtkomst från undernätet för den virtuella datorn. Hoppa över det här avsnittet om du vill komma åt registret från en annan virtuell dator.

* Uppdatera Undernätverksinställningarna för för att ta bort undernätets tjänstslutpunkt för Azure Container Registry. 

  1. I den [Azure-portalen][azure-portal], navigera till det virtuella nätverket där den virtuella datorn har distribuerats.
  1. Under **inställningar**väljer **undernät**.
  1. Välj det undernät där den virtuella datorn har distribuerats.
  1. Under **tjänstslutpunkter**, ta bort kryssrutan för **Microsoft.ContainerRegistry**. 
  1. Välj **Spara**.

* Ta bort network-regel som tillåter undernätet för att komma åt registret.

  1. Gå till behållarregistret i portalen.
  1. Under **inställningar**väljer **brandvägg och virtuella nätverk**.
  1. Under **virtuella nätverk**, Välj namnet på det virtuella nätverket och välj sedan **ta bort**.
  1. Välj **Spara**.

#### <a name="add-network-rule-to-registry"></a>Lägg till regel för registret

1. Gå till behållarregistret i portalen.
1. Under **inställningar**väljer **brandvägg och virtuella nätverk**.
1. Om du inte redan gjort det, välja att tillåta åtkomst från **valda nätverk**. 
1. Under **virtuella nätverk**, se till att inga nätverk har valts.
1. Under **brandväggen**, ange den offentliga IP-adressen för en virtuell dator. Alternativt kan ange ett adressintervall i CIDR-notation som innehåller den Virtuella datorns IP-adress.
1. Välj **Spara**.

![Konfigurera brandväggsregel för container registry][acr-vnet-firewall-portal]

Fortsätta att [Kontrollera åtkomst till registret](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Kontrollera åtkomst till registret

Efter några minuter att uppdatera konfigurationen, kontrollera att den virtuella datorn har åtkomst till behållarregistret. Se en SSH-anslutning till den virtuella datorn och kör den [docker login] [ az-acr-login] för att logga in på ditt register. 

```bash
az acr login --name mycontainerregistry
```

Du kan utföra åtgärder för registret t.ex. köra `docker pull` att hämta en exempelbild från registret. Ersätta en bild och tagg värdet som är lämpliga för ditt register föregås av registret namnet på inloggningsservern (endast gemener):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker hämtar har avbildningen till den virtuella datorn.

Det här exemplet visar att du kan komma åt privat behållarregister via nätverket åtkomstregel. Registret kan inte nås från en annan inloggning-värd som inte har konfigurerat en regel för åtkomst. Om du försöker logga in från en annan värd med hjälp av den `az acr login` kommando eller `docker login` kommandot utdata som liknar följande:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Återställ standard registeråtkomst

Ta bort alla Nätverksregler som är konfigurerade för att återställa registret för att tillåta åtkomst som standard. Sedan ställer du in standardåtgärden att tillåta åtkomst. Motsvarande steg med hjälp av Azure CLI och Azure-portalen finns.

### <a name="restore-default-registry-access---cli"></a>Återställ standard registeråtkomst – CLI

#### <a name="remove-network-rules"></a>Ta bort Nätverksregler

Om du vill se en lista över Nätverksregler som konfigurerats för registret, kör du följande [az acr-regel lista] [ az-acr-network-rule-list] kommando:

```azurecli
az acr network-rule list--name mycontainerregistry 
```

För varje regel som är konfigurerad, kör den [az acr-nätverksregel ta bort] [ az-acr-network-rule-remove] kommando för att ta bort den. Exempel:

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

Ersätt namnet på registret i följande [az acr update] [ az-acr-update] kommando:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Återställ standard registeråtkomst - portalen


1. Gå till behållarregistret i portalen och välj **brandvägg och virtuella nätverk**.
1. Under **virtuella nätverk**, markerar du de virtuella nätverk och välj sedan **ta bort**.
1. Under **brandväggen**, Välj varje adressintervall och välj ikonen Ta bort.
1. Under **tillåta åtkomst från**väljer **alla nätverk**. 
1. Välj **Spara**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat alla Azure-resurser i samma resurs gruppen och inte längre behöver dem. Du kan ta eventuellt bort resurserna med hjälp av en enda [az group delete](/cli/azure/group) kommando:

```azurecli
az group delete --name myResourceGroup
```

Om du vill rensa dina resurser i portalen, navigera till resursgruppen myResourceGroup. När resursgruppen har lästs in, klickar du på **ta bort resursgrupp** att ta bort resursgruppen och de resurser som lagras där.

## <a name="next-steps"></a>Nästa steg

Flera virtuella nätverksresurser och funktioner beskrivs i den här artikeln, även om en kort stund. Azure Virtual Network-dokumentationen beskriver hur de här ämnena stor utsträckning:

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
