---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 06/08/2020
ms.author: ccompy
ms.openlocfilehash: 14b9d9fe0eb9dfe2f25373c2d87d9b4af15dd0d9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371603"
---
Genom att använda regional VNet-integrering kan din app komma åt:

* Resurser i ett VNet i samma region som din app.
* Resurser i virtuella nätverk som peer-kopplas till det virtuella nätverk som din app är integrerat med.
* Säkra tjänster för tjänst slut punkt.
* Resurser över Azure ExpressRoute-anslutningar.
* Resurser i det virtuella nätverk som du är integrerad med.
* Resurser mellan peer-anslutningar, som innehåller Azure ExpressRoute-anslutningar.
* Privata slut punkter 

När du använder VNet-integrering med virtuella nätverk i samma region kan du använda följande funktioner i Azure-nätverk:

* **Nätverks säkerhets grupper (NSG: er)** : du kan blockera utgående trafik med en NSG som placeras i ditt integrations undernät. Reglerna för inkommande trafik gäller inte eftersom du inte kan använda VNet-integrering för att ge inkommande åtkomst till din app.
* **Routningstabeller (UDR)** : du kan placera en routningstabell i integrations under nätet för att skicka utgående trafik där du vill.

Som standard dirigerar din app endast RFC1918 trafik till ditt VNet. Om du vill dirigera all utgående trafik till ditt VNet använder du appens inställning WEBSITE_VNET_ROUTE_ALL till din app. Konfigurera appens inställning:

1. Gå till **konfigurations** gränssnittet på din app-Portal. Välj **Ny programinställning**.
1. Ange **WEBSITE_VNET_ROUTE_ALL** i rutan **namn** och skriv **1** i rutan **värde** .

   ![Ange program inställning][4]

1. Välj **OK**.
1. Välj **Spara**.

> [!NOTE]
> Om du dirigerar all utgående trafik till ditt VNet, omfattas den av NSG: er och UDR som tillämpas på ditt integrations undernät. När du dirigerar all utgående trafik till ditt VNet, är dina utgående adresser fortfarande de utgående adresser som visas i dina app-egenskaper, om du inte tillhandahåller vägar för att skicka trafiken någon annan stans.

Det finns vissa begränsningar med att använda VNet-integrering med virtuella nätverk i samma region:

* Du kan inte komma åt resurser över global peering anslutningar.
* Funktionen är endast tillgänglig från nyare Azure App Service skalnings enheter som stöder PremiumV2 App Service-planer. Observera att *detta inte innebär att din app måste köras på en PremiumV2-pris nivå* , bara att den måste köras i en app service plan där alternativet PremiumV2 är tillgängligt (vilket innebär att det är en nyare skalnings enhet där denna VNet-integrering också är tillgänglig).
* Integrations under nätet kan bara användas av en App Service plan.
* Funktionen kan inte användas av isolerade plan-appar som finns i en App Service-miljön.
* Funktionen kräver ett oanvänt undernät som är a/27 med 32 adresser eller större i ett Azure Resource Manager VNet.
* Appen och VNet måste finnas i samma region.
* Du kan inte ta bort ett VNet med en integrerad app. Ta bort integrationen innan du tar bort det virtuella nätverket.
* Du kan bara integrera med virtuella nätverk i samma prenumeration som appen.
* Du kan bara ha en regional VNet-integration per App Service plan. Flera appar i samma App Service plan kan använda samma VNet.
* Du kan inte ändra prenumerationen på en app eller en plan när det finns en app som använder regional VNet-integrering.
* Din app kan inte matcha adresser i Azure DNS Private Zones utan konfigurations ändringar

En adress används för varje plan instans. Om du skalar din app till fem instanser används fem adresser. Eftersom det inte går att ändra under näts storleken efter tilldelningen, måste du använda ett undernät som är tillräckligt stort för att anpassa vilken skala appen kan komma åt. En/26 med 64-adresser är den rekommenderade storleken. A/26 med 64 adresser uppfyller en Premium-plan med 30 instanser. När du skalar upp eller ned en plan behöver du två gånger så många adresser under en kort tids period.

Om du vill att dina appar i en annan plan ska kunna komma åt ett VNet som redan är anslutet till av appar i ett annat abonnemang väljer du ett annat undernät än det som används av den befintliga VNet-integreringen.

Funktionen stöds fullt ut för både Windows-och Linux-appar, inklusive [anpassade behållare](../articles/app-service/quickstart-custom-container.md). Alla beteenden fungerar på samma sätt mellan Windows-appar och Linux-appar.

### <a name="service-endpoints"></a>Tjänstslutpunkter

Regional VNet-integrering gör att du kan använda tjänst slut punkter. Om du vill använda tjänstens slut punkter med din app använder du regional VNet-integrering för att ansluta till ett valt VNet och konfigurerar sedan tjänstens slut punkter med mål tjänsten på under nätet som du använde för integreringen. Om du sedan ville få åtkomst till en tjänst via tjänst slut punkter:

1. Konfigurera regional VNet-integrering med din webbapp
1. Gå till mål tjänsten och konfigurera tjänst slut punkter mot det undernät som används för integrering

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Du kan använda nätverks säkerhets grupper för att blockera inkommande och utgående trafik till resurser i ett VNet. En app som använder regional VNet-integrering kan använda en [nätverks säkerhets grupp][VNETnsg] för att blockera utgående trafik till resurser i ditt VNet eller Internet. Om du vill blockera trafik till offentliga adresser måste du ha program inställningen WEBSITE_VNET_ROUTE_ALL inställd på 1. Reglerna för inkommande trafik i en NSG gäller inte för din app eftersom VNet-integrering endast påverkar utgående trafik från din app.

Om du vill kontrol lera inkommande trafik till din app använder du funktionen begränsningar för åtkomst. En NSG som tillämpas på ditt integrations under nät gäller oavsett vilka vägar som tillämpas på ditt integrations under nät. Om WEBSITE_VNET_ROUTE_ALL har angetts till 1 och du inte har några vägar som påverkar offentlig adress trafik i ditt integrations undernät, omfattas all utgående trafik fortfarande till NSG: er som har tilldelats ditt integrations under nät. Om WEBSITE_VNET_ROUTE_ALL inte har angetts tillämpas NSG: er endast på RFC1918-trafik.

### <a name="routes"></a>Vägar

Du kan använda routningstabeller för att dirigera utgående trafik från din app till var du vill. Som standard påverkar routningstabeller bara din RFC1918-destinations trafik. Om du anger WEBSITE_VNET_ROUTE_ALL till 1 påverkas alla utgående samtal. Vägar som anges i ditt integrations undernät påverkar inte svar på inkommande app-begäranden. Vanliga mål kan omfatta brand Väggs enheter eller gatewayer.

Om du vill dirigera all utgående trafik lokalt kan du använda en routningstabell för att skicka all utgående trafik till din ExpressRoute-Gateway. Om du dirigerar trafik till en gateway måste du ställa in vägar i det externa nätverket för att skicka svar tillbaka.

Border Gateway Protocol (BGP) vägar påverkar också din app-trafik. Om du har BGP-vägar från något som liknar en ExpressRoute-gateway kommer din app utgående trafik att påverkas. BGP-vägar påverkar som standard bara din RFC1918-destinations trafik. Om WEBSITE_VNET_ROUTE_ALL är inställt på 1 kan all utgående trafik påverkas av BGP-vägar.

### <a name="azure-dns-private-zones"></a>Azure DNS Private Zones 

När din app har integrerats med ditt VNet, använder den samma DNS-server som ditt VNet har kon figurer ATS med. Som standard fungerar inte appen med Azure DNS Private Zones. Om du vill arbeta med Azure DNS Private Zones måste du lägga till följande appinställningar:

1. WEBSITE_DNS_SERVER med värdet 168.63.129.16
1. WEBSITE_VNET_ROUTE_ALL med värdet 1

De här inställningarna kommer att skicka alla utgående samtal från din app till ditt VNet. Dessutom kommer appen att tillåta att appen använder Azure DNS genom att fråga zonen Privat DNS på arbets nivå. Den här funktionen ska användas när en app som körs har åtkomst till en Privat DNS zon.

> [!NOTE]
>Det går inte att lägga till en anpassad domän i en webbapp som använder Privat DNS zon med VNET-integration. Anpassad domän verifiering görs på styrenhets nivå, inte på arbets nivå, vilket förhindrar att DNS-poster visas. Om du vill använda en anpassad domän från en Privat DNS zon måste verifieringen kringgås med en Application Gateway-eller ILB-App Service-miljön.



### <a name="private-endpoints"></a>Privata slut punkter

Om du vill göra anrop till [privata slut punkter][privateendpoints]måste du antingen integrera med Azure DNS Private Zones eller hantera den privata slut punkten på den DNS-server som används av din app. 

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
