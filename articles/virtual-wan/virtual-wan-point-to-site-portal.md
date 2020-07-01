---
title: Skapa en punkt-till-plats-anslutning till Azure med Azure Virtual WAN | Microsoft Docs
description: I den här självstudien förklarar vi hur du skapar en punkt-till-plats-VPN-anslutning till Azure med Azure Virtual WAN.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 06/29/2020
ms.author: alzam
ms.openlocfilehash: 9c93ad0357011008c45b2898260a655509b02dc2
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560720"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Självstudie: skapa en VPN-anslutning för användare med Azure Virtual WAN

I den här självstudien förklarar vi hur du använder Virtual WAN för att ansluta till dina resurser i Azure via en IPsec/IKE-anslutning (IKEv2) eller en OpenVPN VPN-anslutning. Den här typen av anslutning kräver att en klient konfigureras på klientdatorn. Mer information om virtuellt WAN finns i [Översikt över virtuella WAN-nätverk](virtual-wan-about.md)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa WAN (Wide Area Network)
> * Skapa en P2S-konfiguration
> * Skapa en hubb
> * Ange DNS-servrar
> * Ladda ned en VPN-klient profil
> * Visa virtuellt WAN

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Du har ett virtuellt nätverk som du vill ansluta till. Kontrol lera att inget av under näten i dina lokala nätverk överlappar de virtuella nätverk som du vill ansluta till. Information om hur du skapar ett virtuellt nätverk i Azure Portal finns i [snabb](../virtual-network/quick-create-portal.md)starten.

* Det virtuella nätverket har inga virtuella Nätverksgatewayen. Om ditt virtuella nätverk har en gateway (antingen VPN eller ExpressRoute) måste du ta bort alla gatewayer. Den här konfigurationen kräver att virtuella nätverk är anslutna i stället till den virtuella WAN Hub-gatewayen.

* Hämta ett IP-adressintervall för din hubbregion. Hubben är ett virtuellt nätverk som skapas och används av virtuellt WAN-nätverk. Det adress intervall som du anger för hubben får inte överlappa något av dina befintliga virtuella nätverk som du ansluter till. Det får inte heller överlappa det adressintervall som du ansluter till lokalt. Om du inte känner till IP-adressintervall som finns i din lokala nätverks konfiguration, koordinerar du med någon som kan ge den informationen åt dig.

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Skapa ett virtuellt WAN

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

1. Gå till den virtuella WAN-sidan. I portalen väljer du **+ skapa en resurs**. Skriv det **virtuella WAN-nätverket** i sökrutan och välj **RETUR**.
1. Välj **virtuellt WAN** från resultaten. På sidan virtuellt WAN väljer du **skapa** för att öppna sidan Skapa WAN.
1. På sidan **skapa WAN** , på fliken **grundläggande** , fyller du i följande fält:

   ![Virtuellt WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Prenumeration** – Välj vilken prenumeration du vill använda.
   * **Resurs grupp** – skapa ny eller Använd befintlig.
   * **Resurs grupps plats** – Välj en resurs plats i list rutan. Ett WAN är en global resurs och är inte kopplad till en viss region. Du måste dock välja en region för att lättare att hantera och leta upp WAN-resursen som du skapar.
   * **Namn** – ange det namn som du vill anropa ditt WAN.
   * **Typ:** Standard. Om du skapar ett grundläggande WAN-nätverk kan du bara skapa en Basic-hubb. Basic-hubbar har endast stöd för VPN för plats-till-plats-anslutning.
1. När du har fyllt i fälten väljer du **Granska + skapa**.
1. När verifieringen har godkänts väljer du **skapa** för att skapa det virtuella WAN-nätverket.

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Skapa en P2S-konfiguration

En P2S-konfiguration definierar parametrarna för att ansluta fjärrklienter.

1. Navigera till **Alla resurser**.
1. Välj det virtuella WAN som du har skapat.
1. Välj **+ skapa användare VPN-konfiguration** längst upp på sidan för att öppna sidan **Skapa ny användare VPN-konfiguration** .

   :::image type="content" source="media/virtual-wan-point-to-site-portal/p2s1.jpg" alt-text="VPN-konfigurationer för användare":::

1. På sidan **Skapa ny användares VPN-konfiguration** fyller du i följande fält:

   * **Konfigurationsnamn** – Det här är det namn som du vill använda för att referera till konfigurationen.
   * **Tunneltyp** – Protokollet som ska användas för tunneln.
   * **Root Certificate Name** (Rotcertifikatnamn) – Ett beskrivande namn för certifikatet.
   * **Offentliga certifikat data** – Base-64-kodade X. 509 certifikat data.
  
1. Välj **skapa** för att skapa konfigurationen.

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Skapa hubb med punkt-till-plats-Gateway

1. Under ditt virtuella WAN väljer du hubbar och väljer **+ nytt nav**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub1.jpg" alt-text="ny hubb":::

1. Fyll i följande fält på sidan Skapa virtuellt nav.

   * **Region** – Välj den region som du vill distribuera den virtuella hubben i.
   * **Namn** – ange det namn som du vill använda för att anropa den virtuella hubben.
   * **Hubb privat adress utrymme** – hubbens adress intervall i CIDR-format.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub2.jpg" alt-text="Skapa virtuell hubb":::

1. På fliken punkt-till-plats fyller du i följande fält:

   * **Enheter för gateway-skalning** – som representerar den sammanställda kapaciteten för användarens VPN-gateway.
   * **Peka på plats konfiguration** – som du skapade i föregående steg.
   * **Klient-adresspool** – för fjärran vändare.
   * **Anpassad IP för DNS-Server**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub-with-p2s.png" alt-text="hubb med punkt-till-plats":::

1. Välj **Granska + skapa**.
1. På sidan **validering som skickats** väljer du **skapa**.

## <a name="specify-dns-server"></a><a name="dns"></a>Ange DNS-Server

Med VPN-gatewayer för virtuella WAN-användare kan du ange upp till fem DNS-servrar. Du kan konfigurera detta när du skapar hubben eller ändrar det vid ett senare tillfälle. Det gör du genom att leta upp den virtuella hubben. Under **användar-VPN (peka på plats)** klickar du på Konfigurera och anger DNS-SERVERns IP-adress (er) i text rutan för **anpassade DNS-servrar** .

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="anpassad DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>Ladda ned VPN-profil

Använd VPN-profilen för att konfigurera dina klienter.

1. På sidan för ditt virtuella WAN väljer du **användare VPN-konfigurationer**.
2. Överst på sidan väljer du **Hämta användare VPN-konfiguration**. Att hämta en konfiguration på WAN-nivå är en inbyggd Traffic Manager-baserad VPN-profil för användare. Mer information om globala profiler eller hubbbaserade profiler finns i dessa [nav profiler](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile).   Failover-scenarier är förenklade med global profil.

   Om en hubb av någon anledning inte är tillgänglig, garanterar den inbyggda trafik hanteringen som tjänsten tillhandahåller anslutning via en annan hubb till Azure-resurser för punkt-till-plats-användare. Du kan alltid Ladda ned en hubb-speciell VPN-konfiguration genom att gå till den angivna hubben. Under **användar-VPN (peka på plats)** hämtar du **VPN-** profilen för den virtuella hubben användare.

1. När filen har skapats kan du välja länken för att hämta den.
1. Använd profil filen för att konfigurera VPN-klienterna.

### <a name="configure-user-vpn-clients"></a>Konfigurera användares VPN-klienter

Använd den nedladdade profilen för att konfigurera fjärråtkomstklienterna. Proceduren skiljer sig beroende på operativsystem. Var noga med att följa rätt anvisningar nedan:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Ladda ned och installera OpenVPN-klienten från den officiella webbplatsen.
1. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken User VPN-konfigurationer i Azure Portal, eller New-AzureRmVpnClientConfiguration i PowerShell.
1. Packa upp profilen. Öppna konfigurationsfilen vpnconfig.ovpn från OpenVPN-mappen i Anteckningar.
1. Fyll i avsnittet för P2S-klientcertifikatet med P2S-klientcertifikatets offentliga nyckel i base64. I ett PEM-formaterat certifikat öppnar du bara CER-filen och kopierar över base64-nyckeln mellan certifikathuvudena. Anvisningar finns i [så här exporterar du ett certifikat för att hämta den kodade offentliga nyckeln.](certificates-point-to-site.md)
1. Fyll i avsnittet för den privata nyckeln med P2S-klientcertifikatets privata nyckel i base64. Anvisningar finns i [extrahera privat nyckel.](howto-openvpn-clients.md#windows).
1. Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
1. Kopiera filen vpnconfig.ovpn till mappen C:\Program Files\OpenVPN\config.
1. Högerklicka på ikonen OpenVPN i system fältet och välj **Anslut**.

##### <a name="ikev2"></a>IKEv2

1. Välj de VPN-klientkonfigurationsfiler som motsvarar Windows-datorns arkitektur. Välj ”VpnClientSetupAmd64”-installationspaketet för en 64-bitars processorarkitektur. Välj ”VpnClientSetupX86”-installationspaketet för en 32-bitars processorarkitektur.
1. Dubbelklicka på paketet för att installera det. Om du ser ett SmartScreen-fönster väljer du **Mer information**och **kör ändå**.
1. På klient datorn går du till **nätverks inställningar** och väljer **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till.
1. Innan du försöker ansluta kontrollerar du att du har installerat ett klientcertifikat på klientdatorn. Ett klientcertifikat krävs för autentisering när du använder den interna Azure-certifikatautentiseringstypen. Mer information om hur du skapar certifikat finns i [Skapa certifikat](certificates-point-to-site.md). Information om hur du installerar ett klient certifikat finns i [Installera ett klient certifikat](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Visa virtuellt WAN

1. Navigera till det virtuella WAN-nätverket.
1. På sidan **Översikt** representerar varje punkt på kartan ett nav.
1. I avsnittet **hubbar och anslutningar** kan du Visa nav status, plats, region, status för VPN-anslutning och byte in och ut.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Rensa resurser

Du kan använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla resurser den innehåller när du inte längre behöver dem. Ersätt myResourceGroup med namnet på resursgruppen och kör följande PowerShell-kommando:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
