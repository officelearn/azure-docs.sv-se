---
title: Konfigurera ett S2S-VPN (Site-to-Site) för användning med Azure Files | Microsoft-dokument
description: Konfigurera ett S2S-VPN (Site-to-Site) för användning med Azure-filer
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ae3d38d92990d7a1af4146c25b017286ebd29352
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061027"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Konfigurera ett VPN från plats till plats för användning med Azure-filer
Du kan använda en S2S-anslutning (Site-to-Site) för att montera dina Azure-filresurser via SMB från ditt lokala nätverk, utan att öppna port 445. Du kan konfigurera en plats-till-plats-VPN med [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md), som är en Azure-resurs som erbjuder VPN-tjänster, och distribueras i en resursgrupp tillsammans med lagringskonton eller andra Azure-resurser.

![Ett topologidiagram som illustrerar topologin för en Azure VPN-gateway som ansluter en Azure-filresurs till en lokal plats med hjälp av en S2S VPN](media/storage-files-configure-s2s-vpn/s2s-topology.png)

Vi rekommenderar starkt att du läser [Azure Files nätverksöversikt](storage-files-networking-overview.md) innan du fortsätter med den här artikeln för en fullständig diskussion om nätverksalternativen som är tillgängliga för Azure-filer.

Artikeln beskriver stegen för att konfigurera en plats-till-plats-VPN för att montera Azure-filresurser direkt lokalt. Om du vill dirigera synkroniseringstrafik för Azure File Sync via ett VPN från plats till plats läser du [konfigurera proxy- och brandväggsinställningar för Azure File Sync](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Krav
- En Azure-filresurs som du vill montera lokalt. Azure-filresurser distribueras i lagringskonton, som är hanteringskonstruktioner som representerar en delad lagringspool där du kan distribuera flera filresurser, samt andra lagringsresurser, till exempel blob-behållare eller köer. Du kan läsa mer om hur du distribuerar Azure-filresurser och lagringskonton i [Skapa en Azure-filresurs](storage-how-to-create-file-share.md).

- En privat slutpunkt för lagringskontot som innehåller den Azure-filresurs som du vill montera lokalt. Mer information om hur du skapar en privat slutpunkt finns i [Konfigurera slutpunkter för Azure Files-nätverk](storage-files-networking-endpoints.md?tabs=azure-portal). 

- En nätverksinstallation eller -server i ditt lokala datacenter som är kompatibelt med Azure VPN Gateway. Azure Files är agnostiker för den lokala nätverksinstallation som valts, men Azure VPN Gateway har en [lista över testade enheter](../../vpn-gateway/vpn-gateway-about-vpn-devices.md). Olika nätverksenheter erbjuder olika funktioner, prestandaegenskaper och hanteringsfunktioner, så tänk på dessa när du väljer en nätverksanordning.

    Om du inte har någon befintlig nätverksinstallation innehåller Windows Server en inbyggd serverroll, routning och fjärråtkomst (RRAS), som kan användas som lokal nätverksinstallation. Mer information om hur du konfigurerar Routning och fjärråtkomst i Windows Server finns i [RAS Gateway](https://docs.microsoft.com/windows-server/remote/remote-access/ras-gateway/ras-gateway).

## <a name="add-storage-account-to-vnet"></a>Lägga till lagringskonto i VNet
I Azure-portalen navigerar du till lagringskontot som innehåller den Azure-filresurs som du vill montera lokalt. Välj posten **Brandväggar och virtuella nätverk** i innehållsförteckningen för lagringskontot. Om du inte har lagt till ett virtuellt nätverk i ditt lagringskonto när du skapade det, bör den resulterande rutan ha knappen **Tillåt åtkomst från** radio för alla **nätverk** markerat.

Om du vill lägga till ditt lagringskonto i önskat virtuellt nätverk väljer du **Valda nätverk**. Under underrubriken **Virtuella nätverk** klickar du antingen på + Lägg **till befintligt virtuellt nätverk** eller **+Lägg till nytt virtuellt nätverk** beroende på önskat tillstånd. Om du skapar ett nytt virtuellt nätverk skapas en ny Azure-resurs. Den nya eller befintliga virtuella resursen behöver inte finnas i samma resursgrupp eller prenumeration som lagringskontot, men den måste vara i samma region som lagringskontot och resursgruppen och prenumerationen som du distribuerar ditt virtuella nätverk till måste matcha det du vill distribuera din VPN-gateway till. 

![Skärmbild av Azure-portalen som ger möjlighet att lägga till ett befintligt eller nytt virtuellt nätverk i lagringskontot](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Om du lägger till befintliga virtuella nätverk blir du ombedd att välja ett eller flera undernät till det virtuella nätverket som lagringskontot ska läggas till i. Om du väljer ett nytt virtuellt nätverk skapar du ett undernät som en del av skapandet av det virtuella nätverket och du kan lägga till fler senare via den resulterande Azure-resursen för det virtuella nätverket.

Om du inte har lagt till ett lagringskonto i din prenumeration tidigare måste slutpunkten för Microsoft.Storage-tjänsten läggas till i det virtuella nätverket. Detta kan ta lite tid, och tills den här åtgärden har slutförts kommer du inte att kunna komma åt Azure-filresurserna inom det lagringskontot, inklusive via VPN-anslutningen. 

## <a name="deploy-an-azure-vpn-gateway"></a>Distribuera en Azure VPN-gateway
I innehållsförteckningen för Azure-portalen väljer du **Skapa en ny resurs** och söker efter virtuell *nätverksgateway*. Din virtuella nätverksgateway måste finnas i samma prenumeration, Azure-region och resursgrupp som det virtuella nätverket som du distribuerade i föregående steg (observera att resursgruppen väljs automatiskt när det virtuella nätverket plockas). 

För att distribuera en Azure VPN-gateway måste du fylla i följande fält:

- **Namn**: Namnet på Azure-resursen för VPN-gatewayen. Det här namnet kan vara vilket namn som helst som du tycker är användbart för din hantering.
- **Region**: Den region där VPN-gatewayen ska distribueras.
- **Gateway typ**: För att distribuera en plats-till-plats VPN, måste du välja **VPN**.
- **VPN-typ:** Du kan välja antingen *Ruttbaserad** eller **Principbaserad** beroende på din VPN-enhet. Ruttbaserade VPN stöder IKEv2, medan principbaserade VPN endast stöder IKEv1. Mer information om de två typerna av VPN-gateways finns i [Om principbaserade och ruttbaserade VPN-gateways](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about)
- **SKU**: SKU styr antalet tillåtna tunneler från plats till plats och önskad prestanda för VPN. Om du vill välja lämplig SKU för ditt användningsfall läser du [gateway-SKU-listan.](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) SKU för VPN-gatewayen kan ändras senare om det behövs.
- **Virtuellt nätverk:** Det virtuella nätverk som du skapade i föregående steg.
- **Offentlig IP-adress:** IP-adressen för VPN Gateway som kommer att exponeras för internet. Troligtvis måste du skapa en ny IP-adress, men du kan också använda en befintlig oanvänd IP-adress om det är lämpligt. Om du väljer att **skapa ny**skapas en ny IP-adress Azure-resurs i samma resursgrupp som VPN Gateway och det **offentliga IP-adressnamnet** kommer att vara namnet på den nyligen skapade IP-adressen. Om du väljer **Använd befintlig**måste du välja den befintliga oanvända IP-adressen.
- **Aktivera aktivt aktivt läge:** Välj Bara **Aktiverad** om du skapar en aktiv-aktiv gateway-konfiguration, annars lämnar **inaktiverad markerad.** Mer information om aktivt aktivt läge finns i Anslutning [mellan lokala och tillgängliga mellan lokala och virtuella nätverk](../../vpn-gateway/vpn-gateway-highlyavailable.md).
- **Konfigurera BGP ASN:** Välj Endast **Aktiverad** om konfigurationen specifikt kräver den här inställningen. Mer information om den här inställningen finns i [Om BGP med Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).

Välj **Granska + skapa** för att skapa VPN-gatewayen. Det kan ta upp till 45 minuter att skapa och distribuera en VPN-gateway.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Skapa en lokal nätverksgateway för din lokala gateway 
En lokal nätverksgateway är en Azure-resurs som representerar din lokala nätverksinstallation. I innehållsförteckningen för Azure-portalen väljer du **Skapa en ny resurs** och söker efter lokal *nätverksgateway*. Den lokala nätverksgatewayen är en Azure-resurs som kommer att distribueras tillsammans med ditt lagringskonto, virtuellt nätverk och VPN-gateway, men som inte behöver finnas i samma resursgrupp eller prenumeration som lagringskontot. 

För att distribuera den lokala nätverksgatewayresursen måste du fylla i följande fält:

- **Namn**: Namnet på Azure-resursen för den lokala nätverksgatewayen. Det här namnet kan vara vilket namn som helst som du tycker är användbart för din hantering.
- **IP-adress**: Den offentliga IP-adressen för din lokala gateway lokalt.
- **Adressutrymme:** Adressintervallen för det nätverk som den lokala nätverksgatewayen representerar. Du kan lägga till flera adressutrymmesintervall, men se till att de områden som du anger här inte överlappar intervall för andra nätverk som du vill ansluta till. 
- **Konfigurera BGP-inställningar:** Konfigurera endast BGP-inställningar om konfigurationen kräver den här inställningen. Mer information om den här inställningen finns i [Om BGP med Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).
- **Prenumeration**: Den önskade prenumerationen. Detta behöver inte matcha prenumerationen som används för VPN Gateway eller lagringskontot.
- **Resursgrupp**: Önskad resursgrupp. Detta behöver inte matcha resursgruppen som används för VPN-gatewayen eller lagringskontot.
- **Plats**: Azure-regionen som den lokala nätverksgatewayresursen ska skapas i. Detta bör matcha den region som du valde för VPN-gatewayen och lagringskontot.

Välj **Skapa** om du vill skapa den lokala nätverksgatewayresursen.  

## <a name="configure-on-premises-network-appliance"></a>Konfigurera lokal nätverksinstallation
Vilka steg du ska konfigurera den lokala nätverksinstallationen beror på vilken nätverksinstallation som organisationen har valt. Beroende på vilken enhet din organisation har valt kan [listan över testade enheter](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) ha en länk till enhetsleverantörens instruktioner för att konfigurera med Azure VPN Gateway.

## <a name="create-the-site-to-site-connection"></a>Skapa anslutning mellan webbplats och plats
Om du vill slutföra distributionen av en S2S VPN måste du skapa en anslutning mellan den lokala nätverksinstallationen (representerad av den lokala nätverksgatewayresursen) och VPN-gatewayen. För att göra detta navigerar du till VPN-gatewayen som du skapade ovan. Välj Anslutningar i **innehållsförteckningen**för VPN-gatewayen och klicka på **Lägg till**. Det resulterande **fönstret Lägg till anslutning** kräver följande fält:

- **Namn**: Namnet på anslutningen. En VPN-gateway kan vara värd för flera anslutningar, så välj ett namn som är användbart för din hantering som kommer att skilja just den här anslutningen.
- **Anslutningstyp**: Sedan den här S2S-anslutningen väljer du **Plats till plats (IPSec)** i listrutan.
- **Virtuell nätverksgateway**: Det här fältet väljs automatiskt till den VPN-gateway som du gör anslutningen till och kan inte ändras.
- **Lokal nätverksgateway:** Det här är den lokala nätverksgateway som du vill ansluta till din VPN-gateway. Den resulterande markeringsfönstret bör ha namnet på den lokala nätverksgateway som du skapade ovan.
- **Delad nyckel (PSK):** En blandning av bokstäver och siffror som används för att upprätta kryptering för anslutningen. Samma delade nyckel måste användas i både det virtuella nätverket och lokala nätverksgatewayer. Om gateway-enheten inte tillhandahåller en sådan kan du göra en här och ge den till enheten.

Välj **OK** för att skapa anslutningen. Du kan kontrollera att anslutningen har gjorts via sidan **Anslutningar.**

## <a name="mount-azure-file-share"></a>Montera Azure-filresurs 
Det sista steget i att konfigurera en S2S VPN är att verifiera att det fungerar för Azure-filer. Du kan göra detta genom att montera din Azure-filresurs lokalt med ditt önskade operativsystem. Se instruktionerna för att montera av OS här:

- [Windows](storage-how-to-use-files-windows.md)
- [Macos](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Se även
- [Översikt över Azure Files-nätverk](storage-files-networking-overview.md)
- [Konfigurera ett P2S-VPN (Point-to-Site) i Windows för användning med Azure-filer](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurera ett P2S-VPN (Point-to-Site) på Linux för användning med Azure-filer](storage-files-configure-p2s-vpn-linux.md)