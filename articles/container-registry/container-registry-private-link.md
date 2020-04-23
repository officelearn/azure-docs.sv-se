---
title: Konfigurera privat länk
description: Konfigurera en privat slut punkt i ett behållar register och aktivera en privat länk i ett lokalt virtuellt nätverk
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: de8228d84497e71f24dba3dd4e6162cb6735a8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498910"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Konfigurera en privat Azure-länk för ett Azure Container Registry 

Du kan konfigurera en [privat slut punkt](../private-link/private-endpoint-overview.md) för ditt Azure Container Registry så att klienter på ett virtuellt Azure-nätverk säkert kan komma åt registret via en [privat länk](../private-link/private-link-overview.md). Den privata slut punkten använder en IP-adress från det virtuella nätverkets adress utrymme för registret. Nätverks trafik mellan klienterna i det virtuella nätverket och registret passerar det virtuella nätverket och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet.

Du kan [Konfigurera DNS-inställningar](../private-link/private-endpoint-overview.md#dns-configuration) för din privata slut punkt, så att inställningarna matchas mot registrets allokerade privata IP-adress. Med DNS-konfiguration kan klienter och tjänster i nätverket fortsätta att komma åt registret i registrets fullständigt kvalificerade domän namn, till exempel *myregistry.azurecr.io*.

Den här funktionen är tillgänglig i tjänst nivån **Premium** container Registry. Information om nivåer och gränser för register tjänster finns i [Azure Container Registry SKU: er](container-registry-skus.md).

> [!IMPORTANT]
> Den här funktionen är för närvarande en för hands version och vissa [begränsningar](#preview-limitations) gäller. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* För närvarande kan du inte konfigurera en privat länk med en privat slut punkt i ett [geo-replikerat register](container-registry-geo-replication.md). 

## <a name="prerequisites"></a>Krav

* För att kunna använda Azure CLI-stegen i den här artikeln rekommenderas Azure CLI version 2.2.0 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli]. Eller kör i [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Om du inte redan har ett behållar register kan du skapa en (Premium-nivå krävs) och skicka en `hello-world` exempel avbildning, till exempel från Docker Hub. Använd till exempel [Azure Portal][quickstart-portal] eller [Azure CLI][quickstart-cli] för att skapa ett register.
* Om du vill konfigurera register åtkomst med en privat länk i en annan Azure-prenumeration måste du registrera resurs leverantören för Azure Container Registry i den prenumerationen. Ett exempel:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Azure CLI-exemplen i den här artikeln använder följande miljövariabler. Ersätt värden som passar din miljö. Alla exempel är formaterade för bash-gränssnittet:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Skapa en Docker-aktiverad virtuell dator

I test syfte använder du en Docker-aktiverad virtuell Ubuntu-dator för att få åtkomst till ett Azure Container Registry. Om du vill använda Azure Active Directory autentisering i registret installerar du även [Azure CLI][azure-cli] på den virtuella datorn. Hoppa över det här steget om du redan har en virtuell Azure-dator.

Du kan använda samma resurs grupp för din virtuella dator och behållar registret. Den här installationen fören klar rensningen, men är inte obligatorisk. Om du väljer att skapa en separat resurs grupp för den virtuella datorn och det virtuella nätverket kör du [AZ Group Create][az-group-create]:

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

Distribuera nu en standard virtuell Azure-Ubuntu med [AZ VM Create][az-vm-create]. I följande exempel skapas en virtuell dator med namnet *myDockerVM*.

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När kommandot har slutförts noterar du det `publicIpAddress` som visas av Azure CLI. Använd den här adressen för att göra SSH-anslutningar till den virtuella datorn.

### <a name="install-docker-on-the-vm"></a>Installera Docker på den virtuella datorn

När den virtuella datorn har körts skapar du en SSH-anslutning till den virtuella datorn. Ersätt *publicIpAddress* med den offentliga IP-adressen för den virtuella datorn.

```bash
ssh azureuser@publicIpAddress
```

Kör följande kommandon för att installera Docker på den virtuella datorn Ubuntu:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Efter installationen kör du följande kommando för att kontrol lera att Docker körs korrekt på den virtuella datorn:

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

Följ stegen i [Installera Azure CLI med apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) för att installera Azure CLI på den virtuella Ubuntu-datorn. Ett exempel:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Avsluta SSH-anslutningen.

## <a name="set-up-private-link---cli"></a>Konfigurera privat länk – CLI

### <a name="get-network-and-subnet-names"></a>Hämta nätverks-och under nät namn

Om du inte redan har dem behöver du namnen på ett virtuellt nätverk och undernät för att skapa en privat länk. I det här exemplet använder du samma undernät för den virtuella datorn och registrets privata slut punkt. I många scenarier skulle du dock konfigurera slut punkten i ett separat undernät. 

När du skapar en virtuell dator skapar Azure som standard ett virtuellt nätverk i samma resurs grupp. Namnet på det virtuella nätverket baseras på namnet på den virtuella datorn. Om du till exempel namnger din virtuella dator *myDockerVM*är det virtuella standard nätverks namnet *myDockerVMVNET*, med ett undernät som heter *myDockerVMSubnet*. Ange dessa värden i miljövariabler genom att köra kommandot [AZ Network VNet List][az-network-vnet-list] :

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

### <a name="disable-network-policies-in-subnet"></a>Inaktivera nätverks principer i undernät

[Inaktivera nätverks principer](../private-link/disable-private-endpoint-network-policy.md) som nätverks säkerhets grupper i under nätet för den privata slut punkten. Uppdatera din under näts konfiguration med [AZ Network VNet Subnet Update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Konfigurera den privata DNS-zonen

Skapa en privat DNS-zon för priviate Azure Container Registry-domänen. I senare steg skapar du DNS-poster för din register domän i den här DNS-zonen.

Om du vill använda en privat zon för att åsidosätta standard-DNS-matchningen för Azure Container Registry måste zonen ha namnet **privatelink.azurecr.io**. Kör följande [AZ-nätverk privat-DNS Zone-][az-network-private-dns-zone-create] kommando för att skapa den privata zonen:

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Skapa en kopplings länk

Kör [AZ Network Private-DNS Link VNet Create][az-network-private-dns-link-vnet-create] för att associera din privata zon med det virtuella nätverket. I det här exemplet skapas en länk som heter *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Skapa en privat slut punkt för registret

I det här avsnittet skapar du registrets privata slut punkt i det virtuella nätverket. Börja med att hämta resurs-ID för registret:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Kör kommandot [AZ Network Private-Endpoint Create][az-network-private-endpoint-create] för att skapa registrets privata slut punkt.

I följande exempel skapas *myPrivateEndpoint* för slut punkt och tjänst *anslutning.* Om du vill ange en container Registry-resurs för slut `--group-ids registry`punkten, pass:

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

Kör [AZ Network Private-Endpoint show][az-network-private-endpoint-show] för att fråga slut punkten för nätverks gränssnittets ID:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Associerad med nätverks gränssnittet är två privata IP-adresser för behållar registret: ett för själva registret och ett för registrets data slut punkt. Kör följande [AZ-resurs Visa][az-resource-show] kommandon för att hämta de privata IP-adresserna för behållar registret och registrets data slut punkt:

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

Följande kommandon skapar DNS-poster i den privata zonen för register slut punkten och dess data slut punkt. Om du till exempel har ett register med namnet " *register* " i *westeurope* -regionen är `myregistry.azurecr.io` slut punkts namnen `myregistry.westeurope.data.azurecr.io`och. 

Börja med att köra [AZ Network Private – DNS Record-set a Create][az-network-private-dns-record-set-a-create] för att skapa tomma a post uppsättningar för register slut punkten och data slut punkten:

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

Kör kommandot [AZ Network Private-DNS Record-set a Add-Record][az-network-private-dns-record-set-a-add-record] för att skapa en post för register slut punkten och data slut punkten:

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

Den privata länken har nu kon figurer ATS och är redo att användas.

## <a name="set-up-private-link---portal"></a>Konfigurera privat länk – Portal

Följande steg förutsätter att du redan har ett virtuellt nätverk och undernät konfigurerat med en virtuell dator för testning. Du kan också [skapa ett nytt virtuellt nätverk och undernät](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt

1. I portalen navigerar du till behållar registret.
1. Under **Inställningar**väljer du **privata slut punkter anslutningar (för hands version)**.
1. Välj **+ privat slut punkt**.
1. På fliken **grundläggande** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projekt information** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Ange namnet på en befintlig grupp eller skapa en ny.|
    | **Instans information** |  |
    | Namn | Ange ett unikt namn. |
    |Region|Välj en region.|
    |||
5. Välj **Nästa: resurs**.
6. Ange eller Välj följande information:

    | Inställning | Värde |
    | ------- | ----- |
    |Anslutningsmetod  | Välj **Anslut till en Azure-resurs i min katalog**.|
    | Prenumeration| Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft. ContainerRegistry/register**. |
    | Resurs |Välj namnet på ditt register|
    |Mål under resurs |Välj **register**|
    |||
7. Välj **Nästa: konfiguration**.
8. Ange eller Välj informationen:

    | Inställning | Värde |
    | ------- | ----- |
    |**Nätverk**| |
    | Virtuellt nätverk| Välj det virtuella nätverk där den virtuella datorn distribueras, till exempel *myDockerVMVNET*. |
    | Undernät | Välj ett undernät, till exempel *myDockerVMSubnet* där den virtuella datorn har distribuerats. |
    |**Privat DNS-integrering**||
    |Integrera med privat DNS-zon |Välj **Ja**. |
    |Privat DNS zon |Välj *(ny) privatelink.azurecr.io* |
    |||

1. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen. 
2. När du ser meddelandet **valideringen har skickats** väljer du **skapa**.

När den privata slut punkten har skapats visas DNS-inställningarna i den privata zonen på **översikts** sidan för slut punkten.

![DNS-inställningar för slut punkt](./media/container-registry-private-link/private-endpoint-overview.png)

Din privata länk har nu kon figurer ATS och är redo att användas.

## <a name="validate-private-link-connection"></a>Verifiera anslutning till privat länk

Du bör kontrol lera att resurserna i under nätet för den privata slut punkten är anslutna till registret via en privat IP-adress och att du har rätt integrering av privata DNS-zoner.

För att verifiera anslutningen till den privata länken, kan SSH till den virtuella dator som du konfigurerar i det virtuella nätverket.

Kör `nslookup` kommandot för att matcha IP-adressen för registret via den privata länken:

```bash
nslookup $registryName.azurecr.io
```

Exempel på utdata visar registerets IP-adress i under nätets adress utrymme:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Jämför det här resultatet med den offentliga IP- `nslookup` adressen i utdata för samma register över en offentlig slut punkt:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Register åtgärder via privat länk

Kontrol lera också att du kan utföra register åtgärder från den virtuella datorn i under nätet. Skapa en SSH-anslutning till den virtuella datorn och kör [AZ ACR login][az-acr-login] för att logga in i registret. Beroende på konfigurationen av din virtuella dator kan du behöva använda prefixet för följande kommandon `sudo`med.

```bash
az acr login --name $registryName
```

Utföra register åtgärder, till `docker pull` exempel för att hämta en exempel avbildning från registret. Ersätt `hello-world:v1` med en avbildning och tagga lämplig för registret, med prefixet till inloggnings Server namnet för registret (alla gemener):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker hämtar avbildningen till den virtuella datorn.

## <a name="manage-private-endpoint-connections"></a>Hantera anslutningar för privata slut punkter

Hantera ett registers privata slut punkts anslutningar med hjälp av Azure Portal eller med hjälp av kommandon i kommando gruppen [AZ ACR Private-Endpoint-Connection][az-acr-private-endpoint-connection] . Åtgärderna inkluderar Godkänn, ta bort, Visa, avvisa eller Visa information om ett registers privata slut punkts anslutningar.

Om du till exempel vill visa en lista över anslutningar för privata slut punkter för ett register kör du kommandot [AZ ACR Private-Endpoint-Connection List][az-acr-private-endpoint-connection-list] . Ett exempel:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

När du konfigurerar en privat slut punkts anslutning med hjälp av stegen i den här artikeln, accepterar registret automatiskt anslutningar från klienter och tjänster som har RBAC-behörighet för registret. Du kan ställa in slut punkten för att kräva manuellt godkännande av anslutningar. Information om hur du godkänner och avvisar anslutningar för privata slut punkter finns i [hantera en privat slut punkts anslutning](../private-link/manage-private-endpoint.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat alla Azure-resurser i samma resurs grupp och inte längre behöver dem, kan du välja att ta bort resurserna med hjälp av ett enda [AZ Group Delete](/cli/azure/group) -kommando:

```azurecli
az group delete --name $resourceGroup
```

Om du vill rensa dina resurser i portalen navigerar du till din resurs grupp. När resurs gruppen har lästs in klickar du på **ta bort resurs** grupp för att ta bort resurs gruppen och resurserna som lagras där.

## <a name="next-steps"></a>Nästa steg

* Mer information om privat länk finns i dokumentationen till [Azures privata länkar](../private-link/private-link-overview.md) .
* Ett alternativ till en privat länk är att konfigurera nätverks åtkomst regler för att begränsa åtkomsten till registret. Mer information finns i [begränsa åtkomsten till ett Azure Container Registry med hjälp av ett virtuellt Azure-nätverk eller brand Väggs regler](container-registry-vnet.md).

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
