---
title: Tvingad tunnel trafik i Azure-brandväggen
description: Du kan konfigurera Tvingad tunnel trafik för att dirigera Internet-baserad trafik till ytterligare en brand vägg eller virtuell nätverks installation för vidare bearbetning.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/18/2020
ms.author: victorh
ms.openlocfilehash: cf0af93d95c5af56be6168bc8e4f79d3005e2ec2
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649594"
---
# <a name="azure-firewall-forced-tunneling"></a>Tvingad tunnel trafik i Azure-brandväggen

När du konfigurerar en ny Azure-brandvägg kan du dirigera all Internet-baserad trafik till en utsedd nästa hopp i stället för att gå direkt till Internet. Du kan till exempel ha en lokal gräns brand vägg eller en annan virtuell nätverks installation (NVA) för att bearbeta nätverks trafik innan den skickas till Internet. Du kan dock inte konfigurera en befintlig brand vägg för Tvingad tunnel trafik.

Som standard är Tvingad tunnel trafik inte tillåten i Azure-brandväggen för att se till att alla dess utgående Azure-beroenden är uppfyllda. UDR-konfigurationer (User Defined Route) på *AzureFirewallSubnet* som har en standard väg som inte kommer direkt till Internet är inaktiverade.

## <a name="forced-tunneling-configuration"></a>Konfiguration av Tvingad tunnel trafik

För att stödja Tvingad tunnel trafik separeras Service Management-trafiken från kund trafiken. Ytterligare ett dedikerat undernät med namnet *AzureFirewallManagementSubnet* (minsta under näts storlek/26) krävs med en egen ASSOCIERAD offentlig IP-adress. Den enda vägen som tillåts i det här under nätet är en standard väg till Internet och en vidarebefordring av BGP-vägar måste inaktive ras.

Om du har en standard väg som annonseras via BGP för att tvinga trafik till lokalt, måste du skapa *AzureFirewallSubnet* och *AzureFirewallManagementSubnet* innan du distribuerar brand väggen och har en UDR med en standard väg till Internet, och den **virtuella nätverks-gatewayens väg spridning** inaktive rad.

I den här konfigurationen kan *AzureFirewallSubnet* nu inkludera vägar till valfri lokal brand vägg eller NVA för att bearbeta trafik innan den skickas till Internet. Du kan också publicera dessa vägar via BGP till *AzureFirewallSubnet* om **spridning av virtuella nätverks-Gateway** -vägar är aktiverat på det här under nätet.

Du kan till exempel skapa en standard väg på *AzureFirewallSubnet* med din VPN-gateway som nästa hopp för att komma till din lokala enhet. Eller så kan du aktivera **spridning av väg för virtuell nätverks-Gateway** för att hämta lämpliga vägar till det lokala nätverket.

![Väg spridning för virtuell nätverks-Gateway](media/forced-tunneling/route-propagation.png)

När du har konfigurerat Azure Firewall som stöd för Tvingad tunnel trafik kan du inte återställa konfigurationen. Om du tar bort alla andra IP-konfigurationer i brand väggen tas även hanterings-IP-konfigurationen bort och brand väggen frigörs. Det går inte att ta bort den offentliga IP-adress som tilldelats hanterings-IP-konfigurationen, men du kan tilldela en annan offentlig IP-adress.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Distribuera och konfigurera Azure-brandväggen i ett hybrid nätverk med hjälp av Azure Portal](tutorial-hybrid-portal.md)
