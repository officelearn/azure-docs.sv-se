---
title: 'Skapa och installera P2S VPN-klientkonfigurationsfiler för Azure certifikatautentisering: PowerShell: Azure | Microsoft Docs'
description: Skapa och installera Windows, Linux (strongSwan) och Mac OS X VPN-klientkonfigurationsfiler för P2S certifikatautentisering.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: cherylmc
ms.openlocfilehash: 9b9528aba0be8fd46087d97bc294552db608f1c1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-point-to-site-configurations"></a>Skapa och installera VPN klientkonfigurationsfiler för interna Azure certifikat autentisering punkt-till-plats-konfigurationer

Konfigurationsfiler för VPN-klienten finns i en zip-fil. Konfigurationsfiler ange inställningar som krävs för en ursprunglig Windows, Mac IKEv2 VPN- eller Linux-klient att ansluta till ett virtuellt nätverk via punkt-till-plats-anslutningar som använder interna Azure certifikatautentisering.

### <a name="workflow"></a>P2S-arbetsflöde

  1. Ställ in Azure VPN-gateway för en P2S-anslutning.
  2. Generera rotcertifikat och klientcertifikat. Överför den offentliga nyckeln för rot-certifikatet till Azure och installera klientcertifikat på klienheterna. Certifikat används för autentisering av anslutande användare.
  3. Hämta konfigurationen för VPN-klienten för autentiseringsalternativet som du väljer och använda den för att ställa in VPN-klienten på dina Windows- och Mac-enheter. På så sätt kan du ansluta till virtuella Azure-nätverk via en punkt-till-plats-anslutning.

>[!NOTE]
>Klientkonfigurationsfiler är specifika för VPN-konfigurationen för det virtuella nätverket. Om det finns ändringar i punkt-till-plats VPN-konfiguration när du skapar VPN-klienten konfigurationsfilerna, till exempel den VPN-protokollet eller autentiseringstyp, måste du generera en ny VPN-klientkonfigurationsfiler för användarenheter.
>
>

## <a name="generate"></a>Generera konfigurationsfiler för VPN-klienten

Innan du börjar bör du kontrollera att alla anslutande användare ha ett giltigt certifikat som installeras på användarens enhet. Mer information om hur du installerar ett klientcertifikat finns [installera ett klientcertifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).

Du kan generera client configuration-filer med hjälp av PowerShell eller med hjälp av Azure portal. Antingen metoden returnerar samma zip-filen. Packa upp filen om du vill visa följande mappar:

  * **WindowsAmd64** och **WindowsX86**, som innehåller installationsprogrammet för Windows 32-bitars och 64-bitars-paket. Den **WindowsAmd64** installer-paketet är för alla 64-bitars Windows-klienter, inte bara Amd.
  * **Allmän**, som innehåller allmän information som används för att skapa din egen konfiguration för VPN-klienten. Den generiska mappen tillhandahålls om IKEv2 eller SSTP + IKEv2 konfigurerades på gateway. Den generiska mappen är inte installerat om endast SSTP har konfigurerats.

### <a name="zipportal"></a>Skapa filer med hjälp av Azure portal

1. Navigera till den virtuella nätverksgatewayen för det virtuella nätverket som du vill ansluta till i Azure-portalen.
2. På sidan virtuella nätverkets gateway **punkt-till-plats configuration**.
3. Klicka på överst på sidan punkt-till-plats för **hämta VPN-klienten**. Det tar några minuter för klienten konfigurationspaketet ska genereras.
4. Webbläsaren visar att en klient configuration zip-fil är tillgänglig. Den heter samma namn som din gateway. Packa upp filen om du vill visa mapparna.

### <a name="zipps"></a>Skapa filer med hjälp av PowerShell

1. När genererar VPN-klienten konfigurationsfiler, värdet för '-AuthenticationMethod' är 'EapTls'. Generera konfigurationsfiler för VPN-klienten med följande kommando:

  ```powershell
  $profile=New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

  $profile.VPNProfileSASUrl
  ```
2. Kopiera Webbadressen till webbläsaren för att hämta zip-filen och sedan packa upp filen om du vill visa mapparna.

## <a name="installwin"></a>Installera - Windows

Du kan använda samma konfigurationspaketet för VPN-klienten på alla Windows-klientdatorer, förutsatt att versionen matchar arkitekturen för klienten. Lista över klientoperativsystem som stöds finns i avsnittet punkt-till-plats för den [VPN-Gateway FAQ](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Du måste ha administratörsbehörighet på Windows-klientdator som du vill ansluta.
>
>

Använd följande steg för att konfigurera den inbyggda Windows VPN-klienten för autentisering med datorcertifikat:

1. Välj VPN-klienten konfigurationsfilerna som motsvarar arkitekturen på Windows-dator. Välj installationspaketet 'VpnClientSetupAmd64' för en 64-bitars processorarkitektur. Välj installationspaketet 'VpnClientSetupX86' för en 32-bitars processorarkitektur. 
2. Dubbelklicka på paketet du vill installera den. Om du ser ett SmartScreen-popup-fönster klickar du på **Mer information** och sedan på **Kör ändå**.
3. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till. 
4. Innan du försöker ansluta och kontrollera att har du installerat ett klientcertifikat på klientdatorn. Ett klientcertifikat krävs för autentisering när du använder inbyggd Azure authentication certifikattyp. Mer information om hur du genererar certifikat finns [generera certifikat](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Information om hur du installerar ett klientcertifikat finns [installera ett klientcertifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>Installera - Mac (OS X)

Azure tillhandahåller inte mobileconfig-filen för interna Azure certifikatautentisering. Du måste manuellt konfigurera den inbyggda IKEv2 VPN-klienten på alla Mac som ska ansluta till Azure. Den **allmänna** mappen innehåller all information du behöver konfigurera den. Om du inte ser den generiska mappen i hämtningen, är det troligt att IKEv2 inte var markerat som ett Tunneltyp. Skapa zip-filen om du vill hämta den generiska mappen när IKEv2 är markerad. Den generiska mappen innehåller följande filer:

* **VpnSettings.xml**, som innehåller viktiga inställningar som typ av adress och tunnel. 
* **VpnServerRoot.cer**, som innehåller rotcertifikat som krävs för att validera Azure VPN-gatewayen under installationen av P2S-anslutning.

Använd följande steg för att konfigurera den inbyggda VPN-klienten på Mac för autentisering med datorcertifikat. Du har slutfört de här stegen på varje Mac som ska ansluta till Azure:

1. Importera den **VpnServerRoot** rotcertifikat till din Mac. Detta kan göras genom att kopiera filen till din Mac och dubbelklicka på den.  
Klicka på **Lägg till** att importera.

  ![Lägg till certifikat](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Dubbelklicka på certifikatet kan inte visa den **Lägg till** dialogrutan, men certifikatet har installerats i rätt lager. Du kan söka efter certifikatet i nyckelringen inloggningen under kategorin certifikat.
  
2. Kontrollera att du har installerat ett klientcertifikat som har utfärdats av rotcertifikatet som du överfört till Azure när du konfigurerade P2S inställningar. Detta skiljer sig från VPNServerRoot som du installerade i föregående steg. Klientcertifikatet används för autentisering och krävs. Mer information om hur du genererar certifikat finns [generera certifikat](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Information om hur du installerar ett klientcertifikat finns [installera ett klientcertifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Öppna den **nätverk** dialogrutan under **nätverksinställningar** och på **”+”** att skapa en ny klient för VPN-anslutningsprofil för en P2S-anslutning till Azure VNet.

  Den **gränssnittet** värdet är 'VPN- och **VPN-typ** värdet är 'IKEv2'. Ange ett namn för profilen i den **tjänstnamnet** fältet och klicka sedan på **skapa** att skapa VPN-anslutningsprofilen för klienten.

  ![Nätverk](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. I den **allmänna** mappen från den **VpnSettings.xml** fil, kopiera den **VpnServer** taggvärde. Klistra in det här värdet i den **serveradress** och **fjärr-ID för** för profilen.

  ![server info](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Klicka på **autentiseringsinställningar** och välj **certifikat**. 

  ![autentiseringsinställningar](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. Klicka på **Välj...** att välja det klientcertifikat som du vill använda för autentisering. Detta är det certifikat som du installerade i steg 2.

  ![certifikat](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Välj en identitet** visar en lista över certifikat som du kan välja från. Välj rätt certifikat och klicka sedan på **Fortsätt**.

  ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. I den **lokalt ID** anger namnet på certifikatet (från steg 6). I det här exemplet är det ”ikev2Client.com”. Klicka på **tillämpa** för att spara ändringarna.

  ![tillämpa](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. På den **nätverk** dialogrutan klickar du på **tillämpa** spara alla ändringar. Klicka på **Anslut** att starta P2S-anslutning till Azure VNet.

## <a name="installlinux"></a>Installera - Linux (strongSwan)

### <a name="extract-the-key-and-certificate"></a>Extrahera nyckeln och certifikatet

För strongSwan måste du extrahera nyckeln och certifikatet från klientens certifikat (.pfx-fil) och spara dem i enskilda PEM-filer.
Följ stegen nedan:

1. Hämta och installera OpenSSL från [OpenSSL](https://www.openssl.org/source/).
2. Öppna ett kommandoradsfönster och ändra till den katalog där du installerade OpenSSL, till exempel ' c:\OpenSLL-Win64\bin\'.
3. Kör följande kommando för att extrahera den privata nyckeln och spara den till en ny fil med namnet 'privatekey.pem' från ditt klientcertifikat:

  ```
  C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nocerts -out privatekey.pem -nodes
  ```
4.  Nu ska du köra följande kommando för att extrahera offentliga certifikat och spara den till en ny fil:

  ```
  C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nokeys -out publiccert.pem -nodes
  ```

### <a name="install"></a>Installera

Följande instruktioner har skapats via strongSwan 5.5.1 på Ubuntu 17.0.4. Ubuntu 16.0.10 stöder inte strongSwan GUI. Om du vill använda Ubuntu 16.0.10 behöver du använda kommandoraden. Exemplen nedan är inte matchar skärmar som visas, beroende på din version av Linux- och strongSwan.

1. Öppna den **Terminal** installera **strongSwan** och dess Nätverkshanteraren genom att köra kommandot i exemplet. Om du får ett fel som rör *libcharon-extra-plugin-program för*, Ersätt den med 'strongswan-plugin-eap-mschapv2'.

  ```
  sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
  ```
2. Välj den **Nätverkshanteraren** ikon (uppåt-pilen/nedåtpilen), välj sedan **redigera anslutningar**.

  ![Redigera anslutningar](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Klicka på den **Lägg till** för att skapa en ny anslutning.

  ![Lägga till en anslutning](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Välj **IPsec/IKEv2 (strongswan)** från den nedrullningsbara menyn och klicka sedan på **skapa**. Du kan byta namn på anslutningen i det här steget.

  ![Välj en anslutningstyp](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Öppna den **VpnSettings.xml** filen från den **allmänna** mappen som finns i de hämtade klienten konfigurationsfilerna. Taggen kallas **VpnServer** och kopiera namnet börjar med 'azuregateway' och slutar med ”. cloudapp.net'.

  ![Kopiera namn](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Klistra in det här namnet i den **adress** för din nya VPN-anslutningen i den **Gateway** avsnitt. Sedan väljer du mappikonen i slutet av den **certifikat** fältet, bläddra till den **allmänna** och välj den **VpnServerRoot** fil.
7. I den **klienten** delen av anslutningen för **autentisering**väljer **certifikat och privata nycklar**. För **certifikat** och **privata nyckeln**, Välj certifikatet och den privata nyckeln som skapats tidigare. I **alternativ**väljer **begära en inre IP-adress**. Klicka på **Lägg till**.

  ![begär en inre IP-adress](./media/point-to-site-vpn-client-configuration-azure-cert/inneripreq.png)
8. Klicka på den **Nätverkshanteraren** ikonen (pilen/ned-uppilen) och över **VPN-anslutningar**. Du ser VPN-anslutningen som du skapade. Klicka för att upprätta anslutningen.

## <a name="next-steps"></a>Nästa steg

Gå tillbaka till artikeln [Slutför konfigurationen P2S](vpn-gateway-howto-point-to-site-rm-ps.md).

P2S felsökningsinformation [felsöka Azure punkt-till-plats-anslutningar](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) och [felsökning av VPN-anslutningar från VPN för Mac OS X-klienter](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md).