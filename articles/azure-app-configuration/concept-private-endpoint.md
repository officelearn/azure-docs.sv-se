---
title: Använda privata slutpunkter för Azure App-konfiguration
description: Skydda appkonfigurationsbutiken med privata slutpunkter
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366776"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Använda privata slutpunkter för Azure App-konfiguration

Du kan använda [privata slutpunkter](../private-link/private-endpoint-overview.md) för Azure App-konfiguration för att tillåta klienter i ett virtuellt nätverk (VNet) att på ett säkert sätt komma åt data via en [privat länk](../private-link/private-link-overview.md). Den privata slutpunkten använder en IP-adress från VNet-adressutrymmet för appkonfigurationsarkivet. Nätverkstrafik mellan klienterna på det virtuella nätverket och App Configuration-arkivet passerar över det virtuella nätverket med hjälp av en privat länk i Microsofts stamnätsnätverk, vilket eliminerar exponering för det offentliga internet.

Med hjälp av privata slutpunkter för App Configuration Store kan du:
- Skydda programkonfigurationsinformationen genom att konfigurera brandväggen så att alla anslutningar till appkonfiguration blockeras på den offentliga slutpunkten.
- Öka säkerheten för det virtuella nätverket (VNet) så att data inte kommer ut från det virtuella nätverket.
- Anslut till App Configuration Store på ett säkert sätt från lokala nätverk som ansluter till det virtuella nätverket med HJÄLP AV [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoutes](../expressroute/expressroute-locations.md) med privat peering.

> [!NOTE]
> Azure App Configuration erbjuder användning av privata slutpunkter som en offentlig förhandsversion. Offentliga förhandsversioner gör det möjligt för kunder att experimentera med nya funktioner innan de släpps officiellt.  Offentliga förhandsversionsfunktioner och tjänster är inte avsedda för produktionsanvändning.

## <a name="conceptual-overview"></a>Konceptuell översikt

En privat slutpunkt är ett speciellt nätverksgränssnitt för en Azure-tjänst i ditt [virtuella nätverk](../virtual-network/virtual-networks-overview.md) (VNet). När du skapar en privat slutpunkt för App Config-butiken ger den säker anslutning mellan klienter på ditt virtuella nätverk och din konfigurationsbutik. Den privata slutpunkten tilldelas en IP-adress från IP-adressintervallet för ditt virtuella nätverk. Anslutningen mellan den privata slutpunkten och konfigurationsarkivet använder en säker privat länk.

Program i det virtuella nätverket kan ansluta till konfigurationsarkivet via den privata slutpunkten **med samma anslutningssträngar och auktoriseringsmekanismer som de annars skulle använda**. Privata slutpunkter kan användas med alla protokoll som stöds av App Configuration Store.

Appkonfiguration stöder inte tjänstslutpunkter, men privata slutpunkter kan skapas i undernät som använder [tjänstslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md). Klienter i ett undernät kan ansluta säkert till ett App Configuration-arkiv med hjälp av den privata slutpunkten när du använder tjänstslutpunkter för att komma åt andra.  

När du skapar en privat slutpunkt för en tjänst i ditt virtuella nätverk skickas en begäran om medgivande för godkännande till tjänstkontoägaren. Om användaren som begär att den privata slutpunkten ska skapas också är ägare till kontot godkänns den här begäran om medgivande automatiskt.

Tjänstkontoägare kan hantera begäranden om medgivande och privata slutpunkter via `Private Endpoints` fliken i konfigurationsarkivet i [Azure-portalen](https://portal.azure.com).

### <a name="private-endpoints-for-app-configuration"></a>Privata slutpunkter för appkonfiguration 

När du skapar en privat slutpunkt måste du ange det Appkonfigurationslager som den ansluter till. Om du har flera appkonfigurationsinstanser i ett konto behöver du en separat privat slutpunkt för varje butik.

### <a name="connecting-to-private-endpoints"></a>Ansluta till privata slutpunkter

Azure förlitar sig på DNS-lösning för att dirigera anslutningar från det virtuella nätverket till konfigurationsarkivet via en privat länk. Du kan snabbt hitta anslutningssträngar i Azure-portalen genom att välja ditt App Configuration Store och sedan välja **Inställningar** > **Access Nycklar**.  

> [!IMPORTANT]
> Använd samma anslutningssträng för att ansluta till App Configuration Store med privata slutpunkter som du skulle använda för en offentlig slutpunkt. Anslut inte till lagringskontot `privatelink` med hjälp av dess underdomän-URL.

## <a name="dns-changes-for-private-endpoints"></a>DNS-ändringar för privata slutpunkter

När du skapar en privat slutpunkt uppdateras DNS CNAME-resursposten för konfigurationsarkivet till ett `privatelink`alias i en underdomän med prefixet . Azure skapar också en [privat DNS-zon](../dns/private-dns-overview.md) som motsvarar underdomänen, `privatelink` med DNS A-resursposterna för de privata slutpunkterna.

När du löser slutpunkts-URL:en utanför det virtuella nätverket matchas den till den offentliga slutpunkten för butiken. När slutpunkts-URL:en matchas inifrån det virtuella nätverket som är värd för den privata slutpunkten matchas slutpunkts-URL:en till den privata slutpunkten.

Du kan styra åtkomsten för klienter utanför det virtuella nätverket via den offentliga slutpunkten med hjälp av Azure Firewall-tjänsten.

Den här metoden ger åtkomst till butiken **med samma anslutningssträng** för klienter på det virtuella nätverket som är värd för privata slutpunkter samt klienter utanför det virtuella nätverket.

Om du använder en anpassad DNS-server i nätverket måste klienter kunna matcha det fullständigt kvalificerade domännamnet (FQDN) för tjänstslutpunkten till den privata slutpunkts-IP-adressen. Konfigurera DNS-servern så att den delegerar den privata länkunderdomänen till den `AppConfigInstanceA.privatelink.azconfig.io` privata DNS-zonen för virtuella nätverk, eller konfigurera A-posterna för med den privata slutpunkts-IP-adressen.

> [!TIP]
> När du använder en anpassad eller lokal DNS-server bör du konfigurera `privatelink` DNS-servern så att butiksnamnet i underdomänen matchar den privata slutpunkts-IP-adressen. Du kan göra detta `privatelink` genom att delegera underdomänen till den privata DNS-zonen i det virtuella nätverket, eller konfigurera DNS-zonen på DNS-servern och lägga till DNS A-posterna.

## <a name="pricing"></a>Prissättning

För att aktivera privata slutpunkter krävs ett App Configuration Store [på standardnivå.](https://azure.microsoft.com/pricing/details/app-configuration/)  Mer information om privat länkprissättning finns i [Azure Private Link-priser](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du skapar en privat slutpunkt för App Configuration Store finns i följande artiklar:

- [Skapa en privat slutpunkt med private link center i Azure-portalen](../private-link/create-private-endpoint-portal.md)
- [Skapa en privat slutpunkt med Azure CLI](../private-link/create-private-endpoint-cli.md)
- [Skapa en privat slutpunkt med Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

Lär dig att konfigurera DNS-servern med privata slutpunkter:

- [Namnmatchning för resurser i virtuella nätverk i Azure](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [DNS-konfiguration för privata slutpunkter](/azure/private-link/private-endpoint-overview#dns-configuration)
