---
title: 'Skapa och installera P2S VPN-klientens konfigurationsfiler för Azure-certifikat-autentisering: Azure'
description: Skapa och installera Windows, Linux, Linux (strongSwan) och Mac OS VPN-klientens konfigurationsfiler för P2S-certifikatautentisering.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/13/2019
ms.author: cherylmc
ms.openlocfilehash: 07d48ed195f74c9aef5d34de1925ab8fc2a3ae21
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899876"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Skapa och installera konfigurationsfiler för VPN-klienten för interna P2S-konfigurationer för Azure-certifikatautentisering

Konfigurationsfiler för VPN-klienter finns i en zip-fil. Konfigurationsfiler ger de inställningar som krävs för att en ursprunglig Windows-, Mac IKEv2 VPN-eller Linux-klient ska kunna ansluta till ett VNet över punkt-till-plats-anslutningar som använder intern autentisering med Azure-certifikat.

Klientens konfigurationsfiler är speciella för VPN-konfigurationen för VNet. Om det finns några ändringar i VPN-konfigurationen för punkt-till-plats efter att du har genererat VPN-klientens konfigurationsfiler, till exempel typ av VPN-protokoll eller autentiseringstyp, måste du skapa nya konfigurationsfiler för VPN-klienter för dina användar enheter. 

* Mer information om punkt-till-plats-anslutningar finns i [About Point-to-Site VPN](point-to-site-about.md) (Om VPN för punkt-till-plats).
* OpenVPN-instruktioner finns i [konfigurera OpenVPN för P2s](vpn-gateway-howto-openvpn.md) och [konfigurera OpenVPN-klienter](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate"></a>Generera konfigurationsfiler för VPN-klienten

Innan du börjar ska du kontrol lera att alla anslutna användare har ett giltigt certifikat installerat på användarens enhet. Mer information om hur du installerar ett klient certifikat finns i [Installera ett klient certifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).

Du kan generera konfigurationsfiler för klienter med hjälp av PowerShell, eller med hjälp av Azure Portal. Båda metoderna returnerar samma zip-fil. Zippa upp filen om du vill visa följande mappar:

  * **WindowsAmd64** och **WindowsX86**, som innehåller installations paket för Windows 32-bitars och 64-bitars. **WindowsAmd64** Installer-paketet är för alla 64-bitars Windows-klienter som stöds, inte bara AMD.
  * **Generisk**, som innehåller allmän information som används för att skapa en egen VPN-klientkonfiguration. Den allmänna mappen anges om IKEv2 eller SSTP + IKEv2 har kon figurer ATS på gatewayen. Om endast SSTP har kon figurer ATS finns inte den generiska mappen.

### <a name="zipportal"></a>Skapa filer med hjälp av Azure Portal

1. I Azure Portal navigerar du till den virtuella Nätverksgatewayen för det virtuella nätverk som du vill ansluta till.
2. På sidan virtuell nätverksgateway klickar du på **punkt-till-plats-konfiguration**.
3. Klicka på **Ladda ned VPN-klient**överst på sidan punkt-till-plats-konfiguration. Det tar några minuter för klient konfigurations paketet att genereras.
4. Din webbläsare indikerar att en zip-fil för klient konfiguration är tillgänglig. Den heter samma namn som din gateway. Zippa upp filen för att visa mapparna.

### <a name="zipps"></a>Generera filer med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. När du genererar konfigurationsfiler för VPN-klienter är värdet för "-AuthenticationMethod" EapTls ". Generera VPN-klientens konfigurationsfiler med följande kommando:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Kopiera URL: en till webbläsaren för att ladda ned ZIP-filen och packa sedan upp filen för att visa mapparna.

## <a name="installwin"></a>Windows

Du kan använda samma konfigurations paket för VPN-klienten på varje Windows-klientdator, förutsatt att versionen överensstämmer med arkitekturen för-klienten. En lista över klient operativ system som stöds finns i avsnittet punkt-till-plats i [vanliga frågor och svar om VPN gateway](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Du måste ha administratörs behörighet på den Windows-klientdator från vilken du vill ansluta.
>
>

Använd följande steg för att konfigurera den interna Windows VPN-klienten för certifikatautentisering:

1. Välj de VPN-klientkonfigurationsfiler som motsvarar Windows-datorns arkitektur. Välj ”VpnClientSetupAmd64”-installationspaketet för en 64-bitars processorarkitektur. Välj ”VpnClientSetupX86”-installationspaketet för en 32-bitars processorarkitektur. 
2. Dubbelklicka på paketet för att installera det. Om du ser ett SmartScreen-popup-fönster klickar du på **Mer information** och sedan på **Kör ändå**.
3. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till. 
4. Innan du försöker ansluta kontrollerar du att du har installerat ett klientcertifikat på klientdatorn. Ett klientcertifikat krävs för autentisering när du använder den interna Azure-certifikatautentiseringstypen. Mer information om hur du skapar certifikat finns i [Skapa certifikat](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Information om hur du installerar ett klient certifikat finns i [Installera ett klient certifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>Mac (OS X)

 Du måste konfigurera den inbyggda IKEv2 VPN-klienten manuellt på varje Mac som ska ansluta till Azure. Azure tillhandahåller inte mobileconfig-filen för intern Azure-certifikatautentisering. Den **generiska** innehåller all information som du behöver för konfigurationen. Om du inte ser mappen Generic i nedladdningen beror det antagligen på att du inte valde IKEv2 som tunneltyp. Observera att VPN gateway Basic SKU inte stöder IKEv2. När du har valt IKEv2 genererar du ZIP-filen igen för att hämta mappen Generic.<br>Mappen Generic innehåller följande filer:

* **VpnSettings. XML**, som innehåller viktiga inställningar som server adress och tunnel typ. 
* **VpnServerRoot. cer**, som innehåller rot certifikatet som krävs för att verifiera Azure-VPN gateway under installationen av P2s-anslutningen.

Använd följande steg för att konfigurera den inbyggda VPN-klienten på Mac för certifikatautentisering. Du måste utföra de här stegen på varje Mac som ska ansluta till Azure:

1. Importera rot certifikatet **VpnServerRoot** till din Mac. Detta gör du genom att kopiera filen till din Mac och dubbelklicka på den. Klicka på **Lägg till** för att importera.

   ![Lägg till certifikat](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Om du dubbelklickar på certifikatet visas eventuellt inte dialog rutan **Lägg till** , men certifikatet installeras i rätt arkiv. Du kan kontrol lera certifikatet i inloggnings nyckel ringen under kategorin certifikat.
    >
  
2. Kontrol lera att du har installerat ett klient certifikat som har utfärdats av rot certifikatet som du laddade upp till Azure när du konfigurerade P2S-inställningar. Detta skiljer sig från VPNServerRoot som du installerade i föregående steg. Klient certifikatet används för autentisering och krävs. Mer information om hur du skapar certifikat finns i [Skapa certifikat](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Information om hur du installerar ett klient certifikat finns i [Installera ett klient certifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Öppna dialog rutan **nätverk** under **nätverks inställningar** och klicka på **+** om du vill skapa en ny anslutnings profil för VPN-klienten för en P2s-anslutning till Azure VNet.

   **Gränssnittets** värde är "VPN" och värdet för **VPN-typ** är IKEv2. Ange ett namn för profilen i fältet **tjänst namn** och klicka sedan på **skapa** för att skapa anslutnings profilen för VPN-klienten.

   ![nätverk](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. I den **allmänna** mappen, i filen **VpnSettings. XML** , kopierar du värdet för **VpnServer** -taggen. Klistra in det här värdet i fälten **Server adress** och **fjärr-ID** för profilen.

   ![Server information](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Klicka på **autentiseringsinställningar** och välj **certifikat**. 

   ![autentiseringsinställningar](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. Klicka på **Välj...** för att välja det klient certifikat som du vill använda för autentisering. Det här är det certifikat som du installerade i steg 2.

   ![certifikat](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Välj en identitet** visar en lista över certifikat som du kan välja bland. Välj rätt certifikat och klicka sedan på **Fortsätt**.

   ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. I fältet **lokalt ID** anger du namnet på certifikatet (från steg 6). I det här exemplet är det "ikev2Client.com". Klicka sedan på knappen **tillämpa** för att spara ändringarna.

   ![använd](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. I dialog rutan **nätverk** klickar du på **tillämpa** för att spara alla ändringar. Klicka sedan på **Anslut** för att starta P2s-anslutningen till det virtuella Azure-nätverket.

## <a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="installstrongswan"></a>Installera strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="genlinuxcerts"></a>Generera certifikat

Om du inte redan har skapat certifikat kan du använda följande steg:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install"></a>Installera och konfigurera

Följande instruktioner skapades på Ubuntu 18.0.4. Ubuntu-16.0.10 stöder inte strongSwan-ANVÄNDARGRÄNSSNITTET. Om du vill använda Ubuntu 16.0.10 måste du använda [kommando raden](#linuxinstallcli). Exemplen nedan kan inte matcha skärmar som visas, beroende på din version av Linux och strongSwan.

1. Öppna **terminalen** för att installera **StrongSwan** och dess nätverks hanterare genom att köra kommandot i exemplet.

   ```
   sudo apt install network-manager-strongswan
   ```
2. Välj **Inställningar** och välj sedan **nätverk**.

   ![redigera anslutningar](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Klicka på **+** knappen för att skapa en ny anslutning.

   ![Lägg till en anslutning](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Välj **IPSec/IKEv2 (strongSwan)** på menyn och dubbelklicka på. Du kan ge anslutningen ett namn i det här steget.

   ![Välj en Anslutnings typ](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Öppna filen **VpnSettings. XML** från den **allmänna** mappen som finns i hämtade konfigurationsfiler för klienter. Hitta taggen med namnet **VpnServer** och kopiera namnet, från och med "azuregateway" och sluta med ". cloudapp.net".

   ![namn på kopia](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Klistra in det här namnet i fältet **adress** i den nya VPN-anslutningen i **Gateway** -avsnittet. Sedan väljer du mappikonen i slutet av fältet **certifikat** , bläddrar till den **allmänna** mappen och väljer filen **VpnServerRoot** .
7. I avsnittet **klient** i anslutningen, för **autentisering**, väljer du **certifikat/privat nyckel**. För **certifikat** och **privat nyckel**väljer du certifikatet och den privata nyckeln som skapades tidigare. I **alternativ**väljer du **begär en inre IP-adress**. Klicka sedan på **Lägg till**.

   ![begär en inre IP-adress](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. Aktivera anslutningen.

## <a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>Installera strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Generera certifikat

Om du inte redan har skapat certifikat kan du använda följande steg:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Installera och konfigurera

1. Ladda ned VPNClient-paketet från Azure Portal.
2. Extrahera filen.
3. Kopiera eller flytta VpnServerRoot. cer till/etc/IPSec.d/cacerts. i den **allmänna** mappen
4. Kopiera eller flytta CP-klienten. p12 till/etc/IPSec.d/Private/. Den här filen är klient certifikatet för Azure VPN Gateway.
5. Öppna filen VpnSettings. xml och kopiera `<VpnServer>` värdet. Du kommer att använda det här värdet i nästa steg.
6. Justera värdena i exemplet nedan och Lägg sedan till exemplet i/etc/IPSec.conf-konfigurationen.
  
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
6. Lägg till följande i */etc/IPSec.Secrets*.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Kör följande kommandon:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Nästa steg

Gå tillbaka till artikeln för att [slutföra din P2s-konfiguration](vpn-gateway-howto-point-to-site-rm-ps.md).

Information om hur du felsöker P2S-anslutningar finns i följande artiklar:

  * [Felsöka Azure punkt-till-plats-anslutningar](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Felsöka VPN-anslutningar från Mac OS X VPN-klienter](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
