---
title: Hur du konfigurerar OpenVPN klienter för Azure VPN Gateway | Microsoft Docs
description: Steg för att konfigurera OpenVPN klienter för Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 141f29ea1c7ebdd8208dda6bc39a60f7873dab7d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977850"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway-preview"></a>Konfigurera OpenVPN klienter för Azure VPN Gateway (förhandsversion)

Den här artikeln hjälper dig att konfigurera OpenVPN klienter.

> [!IMPORTANT]
> Den allmänt tillgängliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har slutfört stegen för att konfigurera OpenVPN för din VPN-gateway. Mer information finns i [konfigurera OpenVPN för Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Windows-klienter

1. Hämta och installera klienten OpenVPN från officiellt [OpenVPN webbplats](https://openvpn.net/index.php/open-source/downloads.html).
2. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken för punkt-till-plats-konfiguration i Azure-portalen eller ”New-AzureRmVpnClientConfiguration” i PowerShell.
3. Packa upp profilen. Öppna konfigurationsfilen vpnconfig.ovpn från mappen OpenVPN i anteckningar.
4. Fyll i avsnittet för P2S-klienten med P2S klienten certifikatets offentliga nyckel i base64. I ett formaterat PEM-certifikat öppna du helt enkelt CER-filen och kopiera över base64-nyckel mellan certifikat-huvuden. Se hur du exporterar ett certifikat för att få den kodade offentliga nyckeln.
5. Fyll i avsnittet för privat nyckel med P2S klienten certifikatets privata nyckel i base64. Information om hur man extraherar privata nyckel finns i [exportera nyckeln](vpn-gateway-certificates-point-to-site.md#clientexport).
6. Ändra inte några andra fält. Använd den fyllda i konfigurationen i indata för klienten för att ansluta till VPN.
7. Kopiera filen vpnconfig.ovpn till mappen C:\Program Files\OpenVPN\config.
8. Högerklicka på OpenVPN-ikonen i systemfältet och klicka på Anslut.

## <a name="mac"></a>Mac-klienter

1. Ladda ned och installera en OpenVPN-klient, till exempel [TunnelBlik](https://tunnelblick.net/downloads.html). 
2. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken punkt-till-plats-konfiguration i Azure-portalen eller genom att använda ”New-AzureRmVpnClientConfiguration” i PowerShell.
3. Packa upp profilen. Öppna konfigurationsfilen vpnconfig.ovpn från mappen OpenVPN i anteckningar.
4. Fyll i avsnittet för P2S-klienten med P2S klienten certifikatets offentliga nyckel i base64. I ett formaterat PEM-certifikat öppna du helt enkelt CER-filen och kopiera över base64-nyckel mellan certifikat-huvuden. Se [exportera den offentliga nyckeln](vpn-gateway-certificates-point-to-site.md#cer) information om hur du exporterar ett certifikat för att få den kodade offentliga nyckeln.
5. Fyll i avsnittet för privat nyckel med P2S klienten certifikatets privata nyckel i base64. Se [exportera den privata nyckeln](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/) information om hur man extraherar privat nyckel.
6. Ändra inte några andra fält. Använd den fyllda i konfigurationen i indata för klienten för att ansluta till VPN.
7. Dubbelklicka på profilfilen för att skapa profilen i tunnelblik.
8. Starta Tunnelblik från mappen program.
9. Klicka på ikonen Tunneblik i systemfältet och Välj Anslut.

## <a name="linux"></a>Linux-klienter

1. Öppna en ny terminalsession. Du kan öppna en ny session genom att trycka på ”Ctrl + Alt + t” på samma gång
2. Ange följande kommando för att installera nödvändiga komponenter:

  ```
  sudo apt-get install openvpn
  sudo apt-get -y install network-manager-openvpn
  sudo service network-manager restart
  ```
3. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken punkt-till-plats-konfiguration i Azure-portalen eller genom att använda ”New-AzureRmVpnClientConfiguration” i PowerShell.
4. Fyll i avsnittet för privat nyckel med P2S klienten certifikatets privata nyckel i base64. Se [exportera den privata nyckeln](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/) information om hur man extraherar privat nyckel.
5. Om du vill ansluta med hjälp av kommandoraden, skriver du följande:
  
  ```
  Sudo openvpn –config <name and path of your VPN profile file>
  ```
5. Gå till inställningar för att ansluta med hjälp av det grafiska Användargränssnittet.
6. Klicka på **+** att lägga till en ny VPN-anslutning.
7. Under **lägga till VPN-**, Välj **importera från fil...**
8. Bläddra till fil för Konfigurationsprofil och dubbelklicka på eller välj **öppna**
9. Klicka på **Lägg till** på den **lägga till VPN-** fönster.
  
  ![Importera från fil](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
10. Du kan ansluta genom att aktivera VPN **på** på den **nätverksinställningar** sida, eller under nätverksikonen i systemfältet.

## <a name="next-steps"></a>Nästa steg

Om du vill att VPN-klienter ska kunna komma åt resurser i ett annat virtuellt nätverk (produktion), följ instruktionerna på den [Vnet-till-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) artikeln om du vill konfigurera en vnet-till-vnet-anslutning. Var noga med att aktivera BGP på gateway och anslutningar, annars kommer inte trafiken.
