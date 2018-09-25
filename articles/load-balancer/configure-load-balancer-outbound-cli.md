---
title: Konfigurera load belastningsutjämning och utgående regler i Standardbelastningsutjämnare med Azure CLI | Microsoft Docs
description: Den här artikeln visar hur du konfigurerar load belastningsutjämning och utgående regler i en Standardbelastningsutjämnare med Azure CLI.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jpconnock
tags: azure-resource-manager
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: kumud
ms.openlocfilehash: dd549005776656bb755b78fc0ca73d24edcc77bd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973753"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Konfigurera belastningsutjämning och utgående regler i Standardbelastningsutjämnare med Azure CLI

Den här snabbstarten visar hur konfigurerar regler för utgående trafik i Standardbelastningsutjämnare med Azure CLI.  

När du är klar belastningsutjämnaren resursen innehåller två klientdelar och regler som är associerade med dem: en för inkommande och en annan för utgående.  Varje klientdel innehåller en referens till en offentlig IP-adress och det här scenariot använder en annan offentlig IP-adress för inkommande och utgående.   Belastningsutjämningsregeln ger endast belastningsutjämning inkommande och utgående regel styr utgående NAT för den virtuella datorn.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Om du väljer att installera och använda CLI lokalt så kräver den här självstudiekursen att du kör Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med [az group create](https://docs.microsoft.com/cli/azure/group#create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet *myresourcegroupoutbound* i den *usaöstra2* plats:

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Skapa det virtuella nätverket
Skapa ett virtuellt nätverk med namnet *myvnetoutbound* med ett undernät med namnet *mysubnetoutbound* i den *myresourcegroupoutbound* med [az network vnet Skapa](https://docs.microsoft.com/cli/azure/network/vnet#create).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Skapa inkommande offentliga IP-adress 

För att du ska kunna komma åt din webbapp på Internet behöver du en offentlig IP-adress för lastbalanseraren. En Standard Load Balancer stöder endast offentliga IP-standardadresser. Använd [az nätverket offentliga ip-skapa](https://docs.microsoft.com/cli/azure/network/public-ip#create) att skapa en offentlig IP-adress med namnet *mypublicipinbound* i *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Skapa utgående offentliga IP-adress 

Skapa en-IP-standardadress för utgående konfiguration för Belastningsutjämnarens klientdel [az nätverket offentliga ip-skapa](https://docs.microsoft.com/cli/azure/network/public-ip#create) med namnet *mypublicipoutbound* i *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```


## <a name="create-azure-load-balancer"></a>Skapa en Azure Load Balancer

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:
  - En frontend-IP som tar emot den inkommande nätverkstrafiken på belastningsutjämnaren.
  - En backend-adresspool där frontend-IP skickar den belastningsutjämnade nätverkstrafiken.
  - en hälsoavsökning som fastställer hälsan för serverdelens Virtuella datorinstanser.
  - En inkommande regel för belastningsutjämnaren som definierar hur trafiken ska distribueras till de virtuella datorerna.
  - En utgående regel för belastningsutjämnaren som definierar hur trafiken ska distribueras från de virtuella datorerna.

### <a name="create-load-balancer"></a>Skapa belastningsutjämnare

Skapa en belastningsutjämnare med den inkommande IP-adress med hjälp av [az network lb skapa](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) med namnet *lb* som innehåller en inkommande klientdelens IP-konfiguration med namnet *myfrontendinbound*, en serverdelspool med namnet *bepool* som är associerad med den offentliga IP-adressen *mypublicipinbound* som du skapade i föregående steg.

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepool \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-frontend-ip"></a>Skapa utgående klientdels-IP
Skapa utgående frontend IP-konfigurationen för belastningsutjämnaren med [az network lb frontend-IP-skapa](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) som innehåller och utgående klientdelens IP-konfiguration med namnet *myfrontendoutbound* dvs som är kopplad till den offentliga IP-adressen *mypublicipoutbound*

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Skapa hälsoavsökning

En hälsoavsökning kontrollerar alla virtuella datorinstanser för att säkerställa att de kan skicka nätverkstrafik. Den virtuella datorinstansen med misslyckad hälsoavsökning tas bort från lastbalanseraren tills den är tillbaka online och en avsökningskontroll visar att den är felfri. Skapa en hälsoavsökning med [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create) så att du kan övervaka de virtuella datorernas hälsotillstånd. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>Skapa regel för belastningsutjämning

En belastningsutjämningsregel definierar klientdelens IP-konfiguration för inkommande trafik och backend-poolen för att ta emot trafik samt nödvändig käll- och port. Skapa en regel för belastningsutjämnaren *myinboundlbrule* med [az network lb rule skapa](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create) för att lyssna på port 80 i klientdelspoolen *myfrontendinbound* och skicka belastningsutjämnad nätverkstrafik till serverdelsadresspoolen *bepool* också använder port 80. 

>[!NOTE]
>Den här belastningsutjämningsregel inaktiverar automatisk utgående (S) NAT till följd av den här regeln med parametern – inaktivera utgående snat. Utgående NAT tillhandahålls endast av regel för utgående trafik.

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
--backend-pool-name bepool \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>Skapa regel för utgående trafik

En utgående regel som definierar den frontend offentlig IP-adress som representeras av klientdelen *myfrontendoutbound*, som används för all utgående NAT-trafik samt serverdelspoolen som regeln gäller.  Skapa en utgående regel *myoutboundrule* för utgående nätverkstrafik översättning av alla virtuella datorer (NIC IP-konfigurationer) i *bepool* serverdelspool.  Kommandot nedan även ändras utgående tidsgränsen för inaktivitet från 4 till 15 minuter och allokerar 10000 SNAT portar i stället för 1024.  Granska [utgående regler](https://aka.ms/lboutboundrules) för mer information.

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepool
```
## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort resursgruppen, lastbalanseraren och alla relaterade resurser med kommandot [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Nästa steg
I den här artikeln skapade Standardbelastningsutjämnare, konfigurerat både inkommande trafik belastningsutjämningsregler, konfigurerats och hälsoavsökning för de virtuella datorerna i serverdelspoolen. Om du vill läsa mer om Azure Load Balancer fortsätter du till självstudierna för Azure Load Balancer.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

