---
title: DNS-inställningar för Azure-brandväggen (för hands version)
description: Du kan konfigurera Azure-brandväggen med inställningar för DNS-server och DNS-proxy.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 09ffac4f19d50d9a386110e1b89f8f147652a2cd
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132014"
---
# <a name="azure-firewall-dns-settings-preview"></a>DNS-inställningar för Azure-brandväggen (för hands version)

> [!IMPORTANT]
> Azure Firewall DNS-inställningar finns för närvarande i offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan konfigurera en anpassad DNS-server och aktivera DNS-proxy för Azure-brandvägg. Du kan konfigurera de här inställningarna när du distribuerar brand väggen eller senare från sidan **DNS-inställningar** .

## <a name="dns-servers"></a>DNS-servrar

En DNS-Server upprätthåller och löser domän namn till IP-adresser. Som standard använder Azure-brandväggen Azure DNS för namn matchning. Med **DNS-serverns** inställning kan du konfigurera dina egna DNS-servrar för namn matchning av Azure-brandvägg. Du kan konfigurera en enda eller flera servrar.

### <a name="configure-custom-dns-servers-preview"></a>Konfigurera anpassade DNS-servrar (förhands granskning)

1. Under **Inställningar**för Azure-brandvägg väljer du **DNS-inställningar**.
2. Under **DNS-servrar**kan du skriva eller lägga till befintliga DNS-servrar som tidigare har angetts i Virtual Network.
3. Välj **Spara**.
4. Brand väggen dirigerar nu DNS-trafik till de angivna DNS-servrarna för namn matchning.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="DNS-servrar":::

## <a name="dns-proxy-preview"></a>DNS-proxy (för hands version)

Du kan konfigurera Azure-brandväggen så att den fungerar som en DNS-proxy. En DNS-proxy fungerar som en mellanhand för DNS-begäranden från klientens virtuella datorer till en DNS-server. Om du konfigurerar en anpassad DNS-server bör du aktivera DNS-proxy för att undvika matchning av DNS-matchning och aktivera FQDN-filtrering i nätverks regler.

Om du inte aktiverar DNS-proxy kan DNS-begäranden från klienten skickas till en DNS-Server vid en annan tidpunkt eller returnera ett annat svar jämfört med den i brand väggen. DNS-proxy placerar Azure-brandväggen i sökvägen för klient begär Anden för att undvika inkonsekvens.

Konfiguration av DNS-proxy kräver tre steg:
1. Aktivera DNS-proxy i Azure Firewall DNS-inställningar.
2. Om du vill kan du konfigurera din anpassade DNS-server eller använda det angivna standardvärdet.
3. Slutligen måste du konfigurera Azure firewalls privata IP-adress som en anpassad DNS-adress i DNS-serverinställningar för det virtuella nätverket. Detta säkerställer att DNS-trafik dirigeras till Azure-brandväggen.

### <a name="configure-dns-proxy-preview"></a>Konfigurera DNS-proxy (förhands granskning)

Om du vill konfigurera DNS-proxy måste du konfigurera inställningen för DNS-servrar för virtuellt nätverk så att den använder brand väggens privata IP-adress. Aktivera DNS-proxy i Azure brand Väggs princip **DNS-inställningar**.

#### <a name="configure-virtual-network-dns-servers"></a>Konfigurera virtuella nätverks-DNS-servrar

1. Välj det virtuella nätverk där DNS-trafiken ska dirigeras via Azure-brandväggen.
2. Under **Inställningar**väljer du **DNS-servrar**.
3. Välj **anpassad** under **DNS-servrar**.
4. Ange brand väggens privata IP-adress.
5. Välj **Spara**.
6. Starta om de virtuella datorerna som är anslutna till det virtuella nätverket så tilldelas de nya DNS-serverinställningarna. De virtuella datorerna fortsätter att använda sina aktuella DNS-inställningar tills de startas om.

#### <a name="enable-dns-proxy-preview"></a>Aktivera DNS-proxy (för hands version)

1. Välj din Azure-brandvägg.
2. Under **Inställningar**väljer du **DNS-inställningar**.
3. Som standard är **DNS-proxy** inaktive rad. När den är aktive rad lyssnar brand väggen på port 53 och vidarebefordrar DNS-begäranden till de konfigurerade DNS-servrarna.
4. Granska konfigurationen av **DNS-servrarna** för att se till att inställningarna är lämpliga för din miljö.
5. Välj **Spara**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="DNS-servrar":::

## <a name="next-steps"></a>Nästa steg

[FQDN-filtrering i nätverks regler](fqdn-filtering-network-rules.md)
