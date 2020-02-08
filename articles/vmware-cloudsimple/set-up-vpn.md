---
title: Azure VMware-lösningar (AVS) – Konfigurera VPN mellan lokalt och AVS-privat moln
description: Beskriver hur du konfigurerar en plats-till-plats-eller punkt-till-plats-VPN-anslutning mellan ditt lokala nätverk och ditt moln privata moln
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8188fac270eadb6e09cc3561ddefb05aa59ba661
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087140"
---
# <a name="configure-a-vpn-connection-to-your-avs-private-cloud"></a>Konfigurera en VPN-anslutning till ditt moln privata moln

Med VPN-gatewayer kan du ansluta till AVS-nätverket från ditt lokala nätverk och från en klient dator via fjärr anslutning. I den här artikeln hittar du information om hur du konfigurerar VPN-gatewayer från AVS-portalen. En VPN-anslutning mellan ditt lokala nätverk och ditt AVS-nätverk ger åtkomst till vCenter och arbets belastningar i ditt AVS-privata moln. AVS stöder både punkt-till-plats-VPN och plats-till-plats VPN-gatewayer.

## <a name="vpn-gateway-types"></a>VPN gateway-typer

* **Punkt-till-plats-VPN-** anslutning är det enklaste sättet att ansluta till ditt molns privata moln från din dator. Använd punkt-till-plats-VPN-anslutning för att fjärrans luta till molnets privata moln.
* Med **plats-till-plats-VPN-** anslutning kan du konfigurera dina arbets belastningar för molnets privata moln för att komma åt lokala tjänster. Du kan också använda lokala Active Directory som identitets källa för att autentisera till ditt molns lokala Cloud-moln vCenter. För närvarande stöds **Principbaserad VPN-** typ.

I en region kan du skapa en plats-till-plats-VPN-gateway och en punkt-till-plats-VPN-gateway.

## <a name="point-to-site-vpn"></a>Punkt-till-plats-VPN

Information om hur du skapar en punkt-till-plats-VPN-gateway finns i [skapa punkt-till-plats-VPN-gateway](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-avs-using-point-to-site-vpn"></a>Ansluta till AVS med punkt-till-plats-VPN

VPN-klienten krävs för att ansluta till AVS från din dator. Ladda ned [OpenVPN-klienten](https://openvpn.net/community-downloads/) för Windows eller [viskositet](https://www.sparklabs.com/viscosity/download/) för MacOS och OS X.

1. Starta AVS-portalen och välj **nätverk**.
2. Välj **VPN gateway**.
3. I listan med VPN-gatewayer klickar du på punkt-till-plats-VPN-gatewayen.
4. Välj **användare**.
5. Klicka på **Ladda ned min VPN-konfiguration**

    ![Ladda ned VPN-konfiguration](media/download-p2s-vpn-configuration.png)

6. Importera konfigurationen på VPN-klienten

    * Instruktioner för att [importera konfigurationen på Windows-klienten](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruktioner för att [importera konfigurationen på MacOS eller OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Anslut till AVS VPN gateway.

Exemplet nedan visar hur du importerar en anslutning med hjälp av **viskositet-klienten**.

#### <a name="import-connection-on-viscosity-client"></a>Importera anslutning på viskositet-klient

1. Extrahera innehållet i VPN-konfigurationen från den hämtade ZIP-filen.

2. Öppna viskositet på datorn.

3. Klicka på ikonen **+** och välj **Importera anslutnings** > **från fil**.

    ![Importera VPN-konfiguration från en fil](media/import-p2s-vpn-config.png)

4. Välj konfigurations filen för OpenVPN (. ovpn) för det protokoll som du vill använda och klicka på **Öppna**.

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

Anslutningen visas nu på menyn viskositet.

#### <a name="connect-to-the-vpn"></a>Ansluta till VPN

Om du vill ansluta till VPN med hjälp av OpenVPN-klienten för viskositet väljer du anslutningen på menyn. Meny ikonen uppdateras för att indikera att anslutningen har upprättats.

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-avs-private-clouds"></a>Ansluta till flera moln privata moln

En punkt-till-plats-VPN-anslutning matchar DNS-namnen för det första AVS-privata moln som du skapar. När du vill komma åt andra moln privata moln måste du uppdatera DNS-servern på VPN-klienten.

1. Starta [AVS-portalen](access-cloudsimple-portal.md).

2. Navigera till **resurser** > **AVS-privata moln** och välj det AVS-privata moln som du vill ansluta till.

3. På sidan **Sammanfattning** i det privata AVS-molnet kopierar du IP-adressen för DNS-serverns IP-adress i molnet under **grundläggande information**.

    ![DNS-servrar för molnets privata moln](media/private-cloud-dns-server.png)

4. Högerklicka på ikonen för viskositet i datorns system fält och välj **Inställningar**.

    ![VPN](media/vis00.png)

5. Välj AVS VPN-anslutningen.

    ![VPN-anslutning](media/viscosity-client.png)

6. Klicka på **Redigera** för att ändra anslutnings egenskaperna.

    ![Redigera VPN-anslutning](media/viscosity-edit-connection.png)

7. Klicka på fliken **nätverk** och ange IP-adresserna för DNS-serverns IP-adresser, åtskilda med kommatecken eller blank steg och domänen som ```az.cloudsimple.io```. Välj **Ignorera DNS-inställningar som skickats av VPN-servern**.

    ![VPN-nätverk](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Ta bort inställningarna och Anslut till VPN-servern för att ansluta till ditt första AVS-moln.

## <a name="site-to-site-vpn"></a>Plats-till-plats-VPN

Information om hur du skapar en plats-till-plats-VPN-gateway finns i [skapa plats-till-plats-VPN-gateway](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway). Plats-till-plats-VPN-anslutning från ditt lokala nätverk till ditt moln privata moln ger dessa fördelar. 

* Tillgänglighet för ditt moln privata moln vCenter från valfri arbets station i ditt lokala nätverk
* Använda din lokala Active Directory som en vCenter-identitets källa
* Bekväm överföring av VM-mallar, ISO och andra filer från dina lokala resurser till ditt molns privata moln vCenter
* Tillgänglighet för arbets belastningar som körs på ditt moln privata moln från ditt lokala nätverk

Information om hur du konfigurerar din lokala VPN-gateway i läget för hög tillgänglighet finns i [Konfigurera en VPN-anslutning med hög tillgänglighet](high-availability-vpn-connection.md).

> [!IMPORTANT]
>    1. Ange TCP MSS-ihopfogning vid 1200 på VPN-enheten. Eller om VPN-enheterna inte stöder MSS-ihopfogning kan du i stället ange MTU för tunnel gränssnittet till 1240 byte.
> 2. När plats-till-plats-VPN har kon figurer ATS vidarebefordrar DNS-begäranden för *. cloudsimple.io till DNS-servrarna i AVS-molnet. Följ anvisningarna i [den lokala DNS-installationen](on-premises-dns-setup.md).
