---
title: 'Skapa & installera P2S VPN-klientkonfigurationsfiler: certifikatautentisering'
titleSuffix: Azure VPN Gateway
description: Skapa och installera Windows, Linux, Linux (strongSwan) och Mac OS X VPN-klientkonfigurationsfiler för P2S-certifikatautentisering.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/04/2020
ms.author: cherylmc
ms.openlocfilehash: d15efee635e131d658cd650b7f80eb9e670a0dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279421"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Skapa och installera KONFIGURATIONSFILER för VPN-klient för inbyggda P2S-konfigurationer för Azure-certifikat

VPN-klientkonfigurationsfiler finns i en zip-fil. Konfigurationsfiler innehåller de inställningar som krävs för en inbyggd Windows-, Mac IKEv2 VPN- eller Linux-klienter för att ansluta till ett virtuellt nätverk via Point-to-Site-anslutningar som använder inbyggd Azure-certifikatautentisering.

Klientkonfigurationsfiler är specifika för VPN-konfigurationen för det virtuella nätverket. Om det finns några ändringar i VPN-konfigurationen för Point-to-Site när du har genererat KONFIGURATIONSFILERNA för VPN-klienten, till exempel VPN-protokolltypen eller autentiseringstypen, ska du se till att generera nya VPN-klientkonfigurationsfiler för dina användarenheter. 

* Mer information om punkt-till-plats-anslutningar finns i [About Point-to-Site VPN](point-to-site-about.md) (Om VPN för punkt-till-plats).
* För OpenVPN-instruktioner finns i [Konfigurera OpenVPN för P2S](vpn-gateway-howto-openvpn.md) och [Konfigurera OpenVPN-klienter](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Generera konfigurationsfiler för VPN-klient

Innan du börjar kontrollerar du att alla anslutande användare har ett giltigt certifikat installerat på användarens enhet. Mer information om hur du installerar ett klientcertifikat finns i [Installera ett klientcertifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).

Du kan generera klientkonfigurationsfiler med PowerShell eller med hjälp av Azure-portalen. Båda metoderna returnerar samma zip-fil. Packa upp filen för att visa följande mappar:

  * **WindowsAmd64** och **WindowsX86**, som innehåller Windows 32-bitars och 64-bitars installationspaket, respektive. **WindowsAmd64-installationspaketet** är för alla 64-bitars Windows-klienter som stöds, inte bara Amd.
  * **Generic**, som innehåller allmän information som används för att skapa din egen VPN-klientkonfiguration. Den allmänna mappen tillhandahålls om IKEv2 eller SSTP+IKEv2 har konfigurerats på gatewayen. Om bara SSTP är konfigurerad finns inte mappen Allmänt.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Generera filer med Azure-portalen

1. I Azure-portalen navigerar du till den virtuella nätverksgatewayen för det virtuella nätverk som du vill ansluta till.
2. Klicka på **Point-to-site-konfiguration**på sidan för virtuell nätverksgateway .

   ![ladda ner klientportalen](./media/point-to-site-vpn-client-configuration-azure-cert/client-configuration-portal.png)
3. Klicka på **Hämta VPN-klient**högst upp på konfigurationssidan för Punkt till plats . Det tar några minuter för klientkonfigurationspaketet att generera.
4. Webbläsaren anger att en zip-fil för klientkonfiguration är tillgänglig. Det heter samma namn som din gateway. Packa upp filen för att visa mapparna.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Generera filer med PowerShell


1. När du genererar VPN-klientkonfigurationsfiler är värdet för '-AuthenticationMethod' 'EapTls'. Generera konfigurationsfilerna för VPN-klienten med följande kommando:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Kopiera webbadressen till webbläsaren för att hämta zip-filen och packa sedan upp filen för att visa mapparna.

## <a name="windows"></a><a name="installwin"></a>Windows

Du kan använda samma VPN-klientkonfigurationspaket på varje Windows-klientdator, så länge versionen matchar klientens arkitektur. En lista över klientoperativsystem som stöds finns i avsnittet Punkt-till-plats i [vanliga frågor och svar om VPN Gateway](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Du måste ha administratörsrättigheter på den Windows-klientdator som du vill ansluta från.
>
>

Använd följande steg för att konfigurera den inbyggda Windows VPN-klienten för certifikatautentisering:

1. Välj de VPN-klientkonfigurationsfiler som motsvarar Windows-datorns arkitektur. Välj ”VpnClientSetupAmd64”-installationspaketet för en 64-bitars processorarkitektur. Välj ”VpnClientSetupX86”-installationspaketet för en 32-bitars processorarkitektur. 
2. Dubbelklicka på paketet för att installera det. Om du ser en SmartScreen-popup klickar du på **Mer info**och **kör**ändå .
3. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till. 
4. Innan du försöker ansluta kontrollerar du att du har installerat ett klientcertifikat på klientdatorn. Ett klientcertifikat krävs för autentisering när du använder den interna Azure-certifikatautentiseringstypen. Mer information om hur du genererar certifikat finns i [Generera certifikat](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Information om hur du installerar ett klientcertifikat finns i [Installera ett klientcertifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 Du måste konfigurera den inbyggda IKEv2 VPN-klienten manuellt på varje Mac som ska ansluta till Azure. Azure tillhandahåller inte mobileconfig-filen för intern Azure-certifikatautentisering. Den **allmänna** innehåller all information som du behöver för konfiguration. Om du inte ser mappen Generic i nedladdningen beror det antagligen på att du inte valde IKEv2 som tunneltyp. Observera att VPN-gatewayen Basic SKU inte stöder IKEv2. När du har valt IKEv2 genererar du ZIP-filen igen för att hämta mappen Generic.<br>Mappen Generic innehåller följande filer:

* **VpnSettings.xml**, som innehåller viktiga inställningar som serveradress och tunneltyp. 
* **VpnServerRoot.cer**, som innehåller rotcertifikatet som krävs för att validera Azure VPN Gateway under P2S-anslutningskonfiguration.

Använd följande steg för att konfigurera den inbyggda VPN-klienten på Mac för certifikatautentisering. Du måste slutföra de här stegen på alla Mac-datorer som ansluter till Azure:

1. Importera **VpnServerRoot-rotcertifikatet** till din Mac. Detta kan göras genom att kopiera filen över till din Mac och dubbelklicka på den. Klicka på **Lägg till** för import.

   ![lägga till certifikat](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Om du dubbelklickar på certifikatet kanske du inte visar dialogrutan **Lägg till,** men certifikatet är installerat i rätt arkiv. Du kan söka efter certifikatet i inloggningsnyckelringen under certifikatkategorin.
    >
  
2. Kontrollera att du har installerat ett klientcertifikat som utfärdades av rotcertifikatet som du laddade upp till Azure när du konfigurerade P2S-inställningar för dig. Detta skiljer sig från VPNServerRoot som du installerade i föregående steg. Klientcertifikatet används för autentisering och krävs. Mer information om hur du genererar certifikat finns i [Generera certifikat](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Information om hur du installerar ett klientcertifikat finns i [Installera ett klientcertifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Öppna dialogrutan **Nätverk** under **Nätverksinställningar** och klicka på **'+'** för att skapa en ny VPN-klientanslutningsprofil för en P2S-anslutning till det virtuella Azure-nätverket.

   **Gränssnittsvärdet** är "VPN" och **VPN-typvärdet** är "IKEv2". Ange ett namn för profilen i fältet **Tjänstnamn** och klicka sedan på **Skapa** för att skapa VPN-klientanslutningsprofilen.

   ![network](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. Kopiera **vpnserver-taggvärdet** från filen **VpnSettings.xml** i mappen **Allmänt.** Klistra in det här värdet i fälten **Serveradress** och **Fjärr-ID** i profilen.

   ![serverinformation](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Klicka på **Autentiseringsinställningar** och välj **Certifikat**.För **Catalina**klickar du på **Ingen** och sedan **certifikat**

   ![inställningar för autentisering](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)

   * För Catalina väljer du **Ingen** och sedan **Certifikat**. **Välj** rätt certifikat:
   
   ![Catalina](./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png)

6. Klicka på **Välj...** för att välja det klientcertifikat som du vill använda för autentisering. Det här är certifikatet som du installerade i steg 2.

   ![certifikat](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Välj En identitet** visar en lista över certifikat som du kan välja mellan. Markera rätt certifikat och klicka sedan på **Fortsätt**.

   ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. I fältet **Lokalt ID** anger du namnet på certifikatet (från steg 6). I det här exemplet är det "ikev2Client.com". Klicka sedan på **Knappen Använd** för att spara ändringarna.

   ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. Klicka på **Använd** i dialogrutan **Nätverk** om du vill spara alla ändringar. Klicka sedan på **Anslut** för att starta P2S-anslutningen till det virtuella Azure-nätverket.

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>Installera strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Generera certifikat

Om du inte redan har genererat certifikat gör du så här:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Installera och konfigurera

Följande instruktioner skapades på Ubuntu 18.0.4. Ubuntu 16.0.10 stöder inte strongSwan GUI. Om du vill använda Ubuntu 16.0.10 måste du använda [kommandoraden](#linuxinstallcli). Exemplen nedan kanske inte matchar skärmar som du ser, beroende på din version av Linux och strongSwan.

1. Öppna **terminalen** för att installera **strongSwan** och dess Network Manager genom att köra kommandot i exemplet.

   ```
   sudo apt install network-manager-strongswan
   ```
2. Välj **Inställningar**och välj sedan **Nätverk**.

   ![redigera anslutningar](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Klicka **+** på knappen för att skapa en ny anslutning.

   ![lägga till en anslutning](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Välj **IPsec/IKEv2 (strongSwan)** på menyn och dubbelklicka. Du kan namnge din anslutning i det här steget.

   ![välja en anslutningstyp](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Öppna filen **VpnSettings.xml** från mappen **Generisk** i de hämtade klientkonfigurationsfilerna. Hitta taggen **VpnServer** och kopiera namnet, som börjar med "azuregateway" och slutar med '.cloudapp.net'.

   ![kopiera namn](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Klistra in det här namnet i **adressfältet** för din nya VPN-anslutning i avsnittet **Gateway.** Välj sedan mappikonen i slutet av **fältet Certifikat,** bläddra till mappen **Allmänt** och välj filen **VpnServerRoot.**
7. Välj **Certifikat/privat** **nyckel**för **autentisering**i avsnittet Klient i anslutningen . För **certifikat** och **privat nyckel**väljer du certifikatet och den privata nyckeln som skapades tidigare. Välj Begär **en inre IP-adress**i **Alternativ**. Klicka sedan på **Lägg till**.

   ![begära en inre IP-adress](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. Slå **på**anslutningen .

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>Installera strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Generera certifikat

Om du inte redan har genererat certifikat gör du så här:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Installera och konfigurera

1. Ladda ned VPNClient-paketet från Azure-portalen.
2. Extrahera filen.
3. Kopiera eller flytta VpnServerRoot.cer till /etc/ipsec.d/cacerts från mappen **Generic.**
4. Kopiera eller flytta cp client.p12 till /etc/ipsec.d/private/. Den här filen är klientcertifikat för Azure VPN Gateway.
5. Öppna filen VpnSettings.xml `<VpnServer>` och kopiera värdet. Du kommer att använda det här värdet i nästa steg.
6. Justera värdena i exemplet nedan och lägg sedan till exemplet i konfigurationen /etc/ipsec.conf.
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
6. Lägg till följande till */etc/ipsec.secrets*.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Kör följande kommandon:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Nästa steg

Gå tillbaka till artikeln för att [slutföra din P2S-konfiguration](vpn-gateway-howto-point-to-site-rm-ps.md).

Mer om du vill felsöka P2S-anslutningar finns i följande artiklar:

  * [Felsöka Azure point-to-site-anslutningar](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Felsöka VPN-anslutningar från Mac OS X VPN-klienter](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
