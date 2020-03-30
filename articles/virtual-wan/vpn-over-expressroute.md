---
title: 'Konfigurera ExpressRoute-kryptering: IPsec över ExpressRoute för Virtuellt AZURE WAN'
description: I den här självstudien kan du lära dig hur du använder Azure Virtual WAN för att skapa en VPN-anslutning från plats till plats via ExpressRoute-privat peering.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: b1e6305d142530ab19849f61f12a122d0c6434aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059308"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>ExpressRoute-kryptering: IPsec över ExpressRoute för VirtuellT WAN

Den här artikeln visar hur du använder Azure Virtual WAN för att upprätta en IPsec/IKE VPN-anslutning från ditt lokala nätverk till Azure via den privata peeringen av en Azure ExpressRoute-krets. Den här tekniken kan ge en krypterad överföring mellan lokala nätverk och virtuella Azure-nätverk via ExpressRoute, utan att gå över det offentliga internet eller använda offentliga IP-adresser.

## <a name="topology-and-routing"></a>Topologi och routning

Följande diagram visar ett exempel på VPN-anslutning via ExpressRoute privat peering:

![VPN över ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

Diagrammet visar ett nätverk inom det lokala nätverket som är anslutet till Azure hub VPN-gatewayen via ExpressRoute-privat peering. Anslutningsanläggningen är enkel:

1. Upprätta ExpressRoute-anslutning med en ExpressRoute-krets och privat peering.
2. Upprätta VPN-anslutningen enligt beskrivningen i den här artikeln.

En viktig aspekt av den här konfigurationen är routning mellan lokala nätverk och Azure över både ExpressRoute- och VPN-sökvägarna.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Trafik från lokala nätverk till Azure

För trafik från lokala nätverk till Azure annonseras Azure-prefixen (inklusive det virtuella navet och alla eker virtuella nätverk som är anslutna till navet) via både ExpressRoute private peering BGP och VPN BGP. Detta resulterar i två nätverksvägar (sökvägar) mot Azure från de lokala nätverken:

- En över den IPsec-skyddade sökvägen
- En direkt över ExpressRoute *utan* IPsec skydd 

Om du vill tillämpa kryptering på kommunikationen måste du se till att Azure-vägarna via lokal VPN-gateway är att föredra framför den direkta ExpressRoute-sökvägen för det VPN-anslutna nätverket i diagrammet.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Trafik från Azure till lokala nätverk

Samma krav gäller för trafiken från Azure till lokala nätverk. Om du vill vara säkra på att IPsec-sökvägen är att föredra framför den direkta ExpressRoute-sökvägen (utan IPsec) har du två alternativ:

- Annonsera mer specifika prefix på VPN BGP-sessionen för det VPN-anslutna nätverket. Du kan annonsera ett större intervall som omfattar VPN-anslutna nätverk via ExpressRoute privat peering, sedan mer specifika intervall i VPN BGP-sessionen. Annonsera till exempel 10.0.0.0/16 via ExpressRoute och 10.0.1.0/24 via VPN.

- Annonsera osammanhängande prefix för VPN och ExpressRoute. Om vpn-anslutna nätverksintervall är osammanhängande från andra ExpressRoute-anslutna nätverk kan du annonsera prefixen i VPN- respektive ExpressRoute BGP-sessionerna. Annonsera till exempel 10.0.0.0/24 via ExpressRoute och 10.0.1.0/24 via VPN.

I båda dessa exempel skickar Azure trafik till 10.0.1.0/24 via VPN-anslutningen i stället för direkt via ExpressRoute utan VPN-skydd.

> [!WARNING]
> Om du annonserar *samma* prefix via både ExpressRoute- och VPN-anslutningar använder Azure Sökvägen ExpressRoute direkt utan VPN-skydd.
>

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. Skapa ett virtuellt WAN och nav med gateways

Följande Azure-resurser och motsvarande lokala konfigurationer måste finnas på plats innan du fortsätter:

- Ett virtuellt WAN i Azure
- En virtuell WAN-hubb med en [ExpressRoute-gateway](virtual-wan-expressroute-portal.md) och en [VPN-gateway](virtual-wan-site-to-site-portal.md)

Steg för att skapa ett virtuellt AZURE-WAN och ett nav med en ExpressRoute-association finns i [Skapa en ExpressRoute-association med Azure Virtual WAN](virtual-wan-expressroute-portal.md). Steg för att skapa en VPN-gateway i det virtuella WAN finns i [Skapa en plats-till-plats-anslutning med Azure Virtual WAN](virtual-wan-site-to-site-portal.md).

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. Skapa en plats för det lokala nätverket

Platsresursen är samma som icke-ExpressRoute VPN-platser för ett virtuellt WAN. IP-adressen för den lokala VPN-enheten kan nu vara antingen en privat IP-adress eller en offentlig IP-adress i det lokala nätverket som kan nås via ExpressRoute privat peering som skapats i steg 1.

> [!NOTE]
> IP-adressen för den lokala VPN-enheten *måste* vara en del av adressprefixen som annonseras till den virtuella WAN-hubben via Azure ExpressRoute-privat peering.
>

1. Gå till Azure-portalen i webbläsaren. 
1. Markera det WAN som du har skapat. Välj **VPN-platser**under **Anslutning**på WAN-sidan.
1. På sidan **VPN-platser** väljer du **+Skapa webbplats**.
1. Fyll i följande fält på sidan **Skapa webbplats**:
   * **Prenumeration**: Verifiera prenumerationen.
   * **Resursgrupp:** Markera eller skapa den resursgrupp som du vill använda.
   * **Region**: Ange Azure-regionen för VPN-platsresursen.
   * **Namn**: Ange det namn som du vill referera till din lokala webbplats.
   * **Enhetsleverantör**: Ange leverantören av den lokala VPN-enheten.
   * **Border Gateway Protocol**: Välj "Aktivera" om ditt lokala nätverk använder BGP.
   * **Privat adressutrymme**: Ange det IP-adressutrymme som finns på din lokala webbplats. Trafik som är avsedd för det här adressutrymmet dirigeras till det lokala nätverket via VPN-gatewayen.
   * **Hubbar:** Välj en eller flera hubbar för att ansluta den här VPN-platsen. De valda hubbar måste ha VPN-gateways redan skapade.
1. Välj **Nästa: Länkar >** för VPN-länkinställningarna:
   * **Länknamn**: Det namn som du vill referera till den här anslutningen med.
   * **Leverantörens namn:** Namnet på internetleverantören för den här webbplatsen. För ett ExpressRoute lokalt nätverk är det namnet på ExpressRoute-tjänsteleverantören.
   * **Hastighet**: Internet-länkens hastighet eller ExpressRoute-kretsen.
   * **IP-adress**: Den offentliga IP-adressen för DEN VPN-enhet som finns på din lokala webbplats. Eller, för ExpressRoute lokalt, det är den privata IP-adressen för VPN-enheten via ExpressRoute.

   Om BGP är aktiverat gäller det för alla anslutningar som skapas för den här platsen i Azure. Konfigurera BGP på ett virtuellt WAN motsvarar att konfigurera BGP på en Azure VPN-gateway. 
   
   Din lokala BGP-peer-adress *får inte* vara samma som IP-adressen för din VPN till enheten eller vpn-platsens virtuella nätverksadressutrymme. Använd en annan IP-adress på VPN-enheten för din BGP-peer-IP. Det kan vara en adress som tilldelats till loopback-gränssnittet på enheten. Det kan dock *inte* vara ett APIPA (169.254.* x*. *x*) adress. Ange den här adressen i motsvarande lokala nätverksgateway som representerar platsen. För BGP-förutsättningar finns i [Om BGP med Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

1. Välj **Nästa: Granska + skapa >** för att kontrollera inställningsvärdena och skapa VPN-platsen. Om du valde Hubbar att ansluta **upprättas** anslutningen mellan det lokala nätverket och nav-VPN-gatewayen.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. Uppdatera VPN-anslutningsinställningen för att använda ExpressRoute

När du har skapat VPN-platsen och anslutit till navet följer du följande steg för att konfigurera anslutningen så att den använder ExpressRoute-privat peering:

1. Gå tillbaka till den virtuella WAN-resurssidan och välj navresursen. Eller navigera från VPN-platsen till den anslutna hubben.
1. Under **Anslutning**väljer du **VPN (Plats-till-plats)**.
1. Välj ellips (**...**) på VPN-webbplatsen över ExpressRoute och välj **Redigera VPN-anslutning till det här navet**.
1. För **Användning av Azure Private IP-adress**väljer du **Ja**. Inställningen konfigurerar nav-VPN-gatewayen för att använda privata IP-adresser inom navadressintervallet på gatewayen för den här anslutningen i stället för de offentliga IP-adresserna. Detta säkerställer att trafiken från det lokala nätverket passerar ExpressRoute privata peering-sökvägar i stället för att använda det offentliga internet för den här VPN-anslutningen. Följande skärmbild visar inställningen.

   ![Inställning för användning av en privat IP-adress för VPN-anslutningen](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
1. Välj **Spara**.

När du har sparat ändringarna använder vpn-hubbgatewayen de privata IP-adresserna på VPN-gatewayen för att upprätta IPsec/IKE-anslutningarna med den lokala VPN-enheten via ExpressRoute.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. Hämta de privata IP-adresserna för nav-VPN-gatewayen

Ladda ned VPN-enhetskonfigurationen för att hämta de privata IP-adresserna för nav-VPN-gatewayen. Du behöver dessa adresser för att konfigurera den lokala VPN-enheten.

1. På sidan för hubben väljer du **VPN (Plats till plats)** under **Anslutning**.
1. Högst upp på **sidan Översikt** väljer du Hämta VPN **Config**. 

   Azure skapar ett lagringskonto i resursgruppen "microsoft-network-[location]," där *platsen* är platsen för WAN. När du har tillämpat konfigurationen på dina VPN-enheter kan du ta bort det här lagringskontot.
1. När filen har skapats väljer du länken för att hämta den.
1. Tillämpa konfigurationen på VPN-enheten.

### <a name="vpn-device-configuration-file"></a>Konfigurationsfil för VPN-enhet

Enhetskonfigurationsfilen innehåller de inställningar som ska användas när du konfigurerar om din lokala VPN-enhet. När du visar den här filen ser du följande information:

* **vpnSiteConfiguration**: Det här avsnittet betecknar enhetsinformationen som konfigureras som en webbplats som ansluter till det virtuella WAN.vpnSiteConfiguration : This section betecknar enhetsinformationen som konfigureras som en plats som ansluter till det virtuella WAN.vpnSiteConfiguration : This section betecknar enhetsinformationen som konfigureras som en webbplats som ansluter till det virtuella WAN.vpn: Den innehåller namn och offentlig IP-adress för grenenheten.
* **vpnSiteConnections**: Det här avsnittet innehåller information om följande inställningar:

    * Adressutrymme för det virtuella navets virtuella nätverk.<br/>Exempel:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Adressutrymme för de virtuella nätverk som är anslutna till navet.<br>Exempel:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * IP-adresser till den virtuella hubbens VPN-gateway. Eftersom varje anslutning till VPN-gatewayen består av två tunnlar i aktiv aktiv konfiguration visas båda IP-adresserna i den här filen. I det här `Instance0` exemplet `Instance1` ser och för varje plats, och de är privata IP-adresser i stället för offentliga IP-adresser.<br>Exempel:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * Konfigurationsinformation för VPN-gatewayanslutningen, till exempel BGP och fördelad nyckel. Den fördelade nyckeln genereras automatiskt åt dig. Du kan alltid redigera anslutningen på sidan **Översikt** för en anpassad fördelad nyckel.
  
### <a name="example-device-configuration-file"></a>Konfigurationsfil för exempelenhet

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>Konfigurera en VPN-enhet

Om du behöver anvisningar för att konfigurera enheten kan du använda instruktionerna på sidan om [konfigurationsskript till VPN-enheter](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) med följande förbehåll:

* Instruktionerna på VPN-enhetssidan är inte skrivna för ett virtuellt WAN. Men du kan använda de virtuella WAN-värdena från konfigurationsfilen för att manuellt konfigurera din VPN-enhet. 
* De nedladdningsbara enhetskonfigurationsskripten som är för VPN-gatewayen fungerar inte för det virtuella WAN:n, eftersom konfigurationen är annorlunda.
* Ett nytt virtuellt WAN kan stödja både IKEv1 och IKEv2.
* Ett virtuellt WAN kan endast använda ruttbaserade VPN-enheter och enhetsinstruktioner.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. Visa ditt virtuella WAN

1. Gå till det virtuella WAN.Go to the virtual WAN.
1. På sidan **Översikt** representerar varje punkt på kartan ett nav.
1. I avsnittet **Nav och anslutningar** kan du visa hubb-, plats-, region- och VPN-anslutningsstatus. Du kan också visa byte in och ut.

## <a name="7-monitor-a-connection"></a><a name="connectmon"></a>7. Övervaka en anslutning

Skapa en anslutning för att övervaka kommunikationen mellan en virtuell Azure-dator (VM) och en fjärrplats. Information om hur du ställer in en anslutningsövervakare finns i dokumentationen om att [övervaka nätverkskommunikation](~/articles/network-watcher/connection-monitor.md). Källfältet är VM-IP i Azure och mål-IP är platsens IP.

## <a name="8-clean-up-resources"></a><a name="cleanup"></a>8. Rensa resurser

När du inte längre behöver dessa resurser kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen och alla resurser som den innehåller. Kör följande PowerShell-kommando `myResourceGroup` och ersätt med namnet på resursgruppen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln hjälper dig att skapa en VPN-anslutning via ExpressRoute privat peering med hjälp av Virtual WAN. Mer information om Virtuellt WAN och relaterade funktioner finns i [översikten över virtuellt WAN](virtual-wan-about.md).
