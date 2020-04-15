---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: 7f2b011b2de5af0e4ace9cbeb4399911d8e83b7f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312830"
---
Med hjälp av regional VNet-integrering kan appen komma åt:

* Resurser i ett virtuella nätverk i samma region som din app.
* Resurser i virtuella nätverk som peeras till det virtuella nätverk som appen är integrerad med.
* Tjänstslutpunkt säkrade tjänster.
* Resurser över Azure ExpressRoute-anslutningar.
* Resurser i det virtuella nätverk som du är integrerad med.
* Resurser över peer-anslutningar, som innehåller Azure ExpressRoute-anslutningar.
* Privata slutpunkter - Obs: DNS måste hanteras separat i stället för att använda privata Azure DNS-zoner.

När du använder VNet-integrering med virtuella nätverk i samma region kan du använda följande Azure-nätverksfunktioner:

* **Nätverkssäkerhetsgrupper (NSG)**: Du kan blockera utgående trafik med en NSG som är placerad på ditt integrationsundernät. De inkommande reglerna gäller inte eftersom du inte kan använda VNet-integrering för att ge inkommande åtkomst till din app.
* **Flödestabeller (UDRs):** Du kan placera en flödestabell i integrationsundernätet för att skicka utgående trafik där du vill.

Som standard dirigerar appen endast RFC1918-trafik till ditt virtuella nätverk. Om du vill dirigera all utgående trafik till ditt virtuella nätverk använder du appinställningen WEBSITE_VNET_ROUTE_ALL på din app. Så här konfigurerar du appinställningen:

1. Gå till **konfigurationsgränssnittet** i appportalen. Välj **Ny programinställning**.
1. Ange **WEBSITE_VNET_ROUTE_ALL** i rutan **Namn** och ange **1** i rutan **Värde.**

   ![Ange programinställning][4]

1. Välj **OK**.
1. Välj **Spara**.

Om du dirigerar all utgående trafik till ditt virtuella nätverk omfattas det av de NSGs och UDR som tillämpas på ditt integrationsundernät. När du dirigerar all utgående trafik till ditt virtuella nätverk är dina utgående adresser fortfarande de utgående adresser som visas i appegenskaperna om du inte tillhandahåller rutter för att skicka trafiken någon annanstans.

Det finns vissa begränsningar med att använda VNet-integrering med virtuella nätverk i samma region:

* Du kan inte nå resurser över globala peering-anslutningar.
* Funktionen är endast tillgänglig från nyare Azure App Service-skalningsenheter som stöder PremiumV2 App Service-abonnemang.
* Integrationsundernätet kan endast användas av en App Service-plan.
* Funktionen kan inte användas av isolerade planappar som finns i en apptjänstmiljö.
* Funktionen kräver ett oanvänt undernät som är ett /27 med 32 adresser eller större i ett Azure Resource Manager-nätverk.
* Appen och det virtuella nätverket måste finnas i samma region.
* Du kan inte ta bort ett virtuella nätverk med en integrerad app. Ta bort integreringen innan du tar bort det virtuella nätverket.
* Du kan bara integrera med virtuella nätverk i samma prenumeration som appen.
* Du kan bara ha en regional VNet-integrering per apptjänstplan. Flera appar i samma App Service-plan kan använda samma virtuella nätverk.
* Du kan inte ändra prenumerationen på en app eller ett abonnemang medan det finns en app som använder regional VNet-integrering.
* Appen kan inte matcha adresser i Privata Azure DNS-zoner.

En adress används för varje planinstans. Om du skalar appen till fem instanser används fem adresser. Eftersom nätstorleken inte kan ändras efter tilldelningen måste du använda ett undernät som är tillräckligt stort för att hantera vilken skala appen kan nå. A /26 med 64 adresser är den rekommenderade storleken. A /26 med 64 adresser rymmer en Premium-plan med 30 instanser. När du skalar en plan uppåt eller nedåt behöver du dubbelt så många adresser under en kort tidsperiod.

Om du vill att dina appar i ett annat abonnemang ska nå ett virtuella nätverk som redan är anslutet till av appar i ett annat abonnemang väljer du ett annat undernät än det som används av den befintliga virtuella nätverksintegreringen.

Funktionen är i förhandsversion för Linux. Linux-formen för funktionen stöder endast att ringa samtal till RFC 1918-adresser (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-or-function-app-for-containers"></a>Webb- eller funktionsapp för behållare

Om du är värd för din app på Linux med de inbyggda avbildningarna fungerar regional VNet-integrering utan ytterligare ändringar. Om du använder Web eller Function App för behållare måste du ändra dockeravbildningen för att kunna använda VNet-integrering. I docker-avbildningen använder du PORT-miljövariabeln som huvudwebbserverns lyssningsport i stället för att använda ett hårdkodat portnummer. PORT-miljövariabeln ställs automatiskt in av plattformen vid starttiden för behållaren. Om du använder SSH måste SSH-demonen vara konfigurerad för att lyssna på portnumret som anges av den SSH_PORT miljövariabeln när du använder regional VNet-integrering. Det finns inget stöd för gateway-krävs VNet Integration på Linux.

### <a name="service-endpoints"></a>Tjänstslutpunkter

Med regional VNet-integrering kan du använda tjänstslutpunkter. Om du vill använda tjänstslutpunkter med din app använder du regional VNet-integrering för att ansluta till ett valt virtuella nätverk och konfigurerar sedan tjänstslutpunkter med måltjänsten i undernätet som du använde för integreringen. Om du sedan vill komma åt en tjänst via tjänstslutpunkter:

1. konfigurera regional VNet-integrering med din webbapp
1. gå till måltjänsten och konfigurera tjänstslutpunkter mot undernätet som används för integrering

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Du kan använda nätverkssäkerhetsgrupper för att blockera inkommande och utgående trafik till resurser i ett virtuella nätverk. En app som använder regional VNet-integrering kan använda en [nätverkssäkerhetsgrupp][VNETnsg] för att blockera utgående trafik till resurser i ditt virtuella nätverk eller internet. Om du vill blockera trafik till offentliga adresser måste du ha programinställningen WEBSITE_VNET_ROUTE_ALL inställd på 1. De inkommande reglerna i en NSG gäller inte för din app eftersom VNet-integrering endast påverkar utgående trafik från din app.

Om du vill styra inkommande trafik till din app använder du funktionen Åtkomstbegränsningar. En NSG som tillämpas på ditt integrationsundernät gäller oavsett eventuella vägar som tillämpas på integrationsundernätet. Om WEBSITE_VNET_ROUTE_ALL är inställt på 1 och du inte har några vägar som påverkar den offentliga adresstrafiken i integrationsundernätet, är all utgående trafik fortfarande föremål för NSG:er som tilldelats ditt integrationsundernät. Om WEBSITE_VNET_ROUTE_ALL inte är inställt tillämpas NSG endast på RFC1918-trafik.

### <a name="routes"></a>Vägar

Du kan använda rutttabeller för att dirigera utgående trafik från din app till var du vill. Som standard påverkar flödestabeller bara din RFC1918-måltrafik. Om du ställer in WEBSITE_VNET_ROUTE_ALL till 1 påverkas alla utgående samtal. Vägar som är inställda på integrationsundernätet påverkar inte svar på inkommande appbegäranden. Vanliga destinationer kan vara brandväggsenheter eller gateways.

Om du vill dirigera all utgående trafik lokalt kan du använda en vägtabell för att skicka all utgående trafik till din ExpressRoute-gateway. Om du dirigerar trafik till en gateway måste du ange vägar i det externa nätverket för att skicka tillbaka alla svar.

BGP-vägar (Border Gateway Protocol) påverkar också apptrafiken. Om du har BGP-vägar från något som liknar en ExpressRoute-gateway påverkas din app utgående trafik. Som standard påverkar BGP-vägar endast din RFC1918-måltrafik. Om WEBSITE_VNET_ROUTE_ALL är inställt på 1 kan all utgående trafik påverkas av dina BGP-vägar.


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
