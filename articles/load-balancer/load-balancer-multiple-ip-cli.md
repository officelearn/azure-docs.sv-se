---
title: "Belastningsutjämning på flera IP-konfigurationer med hjälp av Azure CLI | Microsoft Docs"
description: "Lär dig hur du tilldelar flera IP-adresser till en virtuell dator med Azure CLI | Resource Manager."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: annahar
ms.openlocfilehash: 1d88c53784cec302f5e67b9d50f84780bbec37db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="load-balancing-on-multiple-ip-configurations"></a>Belastningsutjämning på flera IP-konfigurationer

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Den här artikeln beskriver hur du använder Azure-belastningsutjämnaren med flera IP-adresser på sekundärt nätverksgränssnitt (NIC). I det här scenariot har vi två virtuella datorer som kör Windows med en primär och sekundär NIC. Varje sekundär nätverkskort har två IP-konfigurationer. Varje virtuell värd för både webbplatser contoso.com och fabrikam.com. Varje webbplats är bunden till en IP-konfigurationer på sekundära nätverkskortet. Vi kan använda Azure belastningsutjämnare för att visa två klientdelens IP-adresser, en för varje webbplats för att distribuera trafik till respektive IP-konfiguration för webbplatsen. Det här scenariot använder samma portnummer på både frontends som båda backend poolen IP-adresser.

![LB scenariot bild](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Steg för att belastningsutjämna på flera IP-konfigurationer

För att uppnå det scenario som beskrivs i den här artikeln gör du följande:

1. [Installera och konfigurera Azure CLI](../cli-install-nodejs.md) genom att följa stegen i den länkade artikeln och loggar till ditt Azure-konto.
2. [Skapa en resursgrupp](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group) kallas *contosofabrikam* på följande sätt:

    ```azurecli
    azure group create contosofabrikam westcentralus
    ```

3. [Skapa en tillgänglighetsuppsättning](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) till för de två virtuella datorerna. I det här scenariot använder du följande kommando:

    ```azurecli
    azure availset create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Skapa ett virtuellt nätverk](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) kallas *myVNet* och ett undernät som kallas *mySubnet*:

    ```azurecli
    azure network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus

    azure network vnet subnet create --resource-group contosofabrikam --vnet-name myVnet --name mySubnet --address-prefix 10.0.0.0/24
    ```

5. [Skapa belastningsutjämnaren](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kallas *mylb*:

    ```azurecli
    azure network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Skapa två dynamiska offentliga IP-adresser för klientdelens IP-konfigurationer för din belastningsutjämnare:

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Skapa två klientdelens IP-konfigurationer, *contosofe* och *fabrikamfe* respektive:

    ```azurecli
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Skapa din serverdel för adresspool - *contosopool* och *fabrikampool*, [avsökningen](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - *HTTP*, och din belastningsutjämning regler - *HTTPc* och *HTTPf*:

    ```azurecli
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    azure network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Kontrollera utdata till [Kontrollera din belastningsutjämnare](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) skapades korrekt genom att köra följande kommando:

    ```azurecli
    azure network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Skapa en offentlig IP-adress](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address), *myPublicIp*, och [lagringskonto](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json), *mystorageaccont1* för din första virtuella VM1 på följande sätt:

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [Skapa nätverksgränssnitten](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) för VM1 och lägga till en andra IP-konfiguration, *VM1 ipconfig2*, och [skapa den virtuella datorn](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms) på följande sätt:

    ```azurecli
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. Upprepa steg 10-11 för den virtuella datorn med andra:

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Slutligen måste du konfigurera DNS-resursposter för att peka till respektive klientdelens IP-adressen för belastningsutjämnaren. Du kan vara värd för dina domäner i Azure DNS. Mer information om hur du använder Azure DNS med belastningsutjämnaren finns [med hjälp av Azure DNS med andra Azure-tjänster](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du kombinerar belastningsutjämning för tjänster i Azure i [med belastningsutjämning tjänster i Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Lär dig hur du kan använda olika typer av loggar i Azure för att hantera och felsöka belastningsutjämnare i [logga analytics för Azure belastningsutjämnare](../load-balancer/load-balancer-monitor-log.md).
