---
title: 'Virtuellt AZURE WAN: Skapa anslutningar från plats till plats'
description: I den här självstudien förklarar vi hur du skapar en plats-till-plats-VPN-anslutning till Azure med Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: b4278cb2e8c5152f522258a37c37acda5efbacf8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239688"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Självstudie: Skapa en plats-till-plats-anslutning med Azure Virtual WAN

Här förklarar vi hur du ansluter resurser i Azure via en IPsec/IKE (IKEv1 och IKEv2) VPN-anslutning med Virtual WAN. Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Mer information om virtuella WAN-nätverk finns i [översikten om virtuellt WAN](virtual-wan-about.md).

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa ett virtuellt WAN
> * Skapa en hubb
> * Skapa en plats
> * Ansluta en webbplats till ett nav
> * Ansluta en VPN-plats till ett nav
> * Ansluta ett virtuellt nätverk till en hubb
> * Ladda ned en konfigurationsfil
> * Visa virtuellt WAN

> [!NOTE]
> Om du har många webbplatser är det vanligast att använda en [virtuellt WAN-partner](https://aka.ms/virtualwan) för att skapa den här konfigurationen. Du kan dock skapa den här konfigurationen själv om du har erfarenhet av att arbeta med nätverk och är skicklig på konfigurera din egen VPN-enhet.
>

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan.png)

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Du har ett virtuellt nätverk som du vill ansluta till. Kontrollera att inget av undernäten i dina lokala nätverk överlappar de virtuella nätverk som du vill ansluta till. Information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i [Snabbstart](../virtual-network/quick-create-portal.md).

* Det virtuella nätverket har inga virtuella nätverksgateways. Om ditt virtuella nätverk har en gateway (antingen VPN eller ExpressRoute) måste du ta bort alla gateways. Den här konfigurationen kräver att virtuella nätverk ansluts i stället till virtual WAN-hubbgatewayen.

* Hämta ett IP-adressintervall för din hubbregion. Navet är ett virtuellt nätverk som skapas och används av Virtual WAN. Det adressintervall som du anger för navet kan inte överlappa något av dina befintliga virtuella nätverk som du ansluter till. Det får inte heller överlappa det adressintervall som du ansluter till lokalt. Om du inte känner till IP-adressintervallen i din lokala nätverkskonfiguration, samordna med någon som kan ange dessa uppgifter åt dig.

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Skapa ett virtuellt WAN

Öppna en webbläsare, navigera till Azure Portal och logga in med ditt Azure-konto.

1. Navigera till sidan Virtuellt WAN. Klicka på **+Skapa en resurs** i portalen. Skriv **Virtuellt WAN** i sökrutan och välj Retur.
2. Välj **Virtuellt WAN** i resultatet. På sidan Virtuellt WAN klickar du på **Skapa** för att öppna sidan Skapa WAN.
3. Fyll i följande fält på fliken **Skapa** WAN på sidan **Skapa WAN:**

   ![Virtuellt WAN](./media/virtual-wan-site-to-site-portal/vwan.png)

   * **Prenumeration** – Välj vilken prenumeration du vill använda.
   * **Resursgrupp** - Skapa nytt eller använd befintligt.
   * **Plats för resursgrupp** – Välj en resursplats i listrutan. Ett WAN är en global resurs och är inte kopplad till en viss region. Du måste dock välja en region för att lättare att hantera och leta upp WAN-resursen som du skapar.
   * **Namn** - Skriv det namn som du vill kalla ditt WAN.
   * **Typ:** Grundläggande eller standard. Om du skapar ett basic WAN kan du bara skapa en Basic-hubb. Grundläggande nav kan endast ansluta från plats till plats.
4. När du har fyllt i fälten väljer du **Granska +Skapa**.
5. När valideringen har gått väljer du **Skapa** för att skapa det virtuella WAN-programmet.

## <a name="create-a-hub"></a><a name="hub"></a>Skapa en hubb

Ett nav är ett virtuellt nätverk som kan innehålla gateways för plats-till-plats-, ExpressRoute- eller point-to-site-funktioner. När hubben har skapats debiteras du för hubben, även om du inte kopplar några platser. Det tar 30 minuter att skapa VPN-gatewayen för plats till plats i den virtuella hubben.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-site"></a><a name="site"></a>Skapa en plats

Du är nu redo att skapa de webbplatser som motsvarar dina fysiska platser. Skapa så många platser som du behöver för att motsvara de fysiska platserna. Om du till exempel har ett avdelningskontor i New York, ett i London och ett i LA kan du skapa tre separata platser. Platserna innehåller de lokala VPN-enhetsslutpunkterna. Du kan skapa upp till 1 000 platser per virtuell hubb i ett virtuellt WAN. Om du hade flera hubbar kan du skapa 1000 per var och en av dessa nav. Om du har CPE-cpe-enhet (Virtual WAN-partner(länkinfogning) kontaktar du dem för att lära dig mer om deras automatisering till Azure. Vanligtvis innebär automatisering enkel klickupplevelse för att exportera storskalig filialinformation till Azure och konfigurera anslutning från CPE till Azure Virtual WAN VPN-gateway. Mer information finns i [Automatiseringsvägledning från Azure till CPE-partner](virtual-wan-configure-automation-providers.md).

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-the-vpn-site-to-the-hub"></a><a name="connectsites"></a>Ansluta VPN-platsen till navet

I det här steget ansluter du din VPN-plats till navet.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Ansluta det virtuella nätverket till navet

I det här steget skapar du anslutningen mellan hubben och ett virtuella nätverk. Upprepa de här stegen för varje virtuellt nätverk du vill ansluta.

1. På sidan för det virtuella WAN-nätverket klickar du på **Virtuella nätverksanslutningar**.
2. På sidan för virtuell nätverksanslutning klickar du på **+Lägg till anslutning**.
3. Fyll i följande fält på sidan **Lägg till anslutning**:

    * **Anslutningsnamn** – Namnge anslutningen.
    * **Hubbar** – Välj den hubb du vill koppla till anslutningen.
    * **Prenumeration** – Kontrollera prenumerationen.
    * **Virtuellt nätverk** – Välj det virtuella nätverk du vill ansluta till hubben. Det virtuella nätverket får inte ha någon befintlig gateway för virtuellt nätverk.
4. Klicka på **OK** om du vill skapa den virtuella nätverksanslutningen.

## <a name="download-vpn-configuration"></a><a name="device"></a>Ladda ned VPN-konfiguration

Konfigurera den lokala VPN-enheten med hjälp av konfigurationen för VPN-enheten.

1. Klicka på **Översikt** på sidan för det virtuella WAN-nätverket.
2. Klicka på **Ladda ned VPN-konfiguration**högst upp på sidan **Hub ->VPNSite.** Azure skapar ett lagringskonto i resursgruppen "microsoft-network-[location]", där platsen är platsen för WAN. När du har tillämpat konfigurationen på VPN-enheterna kan du ta bort det här lagringskontot.
3. När filen har skapats klickar du på länken för att ladda ned den.
4. Använd konfigurationen på din lokala VPN-enhet.

### <a name="understanding-the-vpn-device-configuration-file"></a>Förstå konfigurationsfilen för VPN-enheten

Konfigurationsfilen för enheten innehåller de inställningarna du ska använda när du konfigurerar den lokala VPN-enheten. När du visar den här filen ser du följande information:

* **vpnSiteConfiguration** I det här avsnittet anges enhetsinformation konfigurerad som en plats som ansluter till det virtuella WAN-nätverket. Det omfattar namn och offentlig IP-adress för grenenheten.
* **vpnSiteAnslutningar -** Det här avsnittet innehåller information om följande inställningar:

    * **adressutrymme** för de virtuella hubbarnas virtuella nätverk<br>Exempel:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **adressutrymme** för de virtuella nätverk som är anslutna till hubben<br>Exempel:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **IP-adresser** till vpngateway för den virtuella hubben. Eftersom varje anslutning av vpngateway består av två tunnlar i aktiv-aktiv konfiguration, ser du båda IP-adresser som anges i den här filen. I det här exemplet ser du "Instance0" och "Instance1" för varje plats.<br>Exempel:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Vpngateway anslutningskonfiguration detaljer** såsom BGP, pre-delad nyckel etc. PSK är den fördelade nyckeln som genereras automatiskt åt dig. Du kan alltid redigera anslutningen på översiktssidan för en anpassad PSK.
  
### <a name="example-device-configuration-file"></a>Konfigurationsfil för exempelenhet

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.3.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>Konfigurera en VPN-enhet

>[!NOTE]
> Om du arbetar med en partnerlösning med virtuellt WAN-nätverk sker VPN-enhetens konfiguration automatiskt. Enhetens styrenhet hämtar konfigurationsfilen från Azure och tillämpar den på enheten för att skapa en anslutning till Azure. Det innebär att du inte behöver veta hur du konfigurerar VPN-enheten manuellt.
>

Om du behöver anvisningar för att konfigurera enheten kan du använda instruktionerna på sidan om [konfigurationsskript till VPN-enheter](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) med följande förbehåll:

* Instruktionerna på VPN-enhetssidan inte är skrivna för virtuella WAN-nätverk, men du kan använda värdena för de virtuella WAN-nätverken från konfigurationsfilen och manuellt konfigurera VPN-enheten. 
* De nedladdningsbara skripten för enhetskofiguration till VPN-gatewayen fungerar inte för virtuellt WAN. Konfigurationerna skiljer sig åt.
* Ett nytt virtuellt WAN kan stödja både IKEv1 och IKEv2.
* Virtual WAN kan använda både principbaserade och ruttbaserade VPN-enheter och enhetsinstruktioner.

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Visa virtuellt WAN

1. Navigera till det virtuella WAN-nätverket.
2. På sidan **Översikt** representerar varje punkt på kartan ett nav. Hovra över alla synpunkter för att visa hubbhälsosammanfattningen, anslutningsstatus och byte in och ut.
3. I avsnittet Nav och anslutningar kan du visa hubbstatus, VPN-platser osv. Du kan klicka på ett specifikt navnamn och navigera till VPN-webbplatsen för ytterligare information.

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
