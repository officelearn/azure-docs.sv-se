---
title: Azure VMware Solution by CloudSimple – Konfigurera VPN mellan lokalt och privat moln
description: Beskriver hur du konfigurerar en plats-till-plats- eller punkt-till-plats-VPN-anslutning mellan ditt lokala nätverk och ditt CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d000d8390375466232c7daac2a4a056ef424be79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087140"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>Konfigurera en VPN-anslutning till ditt CloudSimple Private Cloud

Med VPN-gateways kan du fjärransluta till CloudSimple-nätverket från ditt lokala nätverk och från en klientdator på distans.  I den här artikeln hittar du information om hur du konfigurerar VPN-gateways från CloudSimple-portalen.  En VPN-anslutning mellan ditt lokala nätverk och ditt CloudSimple-nätverk ger åtkomst till vCenter och arbetsbelastningar i ditt privata moln. CloudSimple stöder både VPN-gateways för point-to-site och VPN-gateways för plats till plats.

## <a name="vpn-gateway-types"></a>TYPER av VPN-gateway

* **Vpn-anslutning mellan punkt och plats** är det enklaste sättet att ansluta till ditt privata moln från datorn. Använd punkt-till-plats-VPN-anslutning för anslutning till det privata molnet på distans.
* **Med VPN-anslutning från plats till plats** kan du konfigurera dina arbetsbelastningar i det privata molnet för att komma åt lokala tjänster. Du kan också använda lokal Active Directory som identitetskälla för autentisering till ditt privata moln vCenter.  För närvarande stöds **principbaserad VPN-typ.**

I en region kan du skapa en Plats-till-plats VPN-gateway och en Point-to-Site VPN-gateway.

## <a name="point-to-site-vpn"></a>Punkt-till-plats-VPN

Om du vill skapa en VPN-gateway från punkt till plats finns i [Skapa vpn-gateway för punkt till plats](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Ansluta till CloudSimple med VPN från punkt till plats

VPN-klient behövs för att ansluta till CloudSimple från din dator.  Ladda ned [OpenVPN-klienten](https://openvpn.net/community-downloads/) för Windows eller [Viskositet](https://www.sparklabs.com/viscosity/download/) för macOS och OS X.

1. Starta CloudSimple-portalen och välj **Nätverk**.
2. Välj **VPN Gateway**.
3. Klicka på VPN-gatewayen från listan över VPN-gateways.
4. Välj **Användare**.
5. Klicka på **Ladda ner min VPN-konfiguration**

    ![Ladda ned VPN-konfiguration](media/download-p2s-vpn-configuration.png)

6. Importera konfigurationen på DIN VPN-klient

    * Instruktioner för [import av konfiguration på Windows-klienten](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruktioner för [import av konfiguration på macOS eller OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Anslut till CloudSimple VPN-gateway.

Exempel nedan visar importanslutning med **Viskositetsklient**.

#### <a name="import-connection-on-viscosity-client"></a>Importera anslutning på Viskositetsklient

1. Extrahera innehållet i VPN-konfigurationen från nedladdad ZIP-fil.

2. Öppna Viskositet på datorn.

3. Klicka **+** på ikonen och välj **Importera anslutning** > **från fil**.

    ![Importera VPN-konfiguration från fil](media/import-p2s-vpn-config.png)

4. Välj OpenVPN-konfigurationsfilen (.ovpn) för det protokoll du vill använda och klicka på **Öppna**.

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

Anslutningen visas nu i menyn Viskositet.

#### <a name="connect-to-the-vpn"></a>Anslut till det privata virtuella nätverket.

Om du vill ansluta till VPN med Viscosity OpenVPN-klienten väljer du anslutningen på menyn. Menyikonen uppdateras för att visa att anslutningen är upprättad.

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Ansluta till flera privata moln

En VPN-anslutning mellan punkt och plats matchar DNS-namnen för det första privata molnet som du skapar. När du vill komma åt andra privata moln måste du uppdatera DNS-servern på din VPN-klient.

1. Starta [CloudSimple-portalen](access-cloudsimple-portal.md).

2. Navigera till **Resurser** > **privata moln** och välj det privata moln som du vill ansluta till.

3. På **sidan Sammanfattning** i det privata molnet kopierar du IP-adressen för den privata moln-DNS-servern under **Grundläggande information**.

    ![Privata DNS-servrar i molnet](media/private-cloud-dns-server.png)

4. Högerklicka på ikonen Viskositet i datorns systemfack och välj **Inställningar**.

    ![VPN](media/vis00.png)

5. Välj CloudSimple VPN-anslutning.

    ![VPN-anslutning](media/viscosity-client.png)

6. Klicka på **Redigera** om du vill ändra anslutningsegenskaperna.

    ![Redigera VPN-anslutning](media/viscosity-edit-connection.png)

7. Klicka på fliken **Nätverk** och ange IP-adresser för den privata moln-DNS-servern avgränsade med ett komma eller utrymme och domänen som ```cloudsimple.io```.  Välj **Ignorera DNS-inställningar som skickas av VPN-servern**.

    ![VPN-nätverk](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Om du vill ansluta till ditt första privata moln tar du bort dessa inställningar och ansluter till VPN-servern.

## <a name="site-to-site-vpn"></a>Plats-till-plats-VPN

Om du vill skapa en VPN-gateway från plats till plats finns i [Skapa vpn-gateway för plats till plats](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).  Vpn-anslutning från plats till plats från ditt lokala nätverk till ditt privata moln ger dessa fördelar.  

* Hjälpmedel för ditt privata moln vCenter från alla arbetsstationer i ditt lokala nätverk
* Användning av din lokala Active Directory som vCenter-identitetskälla
* Bekväm överföring av VM-mallar, Iso-filer och andra filer från lokala resurser till ditt privata moln vCenter
* Hjälpmedel för arbetsbelastningar som körs i ditt privata moln från ditt lokala nätverk

Information om hur du konfigurerar din lokala VPN-gateway i hög tillgänglighetsläge finns i [Konfigurera en VPN-anslutning med hög tillgänglighet](high-availability-vpn-connection.md).

> [!IMPORTANT]
>    1. Ställ in TCP MSS Clamping vid 1200 på din VPN-enhet. Eller om dina VPN-enheter inte stöder MSS-fastspänning kan du alternativt ställa in MTU på tunnelgränssnittet på 1240 byte istället.
> 2. När VPN från plats till plats har konfigurerats vidarebefordrar du DNS-begäranden för *.cloudsimple.io till de privata DNS-servrarna i molnet.  Följ instruktionerna i [lokala DNS-inställningar](on-premises-dns-setup.md).
