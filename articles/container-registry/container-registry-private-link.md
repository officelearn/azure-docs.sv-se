---
title: Konfigurera privat länk
description: Konfigurera en privat slutpunkt i ett behållarregister och aktivera en privat länk i ett lokalt virtuellt nätverk
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: de8228d84497e71f24dba3dd4e6162cb6735a8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498910"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Konfigurera Azure Private Link för ett Azure-behållarregister 

Du kan konfigurera en [privat slutpunkt](../private-link/private-endpoint-overview.md) för ditt Azure-behållarregister så att klienter i ett virtuellt Azure-nätverk på ett säkert sätt får åtkomst till registret via en [privat länk](../private-link/private-link-overview.md). Den privata slutpunkten använder en IP-adress från det virtuella nätverksadressutrymmet för registret. Nätverkstrafik mellan klienterna i det virtuella nätverket och registret passerar det virtuella nätverket och en privat länk på Microsofts stamnätsnätverk, vilket eliminerar exponering från det offentliga internet.

Du kan [konfigurera DNS-inställningar](../private-link/private-endpoint-overview.md#dns-configuration) för den privata slutpunkten så att inställningarna matchas till registrets allokerade privata IP-adress. Med DNS-konfiguration kan klienter och tjänster i nätverket fortsätta att komma åt registret vid registrets fullständigt kvalificerade domännamn, till exempel *myregistry.azurecr.io*.

Den här funktionen **Premium** är tillgänglig på premium-behållarregistertjänstnivån. Information om registertjänstnivåer och -begränsningar finns i [Azure Container Registry SKU: er](container-registry-skus.md).

> [!IMPORTANT]
> Den här funktionen är för närvarande i förhandsversion och vissa [begränsningar](#preview-limitations) gäller. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* För närvarande kan du inte konfigurera en privat länk med en privat slutpunkt i ett [geore replikerat register](container-registry-geo-replication.md). 

## <a name="prerequisites"></a>Krav

* Om du vill använda Azure CLI-stegen i den här artikeln rekommenderas Azure CLI version 2.2.0 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli]. Eller kör i [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Om du inte redan har ett behållarregister skapar du ett (Premium-nivå `hello-world` krävs) och skickar en exempelavbildning, till exempel från Docker Hub. Du kan till exempel använda [Azure-portalen][quickstart-portal] eller [Azure CLI][quickstart-cli] för att skapa ett register.
* Om du vill konfigurera registeråtkomst med en privat länk i en annan Azure-prenumeration måste du registrera resursprovidern för Azure Container Registry i den prenumerationen. Ett exempel:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Azure CLI-exemplen i den här artikeln använder följande miljövariabler. Ersätt värden som är lämpliga för din miljö. Alla exempel är formaterade för Bash-skalet:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Skapa en Docker-aktiverad virtuell dator

I testsyfte använder du en Docker-aktiverad Ubuntu-virtuell dator för att komma åt ett Azure-behållarregister. Om du vill använda Azure Active Directory-autentisering till registret installerar du även [Azure CLI][azure-cli] på den virtuella datorn. Om du redan har en virtuell Azure-dator hoppar du över det här skapandesteget.

Du kan använda samma resursgrupp för den virtuella datorn och behållarregistret. Den här inställningen förenklar rensningen i slutet men krävs inte. Om du väljer att skapa en separat resursgrupp för den virtuella datorn och det virtuella nätverket kör du [az group create:][az-group-create]

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

Distribuera nu en virtuell standarddator för Ubuntu Azure med [az vm create][az-vm-create]. I följande exempel skapas en virtuell dator med namnet *myDockerVM*.

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När kommandot är klart bör `publicIpAddress` du notera vad som visas av Azure CLI. Använd den här adressen om du vill skapa SSH-anslutningar till den virtuella datorn.

### <a name="install-docker-on-the-vm"></a>Installera Docker på den virtuella datorn

När den virtuella datorn har körts gör du en SSH-anslutning till den virtuella datorn. Ersätt *publicIpAddress* med den offentliga IP-adressen för din virtuella dator.

```bash
ssh azureuser@publicIpAddress
```

Kör följande kommandon för att installera Docker på den virtuella datorn i Ubuntu:

```bash
sudo apt-get update
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

Följ stegen i [Installera Azure CLI med apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) för att installera Azure CLI på din virtuella Ubuntu-dator. Ett exempel:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Avsluta SSH-anslutningen.

## <a name="set-up-private-link---cli"></a>Ställ in privat länk - CLI

### <a name="get-network-and-subnet-names"></a>Hämta nätverks- och undernätsnamn

Om du inte redan har dem behöver du namnen på ett virtuellt nätverk och ett undernät för att konfigurera en privat länk. I det här exemplet använder du samma undernät för den virtuella datorn och registrets privata slutpunkt. I många fall konfigurerar du dock slutpunkten i ett separat undernät. 

När du skapar en virtuell dator skapar Azure som standard ett virtuellt nätverk i samma resursgrupp. Namnet på det virtuella nätverket baseras på namnet på den virtuella datorn. Om du till exempel namnger den virtuella datorn *myDockerVM*är standardnamnet för virtuellt nätverk *myDockerVMVNET*, med ett undernät med namnet *myDockerVMSubnet*. Ange dessa värden i miljövariabler genom att köra kommandot [az network vnet list:][az-network-vnet-list]

```azurecli
networkName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Name: name}' --output tsv)

subnetName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo networkName=$networkName
echo subnetName=$subnetName
```

### <a name="disable-network-policies-in-subnet"></a>Inaktivera nätverksprinciper i undernätet

[Inaktivera nätverksprinciper](../private-link/disable-private-endpoint-network-policy.md) som nätverkssäkerhetsgrupper i undernätet för den privata slutpunkten. Uppdatera nätkonfigurationen med uppdatering av [az-nätverkets virtuella nät:][az-network-vnet-subnet-update]

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Konfigurera den privata DNS-zonen

Skapa en privat DNS-zon för den privilegier Azure-behållarregisterdomänen. I senare steg skapar du DNS-poster för registerdomänen i den här DNS-zonen.

Om du vill använda en privat zon för att åsidosätta standard-DNS-lösningen för ditt Azure-behållarregister måste zonen namnges **privatelink.azurecr.io**. Kör följande kommando för [att skapa az-nätverket private-dns-zonen][az-network-private-dns-zone-create] för att skapa den privata zonen:

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Skapa en associationslänk

Kör [az nätverk privat-dns länk vnet skapa][az-network-private-dns-link-vnet-create] för att associera din privata zon med det virtuella nätverket. I det här exemplet skapas en länk som heter *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Skapa en slutpunkt för ett privat register

Skapa registrets privata slutpunkt i det virtuella nätverket i det här avsnittet. Hämta först registrets resurs-ID:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Kör kommandot [az network private-endpoint create][az-network-private-endpoint-create] för att skapa registrets privata slutpunkt.

I följande exempel skapas slutpunkten *myPrivateEndpoint* och serviceanslutning *myConnection*. Om du vill ange en behållarregisterresurs för slutpunkten skickar du: `--group-ids registry`

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $resourceGroup \
    --vnet-name $networkName \
    --subnet $subnetName \
    --private-connection-resource-id $registryID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Hämta privata IP-adresser

Kör [az-nätverks-private-endpoint visa][az-network-private-endpoint-show] för att fråga slutpunkten för nätverksgränssnitts-ID:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Associerad med nätverksgränssnittet är två privata IP-adresser för behållarregistret: en för själva registret och en för registrets dataslutpunkt. Kör följande [az-resursvisningskommandon][az-resource-show] för att hämta privata IP-adresser för behållarregistret och registrets dataslutpunkt:

```azurecli
privateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

dataEndpointPrivateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

### <a name="create-dns-records-in-the-private-zone"></a>Skapa DNS-poster i den privata zonen

Följande kommandon skapar DNS-poster i den privata zonen för registerslutpunkten och dess dataslutpunkt. Om du till exempel har ett register med namnet *mitt register* i `myregistry.westeurope.data.azurecr.io`regionen *Westeurope* är slutpunktsnamnen `myregistry.azurecr.io` och . 

Först köra [az network private-dns record-set a create][az-network-private-dns-record-set-a-create] to create empty A record sets for the register endpoint and data endpoint:

```azurecli
az network private-dns record-set a create \
  --name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup
```

Kör [az-nätverket private-dns-post-ange ett tilläggspostkommando][az-network-private-dns-record-set-a-add-record] för att skapa A-posterna för registerslutpunkten och dataslutpunkten:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $privateIP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $dataEndpointPrivateIP
```

Den privata länken är nu konfigurerad och klar för användning.

## <a name="set-up-private-link---portal"></a>Konfigurera privat länk - portal

Följande steg förutsätter att du redan har ett virtuellt nätverk och undernät konfigurerat med en virtuell dator för testning. Du kan också [skapa ett nytt virtuellt nätverk och undernät](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt

1. Navigera till behållarregistret i portalen.
1. Under **Inställningar**väljer du **Privata slutpunktsanslutningar (förhandsgranskning)**.
1. Välj **+ Privat slutpunkt**.
1. Ange eller välj följande information på fliken **Grunderna:**

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Ange namnet på en befintlig grupp eller skapa en ny.|
    | **Information om instans** |  |
    | Namn | Ange ett unikt namn. |
    |Region|Välj en region.|
    |||
5. Välj **Nästa: Resurs**.
6. Ange eller välj följande information:

    | Inställning | Värde |
    | ------- | ----- |
    |Anslutningsmetod  | Välj **Anslut till en Azure-resurs i katalogen**.|
    | Prenumeration| Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft.ContainerRegistry/register**. |
    | Resurs |Välj namnet på registret|
    |Underkälla för mål |Välj **register**|
    |||
7. Välj **Nästa: Konfiguration**.
8. Ange eller välj information:

    | Inställning | Värde |
    | ------- | ----- |
    |**Nätverk**| |
    | Virtuellt nätverk| Välj det virtuella nätverket där den virtuella datorn distribueras, till exempel *myDockerVMVNET*. |
    | Undernät | Välj ett undernät, till exempel *myDockerVMSubnet* där den virtuella datorn distribueras. |
    |**Privat DNS-integrering**||
    |Integrera med privat DNS-zon |Välj **Ja**. |
    |Privat DNS-zon |Välj *(ny) privatelink.azurecr.io* |
    |||

1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure validerar din konfiguration. 
2. När meddelandet **Validering har skickats** väljer du **Skapa**.

När den privata slutpunkten har skapats visas DNS-inställningar i **Overview** den privata zonen på slutpunktens översiktssida.

![DNS-inställningar för slutpunkt](./media/container-registry-private-link/private-endpoint-overview.png)

Din privata länk är nu konfigurerad och klar för användning.

## <a name="validate-private-link-connection"></a>Validera privat länkanslutning

Du bör verifiera att resurserna i undernätet för den privata slutpunkten ansluter till registret via en privat IP-adress och har rätt integrering av privata DNS-zoner.

För att validera den privata länkanslutningen, SSH till den virtuella datorn som du konfigurerade i det virtuella nätverket.

Kör `nslookup` kommandot för att lösa IP-adressen för registret över den privata länken:

```bash
nslookup $registryName.azurecr.io
```

Exempelutdata visar registrets IP-adress i undernätets adressutrymme:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Jämför det här resultatet med `nslookup` den offentliga IP-adressen i utdata för samma register över en offentlig slutpunkt:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Registeråtgärder över privat länk

Kontrollera också att du kan utföra registeråtgärder från den virtuella datorn i undernätet. Gör en SSH-anslutning till din virtuella dator och kör [az acr-inloggning][az-acr-login] för att logga in på ditt register. Beroende på din vm-konfiguration kan du behöva prefixa följande kommandon med `sudo`.

```bash
az acr login --name $registryName
```

Utför registeråtgärder som `docker pull` att hämta en exempelavbildning från registret. Ersätt `hello-world:v1` med en avbildning och tagg som är lämplig för registret, som föregås av registrets inloggningsservernamn (alla gemener):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker drar avbildningen till den virtuella datorn.

## <a name="manage-private-endpoint-connections"></a>Hantera privata slutpunktsanslutningar

Hantera ett registers privata slutpunktsanslutningar med Azure-portalen eller genom att använda kommandon i kommandogruppen [az acr private-endpoint-connection.][az-acr-private-endpoint-connection] Åtgärderna omfattar godkänna, ta bort, lista, avvisa eller visa information om ett registers privata slutpunktsanslutningar.

Om du till exempel vill visa de privata slutpunktsanslutningarna för ett register kör du kommandot [az acr private-endpoint-connection list.][az-acr-private-endpoint-connection-list] Ett exempel:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

När du konfigurerar en privat slutpunktsanslutning med hjälp av stegen i den här artikeln accepterar registret automatiskt anslutningar från klienter och tjänster som har RBAC-behörigheter i registret. Du kan ställa in slutpunkten så att den kräver manuellt godkännande av anslutningar. Information om hur du godkänner och avvisar privata slutpunktsanslutningar finns i [Hantera en privat slutpunktsanslutning](../private-link/manage-private-endpoint.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat alla Azure-resurser i samma resursgrupp och inte längre behöver dem kan du också ta bort resurserna med hjälp av ett enda [az-gruppborttagningskommando:](/cli/azure/group)

```azurecli
az group delete --name $resourceGroup
```

Om du vill rensa dina resurser i portalen navigerar du till resursgruppen. När resursgruppen har lästs in klickar du på **Ta bort resursgruppen** för att ta bort resursgruppen och de resurser som lagras där.

## <a name="next-steps"></a>Nästa steg

* Mer information om Private Link finns i Azure [Private Link-dokumentationen.](../private-link/private-link-overview.md)
* Ett alternativ till privat länk är att ställa in regler för nätverksåtkomst för att begränsa registeråtkomsten. Mer information finns i [Begränsa åtkomsten till ett Azure-behållarregister med hjälp av en Azure-princip för virtuellt nätverk eller brandvägg](container-registry-vnet.md).

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms
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
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
