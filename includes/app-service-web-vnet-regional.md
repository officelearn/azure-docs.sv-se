---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671538"
---
Genom att använda regional VNet-integrering kan din app komma åt:

* resurser i VNet i samma region som du integrerar med 
* resurser i virtuella nätverk som peer-kopplas till ditt VNet i samma region
* tjänst slut punkt säkra tjänster
* resurser över ExpressRoute-anslutningar
* resurser i det virtuella nätverk som du är ansluten till
* resurser mellan peer-anslutningar inklusive ExpressRoute-anslutningar
* Privata slut punkter 

När du använder VNet-integrering med virtuella nätverk i samma region kan du använda följande funktioner i Azure-nätverk:

* Nätverks säkerhets grupper (NSG: er) – du kan blockera utgående trafik med en nätverks säkerhets grupp som placeras i ditt integrations undernät. Reglerna för inkommande trafik gäller inte eftersom du inte kan använda VNet-integrering för att ge inkommande åtkomst till din app.
* Routningstabeller (UDR) – du kan placera en routningstabell i integrations under nätet för att skicka utgående trafik där du vill. 

Som standard dirigerar din app endast RFC1918-trafik till ditt VNet. Om du vill dirigera all utgående trafik till ditt VNet använder du appens inställning WEBSITE_VNET_ROUTE_ALL till din app. Konfigurera appens inställning:

1. Gå till konfigurations gränssnittet på din app-Portal. Välj **ny program inställning**
1. Skriv **WEBSITE_VNET_ROUTE_ALL** i fältet namn och **1** i fältet värde

   ![Ange program inställning][4]

1. Välj **OK**
1. Välj **Spara**

Om du dirigerar all utgående trafik till ditt VNet, kommer den att omfattas av NSG: er och UDR som tillämpas på ditt integrations undernät. När du dirigerar all utgående trafik till ditt VNet är dina utgående adresser fortfarande de utgående adresser som listas i dina app-egenskaper, om du inte tillhandahåller vägar för att skicka trafiken någon annan stans. 

Det finns vissa begränsningar med att använda VNet-integrering med virtuella nätverk i samma region:

* Du kan inte komma åt resurser över global peering anslutningar
* Funktionen är endast tillgänglig från nyare App Service skalnings enheter som stöder PremiumV2 App Service-planer.
* Integrations under nätet kan bara användas av ett App Service plan
* Funktionen kan inte användas av isolerade plan-appar som finns i en App Service-miljön
* Funktionen kräver ett oanvänt undernät som är a/27 med 32 adresser eller större i ett virtuellt Resource Manager-nätverk
* Appen och VNet måste finnas i samma region
* Du kan inte ta bort ett VNet med en integrerad app. Ta bort integrationen innan du tar bort det virtuella nätverket. 
* Du kan bara integrera med virtuella nätverk i samma prenumeration som appen
* Du kan bara ha en regional VNet-integration per App Service plan. Flera appar i samma App Service plan kan använda samma VNet. 
* Du kan inte ändra prenumerationen på en app eller en plan när det finns en app som använder regional VNet-integrering

En adress används för varje plan instans. Om du skalar din app till fem instanser används fem adresser. Eftersom det inte går att ändra under näts storleken efter tilldelningen, måste du använda ett undernät som är tillräckligt stort för att anpassa vilken skala appen kan komma åt. En/26 med 64-adresser är den rekommenderade storleken. A/26 med 64 adresser kommer att ha en Premium-plan med 30 instanser. När du skalar upp eller ned en plan behöver du två gånger så många adresser under en kort tids period. 

Om du vill att dina appar i en annan plan ska kunna komma åt ett VNet som redan är anslutet till appar i ett annat schema, måste du välja ett annat undernät än det som används av den befintliga VNet-integreringen.  

Funktionen är i för hands version för Linux. Linux-formen för funktionen stöder bara anrop till RFC 1918-adresser (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web--function-app-for-containers"></a>Web/Funktionsapp för behållare

Om du är värd för din app på Linux med de inbyggda avbildningarna fungerar regional VNet-integrering utan ytterligare ändringar. Om du använder webb-Funktionsapp för behållare måste du ändra Docker-avbildningen för att kunna använda VNet-integrering. I Docker-avbildningen använder du PORT miljö variabeln som den huvudsakliga webb serverns lyssnings port, i stället för att använda ett hårdkodad port nummer. PORT miljö variabeln anges automatiskt av plattformen vid behållarens start tid. Om du använder SSH måste SSH-daemonen konfigureras för att lyssna på det port nummer som anges av SSH_PORT-miljövariabeln när du använder regional VNet-integrering.  Det finns inget stöd för gateway som kräver VNet-integrering på Linux. 

### <a name="service-endpoints"></a>Tjänst slut punkter

Regional VNet-integrering gör att du kan använda tjänst slut punkter.  Om du vill använda tjänstens slut punkter med din app använder du regional VNet-integrering för att ansluta till ett valt VNet och konfigurerar sedan tjänst slut punkter på det undernät som du använde för integreringen. 

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Med nätverks säkerhets grupper kan du blockera inkommande och utgående trafik till resurser i ett VNet. En app som använder regional VNet-integrering kan använda [nätverks säkerhets gruppen][VNETnsg] för att blockera utgående trafik till resurser i ditt VNet eller Internet. Om du vill blockera trafik till offentliga adresser måste du ha program inställningen WEBSITE_VNET_ROUTE_ALL inställd på 1. Reglerna för inkommande trafik i en NSG gäller inte för din app eftersom VNet-integration endast påverkar utgående trafik från din app. Om du vill kontrol lera inkommande trafik till din app använder du funktionen begränsningar för åtkomst. En NSG som tillämpas på ditt integrations under nät kommer att gälla oavsett vilka vägar som tillämpas på ditt integrations under nät. Om WEBSITE_VNET_ROUTE_ALL har angetts till 1 och du inte har några vägar som påverkar offentlig adress trafik i ditt integrations under nät, skulle all utgående trafik fortfarande bli föremål för NSG: er som har tilldelats ditt integrations-undernät. Om WEBSITE_VNET_ROUTE_ALL inte har angetts tillämpas NSG: er endast på RFC1918-trafik.

### <a name="routes"></a>Vägar

Med routningstabeller kan du dirigera utgående trafik från din app till var du vill. Som standard kommer routningstabeller bara att påverka din RFC1918-destinations trafik.  Om du anger WEBSITE_VNET_ROUTE_ALL till 1, kommer alla utgående samtal att påverkas. Vägar som anges i ditt integrations undernät påverkar inte svar på inkommande app-begäranden. Vanliga mål kan omfatta brand Väggs enheter eller gatewayer. Om du vill dirigera all utgående trafik lokalt kan du använda en routningstabell för att skicka all utgående trafik till din ExpressRoute-Gateway. Om du dirigerar trafik till en gateway måste du ställa in vägar i det externa nätverket för att skicka svar tillbaka.

BGP-vägar (Border Gateway Protocol) kommer också att påverka din app-trafik. Om du har BGP-vägar från något som liknar en ExpressRoute-gateway kommer din app utgående trafik att påverkas. Som standard kommer BGP-vägar endast att påverka din RFC1918-destinations trafik. Om WEBSITE_VNET_ROUTE_ALL har värdet 1 kan all utgående trafik påverkas av BGP-vägarna. 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/