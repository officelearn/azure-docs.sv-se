---
title: Azure-brandväggen tvingade tunnel
description: Du kan konfigurera tvingande tunnel för att dirigera Internet-bunden trafik till en extra brandvägg eller virtuell nätverksinstallation för vidare bearbetning.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/24/2020
ms.author: victorh
ms.openlocfilehash: e51f6de370a5340082f64a0ca15c61583f75962b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597292"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Azure-brandväggen tvingade tunnel (förhandsversion)

Du kan konfigurera Azure-brandväggen för att dirigera all Internet-bunden trafik till ett angivet nästa hopp i stället för att gå direkt till Internet. Du kan till exempel ha en lokal kantbrandvägg eller annan virtuell nätverksinstallation (NVA) för att bearbeta nätverkstrafik innan den skickas till Internet.

> [!IMPORTANT]
> Azure Firewall forced tunneling är för närvarande i offentlig förhandsversion.
>
> Den allmänt tillgängliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Som standard är tvingad tunnelning inte tillåten i Azure-brandväggen för att säkerställa att alla dess utgående Azure-beroenden uppfylls. UDR-konfigurationer (User Defined Route) på *AzureFirewallSubnet* som har en standardväg som inte går direkt till Internet är inaktiverade.

## <a name="forced-tunneling-configuration"></a>Konfiguration av påtvingad tunnelning

För att stödja påtvingad tunneltrafik separeras servicehanteringstrafiken från kundtrafiken. Ytterligare ett dedikerat undernät med namnet *AzureFirewallManagementSubnet* (minsta undernätsstorlek /26) krävs med en egen associerad offentlig IP-adress. Den enda tillåtna vägen i det här undernätet är en standardväg till Internet och BGP-flödesspridning måste inaktiveras.

Om du har en standardväg som annonseras via BGP för att tvinga trafik till lokala, måste du skapa *AzureFirewallSubnet* och *AzureFirewallManagementSubnet* innan du distribuerar brandväggen och har en UDR med en standardväg till Internet och **virtual network gateway-dirigerarutspridning** inaktiverad.

I den här konfigurationen kan *AzureFirewallSubnet* nu inkludera vägar till valfri lokal brandvägg eller NVA för att bearbeta trafik innan den skickas till Internet. Du kan också publicera dessa vägar via BGP till *AzureFirewallSubnet* om **dirigerar för virtuellt nätverk** är aktiverat i det här undernätet.

Du kan till exempel skapa en standardväg på *AzureFirewallSubnet* med din VPN-gateway som nästa hopp för att komma till din lokala enhet. Du kan också aktivera **dirigerar för virtual network gateway-dirigerar för** att få lämpliga vägar till det lokala nätverket.

![Förökning av dirigerar den virtuella nätverksgatewayen](media/forced-tunneling/route-propagation.png)

När du har konfigurerat Azure-brandväggen för att stödja påtvingad tunnelning kan du inte ångra konfigurationen. Om du tar bort alla andra IP-konfigurationer på brandväggen tas även hanterings-IP-konfigurationen bort och brandväggen är kontrakterad. Den offentliga IP-adressen som tilldelats hanterings-IP-konfigurationen kan inte tas bort, men du kan tilldela en annan offentlig IP-adress.

## <a name="next-steps"></a>Nästa steg

- [Självstudiekurs: Distribuera och konfigurera Azure-brandväggen i ett hybridnätverk med Azure-portalen](tutorial-hybrid-portal.md)