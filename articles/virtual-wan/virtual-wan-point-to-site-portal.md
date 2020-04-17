---
title: Skapa en punkt-till-plats-anslutning till Azure med Azure Virtual WAN | Microsoft Docs
description: I den här självstudien förklarar vi hur du skapar en punkt-till-plats-VPN-anslutning till Azure med Azure Virtual WAN.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: alzam
ms.openlocfilehash: 11007bc39cb1112799c89afaf0ca670aa6760de6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482135"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Självstudiekurs: Skapa en VPN-anslutning för användare med Azure Virtual WAN

I den här självstudien förklarar vi hur du använder Virtual WAN för att ansluta till dina resurser i Azure via en IPsec/IKE-anslutning (IKEv2) eller en OpenVPN VPN-anslutning. Den här typen av anslutning kräver att en klient konfigureras på klientdatorn. Mer information om Virtuellt WAN finns i virtual [WAN-översikten](virtual-wan-about.md)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa WAN (Wide Area Network)
> * Skapa en hubb
> * Skapa en P2S-konfiguration
> * Ladda ned en VPN-klientprofil
> * Tillämpa P2S-konfigurationen på en hubb
> * Ansluta ett virtuellt nätverk till en hubb
> * Ladda ned och tillämpa VPN-klientkonfigurationen
> * Visa virtuellt WAN

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Du har ett virtuellt nätverk som du vill ansluta till. Kontrollera att inget av undernäten i dina lokala nätverk överlappar de virtuella nätverk som du vill ansluta till. Information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i [snabbstarten](../virtual-network/quick-create-portal.md).

* Det virtuella nätverket har inga virtuella nätverksgateways. Om ditt virtuella nätverk har en gateway (antingen VPN eller ExpressRoute) måste du ta bort alla gateways. Den här konfigurationen kräver att virtuella nätverk ansluts i stället till virtual WAN-hubbgatewayen.

* Hämta ett IP-adressintervall för din hubbregion. Navet är ett virtuellt nätverk som skapas och används av Virtual WAN. Det adressintervall som du anger för navet kan inte överlappa något av dina befintliga virtuella nätverk som du ansluter till. Det får inte heller överlappa det adressintervall som du ansluter till lokalt. Om du inte känner till IP-adressintervallen i din lokala nätverkskonfiguration, samordna med någon som kan ange dessa uppgifter åt dig.

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Skapa ett virtuellt WAN

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

1. Navigera till sidan Virtuellt WAN. Klicka på **+Skapa en resurs** i portalen. Skriv **Virtuellt WAN** i sökrutan och välj Retur.
2. Välj **Virtuellt WAN** i resultatet. På sidan Virtuellt WAN klickar du på **Skapa** för att öppna sidan Skapa WAN.
3. Fyll i följande fält på fliken **Skapa** WAN på sidan **Skapa WAN:**

   ![Virtuellt WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Prenumeration** – Välj vilken prenumeration du vill använda.
   * **Resursgrupp** - Skapa nytt eller använd befintligt.
   * **Plats för resursgrupp** – Välj en resursplats i listrutan. Ett WAN är en global resurs och är inte kopplad till en viss region. Du måste dock välja en region för att lättare att hantera och leta upp WAN-resursen som du skapar.
   * **Namn** - Skriv det namn som du vill kalla ditt WAN.
   * **Typ:** Standard. Om du skapar ett basic WAN kan du bara skapa en Basic-hubb. Grundläggande nav kan endast ansluta från plats till plats.
4. När du har fyllt i fälten väljer du **Granska +Skapa**.
5. När valideringen har gått väljer du **Skapa** för att skapa det virtuella WAN-programmet.

## <a name="create-an-empty-virtual-hub"></a><a name="hub"></a>Skapa en tom virtuell hubb

1. Under ditt virtuella WAN väljer du Hubbar och klickar på **+Ny hubb**

   ![ny webbplats](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. Fyll i följande fält på sidan Skapa virtuellt nav.

   **Region** - Välj den region som du vill distribuera den virtuella hubben i.

   **Namn** - Ange det namn som du vill kalla din virtuella hubb.

   **Navprivat adressutrymme** - Navets adressintervall i CIDR-notation.

   ![ny webbplats](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. Klicka på **Granska + skapa**
4. Klicka på **Skapa** på sidan **validerings skickade**

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Skapa en P2S-konfiguration

En P2S-konfiguration definierar parametrarna för att ansluta fjärrklienter.

1. Navigera till **Alla resurser**.
2. Klicka på det virtuella WAN-nätverk du skapade.
3. Klicka på **+Skapa användar-VPN-konfiguration** högst upp på sidan för att öppna sidan **Skapa ny användar-VPN-konfiguration.**

   ![ny webbplats](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. På sidan **Skapa ny användar-VPN-konfiguration** fyller du i följande fält:

   **Konfigurationsnamn** – Det här är det namn som du vill använda för att referera till konfigurationen.

   **Tunneltyp** – Protokollet som ska användas för tunneln.

   **Root Certificate Name** (Rotcertifikatnamn) – Ett beskrivande namn för certifikatet.

   **Offentliga certifikatdata** - Base-64 kodade X.509-certifikatdata.
  
5. Klicka på **Skapa** för att skapa konfigurationen.

## <a name="edit-hub-assignment"></a><a name="edit"></a>Redigera hubbtilldelning

1. Navigera till **hubbarbladet** under det virtuella WAN-programmet
2. Välj den hubb som du vill associera vpn-serverkonfigurationen till och klicka på **...**

   ![ny webbplats](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. Klicka på **Redigera virtuellt nav**.
4. Markera kryssrutan **Inkludera punkt-till-plats-gateway** och välj den **gateway-skalningsenhet** som du vill använda.

   ![ny webbplats](media/virtual-wan-point-to-site-portal/p2s2.jpg)

Tabellen nedan visar information om tillgängliga **skalenheter**

| **Skalenhet** | **Dataflöde** | **P2S-anslutningar** |
| --- | --- | --- |
| 1| 500 Mbps | 500 |
| 2| 1 Gbit/s | 500 |
| 3| 1,5 Gbit/s | 500 |
| 4| 2 Gbit/s | 1000 |
| 5| 2,5 Gbit/s | 1000 |
| 6| 3 Gbit/s | 1000 |
| 7| 3,5 Gbit/s | 5000 |
| 8| 4 Gbit/s | 5000 |
| 9| 4,5 Gbit/s | 5000 |
| 10| 5 Gbit/s | 5000 |
| 11| 5,5 Gbit/s | 5000 |
| 12| 6 Gbit/s | 5000 |
| 13| 6,5 Gbit/s | 10000 |
| 14| 7 Gbit/s | 10000 |
| 15| 7,5 Gbit/s | 10000 |
| 16| 8 Gbit/s | 10000 |
| 17| 8,5 Gbit/s | 10000 |
| 18| 9 Gbit/s | 10000 |
| 19| 9,5 Gbit/s | 10000 |
| 20| 10 Gbit/s | 10000 |

5. Ange **den adresspool** som VPN-klienterna ska tilldelas IP-adresser från.
6. Klicka på **Bekräfta**
7. Åtgärden kan ta upp till 30 minuter att slutföra.

## <a name="download-vpn-profile"></a><a name="download"></a>Ladda ned VPN-profil

Använd VPN-profilen för att konfigurera dina klienter.

1. Klicka på **VPN-konfigurationer**på sidan för ditt virtuella WAN .
2. Högst upp på sidan klickar du på **Hämta användar-VPN-konfiguration**.
3. När filen har skapats klickar du på länken för att ladda ned den.
4. Använd profilfilen för att konfigurera VPN-klienterna.

### <a name="configure-user-vpn-clients"></a>Konfigurera VPN-klienter för användare
Använd den nedladdade profilen för att konfigurera fjärråtkomstklienterna. Proceduren skiljer sig beroende på operativsystem. Var noga med att följa rätt anvisningar nedan:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Ladda ned och installera OpenVPN-klienten från den officiella webbplatsen.
2. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken Användar-VPN-konfigurationer i Azure-portalen eller New-AzureRmVpnClientConfiguration i PowerShell.
3. Packa upp profilen. Öppna konfigurationsfilen vpnconfig.ovpn från OpenVPN-mappen i Anteckningar.
4. Fyll i avsnittet för P2S-klientcertifikatet med P2S-klientcertifikatets offentliga nyckel i base64. I ett PEM-formaterat certifikat öppnar du bara CER-filen och kopierar över base64-nyckeln mellan certifikathuvudena. Steg om du vill göra det finns [i Så här exporterar du ett certifikat för att hämta den kodade offentliga nyckeln.](certificates-point-to-site.md)
5. Fyll i avsnittet för den privata nyckeln med P2S-klientcertifikatets privata nyckel i base64. Steg för steg finns i [Så här extraherar du privat nyckel.](howto-openvpn-clients.md#windows).
6. Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
7. Kopiera filen vpnconfig.ovpn till mappen C:\Program Files\OpenVPN\config.
8. Högerklicka på OpenVPN-ikonen i systemfältet och klicka på Anslut.

##### <a name="ikev2"></a>IKEv2

1. Välj de VPN-klientkonfigurationsfiler som motsvarar Windows-datorns arkitektur. Välj ”VpnClientSetupAmd64”-installationspaketet för en 64-bitars processorarkitektur. Välj ”VpnClientSetupX86”-installationspaketet för en 32-bitars processorarkitektur.
2. Dubbelklicka på paketet för att installera det. Om du ser ett SmartScreen-popup-fönster klickar du på Mer information och sedan på Kör ändå.
3. På klientdatorn går du till Nätverksinställningar och klickar på VPN. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till.
4. Innan du försöker ansluta kontrollerar du att du har installerat ett klientcertifikat på klientdatorn. Ett klientcertifikat krävs för autentisering när du använder den interna Azure-certifikatautentiseringstypen. Mer information om hur du genererar certifikat finns i [Generera certifikat](certificates-point-to-site.md). Information om hur du installerar ett klientcertifikat finns i [Installera ett klientcertifikat](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Visa virtuellt WAN

1. Navigera till det virtuella WAN-nätverket.
2. Varje punkt på kartan på sidan Översikt motsvarar en hubb.
3. I avsnittet om hubbar och anslutningar kan du visa hubbstatus, plats, region, VPN-anslutningsstatus och byte in och ut.


## <a name="clean-up-resources"></a><a name="cleanup"></a>Rensa resurser

Du kan använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla resurser den innehåller när du inte längre behöver dem. Ersätt myResourceGroup med namnet på resursgruppen och kör följande PowerShell-kommando:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
