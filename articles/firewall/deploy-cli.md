---
title: Distribuera och konfigurera Azure-brandväggen med hjälp av Azure CLI
description: I den här artikeln får du lära dig hur du distribuerar och konfigurerar Azure-brandväggen med hjälp av Azure CLI.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 06/11/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: b40ac789fbc331e779e85462724e5c8a8e9bce47
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083358"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>Distribuera och konfigurera Azure-brandväggen med hjälp av Azure CLI

En viktig del av en övergripande säkerhetsplan för nätverket är att kontrollera utgående nätverksåtkomst. Du kan till exempel vill begränsa åtkomsten till webbplatser. Eller du kanske vill begränsa de utgående IP-adresser och portar som kan nås.

Med Azure Firewall kan du kontrollera åtkomsten till utgående nätverk från ett Azure-undernät. Med Azure Firewall kan du konfigurera:

* Programreglerna som definierar fullständigt kvalificerade domännamn (FQDN) kan nås från ett undernät.
* Nätverksregler som definierar källadress, protokoll, målport och måladress.

Nätverkstrafiken måste följa konfigurerade brandväggsregler när du vidarebefordrar den till brandväggen som standardgateway för undernätet.

I den här artikeln skapar du en förenklad enskilt virtuellt nätverk med tre undernät för enkel distribution. Vid Produktionsdistribution måste en [NAV och ekrar modellen](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) rekommenderas. Brandväggen är i ett eget virtuellt nätverk. Arbetsbelastningen-servrar finns i peerkopplade virtuella nätverk i samma region med en eller flera undernät.

* **AzureFirewallSubnet** – brandväggen ligger i det här undernätet.
* **Workload-SN** – arbetsbelastningsservern ligger i det här undernätet. Det här undernätets nätverkstrafik går genom brandväggen.
* **Jump-SN** – ”hopp”-servern ligger i det här undernätet. Hoppservern har en offentlig IP-adress som du kan ansluta till via Fjärrskrivbord. Därifrån kan du sedan ansluta till arbetsbelastningsservern (via ett annat Fjärrskrivbord).

![Självstudie om nätverksinfrastruktur](media/tutorial-firewall-rules-portal/Tutorial_network.png)

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * konfigurera en testnätverksmiljö
> * distribuera en brandvägg
> * Skapa en standardväg
> * Konfigurera en regel för programmet för att tillåta åtkomst till www.google.com
> * Konfigurera en nätverksregel för att tillåta åtkomst till externa DNS-servrar
> * Testa brandväggen

Om du vill kan du slutföra den här proceduren med den [Azure-portalen](tutorial-firewall-deploy-portal.md) eller [Azure PowerShell](deploy-ps.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="azure-cli"></a>Azure CLI

Om du väljer att installera och använda CLI lokalt kör du Azure CLI version 2.0.4 eller senare. Du kan ta reda på versionen genom att köra **az --version**. Information om att installera eller uppgradera finns i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="set-up-the-network"></a>Konfigurera nätverket

Skapa först en resursgrupp som ska innehålla de resurser som behövs till att distribuera brandväggen. Sedan skapa ett virtuellt nätverk, undernät och testservrar.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Resursgruppen innehåller alla resurser för distributionen.

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>Skapa ett virtuellt nätverk

Den här virtuella nätverket har tre undernät.

> [!NOTE]
> Den minsta storleken på AzureFirewallSubnet-undernätet är /26.

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name Workload-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.2.0/24
az network vnet subnet create \
  --name Jump-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.3.0/24
```

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa nu de virtuella hopp- och arbetsbelastningsdatorerna och placera dem i respektive undernät.
När du uppmanas ange ett lösenord för den virtuella datorn.

Skapa Srv-Jump-dator.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Jump \
    --location eastus \
    --image win2016datacenter \
    --vnet-name Test-FW-VN \
    --subnet Jump-SN \
    --admin-username azureadmin
az vm open-port --port 3389 --resource-group Test-FW-RG --name Srv-Jump
```



Skapa ett nätverkskort för Srv-fungerar med specifika IP-adresser för DNS-servrar och ingen offentlig IP-adress för att testa med.

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

Nu ska du skapa den virtuella datorn för arbetsbelastning.
När du uppmanas ange ett lösenord för den virtuella datorn.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Work \
    --location eastus \
    --image win2016datacenter \
    --nics Srv-Work-NIC \
    --admin-username azureadmin
```

## <a name="deploy-the-firewall"></a>Distribuera brandväggen

Distribuera nu brandväggen i det virtuella nätverket.

```azurecli-interactive
az network firewall create \
    --name Test-FW01 \
    --resource-group Test-FW-RG \
    --location eastus
az network public-ip create \
    --name fw-pip \
    --resource-group Test-FW-RG \
    --location eastus \
    --allocation-method static \
    --sku standard
az network firewall ip-config create \
    --firewall-name Test-FW01 \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group Test-FW-RG \
    --vnet-name Test-FW-VN
az network firewall update \
    --name Test-FW01 \
    --resource-group Test-FW-RG 
az network public-ip show \
    --name fw-pip \
    --resource-group Test-FW-RG
fwprivaddr="$(az network firewall ip-config list -g Test-FW-RG -f Test-FW01 --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

Skriv ned den privata IP-adressen. Du kommer att använda den senare när du skapar standardvägen.

## <a name="create-a-default-route"></a>Skapa en standardväg

Skapa en tabell med BGP-spridning inaktiverad

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

Skapa flödet.

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

Associera routningstabellen till undernätet

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>Konfigurera en programregel

Programmet regeln tillåter utgående åtkomst till www.google.com.

```azurecli-interactive
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name Test-FW01 \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group Test-FW-RG \
   --target-fqdns www.google.com \
   --source-addresses 10.0.2.0/24 \
   --priority 200 \
   --action Allow
```

Azure Firewall innehåller en inbyggd regelsamling för fullständiga domännamn för mål (FQDN) i infrastrukturen som tillåts som standard. Dessa FQDN är specifika för plattformen och kan inte användas för andra ändamål. Mer information finns i [Infrastruktur-FQDN](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurera en nätverksregel

Nätverk regeln tillåter utgående åtkomst till två IP-adresser på port 53 (DNS).

```azurecli-interactive
az network firewall network-rule create \
   --collection-name Net-Coll01 \
   --destination-addresses 209.244.0.3 209.244.0.4 \
   --destination-ports 53 \
   --firewall-name Test-FW01 \
   --name Allow-DNS \
   --protocols UDP \
   --resource-group Test-FW-RG \
   --priority 200 \
   --source-addresses 10.0.2.0/24 \
   --action Allow
```

## <a name="test-the-firewall"></a>Testa brandväggen

Testa nu brandväggen för att bekräfta att den fungerar som förväntat.

1. Observera den privata IP-adressen för den **Srv-arbete** virtuell dator:

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. Ansluta ett fjärrskrivbord till **Srv-Jump** virtuella datorn och logga in. Därifrån kan du öppna en fjärrskrivbordsanslutning till den **Srv-arbete** privata IP-adress och logga in.

3. På **SRV-arbete**, öppna ett PowerShell-fönster och kör följande kommandon:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Bägge kommandona ska returnera svar, som visar att DNS-frågor får genom brandväggen.

1. Kör följande kommandon:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Www.google.com-begäranden ska lyckas och www.microsoft.com-begäranden ska misslyckas. Detta demonstrerar att brandväggsreglerna fungerar som förväntat.

Nu har du kontrollera att brandväggsreglerna fungerar:

* Du kan omvandla DNS-namn med hjälp av den konfigurerade externa DNS-servern.
* Du kan bläddra till en tillåten FQDN, men inte till andra.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla resurserna i brandväggen för nästa självstudie, eller om du inte längre behövs kan du ta bort den **Test-VB-RG** resursgrupp för att ta bort alla brandväggen-relaterade resurser:

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>Nästa steg

* [Självstudie: Monitor Azure Firewall-loggar](./tutorial-diagnostics.md)
