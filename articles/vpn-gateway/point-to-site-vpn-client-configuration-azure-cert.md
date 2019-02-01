---
title: 'Skapa och installera P2S VPN-klientkonfigurationsfiler för Azure-certifikatautentisering: Azure | Microsoft Docs'
description: Skapa och installera Windows, Linux, Linux (strongSwan) och Mac OS X VPN-klientkonfigurationsfiler för P2S-certifikatautentisering.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/18/2019
ms.author: cherylmc
ms.openlocfilehash: 0f834c88a22aca52a861309681ea0da204b2a552
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508012"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Skapa och installera VPN-klientkonfigurationsfiler för native Azure certificate authentication P2S-konfigurationer

VPN-klientkonfigurationsfiler finns i en zip-fil. Konfigurationsfiler ger de inställningar som krävs för en intern Windows, Mac IKEv2 VPN- eller Linux-klienter att ansluta till ett virtuellt nätverk via punkt-till-plats-anslutningar som använder Azures interna certifikatautentisering.

-Klientkonfigurationsfiler är specifika för VPN-konfigurationen för det virtuella nätverket. Om det finns några ändringar i punkt-till-plats-VPN-konfigurationen när du har genererat de VPN-klientkonfigurationsfilerna, till exempel typ av VPN-protokoll eller autentiseringstyp, måste du generera nya VPN-klientkonfigurationsfiler för dina användarenheter. 

* Mer information om punkt-till-plats-anslutningar finns i [About Point-to-Site VPN](point-to-site-about.md) (Om VPN för punkt-till-plats).
* OpenVPN anvisningar finns i [konfigurera OpenVPN för P2S](vpn-gateway-howto-openvpn.md) och [konfigurera OpenVPN klienter](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate"></a>Generera konfigurationsfiler för VPN-klienten

Innan du börjar måste du kontrollera att alla anslutande användare har ett giltigt certifikat som installeras på användarens enhet. Läs mer om hur du installerar ett klientcertifikat, [installera ett klientcertifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).

Du kan generera klientkonfigurationsfiler med hjälp av PowerShell, eller genom att använda Azure-portalen. Antingen metoden returnerar samma zip-filen. Packa upp filen om du vill visa följande mappar:

  * **WindowsAmd64** och **WindowsX86**, som innehåller installationsprogrammet för Windows 32-bitars och 64-bitars-paket. Den **WindowsAmd64** installer-paketet är för 64-bitars Windows-klienter, inte bara Amd stöds.
  * **Allmän**, som innehåller allmän information som används för att skapa egna VPN-klientkonfiguration. Den generiska mappen tillhandahålls om IKEv2 eller SSTP + IKEv2 konfigurerades på gatewayen. Om det bara SSTP har konfigurerats, är den generiska mappen inte finns.

### <a name="zipportal"></a>Skapa filer med hjälp av Azure-portalen

1. Navigera till den virtuella nätverksgatewayen för det virtuella nätverket som du vill ansluta till i Azure-portalen.
2. På sidan virtuellt nätverk gateway **punkt-till-plats-konfiguration**.
3. Överst på sidan för punkt-till-plats-konfiguration klickar du på **hämta VPN-klient**. Det tar några minuter för klienten konfigurationspaketet för att generera.
4. Webbläsaren visar att en klient configuration zip-fil finns. Den heter samma namn som din gateway. Packa upp filen om du vill visa mapparna.

### <a name="zipps"></a>Skapa filer med hjälp av PowerShell

1. Vid generering av VPN-klientkonfiguration filer, värdet för ”-AuthenticationMethod” är ”EapTls”. Generera VPN-klientkonfigurationsfiler med följande kommando:

  ```powershell
  $profile=New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

  $profile.VPNProfileSASUrl
  ```
2. Kopiera Webbadressen till webbläsaren för att ladda ned zip-filen och packa upp filen om du vill visa mapparna.

## <a name="installwin"></a>Windows

Du kan använda samma konfigurationspaketet för VPN-klienten på varje Windows-klientdator, förutsatt att versionen matchar arkitekturen för klienten. Listan över klientoperativsystem som stöds finns i avsnittet punkt-till-plats i den [VPN Gateway vanliga frågor och svar](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Du måste ha administratörsbehörighet på Windows-klientdator som du vill ansluta.
>
>

Använd följande steg för att konfigurera den inbyggda Windows VPN-klienten för autentisering med datorcertifikat:

1. Välj de VPN-klientkonfigurationsfiler som motsvarar Windows-datorns arkitektur. Välj ”VpnClientSetupAmd64”-installationspaketet för en 64-bitars processorarkitektur. Välj ”VpnClientSetupX86”-installationspaketet för en 32-bitars processorarkitektur. 
2. Dubbelklicka på paketet för att installera det. Om du ser ett SmartScreen-popup-fönster klickar du på **Mer information** och sedan på **Kör ändå**.
3. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till. 
4. Innan du försöker ansluta kontrollerar du att du har installerat ett klientcertifikat på klientdatorn. Ett klientcertifikat krävs för autentisering när du använder den interna Azure-certifikatautentiseringstypen. Läs mer om hur du genererar certifikat, [generera certifikat](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Information om hur du installerar ett klientcertifikat finns i [installera ett klientcertifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>Mac (OS X)

 Du måste konfigurera den inbyggda IKEv2 VPN-klienten manuellt på varje Mac som ska ansluta till Azure. Azure tillhandahåller inte mobileconfig-filen för intern Azure-certifikatautentisering. Den **allmän** innehåller all information som du behöver för konfigurationen. Om du inte ser mappen Generic i nedladdningen beror det antagligen på att du inte valde IKEv2 som tunneltyp. När du har valt IKEv2 genererar du ZIP-filen igen för att hämta mappen Generic.<br>Mappen Generic innehåller följande filer:

* **VpnSettings.xml**, som innehåller viktiga inställningar som typ av adress och tunnel. 
* **VpnServerRoot.cer**, som innehåller servercertifikatets rotcertifikat som krävs för att validera Azure VPN Gateway under installationen av P2S-anslutning.

Använd följande steg för att konfigurera den inbyggda VPN-klienten på Mac för certifikatautentisering. Du har slutfört de här stegen på varje Mac som ska ansluta till Azure:

1. Importera den **VpnServerRoot** rotcertifikat till din Mac. Detta kan göras genom att kopiera filen till din Mac och dubbelklicka på den.  
Klicka på **Lägg till** att importera.

  ![Lägg till certifikat](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Dubbelklicka på certifikatet visas inte den **Lägg till** dialogrutan, men certifikatet har installerats i rätt lager. Du kan söka efter certifikatet i nyckelringen inloggningen under kategorin certifikat.
    >
  
2. Kontrollera att du har installerat ett klientcertifikat som har utfärdats av det rotcertifikat som du överfört till Azure när du konfigurerade P2S inställningar. Detta skiljer sig från VPNServerRoot som installerades i föregående steg. Klientcertifikatet används för autentisering och är obligatoriskt. Läs mer om hur du genererar certifikat, [generera certifikat](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Information om hur du installerar ett klientcertifikat finns i [installera ett klientcertifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Öppna den **nätverk** dialogrutan under **nätverksinställningar** och klicka på **”+”** att skapa en ny klient för VPN-anslutningsprofil för en P2S-anslutning till Azure VNet.

  Den **gränssnittet** värdet är ”VPN” och **VPN-typ** värdet är ”IKEv2”. Ange ett namn för profilen i det **tjänstnamn** fältet och klickar sedan på **skapa** att skapa VPN-klienten anslutningsprofilen.

  ![nätverk](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. I den **allmän** mappen från den **VpnSettings.xml** fil, kopiera den **VpnServer** taggvärde. Klistra in det här värdet i den **serveradress** och **fjärr-ID för** fälten i profilen.

  ![serverinformation](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Klicka på **autentiseringsinställningar** och välj **certifikat**. 

  ![autentiseringsinställningar](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. Klicka på **Välj...** Ange det klientcertifikat som du vill använda för autentisering. Det här är det certifikat som du installerade i steg 2.

  ![certifikat](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Välj en identitet** visar en lista över certifikat som du kan välja bland. Välj rätt certifikat och klicka sedan på **Fortsätt**.

  ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. I den **lokala ID: T** fältet, anger du namnet på certifikatet (från steg 6). I det här exemplet är det ”ikev2Client.com”. Klicka sedan på **tillämpa** för att spara ändringarna.

  ![använd](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. På den **nätverk** dialogrutan klickar du på **tillämpa** att spara alla ändringar. Klicka sedan på **Connect** att starta P2S-anslutning till Azure VNet.

## <a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="extract-the-key-and-certificate"></a>Extrahera den nyckeln och certifikatet

För strongSwan måste du extrahera nyckeln och certifikatet från klientcertifikatet (.pfx-fil) och spara dem i enskilda .pem-filer.
Följ stegen nedan:

1. Ladda ned och installera OpenSSL från [OpenSSL](https://www.openssl.org/source/).
2. Öppna ett kommandoradsfönster och ändra till den katalog där du installerade OpenSSL, till exempel ”c:\OpenSLL-Win64\bin\'.
3. Kör följande kommando för att extrahera den privata nyckeln och spara den till en ny fil med namnet 'privatekey.pem' från ditt klientcertifikat:

  ```
  C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nocerts -out privatekey.pem -nodes
  ```
4.  Nu ska du köra följande kommando för att extrahera offentligt certifikat och spara den till en ny fil:

  ```
  C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nokeys -out publiccert.pem -nodes
  ```

### <a name="install"></a>Installera och konfigurera

Följande instruktioner har skapats via strongSwan 5.5.1 på Ubuntu 17.0.4. Ubuntu 16.0.10 stöder inte strongSwan GUI. Om du vill använda Ubuntu 16.0.10 du måste använda den [kommandoraden](#linuxinstallcli). I exemplen nedan kanske inte matchar skärmar som visas, beroende på din version av Linux- och strongSwan.

1. Öppna den **Terminal** installera **strongSwan** och dess Nätverkshanteraren genom att köra kommandot i det här exemplet. Om du får ett fel som rör *libcharon-extra-plugin-program*, Ersätt den med ”strongswan-plugin-programmet-eap-mschapv2'.

  ```
  sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
  ```
2. Välj den **nätverkshanterare** ikonen (pilen/ned-uppilen), välj sedan **redigera anslutningar**.

  ![Redigera anslutningar](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Klicka på den **Lägg till** för att skapa en ny anslutning.

  ![Lägg till en anslutning](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Välj **IPsec/IKEv2 (strongswan)** från den nedrullningsbara menyn och sedan på **skapa**. Du kan byta namn på anslutningen i det här steget.

  ![Välj en anslutningstyp](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Öppna den **VpnSettings.xml** fil från den **allmän** mapp i de hämtade klientkonfigurationsfilerna. Hitta taggen kallas **VpnServer** och kopiera namnet börjar med ”azuregateway” och slutar med ”. cloudapp.net'.

  ![Kopiera namn](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Klistra in det här namnet i den **adress** fältet för din nya VPN-anslutningen i den **Gateway** avsnittet. Välj mappikonen i slutet av den **certifikat** fältet, bläddra till den **allmän** mapp och välj den **VpnServerRoot** fil.
7. I den **klienten** avsnittet för anslutningen, för **autentisering**väljer **certifikat och privata nycklar**. För **certifikat** och **privata nyckeln**, väljer certifikatet och den privata nyckeln som skapats tidigare. I **alternativ**väljer **begär en inre IP-adress**. Klicka sedan på **Lägg till**.

  ![begär en inre IP-adress](./media/point-to-site-vpn-client-configuration-azure-cert/inneripreq.png)
8. Klicka på den **nätverkshanterare** ikon (uppåt-pilen/nedpilen) och hovra över **VPN-anslutningar**. Du ser VPN-anslutningen som du skapade. Klicka för att upprätta anslutningen.

## <a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>Installera strongSwan

Du kan använda följande CLI-kommandon eller använda strongSwan stegen i den [GUI](#install) att installera strongSwan.

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

### <a name="install-and-configure"></a>Installera och konfigurera

1. Ladda ned VPNClient-paket från Azure-portalen.
2. Extrahera filen.
3. Från den **allmän** mapp, kopiera eller flytta VpnServerRoot.cer till /etc/ipsec.d/cacerts.
4. Kopiera eller flytta cp client.p12 till /etc/ipsec.d/private/. Den här filen är certifikat för Azure VPN Gateway.
5. Öppna VpnSettings.xml filen och kopiera den <VpnServer> värde. Du använder det här värdet i nästa steg.
6. Justerar du värdena i exemplet nedan, och sedan lägger du till exempel i /etc/ipsec.conf-konfigurationen.
  
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

Gå tillbaka till artikeln om du vill [Slutför konfigurationen P2S](vpn-gateway-howto-point-to-site-rm-ps.md).

Om du vill felsöka P2S-anslutningar finns i följande artiklar:

  * [Felsöka Azure punkt-till-plats-anslutningar](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Felsöka VPN-anslutningar från VPN för Mac OS X-klienter](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
