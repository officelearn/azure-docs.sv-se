---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671538"
---
Med hjälp av regional VNet-integrering kan appen komma åt:

* resurser i det virtuella nätverket i samma region som du integrerar med 
* resurser i virtuella nätverk som peer-datorer till ditt virtuella nätverk som finns i samma region
* tjänstslutpunkt säkrade tjänster
* resurser över ExpressRoute-anslutningar
* resurser i det virtuella nätverk som du är ansluten till
* resurser över peer-anslutningar, inklusive ExpressRoute-anslutningar
* privata slutpunkter 

När du använder VNet-integrering med virtuella nätverk i samma region kan du använda följande Azure Networking-funktioner:

* Nätverkssäkerhetsgrupper(NSG) – Du kan blockera utgående trafik med en nätverkssäkerhetsgrupp som placeras i integrationsundernätet. De inkommande reglerna gäller inte eftersom du inte kan använda VNet-integrering för att ge inkommande åtkomst till din app.
* Flödestabeller (UDRs) – Du kan placera en flödestabell i integrationsundernätet för att skicka utgående trafik där du vill. 

Som standard dirigerar appen endast RFC1918-trafik till ditt virtuella nätverk. Om du vill dirigera all utgående trafik till ditt virtuella nätverk använder du appinställningen WEBSITE_VNET_ROUTE_ALL på din app. Så här konfigurerar du appinställningen:

1. Gå till konfigurationsgränssnittet i appportalen. Välj **Ny programinställning**
1. Skriv **WEBSITE_VNET_ROUTE_ALL** i fältet Namn och **1** i fältet Värde

   ![Ange programinställning][4]

1. Välj **OK**
1. Välj **Spara**

Om du dirigerar all utgående trafik till ditt virtuella nätverk kommer den att omfattas av NSGs och UDRs som tillämpas på ditt integrationsundernät. När du dirigerar all utgående trafik till ditt virtuella nätverk är dina utgående adresser fortfarande de utgående adresser som visas i appegenskaperna om du inte tillhandahåller rutter för att skicka trafiken någon annanstans. 

Det finns vissa begränsningar med att använda VNet-integrering med virtuella nätverk i samma region:

* Du kan inte nå resurser över globala peering-anslutningar
* Funktionen är endast tillgänglig från nyare App Service-skalenheter som stöder PremiumV2 App Service-abonnemang.
* Integrationsundernätet kan endast användas av en App Service-plan
* Funktionen kan inte användas av isolerade planappar som finns i en apptjänstmiljö
* Funktionen kräver ett oanvänt undernät som är ett /27 med 32 adresser eller större i ett resurshanteraren-nätverk
* Appen och det virtuella nätverket måste finnas i samma region
* Du kan inte ta bort ett virtuella nätverk med en integrerad app. Ta bort integreringen innan du tar bort det virtuella nätverket. 
* Du kan bara integrera med virtuella nätverk i samma prenumeration som appen
* Du kan bara ha en regional VNet-integrering per apptjänstplan. Flera appar i samma App Service-plan kan använda samma virtuella nätverk. 
* Du kan inte ändra prenumerationen på en app eller en plan medan det finns en app som använder regional VNet-integrering

En adress används för varje planinstans. Om du skalar appen till fem instanser används fem adresser. Eftersom nätstorleken inte kan ändras efter tilldelningen måste du använda ett undernät som är tillräckligt stort för att hantera vilken skala appen kan nå. A /26 med 64 adresser är den rekommenderade storleken. A /26 med 64 adresser rymmer en Premium-plan med 30 instanser. När du skalar en plan uppåt eller nedåt behöver du dubbelt så många adresser under en kort tidsperiod. 

Om du vill att dina appar i ett annat abonnemang ska nå ett virtuella nätverk som redan är anslutet till appar i ett annat abonnemang måste du välja ett annat undernät än det som används av den befintliga virtuella nätverksintegreringen.  

Funktionen är i förhandsversion för Linux. Linux-formen för funktionen stöder endast att ringa samtal till RFC 1918-adresser (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web--function-app-for-containers"></a>Webb / funktion App för behållare

Om du är värd för din app på Linux med de inbyggda avbildningarna fungerar regional VNet-integrering utan ytterligare ändringar. Om du använder Web / Function App för behållare, måste du ändra din docker bild för att kunna använda VNet Integration. I docker-avbildningen använder du PORT-miljövariabeln som huvudwebbserverns lyssningsport i stället för att använda ett hårdkodat portnummer. PORT-miljövariabeln ställs automatiskt in av plattformen vid starttiden för behållaren. Om du använder SSH måste SSH-demonen konfigureras för att lyssna på portnumret som anges av SSH_PORT-miljövariabeln när du använder regional VNet-integrering.  Det finns inget stöd för gateway krävs VNet Integration på Linux. 

### <a name="service-endpoints"></a>Tjänstslutpunkter

Med regional VNet-integrering kan du använda tjänstslutpunkter.  Om du vill använda tjänstslutpunkter med din app använder du regional VNet-integrering för att ansluta till ett valt virtuella nätverk och konfigurerar sedan tjänstslutpunkter i undernätet som du använde för integreringen. 

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Med nätverkssäkerhetsgrupper kan du blockera inkommande och utgående trafik till resurser i ett virtuella nätverk. En app som använder regional VNet-integrering kan använda [Nätverkssäkerhetsgrupp][VNETnsg] för att blockera utgående trafik till resurser i ditt virtuella nätverk eller internet. Om du vill blockera trafik till offentliga adresser måste du ha programinställningen WEBSITE_VNET_ROUTE_ALL inställd på 1. De inkommande reglerna i en NSG gäller inte för din app eftersom VNet-integrering bara påverkar utgående trafik från din app. Om du vill styra inkommande trafik till din app använder du funktionen Åtkomstbegränsningar. En NSG som tillämpas på ditt integrationsundernät gäller oavsett eventuella vägar som tillämpas på ditt integrationsundernät. Om WEBSITE_VNET_ROUTE_ALL var inställt på 1 och du inte hade några vägar som påverkar den offentliga adresstrafiken i ditt integrationsundernät, skulle all din utgående trafik fortfarande vara föremål för NSG:er som tilldelats ditt integrationsundernät. Om WEBSITE_VNET_ROUTE_ALL inte har ställts in skulle NSG endast tillämpas på RFC1918-trafik.

### <a name="routes"></a>Vägar

Med flödestabeller kan du dirigera utgående trafik från din app till var du vill. Som standard påverkar flödestabeller bara din RFC1918-måltrafik.  Om du ställer in WEBSITE_VNET_ROUTE_ALL till 1 påverkas alla utgående samtal. Vägar som är inställda på integrationsundernätet påverkar inte svar på inkommande appbegäranden. Vanliga destinationer kan vara brandväggsenheter eller gateways. Om du vill dirigera all utgående trafik lokalt kan du använda en vägtabell för att skicka all utgående trafik till din ExpressRoute-gateway. Om du dirigerar trafik till en gateway måste du ange vägar i det externa nätverket för att skicka tillbaka alla svar.

BGP-vägar (Border Gateway Protocol) påverkar också apptrafiken. Om du har BGP-vägar från något som liknar en ExpressRoute-gateway påverkas din app utgående trafik. Som standard påverkar BGP-vägar bara din RFC1918-måltrafik. Om WEBSITE_VNET_ROUTE_ALL är inställt på 1 kan all utgående trafik påverkas av dina BGP-vägar. 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/