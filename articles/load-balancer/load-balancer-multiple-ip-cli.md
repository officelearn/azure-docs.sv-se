---
title: Belastningsutjämning på flera IP-konfigurationer med hjälp av Azure CLI | Microsoft Docs
description: Lär dig hur du tilldelar flera IP-adresser till en virtuell dator med Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: kumud
ms.openlocfilehash: f9cd6405f5c3c87cdf004f8a71b9e72d58532a12
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37108936"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-azure-cli"></a>Belastningsutjämning på flera IP-konfigurationer med hjälp av Azure CLI

Den här artikeln beskriver hur du använder Azure-belastningsutjämnaren med flera IP-adresser på sekundärt nätverksgränssnitt (NIC). I det här scenariot har vi två virtuella datorer som kör Windows med en primär och sekundär NIC. Varje sekundär nätverkskort har två IP-konfigurationer. Varje virtuell värd för både webbplatser contoso.com och fabrikam.com. Varje webbplats är bunden till en IP-konfigurationer på sekundära nätverkskortet. Vi kan använda Azure belastningsutjämnare för att visa två klientdelens IP-adresser, en för varje webbplats för att distribuera trafik till respektive IP-konfiguration för webbplatsen. Det här scenariot använder samma portnummer på både frontends som båda backend poolen IP-adresser.

![LB scenariot bild](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Steg för att belastningsutjämna på flera IP-konfigurationer

För att uppnå det scenario som beskrivs i den här artikeln gör du följande:

1. [Installera och konfigurera Azure CLI]((https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)) genom att följa stegen i den länkade artikeln och loggar till ditt Azure-konto.
2. [Skapa en resursgrupp](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group) kallas *contosofabrikam* på följande sätt:

    ```azurecli
    az group create contosofabrikam westcentralus
    ```

3. [Skapa en tillgänglighetsuppsättning](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) till för de två virtuella datorerna. I det här scenariot använder du följande kommando:

    ```azurecli
    az vm availability-set create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Skapa ett virtuellt nätverk](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) kallas *myVNet* och ett undernät som kallas *mySubnet*:

    ```azurecli
    az network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus --subnet-name MySubnet --subnet-prefix 10.0.0.0/24

    ```

5. [Skapa belastningsutjämnaren](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kallas *mylb*:

    ```azurecli
    az network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Skapa två dynamiska offentliga IP-adresser för klientdelens IP-konfigurationer för din belastningsutjämnare:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Skapa två klientdelens IP-konfigurationer, *contosofe* och *fabrikamfe* respektive:

    ```azurecli
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Skapa din serverdel för adresspool - *contosopool* och *fabrikampool*, [avsökningen](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - *HTTP*, och din belastning NLB regler - *HTTPc* och *HTTPf*:

    ```azurecli
    az network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    az network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Kontrollera utdata till [Kontrollera din belastningsutjämnare](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) skapades korrekt genom att köra följande kommando:

    ```azurecli
    az network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Skapa en offentlig IP-adress](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address), *myPublicIp*, och [lagringskonto](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json), *mystorageaccont1* för din första virtuella VM1 på följande sätt:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [Skapa nätverksgränssnitten](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) för VM1 och lägga till en andra IP-konfiguration, *VM1 ipconfig2*, och [skapa den virtuella datorn](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-vm) på följande sätt:

    ```azurecli
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. Upprepa steg 10-11 för den virtuella datorn med andra:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Slutligen måste du konfigurera DNS-resursposter för att peka till respektive klientdelens IP-adressen för belastningsutjämnaren. Du kan vara värd för dina domäner i Azure DNS. Mer information om hur du använder Azure DNS med belastningsutjämnaren finns [med hjälp av Azure DNS med andra Azure-tjänster](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du kombinerar belastningsutjämning för tjänster i Azure i [med belastningsutjämning tjänster i Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Lär dig hur du kan använda olika typer av loggar i Azure för att hantera och felsöka belastningsutjämnare i [logga analytics för Azure belastningsutjämnare](../load-balancer/load-balancer-monitor-log.md).
