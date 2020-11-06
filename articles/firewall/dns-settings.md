---
title: DNS-inställningar för Azure-brandväggen (för hands version)
description: Du kan konfigurera Azure-brandväggen med inställningar för DNS-server och DNS-proxy.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: efe608437f350a29147cf10989cdb6c17a23196d
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398002"
---
# <a name="azure-firewall-dns-settings-preview"></a>DNS-inställningar för Azure-brandväggen (för hands version)

> [!IMPORTANT]
> Azure Firewall DNS-inställningar finns för närvarande i offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan konfigurera en anpassad DNS-server och aktivera DNS-proxy för Azure-brandvägg. Du kan konfigurera de här inställningarna när du distribuerar brand väggen eller senare från sidan **DNS-inställningar** .

## <a name="dns-servers"></a>DNS-servrar

En DNS-Server upprätthåller och löser domän namn till IP-adresser. Som standard använder Azure-brandväggen Azure DNS för namn matchning. Med **DNS-serverns** inställning kan du konfigurera dina egna DNS-servrar för namn matchning av Azure-brandvägg. Du kan konfigurera en enda eller flera servrar.

> [!NOTE]
> För Azure-brandväggar som hanteras med hjälp av Azure Firewall Manager konfigureras DNS-inställningarna i den associerade Azure Firewall-principen.

### <a name="configure-custom-dns-servers-preview---azure-portal"></a>Konfigurera anpassade DNS-servrar (förhands granskning) – Azure Portal

1. Under **Inställningar** för Azure-brandvägg väljer du **DNS-inställningar**.
2. Under **DNS-servrar** kan du skriva eller lägga till befintliga DNS-servrar som tidigare har angetts i Virtual Network.
3. Välj **Spara**.
4. Brand väggen dirigerar nu DNS-trafik till de angivna DNS-servrarna för namn matchning.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="DNS-servrar":::

### <a name="configure-custom-dns-servers-preview---azure-cli"></a>Konfigurera anpassade DNS-servrar (förhands granskning) – Azure CLI

I följande exempel uppdateras Azure-brandväggen med anpassade DNS-servrar med hjälp av Azure CLI.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> Kommandot `az network firewall` kräver att Azure CLI-tillägget `azure-firewall` är installerat. Den kan installeras med hjälp av kommandot `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers-preview---azure-powershell"></a>Konfigurera anpassade DNS-servrar (förhands granskning) – Azure PowerShell

I följande exempel uppdateras Azure-brandväggen med anpassade DNS-servrar med hjälp av Azure PowerShell.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy-preview"></a>DNS-proxy (för hands version)

Du kan konfigurera Azure-brandväggen så att den fungerar som en DNS-proxy. En DNS-proxy fungerar som en mellanhand för DNS-begäranden från klientens virtuella datorer till en DNS-server. Om du konfigurerar en anpassad DNS-server bör du aktivera DNS-proxy för att undvika matchning av DNS-matchning och aktivera FQDN-filtrering i nätverks regler.

Om du inte aktiverar DNS-proxy kan DNS-begäranden från klienten skickas till en DNS-Server vid en annan tidpunkt eller returnera ett annat svar jämfört med den i brand väggen. DNS-proxy placerar Azure-brandväggen i sökvägen för klient begär Anden för att undvika inkonsekvens.

Konfiguration av DNS-proxy kräver tre steg:
1. Aktivera DNS-proxy i Azure Firewall DNS-inställningar.
2. Om du vill kan du konfigurera din anpassade DNS-server eller använda det angivna standardvärdet.
3. Slutligen måste du konfigurera Azure firewalls privata IP-adress som en anpassad DNS-adress i DNS-serverinställningar för det virtuella nätverket. Detta säkerställer att DNS-trafik dirigeras till Azure-brandväggen.

### <a name="configure-dns-proxy-preview---azure-portal"></a>Konfigurera DNS-proxy (för hands version) – Azure Portal

Om du vill konfigurera DNS-proxy måste du konfigurera inställningen för DNS-servrar för virtuellt nätverk så att den använder brand väggens privata IP-adress. Aktivera sedan DNS-proxy i Azure Firewall **DNS-inställningar**.

#### <a name="configure-virtual-network-dns-servers"></a>Konfigurera virtuella nätverks-DNS-servrar 

1. Välj det virtuella nätverk där DNS-trafiken ska dirigeras via Azure-brandväggen.
2. Under **Inställningar** väljer du **DNS-servrar**.
3. Välj **anpassad** under **DNS-servrar**.
4. Ange brand väggens privata IP-adress.
5. Välj **Spara**.
6. Starta om de virtuella datorerna som är anslutna till det virtuella nätverket så tilldelas de nya DNS-serverinställningarna. De virtuella datorerna fortsätter att använda sina aktuella DNS-inställningar tills de startas om.

#### <a name="enable-dns-proxy-preview"></a>Aktivera DNS-proxy (för hands version)

1. Välj din Azure-brandvägg.
2. Under **Inställningar** väljer du **DNS-inställningar**.
3. Som standard är **DNS-proxy** inaktive rad. När den är aktive rad lyssnar brand väggen på port 53 och vidarebefordrar DNS-begäranden till de konfigurerade DNS-servrarna.
4. Granska konfigurationen av **DNS-servrarna** för att se till att inställningarna är lämpliga för din miljö.
5. Välj **Spara**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="DNS-proxy":::

### <a name="configure-dns-proxy-preview---azure-cli"></a>Konfigurera DNS-proxy (för hands version) – Azure CLI

Konfigurera DNS-proxyinställningar i Azure Firewall och uppdatera virtuella nätverk för att använda Azure-brandväggen som DNS-server kan utföras med Azure CLI.

#### <a name="configure-virtual-network-dns-servers"></a>Konfigurera virtuella nätverks-DNS-servrar

I det här exemplet konfigureras det virtuella nätverket så att det använder Azure-brandväggen som DNS-server.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

#### <a name="enable-dns-proxy-preview"></a>Aktivera DNS-proxy (för hands version)

I det här exemplet aktive ras funktionen DNS-proxy i Azure-brandväggen.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

### <a name="configure-dns-proxy-preview---azure-powershell"></a>Konfigurera DNS-proxy (för hands version) – Azure PowerShell

Konfigurera DNS-proxyinställningar och uppdatera virtuella nätverk för att använda Azure-brandväggen som DNS-server kan utföras med hjälp av Azure PowerShell.

#### <a name="configure-virtual-network-dns-servers"></a>Konfigurera virtuella nätverks-DNS-servrar

 I det här exemplet konfigureras det virtuella nätverket så att det använder Azure-brandväggen som DNS-server.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

#### <a name="enable-dns-proxy-preview"></a>Aktivera DNS-proxy (för hands version)

I det här exemplet aktive ras funktionen DNS-proxy i Azure-brandväggen.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Nästa steg

[FQDN-filtrering i nätverks regler](fqdn-filtering-network-rules.md)
