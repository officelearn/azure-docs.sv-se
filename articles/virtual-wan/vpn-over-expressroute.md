---
title: Skapa en VPN-anslutning från plats till plats via ExpressRoute privat peering i Azure Virtual WAN | Microsoft Docs
description: I den här självstudien får du lära dig hur du använder Azure Virtual WAN för att skapa en plats-till-plats-VPN-anslutning via ExpressRoute privata peering.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/11/2019
ms.author: cherylmc
Customer intent: I want to connect my on-premises networks to my VNets using S2S VPN connection over my ExpressRoute private peering using Azure Virtual WAN.
ms.openlocfilehash: 6272d6fe6f8c35c06a8121e10be2dd5a2e5512a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515036"
---
# <a name="create-a-site-to-site-vpn-connection-over-expressroute-private-peering-using-azure-virtual-wan"></a>Skapa en VPN-anslutning från plats till plats via ExpressRoute privat peering med Azure Virtual WAN

Den här artikeln visar hur du använder Virtual WAN för att upprätta en IPsec/IKE VPN-anslutning från ditt lokala nätverk till Azure över en privat peering av en ExpressRoute-krets. Detta kan ge en krypterad överföring mellan lokala nätverk och virtuella Azure-nätverk via ExpressRoute, utan att gå över det offentliga Internet eller använda offentliga IP-adresser.

## <a name="topology-and-routing"></a>Topologi och routning

I följande diagram visas ett exempel på VPN över ExpressRoute-anslutningar för privat peering:

![VPN över ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

Diagrammet visar ett nätverk i det lokala nätverket som är anslutet till Azure Hub VPN-gateway över ExpressRoute-privat peering. Anslutnings etableringen är enkel:

1. Upprätta ExpressRoute-anslutningar med en ExpressRoute-krets och privat peering
2. Upprätta en VPN-anslutning enligt beskrivningen i det här dokumentet

En viktig aspekt av den här konfigurationen är routning mellan de lokala nätverken och Azure över både ExpressRoute-och VPN-sökvägar.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Trafik från lokala nätverk till Azure

För trafik från lokala nätverk till Azure annonseras Azure-prefixen (inklusive den virtuella hubben och alla eker-virtuella nätverk som är anslutna till hubben) via både ExpressRoute privata peering BGP och VPN BGP. Detta leder till två nätverks vägar (sökvägar) gentemot Azure från de lokala nätverken. en via IPsec-skyddad sökväg och en direkt över ExpressRoute **utan** IPSec-skydd. För att se till att kryptering används för kommunikationen måste du se till att för det VPN-anslutna nätverket i diagrammet är Azure-vägarna via lokal VPN-gateway önskade över den direkta ExpressRoute-sökvägen.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Trafik från Azure till lokala nätverk

Samma krav gäller för trafiken från Azure till lokala nätverk. För att säkerställa att IPsec-sökvägen föredras över den direkta ExpressRoute-sökvägen (utan IPsec) har du två alternativ:

- Annonsera mer detaljerade prefix på VPN BGP-sessionen för det VPN-anslutna nätverket. Du kan annonsera ett större intervall som omfattar det "VPN-anslutna nätverket" över ExpressRoute privata peering, och sedan mer exakta intervall i VPN BGP-sessionen. Annonsera till exempel 10.0.0.0/16 över ExpressRoute och 10.0.1.0/24 via VPN.

- Annonsera åtskilda prefix för VPN-och ExpressRoute. Om de VPN-anslutna nätverks intervallen är åtskilda från andra ExpressRoute anslutna nätverk, kan du annonsera prefixen i VPN-och ExpressRoute BGP-sessionerna. Meddela till exempel 10.0.0.0/24 över ExpressRoute och 10.0.1.0/24 via VPN.

I båda dessa exempel skickar Azure trafik till 10.0.1.0/24 via VPN-anslutningen i stället för direkt över ExpressRoute utan VPN-skydd.

> [!WARNING]
> Om du annonserar **samma** prefix över både ExpressRoute och VPN-anslutningar, kommer Azure att **använda ExpressRoute-sökvägen direkt utan VPN-skydd**.
>

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. skapa ett virtuellt WAN-nätverk och hubb med gateways

Följande Azure-resurser och motsvarande lokala konfigurationer måste vara på plats innan du fortsätter:

1. Ett virtuellt Azure-WAN
2. En virtuell WAN-hubb med en [ExpressRoute-Gateway](virtual-wan-expressroute-portal.md) och en [VPN-gateway](virtual-wan-site-to-site-portal.md)

Läs om hur du skapar en [ExpressRoute-Association med Azure Virtual WAN](virtual-wan-expressroute-portal.md) för att skapa ett virtuellt WAN-nätverk och en hubb med en ExpressRoute-associering och [skapa en plats-till-plats-anslutning med Azure Virtual WAN](virtual-wan-site-to-site-portal.md) för att skapa en VPN-anslutning Gateway i det virtuella WAN-nätverket.

## <a name="site"></a>2. skapa en plats för det lokala nätverket

Plats resursen är samma som icke-ExpressRoute VPN-platser för virtuellt WAN. Den nyckel som är medveten om att IP-adressen för den lokala VPN-enheten kan nu vara antingen en privat IP-adress eller en offentlig IP-adress i det lokala nätverket som kan kommas åt via ExpressRoute privat peering som skapades i steg 1.

> [!NOTE]
> Den lokala VPN-enhetens IP-adress måste ingå i de adressprefix som annonseras till den virtuella WAN-hubben via Azure ExpressRoute Private peering.
>

1. Navigera till Azure Portal i webbläsaren. Klicka på det WAN som du skapade. På WAN-sidan under **anslutning**, klickar du på **VPN-platser** för att öppna sidan VPN-platser.

2. På sidan **VPN-platser** klickar du på **+ Skapa webbplats**.

3. Fyll i följande fält på sidan **Skapa webbplats**:

   * **Prenumeration** – Kontrollera prenumerationen.
   * **Resurs grupp** – Välj eller skapa den resurs grupp som du vill använda.
   * **Region** – Azure-regionen för VPN-webbplatsens resurs.
   * **Namn** – namnet som du vill referera till din lokala plats.
   * **Enhets leverantör** – leverantören av den lokala VPN-enheten.
   * **Border Gateway Protocol** -Välj "Aktivera" om ditt lokala nätverk använder BGP.
   * **Privat adressutrymme** – Det här är adressutrymmet som finns på din lokala plats. Trafik som är avsedd för det här adress utrymmet dirigeras till det lokala nätverket via VPN-gatewayen.
   * **Hubbar** – Välj ett eller flera hubbar för att ansluta den här VPN-platsen. De valda hubbarna måste ha VPN-gatewayer redan skapade.

4. Klicka på **Nästa: länkar >** för inställningarna för VPN-länken:

   * **Länknamn** – namnet som du vill referera till den här anslutningen.
   * **Providernamn** – namnet på Internet tjänst leverantören för den här platsen. När det gäller ExpressRoute lokalt nätverk är namnet på ExpressRoute-tjänstprovidern.
   * **Hastighet** – Internet tjänst länkens hastighet eller ExpressRoute-kretsen.
   * **IP-adress** – den offentliga IP-adressen för VPN-enheten som finns på din lokala plats. Eller, när det gäller ExpressRoute lokalt, den privata IP-adressen för VPN-enheten via ExpressRoute.

   Om BGP är aktiverat kommer det att gälla alla anslutningar som skapats för den här platsen i Azure. Konfiguration av BGP på ett virtuellt WAN-nätverk motsvarar att konfigurera BGP på en Azure VPN-gateway. Din lokala BGP-peer-adress *får inte* vara samma som IP-adressen för VPN-enheten eller det virtuella nätverkets adress utrymme för VPN-platsen. Använd en annan IP-adress på VPN-enheten för din BGP-peer-IP. Det kan vara en adress som tilldelats till loopback-gränssnittet på enheten. Det kan dock *inte* vara en APIPA (169,254. *x*. *x*). Ange den adressen i den motsvarande lokala nätverksgateway som representerar platsen. För BGP-krav, se [om BGP med Azure VPN gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

5. Klicka på **Nästa: granska + skapa >** för att kontrol lera inställnings värden och skapa VPN-platsen. Om du har valt **hubbar** för anslutning upprättas anslutningen mellan det lokala nätverket och HUBBENS VPN-gateway.

## <a name="hub"></a>3. uppdatera inställningen för VPN-anslutning för att använda ExpressRoute

När du har skapat VPN-platsen och anslutit till hubben använder du följande steg för att konfigurera anslutningen till att använda ExpressRoute-privat peering:

1. Gå tillbaka till sidan virtuell WAN-resurs och klicka på NAV resursen. Eller navigera från VPN-platsen till det anslutna hubben.

2. Under **anslutning**klickar du på **VPN (plats-till-plats)**

3. Klicka på "..." på VPN-platsen via ExpressRoute och välj "**Redigera VPN-anslutning till denna hubb**"

4. Välj "Ja" på**Använd Azures privata IP-adress**. Inställningen konfigurerar hubbens VPN-gateway att använda privata IP-adresser inom hubbens adress intervall på gatewayen för den här anslutningen, i stället för offentliga IP-adresser. Detta säkerställer att trafiken från det lokala nätverket passerar de ExpressRoute privata peering-vägarna i stället för att använda det offentliga Internet för den här VPN-anslutningen. Följande skärm bild visar inställnings fönstret.

   ![Inställning för VPN-anslutning](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
5. Klicka på **Spara**.

När den har sparats använder Hub VPN-gatewayen de privata IP-adresserna på VPN-gatewayen för att upprätta IPsec/IKE-anslutningarna med den lokala VPN-enheten över ExpressRoute.

## <a name="associate"></a>4. Hämta hubben för VPN-gatewayens privata IP-adresser

Hämta VPN-enhetens konfiguration för att hämta de privata IP-adresserna för hubbens VPN-gateway. Dessa krävs för att konfigurera den lokala VPN-enheten.

1. På sidan för hubben klickar du på **VPN (plats-till-plats)** under **anslutning**

2. Längst upp på sidan Översikt klickar du på **Hämta VPN-konfiguration**. Azure skapar ett lagrings konto i resurs gruppen "Microsoft-Network-[location]", där plats är platsen för WAN. När du har tillämpat konfigurationen på VPN-enheterna kan du ta bort det här lagringskontot.

3. När filen har skapats klickar du på länken för att ladda ned den.

4. Tillämpa konfigurationen på VPN-enheten.

### <a name="understanding-the-vpn-device-configuration-file"></a>Förstå konfigurationsfilen för VPN-enheten

Konfigurationsfilen för enheten innehåller de inställningarna du ska använda när du konfigurerar den lokala VPN-enheten. När du visar den här filen ser du följande information:

* **vpnSiteConfiguration** I det här avsnittet anges enhetsinformation konfigurerad som en plats som ansluter till det virtuella WAN-nätverket. Det omfattar namn och offentlig IP-adress för grenenheten.
* **vpnSiteConnections –** Det här avsnittet innehåller information om följande inställningar:

    * **adressutrymme** för de virtuella hubbarnas virtuella nätverk<br/>Exempel:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * **adressutrymme** för de virtuella nätverk som är anslutna till hubben<br>Exempel:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * **IP-adresser** till vpngateway för den virtuella hubben. Eftersom varje anslutning av vpngateway består av två tunnlar i en aktiv-aktiv-konfiguration ser du båda IP-adresserna som anges i den här filen. I det här exemplet visas "Instance0" och "instance1" för varje plats och de är privata IP-adresser i stället för offentliga IP-adresser.<br>Exempel:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * **Vpngateway anslutnings konfigurations information** som BGP, i förväg delad nyckel osv. PSK är den i förväg delade nyckeln som skapas automatiskt åt dig. Du kan alltid redigera anslutningen på översiktssidan för en anpassad PSK.
  
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

* Instruktionerna på VPN-enhetssidan inte är skrivna för virtuella WAN-nätverk, men du kan använda värdena för de virtuella WAN-nätverken från konfigurationsfilen och manuellt konfigurera VPN-enheten. 
* De nedladdningsbara skripten för enhetskofiguration till VPN-gatewayen fungerar inte för virtuellt WAN. Konfigurationerna skiljer sig åt.
* Ett nytt virtuellt WAN-nätverk har stöd för både IKEv1 och IKEv2.
* Virtuellt WAN fungerar endast med routningsbaserade VPN-enheter och enhetsanvisningar.

## <a name="viewwan"></a>5. Visa ditt virtuella WAN-nätverk

1. Navigera till det virtuella WAN-nätverket.

2. Varje punkt på kartan på sidan Översikt motsvarar en hubb. För muspekaren över en punkt så visas en sammanfattning av hubbens hälsotillstånd.

3. I avsnittet om hubbar och anslutningar kan du visa hubbstatus, plats, region, VPN-anslutningsstatus och byte in och ut.

## <a name="viewhealth"></a>6. Visa din resurs hälsa

1. Navigera till ditt WAN.

2. Öppna WAN-sidan. I avsnittet **SUPPORT + felsökning** klickar du på **Hälsa** och visar resursen.

## <a name="connectmon"></a>7. övervaka en anslutning

Skapa en anslutning för att övervaka kommunikation mellan en virtuell Azure-dator och en fjärrplats. Information om hur du ställer in en anslutningsövervakare finns i dokumentationen om att [övervaka nätverkskommunikation](~/articles/network-watcher/connection-monitor.md). Källfältet är IP för den virtuella datorn i Azure och mål-IP är plats-IP-adressen.

## <a name="cleanup"></a>8. Rensa resurser

När du inte längre behöver dessa resurser kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser den innehåller. Ersätt myResourceGroup med namnet på resursgruppen och kör följande PowerShell-kommando:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln hjälper dig att skapa en VPN-anslutning via privat ExpressRoute-peering med hjälp av virtuellt WAN. Mer information om virtuella WAN och andra relaterade funktioner finns på [översikts sidan för Virtual WAN](virtual-wan-about.md) .
