---
title: Ansluta privat till en webbapp med Azure Private Endpoint
description: Ansluta privat till en webbapp med Azure Private Endpoint
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/18/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: c2717b1f29af39c6fdc4602b11acba131d959f03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534396"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Använda privata slutpunkter för Azure Web App (förhandsversion)

> [!Note]
> Förhandsversionen är tillgänglig i östra USA och västra USA 2 regioner för alla PremiumV2 Windows och Linux Web Apps och Elastic Premium Functions. 

Du kan använda Privat slutpunkt för din Azure Web App för att tillåta klienter som finns i ditt privata nätverk att på ett säkert sätt komma åt appen via Private Link. Den privata slutpunkten använder en IP-adress från ditt Azure VNet-adressutrymme. Nätverkstrafik mellan en klient i ditt privata nätverk och webbappen passerar över det virtuella nätverket och en privat länk i Microsofts stamnätsnätverk, vilket eliminerar exponering från det offentliga Internet.

Med hjälp av privat slutpunkt för din webbapp kan du:

- Skydda din webbapp genom att konfigurera tjänstens slutpunkt, vilket eliminerar offentlig exponering.
- Anslut till Webbapp från lokala nätverk som ansluter till det virtuella nätverket med hjälp av en privat peer-enhet för VPN eller ExpressRoute.

Om du bara behöver en säker anslutning mellan ditt virtuella nätverk och din webbapp är en tjänstslutpunkt den enklaste lösningen. Om du också behöver nå webbappen lokalt via en Azure-gateway, ett regionalt peered-nätverk eller ett globalt peered-nätverk, är Private Endpoint lösningen.  

Mer information finns i [Tjänstslutpunkter][serviceendpoint].

## <a name="conceptual-overview"></a>Konceptuell översikt

En privat slutpunkt är ett särskilt nätverksgränssnitt (NIC) för din Azure Web App i ett undernät i det virtuella nätverket (VNet).
När du skapar en privat slutpunkt för din webbapp ger den säker anslutning mellan klienter i det privata nätverket och din webbapp. Den privata slutpunkten tilldelas en IP-adress från IP-adressintervallet för ditt virtuella nätverk.
Anslutningen mellan den privata slutpunkten och webbappen använder en säker [privat länk][privatelink]. Privat slutpunkt används endast för inkommande flöden till din webbapp. Utgående flöden kommer inte att använda den här privata slutpunkten, men du kan injicera utgående flöden i nätverket i ett annat undernät via [VNet-integrationsfunktionen][vnetintegrationfeature].

Undernätet där du ansluter den privata slutpunkten kan ha andra resurser i det, du behöver inte ett dedikerat tomt undernät.
Du kan också distribuera den privata slutpunkten i en annan region än webbappen. 

> [!Note]
>VNet-integrationsfunktionen kan inte använda samma undernät som private endpoint, detta är en begränsning av VNet-integrationsfunktionen.

Ur ett säkerhetsperspektiv:

- När du aktiverar privata slutpunkter i din webbapp inaktiverar du all offentlig åtkomst.
- Du kan aktivera flera privata slutpunkter i andra virtuella nätverk och undernät, inklusive virtuella nätverk i andra regioner.
- IP-adressen för det privata slutpunktskortet måste vara dynamisk, men förblir densamma tills du tar bort den privata slutpunkten.
- Nätverkskortet för den privata slutpunkten kan inte ha en NSG associerad.
- Undernätet som är värd för den privata slutpunkten kan ha en NSG associerad, men du måste inaktivera efterlevnaden av nätverksprinciper för den privata slutpunkten: se [Inaktivera nätverksprinciper för privata slutpunkter][disablesecuritype]. Därför kan du inte filtrera av någon NSG åtkomst till din privata slutpunkt.
- När du aktiverar privat slutpunkt i din webbapp utvärderas inte konfigurationen av [åtkomstbegränsningar][accessrestrictions] för webbappen.
- Du kan minska risken för dataexfiltration från det virtuella nätverket genom att ta bort alla NSG-regler där målet är taggen Internet eller Azure-tjänster. Men om du lägger till en privat slutpunkt för webbappar i undernätet kan du nå alla webbprogram som finns i samma distributionsstämpel och som exponeras för Internet.

I webb-HTTP-loggarna för din webbapp hittar du klientkällans IP. Detta implementeras med hjälp av TCP Proxy-protokollet och vidarebefordrar klient-IP-egenskapen upp till Web App. Mer information finns i [Hämta anslutningsinformation med TCP Proxy v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Översikt över privat slutpunkt i Web App](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Eftersom den här funktionen är i förhandsversion ändrar vi inte DNS-posten under förhandsgranskningen. Du måste själv hantera DNS-posten i din privata DNS-server eller Azure DNS-zon.
Om du behöver använda ett anpassat DNS-namn måste du lägga till det anpassade namnet i webappen. Under förhandsgranskningen måste det anpassade namnet valideras som vilket eget namn som helst med hjälp av offentlig DNS-matchning. Mer information finns i [anpassad DNS-validering.][dnsvalidation]

## <a name="pricing"></a>Prissättning

Information om priser finns i [Azure Private Link-priser][pricing].

## <a name="limitations"></a>Begränsningar

Vi förbättrar funktionen Privat länk och privat slutpunkt regelbundet, kontrollera [den här artikeln][pllimitations] för aktuell information om begränsningar.

## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar privat slutpunkt för din webbapp via portalen finns i [Så här ansluter du privat till en webbapp][howtoguide]




<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
