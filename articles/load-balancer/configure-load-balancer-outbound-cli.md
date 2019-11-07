---
title: Konfigurera belastnings utjämning och utgående regler med hjälp av Azure CLI
titlesuffix: Azure Load Balancer
description: Den här artikeln visar hur du konfigurerar belastnings utjämning och utgående regler i en Standard Load Balancer med hjälp av Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: allensu
ms.openlocfilehash: 503c8f71b7e26cfe6803a6df1d3fec9ef55cd5c3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571141"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Konfigurera belastningsutjämning och regler för utgående trafik i Standard Load Balancer med hjälp av Azure CLI

Den här snabb starten visar hur du konfigurerar utgående regler i Standard Load Balancer med Azure CLI.  

När du är färdig innehåller Load Balancer resursen två klient delar och regler som är kopplade till dem: en för inkommande och en annan för utgående.  Varje klient del har en referens till en offentlig IP-adress och det här scenariot använder en annan offentlig IP-adress för inkommande och utgående.   Belastnings Utjämnings regeln tillhandahåller endast inkommande belastnings utjämning och regeln för utgående trafik styr den utgående NAT-filen som har angetts för den virtuella datorn.  I den här snabb starten används två separata backend-pooler, en för inkommande och en för utgående, för att illustrera kapacitet och tillåta flexibilitet för det här scenariot.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Om du väljer att installera och använda CLI lokalt så kräver den här självstudiekursen att du kör Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med [az group create](https://docs.microsoft.com/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resurs grupp med namnet *myresourcegroupoutbound* på *eastus2* -platsen:

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Skapa det virtuella nätverket
Skapa ett virtuellt nätverk med namnet *myvnetoutbound* med ett undernät med namnet *mysubnetoutbound* i *myresourcegroupoutbound* med [AZ Network VNet Create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound \
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Skapa offentlig IP-adress för inkommande trafik 

För att du ska kunna komma åt din webbapp på Internet behöver du en offentlig IP-adress för lastbalanseraren. En Standard Load Balancer stöder endast offentliga IP-standardadresser. Använd [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) för att skapa en offentlig standard-IP-adress med namnet *mypublicipinbound* i *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Skapa utgående offentlig IP-adress 

Skapa en standard-IP-adress för Load Balancer klient delen utgående konfiguration med [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Skapa Azure Load Balancer

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:
  - En IP-adress för klient delen som tar emot inkommande nätverks trafik i belastningsutjämnaren.
  - En backend-pool där klient delens IP-adress skickar den belastningsutjämnade nätverks trafiken.
  - En backend-pool för utgående anslutning. 
  - En hälso avsökning som avgör hälso tillståndet för VM-instanser i Server delen.
  - En regel för inkommande belastnings utjämning som definierar hur trafiken distribueras till de virtuella datorerna.
  - En regel för utgående belastningsutjämnare som definierar hur trafiken distribueras från de virtuella datorerna.

### <a name="create-load-balancer"></a>Skapa Load Balancer

Skapa en Load Balancer med den inkommande IP-adressen med [AZ Network lb Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) med namnet *lb* som innehåller en inkommande IP-konfiguration för klient delen och en *bepoolinbound* som är associerad med den offentliga IP-adressen  *mypublicipinbound* som du skapade i föregående steg.

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepoolinbound \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-pool"></a>Skapa utgående pool

Skapa ytterligare en backend-adresspool för att definiera utgående anslutning för en pool med virtuella datorer med [AZ Network lb Address-pool Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) med namnet *bepooloutbound*.  Att skapa en separat utgående pool ger maximal flexibilitet, men du kan utelämna det här steget och endast använda inkommande *bepoolinbound* .

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Skapa utgående IP-adress för klient del
Skapa den utgående IP-konfigurationen för klient delen för Load Balancer med [AZ Network lb frontend-IP Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) som inkluderar och utgående klient DELENS IP-konfiguration med namnet *myfrontendoutbound* som är kopplad till den offentliga IP-adressen  *mypublicipoutbound*

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Skapa hälso avsökning

En hälsoavsökning kontrollerar alla virtuella datorinstanser för att säkerställa att de kan skicka nätverkstrafik. Den virtuella datorinstansen med misslyckad hälsoavsökning tas bort från lastbalanseraren tills den är tillbaka online och en avsökningskontroll visar att den är felfri. Skapa en hälsoavsökning med [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) så att du kan övervaka de virtuella datorernas hälsotillstånd. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>Skapa belastnings Utjämnings regel

En belastnings Utjämnings regel definierar klient delens IP-konfiguration för den inkommande trafiken och backend-poolen för att ta emot trafiken, tillsammans med den begärda käll-och mål porten. Skapa en regel för belastnings utjämning *myinboundlbrule* med [AZ Network lb Rule Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) för att lyssna på port 80 i klient delens pool *myfrontendinbound* och skicka belastningsutjämnad nätverks trafik till backend-adresspoolen *även* använder port 80. 

>[!NOTE]
>Den här belastnings Utjämnings regeln inaktiverar automatisk utgående NAT som ett resultat av den här regeln med parametern--Disable-utgående-SNAT-SNAT. Utgående NAT anges bara av regeln för utgående trafik.

```azurecli-interactive
az network lb rule create \
--resource-group myresourcegroupoutbound \
--lb-name lb \
--name inboundlbrule \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--probe http \
--frontend-ip-name myfrontendinbound \
--backend-pool-name bepoolinbound \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>Skapa utgående regel

En utgående regel definierar den offentliga klient delens IP-adress, som representeras av klient delens *myfrontendoutbound*, som kommer att användas för all utgående NAT-trafik samt den backend-pool som regeln gäller.  Skapa en utgående regel *myoutboundrule* för utgående nätverks översättning av alla virtuella datorer (NIC IP-konfigurationer) i backend-poolen för *pooler* .  Kommandot nedan ändrar även tids gränsen för utgående inaktivitet från 4 till 15 minuter och tilldelar 10000 SNAT-portar i stället för 1024.  Granska [utgående regler](https://aka.ms/lboutboundrules) för mer information.

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepooloutbound
```

Om du inte vill använda en separat utgående pool kan du ändra argumentet för adresspoolen i föregående kommando för att ange *bepoolinbound* i stället.  Vi rekommenderar att du använder separata pooler för flexibilitet och läsbarhet för den resulterande konfigurationen.

Nu kan du fortsätta med att lägga till den virtuella datorn till backend-poolens *bepoolinbound* __och__ *bepooloutbound* genom att uppdatera IP-konfigurationen för respektive NIC-resurser med [AZ Network NIC IP-config Address-pool Add](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort resursgruppen, lastbalanseraren och alla relaterade resurser med kommandot [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Nästa steg
I den här artikeln skapade du Standard Load Balancer, konfigurerade både inkommande trafik regler för belastnings utjämning, konfigurerade och hälso avsökningar för de virtuella datorerna i backend-poolen. Om du vill läsa mer om Azure Load Balancer fortsätter du till självstudierna för Azure Load Balancer.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
