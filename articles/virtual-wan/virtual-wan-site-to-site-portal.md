---
title: Skapa en plats-till-plats-anslutning till Azure med Azure Virtual WAN | Microsoft Docs
description: I den här självstudien förklarar vi hur du skapar en plats-till-plats-VPN-anslutning till Azure med Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 1b61c335dec2c641862c08fd6f752d78b2ee5866
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056675"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan-preview"></a>Självstudie: Skapa en plats-till-plats-anslutning med Azure Virtual WAN (förhandsversion)

Här förklarar vi hur du ansluter resurser i Azure via en IPsec/IKE (IKEv2) VPN-anslutning med Virtual WAN. Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Mer information om virtuella WAN-nätverk finns i [översikten om virtuellt WAN](virtual-wan-about.md)

> [!NOTE]
> Om du har många webbplatser är det vanligast att använda en [virtuellt WAN-partner](https://aka.ms.virtualwan) för att skapa den här konfigurationen. Du kan dock skapa den här konfigurationen själv om du har erfarenhet av att arbeta med nätverk och är skicklig på konfigurera din egen VPN-enhet.
>

![Virtuellt WAN-diagram](./media/virtual-wan-about/virtualwan.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa WAN (Wide Area Network)
> * Skapa en plats
> * Skapa en hubb
> * Ansluta en hubb till en plats
> * Ansluta ett virtuellt nätverk till en hubb
> * Ladda ned och applicera VPN-enhetskonfigurationen
> * Visa virtuellt WAN
> * Visa information om resurshälsa
> * Övervaka en anslutning

> [!IMPORTANT]
> Azure Virtual WAN är för närvarande en allmänt tillgänglig förhandsversion. Om du vill använda virtuellt WAN måste du [registrera dig för förhandsversionen](#enroll).
>
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Se till att du har en kompatibel ruttbaserad VPN-enhet som fungerar med IKEv2 och någon som kan konfigurera den. Om du arbetar med en virtuellt WAN-partner skapas konfigurationsinställningarna automatiskt och du behöver inte bekymra dig om att konfigurera enheten manuellt.
* Kontrollera att du har en extern offentlig IPv4-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
* Om du redan har ett virtuellt nätverk som du vill ansluta till ska du kontrollera att inget av undernäten i det lokala nätverket överlappar de virtuella nätverk som du vill ansluta till. Det virtuella nätverket kräver inget gatewayundernät och kan inte ha några virtuella nätverksgatewayer. Om du inte har något virtuellt nätverk kan du skapa ett med anvisningarna i den här artikeln.
* Hämta ett IP-adressintervall för din hubbregion. Hubben är ett virtuellt nätverk och det adressintervall du anger för hubbregionen får inte överlappa något av de befintliga virtuella nätverk som du ansluter till. Det får inte heller överlappa det adressintervall som du ansluter till lokalt. Om du inte vet vilka IP-adressintervaller som används i din lokala nätverkskonfiguration kontaktar du relevant person som kan ge dig den här informationen.
* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="enroll"></a>1. Registrera dig för förhandsversionen

Du måste registrera prenumerationen i förhandsversionen innan du kan konfigurera ett virtuellt WAN. I annat fall kan du inte arbeta med virtuellt WAN i portalen. Skicka ett e-postmeddelande till **azurevirtualwan@microsoft.com** med prenumerations-ID:t. Du får ett e-postmeddelande tillbaka när din prenumeration har registrerats.

## <a name="vnet"></a>2. Skapa ett virtuellt nätverk

Om du inte redan har ett virtuellt nätverk kan du snabbt skapa ett med PowerShell. Du kan också skapa ett virtuellt nätverk via Azure-portalen.

* Se till att adressutrymmet för det virtuella nätverket du skapar inte överlappar adressintervallen för andra virtuella nätverk du vill ansluta till, eller dina lokala nätverksadresser. 
* Om du redan har ett virtuellt nätverk kontrollerar du att det uppfyller kraven och inte har någon gateway för virtuellt nätverk.

I den här artikeln kan du klicka och enkelt prova att skapa ett virtuellt nätverk. Du öppnar då en PowerShell-konsol. Justera värdena och kopiera och klistra sedan in kommandona i konsolfönstret.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Justera PowerShell-kommandona och skapa sedan en resursgrupp.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Skapa ett virtuellt nätverk

Justera PowerShell-kommandona och skapa ett virtuellt nätverk som är kompatibelt med din miljö.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```

## <a name="wan"></a>3. Skapa ett virtuellt WAN

1. Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.
2. För närvarande kan du hitta ett virtuellt WAN genom att navigera till **Alla tjänster** och söka efter ett virtuellt Virtual WAN. Du kan söka efter Virtual WAN i sökrutan högst upp i Azure-portalen. Klicka på **virtuellt WAN** för att öppna sidan.
3. Klicka på **Skapa** för att öppna sidan **Skapa WAN**. Om sidan inte finns har du inte godkänts för förhandsversionen ännu.

  ![Skapa WAN](./media/virtual-wan-site-to-site-portal/createwan.png)
4. Fyll i följande fält på sidan Skapa WAN.

  * **Namn** – Välj vad du vill kalla WAN-nätverket.
  * **Prenumeration** – Välj vilken prenumeration du vill använda.
  * **Resursgrupp** – Skapa ny eller använd befintlig.
  * **Resursplats** – Välj en resursplats i listrutan. Ett WAN är en global resurs och är inte kopplad till en viss region. Du måste dock välja en region för att lättare att hantera och leta upp WAN-resursen som du skapar.
5. Skapa WAN-nätverket genom att klicka på **Skapa**.

## <a name="site"></a>4. Skapa en plats

Skapa så många platser som du behöver för att motsvara de fysiska platserna. Om du till exempel har ett avdelningskontor i New York, ett i London och ett i LA kan du skapa tre separata platser. Platserna innehåller de lokala VPN-enhetsslutpunkterna. Du kan för närvarande endast specificera ett privat adressutrymme per plats.

1. Navigera till **Alla resurser**.
2. Klicka på det virtuella WAN-nätverk du skapade.
3. Klicka på **+Skapa webbplats** längst upp på sidan för att öppna sidan **Skapa webbplats**.

  ![ny webbplats](media/virtual-wan-site-to-site-portal/createsite.png)
4. Fyll i följande fält på sidan **Skapa webbplats**:

  *  **Namn** – Det här är namnet du vill ge den lokala platsen.
  *  **Offentlig IP-adress** – Det här är den offentliga IP-adressen för VPN-enheten som hör till den lokala platsen.
  *  **Privat adressutrymme** – Det här är adressutrymmet som finns på din lokala plats. Trafik till det här adressutrymmet dirigeras till den lokala platsen.
  *  **Prenumeration** – Kontrollera prenumerationen.
  *  **Resursgrupp** – Den resursgrupp du vill använda.
5. Klicka på **Visa avancerade** för att visa ytterligare inställningar. Du kan **aktivera BGP** (Valfritt fält, vilket aktiverar den här funktionen för alla anslutningar som skapas för den här platsen i Azure. Du kan också ange **Enhetsinformation** (valfritt fält). Det här kan hjälpa Azure-teamet att bättre förstå din miljö och lägga till ytterligare optimeringsmöjligheter i framtiden eller hjälpa dig att felsöka.

  ![BGP](media/virtual-wan-site-to-site-portal/sitebgp.png)
6. Klicka på **Bekräfta** för att skapa platsen.
7. Upprepa de här stegen för varje plats du vill skapa.

## <a name="hub"></a>5. Skapa en hubb och anslut platser

1. Klicka på **Platser** på sidan för det virtuella WAN-nätverket.
2. Under **	Webbplatser som inte är kopplade** visas platser som inte ännu har anslutits till någon hubb.
3. Välj de platser du vill koppla.
4. I listrutan väljer du den region som hubben ska kopplas till. Du bör koppla hubben till den region där de virtuella nätverk du vill ansluta till finns.
5. Klicka på **Bekräfta**. Om du inte har någon hubb i den här regionen ännu skapas ett virtuellt nätverk automatiskt. I det här fallet visas sidan **Skapa regionala hubbar**.
6. På sidan **Skapa regionala hubbar** anger du adressintervallet för hubbnätverket. Det här är det virtuella nätverk som innehåller dina hubbtjänster. Intervallet du anger här måste vara ett privat IP-adressintervall. Det får inte överlappa lokala adressutrymmen eller adressutrymmen för virtuellt nätverk. En efterföljande VPN-slutpunkt skapas i det virtuella hubbnätverket. (Automatisk generering av hubb och gateway är endast tillgänglig i portalen.)
7. Klicka på **Skapa**.

## <a name="vnet"></a>6. Ansluta ett virtuellt nätverk till en hubb

I det här steget skapar du peeringanslutningen mellan hubben och ett virtuellt nätverk. Upprepa de här stegen för varje virtuellt nätverk du vill ansluta.

1. På sidan för det virtuella WAN-nätverket klickar du på **virtuell nätverksanslutning**.
2. På sidan för virtuell nätverksanslutning klickar du på **+Lägg till anslutning**.
3. Fyll i följande fält på sidan **Lägg till anslutning**:

    * **Anslutningsnamn** – Namnge anslutningen.
    * **Hubbar** – Välj den hubb du vill koppla till anslutningen.
    * **Prenumeration** – Kontrollera prenumerationen.
    * **Virtuellt nätverk** – Välj det virtuella nätverk du vill ansluta till hubben. Det virtuella nätverket får inte ha någon befintlig gateway för virtuellt nätverk.

## <a name="device"></a>7. Ladda ned VPN-konfiguration

Konfigurera den lokala VPN-enheten med hjälp av konfigurationen för VPN-enheten.

1. Klicka på **Översikt** på sidan för det virtuella WAN-nätverket.
2. Överst på sidan Översikt klickar du på **Ladda ned VPN-konfiguration**. Azure skapar ett lagringskonto i resursgruppen microsoft-network-[plats], där plats refererar till WAN-platsen. När du har tillämpat konfigurationen på VPN-enheterna kan du ta bort det här lagringskontot.
3. När filen har skapats klickar du på länken för att ladda ned den.
4. Tillämpa konfigurationen på VPN-enheten.

### <a name="understanding-the-vpn-device-configuration-file"></a>Förstå konfigurationsfilen för VPN-enheten

Konfigurationsfilen för enheten innehåller de inställningarna du ska använda när du konfigurerar den lokala VPN-enheten. När du visar den här filen ser du följande information:

* **vpnSiteConfiguration** I det här avsnittet anges enhetsinformation konfigurerad som en plats som ansluter till det virtuella WAN-nätverket. Det omfattar namn och offentlig IP-adress för grenenheten.
* **vpnSiteConnections** – Det här avsnittet innehåller information om följande:

    * **adressutrymme** för de virtuella hubbarnas virtuella nätverk<br>Exempel:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **adressutrymme** för de virtuella nätverk som är anslutna till hubben<br>Exempel:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **IP-adresser** till vpngateway för den virtuella hubben. Eftersom varje anslutning för vpngateway består av två tunnlar i aktiv-aktiv konfiguration ser du båda IP-adresserna listade i den här filen. I det här exemplet ser du "Instance0" och "Instance1" för varje plats.<br>Exempel:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Konfigurationsinformation för vpngatewayanslutning**, exempelvis BGP, i förväg delad nyckel osv. PSK är den i förväg delade nyckeln som genereras automatiskt åt dig. Du kan alltid redigera anslutningen på översiktssidan för en anpassad PSK.
  
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
                  "10.30.0.0/16"
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
> Om du använder en Virtual WAN-partnerlösning sker VPN-enhetskonfigureringen automatiskt när enhetskontrollen får konfigurationsfilen från Azure och tillämpar den på enheten för att skapa en anslutning till Azure. Det innebär att du inte behöver veta hur du konfigurerar VPN-enheten manuellt.
>

Om du behöver anvisningar för att konfigurera enheten kan du använda instruktionerna på sidan om [konfigurationsskript till VPN-enheter](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) med följande förbehåll:

* Instruktionerna på VPN-enhetssidan inte är skrivna för virtuella WAN-nätverk, men du kan använda värdena för de virtuella WAN-nätverken från konfigurationsfilen och manuellt konfigurera VPN-enheten. 
* De nedladdningsbara skripten för enhetskofiguration till VPN-gatewayen fungerar inte för virtuellt WAN. Konfigurationerna skiljer sig åt.
* Virtuellt WAN kan bara använda IKEv2, inte IKEv1.
* Virtuellt WAN fungerar endast med routningsbaserade VPN-enheter och enhetsanvisningar.

## <a name="viewwan"></a>8. Visa virtuellt WAN

1. Navigera till det virtuella WAN-nätverket.
2. Varje punkt på kartan på sidan Översikt motsvarar en hubb. För muspekaren över en punkt så visas en sammanfattning av hubbens hälsotillstånd.
3. I avsnittet om hubbar och anslutningar kan du visa hubbstatus, plats, region, VPN-anslutningsstatus och byte in och ut.

## <a name="viewhealth"></a>9. Visa resurshälsa

1. Navigera till ditt WAN.
2. Öppna WAN-sidan. I avsnittet **SUPPORT + felsökning** klickar du på **Hälsa** och visar resursen.

## <a name="connectmon"></a>10. Övervaka en anslutning

Skapa en anslutning för att övervaka kommunikation mellan en virtuell Azure-dator och en fjärrplats. Information om hur du ställer in en anslutningsövervakare finns i dokumentationen om att [övervaka nätverkskommunikation](~/articles/network-watcher/connection-monitor.md). Källfältet är IP för den virtuella datorn i Azure och mål-IP är plats-IP-adressen.

## <a name="cleanup"></a>11. Rensa resurser

Du kan använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla resurser den innehåller när du inte längre behöver dem. Ersätt myResourceGroup med namnet på resursgruppen och kör följande PowerShell-kommando:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="feedback"></a>Feedback för förhandsversionen

Vi vill gärna ha din feedback. Skicka ett e-postmeddelande till <azurevirtualwan@microsoft.com> om du vill rapportera problem eller lämna feedback (positiv och negativ) om Virtual WAN. Ange ditt företagsnamn inom [ ] i ämnesraden. Ange även ditt prenumerations-ID om du rapporterar ett problem.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa WAN (Wide Area Network)
> * Skapa en plats
> * Skapa en hubb
> * Ansluta en hubb till en plats
> * Ansluta ett virtuellt nätverk till en hubb
> * Ladda ned och applicera VPN-enhetskonfigurationen
> * Visa virtuellt WAN
> * Visa information om resurshälsa
> * Övervaka en anslutning

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).