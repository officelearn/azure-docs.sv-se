---
title: Använda privata slut punkter för Azure App konfiguration
description: Skydda appens konfigurations lager med privata slut punkter
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79366776"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Använda privata slut punkter för Azure App konfiguration

Du kan använda [privata slut punkter](../private-link/private-endpoint-overview.md) för Azure App konfiguration så att klienter på ett virtuellt nätverk (VNet) kan komma åt data på ett säkert sätt via en [privat länk](../private-link/private-link-overview.md). Den privata slut punkten använder en IP-adress från VNet-adressutrymmet för appens konfigurations lager. Nätverks trafik mellan klienterna i det virtuella nätverket och konfigurations arkivet för appar passerar via VNet med en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponeringen för det offentliga Internet.

Med hjälp av privata slut punkter för konfigurations arkivet för appar kan du:
- Skydda din program konfigurations information genom att konfigurera brand väggen så att alla anslutningar till app-konfigurationen blockeras på den offentliga slut punkten.
- Öka säkerheten för det virtuella nätverket (VNet) som garanterar att data inte är undantagna från det virtuella nätverket.
- Anslut säkert till appens konfigurations arkiv från lokala nätverk som ansluter till VNet med [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../expressroute/expressroute-locations.md) med privat peering.

> [!NOTE]
> Azure App-konfigurationen erbjuder användning av privata slut punkter som en offentlig för hands version. Med den offentliga för hands versionen kan kunder experimentera med nya funktioner före den officiella versionen.  Funktioner och tjänster för offentliga för hands versioner är inte avsedda för användning i produktion.

## <a name="conceptual-overview"></a>Konceptuell översikt

En privat slut punkt är ett särskilt nätverks gränssnitt för en Azure-tjänst i din [Virtual Network](../virtual-network/virtual-networks-overview.md) (VNet). När du skapar en privat slut punkt för appens konfigurations Arkiv ger den säker anslutning mellan klienter i ditt VNet och ditt konfigurations lager. Den privata slut punkten tilldelas en IP-adress från det virtuella nätverkets IP-adressintervall. Anslutningen mellan den privata slut punkten och konfigurations arkivet använder en säker privat länk.

Program i det virtuella nätverket kan ansluta till konfigurations lagringen via den privata slut punkten **med samma anslutnings strängar och auktoriseringsbeslut som de annars skulle använda**. Privata slut punkter kan användas med alla protokoll som stöds av appens konfigurations arkiv.

Även om app-konfigurationen inte stöder tjänst slut punkter kan privata slut punkter skapas i undernät som använder [tjänstens slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md). Klienter i ett undernät kan ansluta säkert till ett konfigurations lager för appar med hjälp av den privata slut punkten när de använder tjänstens slut punkter för att få åtkomst till andra.  

När du skapar en privat slut punkt för en tjänst i ditt VNet, skickas en medgivande förfrågan för godkännande till tjänst kontots ägare. Om användaren som begär att den privata slut punkten ska skapas även är ägare till kontot, godkänns den här medgivande förfrågningen automatiskt.

Tjänst kontots ägare kan hantera medgivande förfrågningar och privata slut punkter via `Private Endpoints` fliken i konfigurations arkivet i [Azure Portal](https://portal.azure.com).

### <a name="private-endpoints-for-app-configuration"></a>Privata slut punkter för konfiguration av appar 

När du skapar en privat slut punkt måste du ange det konfigurations lager för appen som den ansluter till. Om du har flera program konfigurations instanser i ett konto behöver du en separat privat slut punkt för varje butik.

### <a name="connecting-to-private-endpoints"></a>Ansluter till privata slut punkter

Azure förlitar sig på DNS-matchning för att dirigera anslutningar från VNet till konfigurations arkivet via en privat länk. Du kan snabbt hitta anslutnings strängar i Azure Portal genom att välja konfigurations lagret för appen och sedan välja **Inställningar** > **åtkomst nycklar**.  

> [!IMPORTANT]
> Använd samma anslutnings sträng för att ansluta till appens konfigurations lager med hjälp av privata slut punkter som du skulle använda för en offentlig slut punkt. Anslut inte till lagrings kontot med dess `privatelink` under domän-URL.

## <a name="dns-changes-for-private-endpoints"></a>DNS-ändringar för privata slut punkter

När du skapar en privat slut punkt uppdateras DNS CNAME-resursposten för konfigurations arkivet till ett alias i en under domän med prefixet `privatelink`. Azure skapar också en [privat DNS-zon](../dns/private-dns-overview.md) som motsvarar `privatelink` under domänen, med DNS a-resursposter för de privata slut punkterna.

När du löser slut punkts-URL: en från en plats utanför det virtuella nätverket matchas den offentliga slut punkten för butiken. När den är löst inifrån det virtuella nätverket som är värd för den privata slut punkten matchas slut punktens URL till den privata slut punkten.

Du kan kontrol lera åtkomsten för klienter utanför VNet via den offentliga slut punkten med hjälp av Azure Firewall service.

Den här metoden ger åtkomst till butiken **med samma anslutnings sträng** för klienter på det virtuella nätverket som är värd för privata slut punkter samt klienter utanför VNet.

Om du använder en anpassad DNS-server i ditt nätverk måste klienterna kunna matcha det fullständigt kvalificerade domän namnet (FQDN) för tjänst slut punkten till den privata slut punktens IP-adress. Konfigurera DNS-servern så att den delegerar din privata länk under domän till den privata DNS-zonen för det virtuella nätverket eller konfigurera `AppConfigInstanceA.privatelink.azconfig.io` A-posterna för med den privata slut PUNKTens IP-adress.

> [!TIP]
> När du använder en anpassad eller lokal DNS-server bör du konfigurera DNS-servern för att matcha Arkiv namnet i `privatelink` under domänen med IP-adressen för den privata slut punkten. Du kan göra detta genom att delegera under `privatelink` domänen till det virtuella nätverkets privata DNS-zon, eller konfigurera DNS-zonen på DNS-servern och lägga till DNS-posterna.

## <a name="pricing"></a>Prissättning

Att aktivera privata slut punkter kräver ett konfigurations lager för [Standard-nivå](https://azure.microsoft.com/pricing/details/app-configuration/) .  Läs mer om pris information om privata länkar i [Azures priser för privata länkar](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du skapar en privat slut punkt för ditt konfigurations lager för appar, finns i följande artiklar:

- [Skapa en privat slut punkt med hjälp av det privata länk centret i Azure Portal](../private-link/create-private-endpoint-portal.md)
- [Skapa en privat slut punkt med Azure CLI](../private-link/create-private-endpoint-cli.md)
- [Skapa en privat slut punkt med hjälp av Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

Lär dig hur du konfigurerar DNS-servern med privata slut punkter:

- [Namnmatchning för resurser i virtuella nätverk i Azure](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [DNS-konfiguration för privata slut punkter](/azure/private-link/private-endpoint-overview#dns-configuration)
