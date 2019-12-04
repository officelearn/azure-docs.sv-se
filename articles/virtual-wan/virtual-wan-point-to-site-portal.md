---
title: Skapa en punkt-till-plats-anslutning till Azure med Azure Virtual WAN | Microsoft Docs
description: I den här självstudien förklarar vi hur du skapar en punkt-till-plats-VPN-anslutning till Azure med Azure Virtual WAN.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: alzam
ms.openlocfilehash: 0572a919675ab363c55938f8731c691cd8129826
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767661"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Självstudie: skapa en VPN-anslutning för användare med Azure Virtual WAN

I den här självstudien förklarar vi hur du använder Virtual WAN för att ansluta till dina resurser i Azure via en IPsec/IKE-anslutning (IKEv2) eller en OpenVPN VPN-anslutning. Den här typen av anslutning kräver att en klient konfigureras på klientdatorn. Mer information om virtuella WAN-nätverk finns i [översikten om virtuellt WAN](virtual-wan-about.md)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa WAN (Wide Area Network)
> * Skapa en hubb
> * Skapa en P2S-konfiguration
> * Ladda ned en VPN-klient profil
> * Tillämpa P2S-konfigurationen på en hubb
> * Ansluta ett virtuellt nätverk till en hubb
> * Ladda ned och tillämpa VPN-klientkonfigurationen
> * Visa virtuellt WAN
> * Visa information om resurshälsa

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Du har ett virtuellt nätverk som du vill ansluta till. Kontrol lera att inget av under näten i dina lokala nätverk överlappar de virtuella nätverk som du vill ansluta till. Information om hur du skapar ett virtuellt nätverk i Azure Portal finns i [snabb](../virtual-network/quick-create-portal.md)starten.

* Det virtuella nätverket har inga virtuella Nätverksgatewayen. Om ditt virtuella nätverk har en gateway (antingen VPN eller ExpressRoute) måste du ta bort alla gatewayer. Den här konfigurationen kräver att virtuella nätverk är anslutna i stället till den virtuella WAN Hub-gatewayen.

* Hämta ett IP-adressintervall för din hubbregion. Hubben är ett virtuellt nätverk som skapas och används av virtuellt WAN-nätverk. Det adress intervall som du anger för hubben får inte överlappa något av dina befintliga virtuella nätverk som du ansluter till. Det får inte heller överlappa det adressintervall som du ansluter till lokalt. Om du inte känner till IP-adressintervall som finns i din lokala nätverks konfiguration, koordinerar du med någon som kan ge den informationen åt dig.

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="wan"></a>Skapa ett virtuellt WAN

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

1. Gå till den virtuella WAN-sidan. Klicka på **+Skapa en resurs** i portalen. Skriv det **virtuella WAN-nätverket** i sökrutan och välj RETUR.
2. Välj **virtuellt WAN** från resultaten. På den virtuella WAN-sidan klickar du på **skapa** för att öppna sidan Skapa WAN.
3. På sidan **skapa WAN** , på fliken **grundläggande** , fyller du i följande fält:

   ![Virtuellt WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Prenumeration** – Välj vilken prenumeration du vill använda.
   * **Resurs grupp** – skapa ny eller Använd befintlig.
   * **Resurs grupps plats** – Välj en resurs plats i list rutan. Ett WAN är en global resurs och är inte kopplad till en viss region. Du måste dock välja en region för att lättare att hantera och leta upp WAN-resursen som du skapar.
   * **Namn** – ange det namn som du vill anropa ditt WAN.
   * **Typ:** Standard. Om du skapar ett grundläggande WAN-nätverk kan du bara skapa en Basic-hubb. Basic-hubbar har endast stöd för VPN för plats-till-plats-anslutning.
4. När du har fyllt i fälten väljer du **Granska + skapa**.
5. När verifieringen har godkänts väljer du **skapa** för att skapa det virtuella WAN-nätverket.

## <a name="site"></a>Skapa en tom virtuell hubb

1. Under ditt virtuella WAN-nätverk väljer du hubbar och klickar på **+ ny hubb**

   ![ny webbplats](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. Fyll i följande fält på sidan Skapa virtuellt nav.

   **Region** – Välj den region som du vill distribuera den virtuella hubben i.

   **Namn** – ange det namn som du vill använda för att anropa den virtuella hubben.

   **Hubb privat adress utrymme** – hubbens adress intervall i CIDR-format.

   ![ny webbplats](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. Klicka på **Granska + skapa**
4. Klicka på **skapa** på sidan **valideringen har slutförts**

## <a name="site"></a>Skapa en P2S-konfiguration

En P2S-konfiguration definierar parametrarna för att ansluta fjärrklienter.

1. Navigera till **Alla resurser**.
2. Klicka på det virtuella WAN-nätverk du skapade.
3. Klicka på **+ skapa användare VPN-konfiguration** längst upp på sidan för att öppna sidan **Skapa ny användare VPN-konfiguration** .

   ![ny webbplats](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. På sidan **Skapa ny användares VPN-konfiguration** fyller du i följande fält:

   **Konfigurationsnamn** – Det här är det namn som du vill använda för att referera till konfigurationen.

   **Tunneltyp** – Protokollet som ska användas för tunneln.

   **Root Certificate Name** (Rotcertifikatnamn) – Ett beskrivande namn för certifikatet.

   **Offentliga certifikat data** – Base-64-kodade X. 509 certifikat data.
  
   ![ny webbplats](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Klicka på **Skapa** för att skapa konfigurationen.

## <a name="hub"></a>Redigera hubb tilldelning

1. Gå till bladet **hubbar** under det virtuella WAN-nätverket
2. Välj den hubb som du vill associera VPN-serverkonfigurationen till och klicka på **...**

   ![ny webbplats](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. Klicka på **Redigera virtuell hubb**.
4. Markera kryss rutan **Inkludera punkt-till-plats-Gateway** och välj den **enhet för gateway-skalning** som du vill använda.

   ![ny webbplats](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Ange adresspoolen **från vilken** VPN-klienter ska tilldelas IP-adresser.
6. Klicka på **Bekräfta**
7. Åtgärden kan ta upp till 30 minuter att slutföra.

## <a name="device"></a>Ladda ned VPN-profil

Använd VPN-profilen för att konfigurera dina klienter.

1. På sidan för ditt virtuella WAN-nätverk klickar du på **användare VPN-konfigurationer**.
2. Klicka på **Hämta VPN-konfiguration för användare**längst upp på sidan.
3. När filen har skapats klickar du på länken för att ladda ned den.
4. Använd profil filen för att konfigurera VPN-klienterna.

### <a name="configure-user-vpn-clients"></a>Konfigurera användares VPN-klienter
Använd den nedladdade profilen för att konfigurera fjärråtkomstklienterna. Proceduren skiljer sig beroende på operativsystem. Var noga med att följa rätt anvisningar nedan:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Ladda ned och installera OpenVPN-klienten från den officiella webbplatsen.
2. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken User VPN-konfigurationer i Azure Portal, eller New-AzureRmVpnClientConfiguration i PowerShell.
3. Packa upp profilen. Öppna konfigurationsfilen vpnconfig.ovpn från OpenVPN-mappen i Anteckningar.
4. Fyll i avsnittet för P2S-klientcertifikatet med P2S-klientcertifikatets offentliga nyckel i base64. I ett PEM-formaterat certifikat öppnar du bara CER-filen och kopierar över base64-nyckeln mellan certifikathuvudena. Mer information finns i så här [exporterar du ett certifikat för att hämta den kodade offentliga nyckeln.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site)
5. Fyll i avsnittet för den privata nyckeln med P2S-klientcertifikatets privata nyckel i base64. Här ser du hur du extraherar den privata nyckeln.
6. Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
7. Kopiera filen vpnconfig.ovpn till mappen C:\Program Files\OpenVPN\config.
8. Högerklicka på OpenVPN-ikonen i systemfältet och klicka på Anslut.

##### <a name="ikev2"></a>IKEv2

1. Välj de VPN-klientkonfigurationsfiler som motsvarar Windows-datorns arkitektur. Välj ”VpnClientSetupAmd64”-installationspaketet för en 64-bitars processorarkitektur. Välj ”VpnClientSetupX86”-installationspaketet för en 32-bitars processorarkitektur.
2. Dubbelklicka på paketet för att installera det. Om du ser ett SmartScreen-popup-fönster klickar du på Mer information och sedan på Kör ändå.
3. På klientdatorn går du till Nätverksinställningar och klickar på VPN. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till.
4. Innan du försöker ansluta kontrollerar du att du har installerat ett klientcertifikat på klientdatorn. Ett klientcertifikat krävs för autentisering när du använder den interna Azure-certifikatautentiseringstypen. Mer information om hur du skapar certifikat finns i [Skapa certifikat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site). Information om hur du installerar ett klientcertifikat finns i Installera ett klientcertifikat.

## <a name="viewwan"></a>Visa ditt virtuella WAN

1. Navigera till det virtuella WAN-nätverket.
2. Varje punkt på kartan på sidan Översikt motsvarar en hubb. För muspekaren över en punkt så visas en sammanfattning av hubbens hälsotillstånd.
3. I avsnittet om hubbar och anslutningar kan du visa hubbstatus, plats, region, VPN-anslutningsstatus och byte in och ut.

## <a name="viewhealth"></a>Visa din resurs hälsa

1. Navigera till ditt WAN.
2. Öppna WAN-sidan. I avsnittet **SUPPORT + felsökning** klickar du på **Hälsa** och visar resursen.


## <a name="cleanup"></a>Rensa resurser

Du kan använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla resurser den innehåller när du inte längre behöver dem. Ersätt myResourceGroup med namnet på resursgruppen och kör följande PowerShell-kommando:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
