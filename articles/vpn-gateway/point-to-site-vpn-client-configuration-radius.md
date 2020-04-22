---
title: 'Azure VPN Gateway: Skapa & installera VPN-klientkonfigurationsfiler - P2S RADIUS-anslutningar'
description: Skapa konfigurationsfiler för Windows, Mac OS X och Linux VPN-klient för anslutningar som använder RADIUS-autentisering.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 69517d69a26364cf1cc950d7aaa849522decacf1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732740"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Skapa och installera KONFIGURATIONSFILER för VPN-klient för P2S RADIUS-autentisering

Om du vill ansluta till ett virtuellt nätverk via P2S (Point-to-Site) måste du konfigurera klientenheten som du ansluter från. Du kan skapa P2S VPN-anslutningar från Windows-, Mac OS X- och Linux-klientenheter. 

När du använder RADIUS-autentisering finns det flera autentiseringsalternativ: autentisering av användarnamn/lösenord, certifikatautentisering och andra autentiseringstyper. VPN-klientkonfigurationen är olika för varje typ av autentisering. Om du vill konfigurera VPN-klienten använder du klientkonfigurationsfiler som innehåller de nödvändiga inställningarna. Den här artikeln hjälper dig att skapa och installera VPN-klientkonfigurationen för den RADIUS-autentiseringstyp som du vill använda.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Konfigurationsarbetsflödet för P2S RADIUS-autentisering är följande:

1. [Konfigurera Azure VPN-gatewayen för P2S-anslutning](point-to-site-how-to-radius-ps.md).
2. [Konfigurera RADIUS-servern för autentisering](point-to-site-how-to-radius-ps.md#radius). 
3. **Skaffa VPN-klientkonfigurationen för det autentiseringsalternativ du väljer och använd den för att konfigurera VPN-klienten (den** här artikeln).
4. [Slutför din P2S-konfiguration och anslut](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Om det finns några ändringar i VPN-konfigurationen för punkt till plats när du har genererat konfigurationsprofilen för VPN-klienten, till exempel VPN-protokolltypen eller autentiseringstypen, måste du generera och installera en ny VPN-klientkonfiguration på användarnas enheter.
>
>

Om du vill använda avsnitten i den här artikeln bestämmer du först vilken typ av autentisering du vill använda: användarnamn/lösenord, certifikat eller andra typer av autentisering. Varje avsnitt innehåller steg för Windows, Mac OS X och Linux (begränsade steg är tillgängliga för närvarande).


## <a name="usernamepassword-authentication"></a><a name="adeap"></a>Autentisering av användarnamn/lösenord

Du kan konfigurera autentisering av användarnamn/lösenord så att du antingen använder Active Directory eller inte använder Active Directory. Se till att alla anslutande användare har användarnamn/lösenordsuppgifter som kan autentiseras via RADIUS i båda scenarierna.

När du konfigurerar autentisering av användarnamn/lösenord kan du bara skapa en konfiguration för EAP-MSCHAPv2-protokollet för användarnamn/lösenord. I kommandona `-AuthenticationMethod` är `EapMSChapv2`.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="usernamefiles"></a>1. Generera konfigurationsfiler för VPN-klient

Du kan generera KONFIGURATIONSFILER FÖR VPN-klienten med hjälp av Azure-portalen eller med hjälp av Azure PowerShell.

#### <a name="azure-portal"></a>Azure Portal

1. Navigera till den virtuella nätverksgatewayen.
2. Klicka **på Konfigurationen för punkt till plats**.
3. Klicka på **Ladda ner VPN-klient**.
4. Välj klienten och fyll i all information som begärs.
5. Klicka på **Hämta** om du vill generera ZIP-filen.
6. Zip-filen hämtas, vanligtvis till mappen Nedladdningar.

#### <a name="azure-powershell"></a>Azure PowerShell

Generera VPN-klientkonfigurationsfiler för användning med autentisering av användarnamn/lösenord. Du kan generera konfigurationsfilerna för VPN-klienten med följande kommando:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Om du kör kommandot returneras en länk. Kopiera och klistra in länken till en webbläsare för att ladda ner **VpnClientConfiguration.zip**. Packa upp filen för att visa följande mappar: 
 
* **WindowsAmd64** och **WindowsX86**: Dessa mappar innehåller Windows 64-bitars- respektive 32-bitars installationspaket. 
* **Allmän:** Den här mappen innehåller allmän information som du använder för att skapa din egen VPN-klientkonfiguration. Du behöver inte den här mappen för konfigurationer för autentisering av användarnamn/lösenord.
* **Mac**: Om du konfigurerade IKEv2 när du skapade den virtuella nätverksgatewayen visas en mapp med namnet **Mac** som innehåller en **mobileconfig-fil.** Du använder den här filen för att konfigurera Mac-klienter.

Om du redan har skapat klientkonfigurationsfiler `Get-AzVpnClientConfiguration` kan du hämta dem med hjälp av cmdleten. Men om du gör några ändringar i din P2S VPN-konfiguration, till exempel VPN-protokolltyp eller autentiseringstyp, uppdateras inte konfigurationen automatiskt. Du måste `New-AzVpnClientConfiguration` köra cmdlet för att skapa en ny konfigurationshämtning.

Om du vill hämta tidigare genererade klientkonfigurationsfiler använder du följande kommando:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a>2. Konfigurera VPN-klienter

Du kan konfigurera följande VPN-klienter:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux med strongSwan](#adlinuxcli)
 
#### <a name="windows-vpn-client-setup"></a><a name="adwincli"></a>Installation av Windows VPN-klient

Du kan använda samma VPN-klientkonfigurationspaket på varje Windows-klientdator, så länge versionen matchar klientens arkitektur. En lista över klientoperativsystem som stöds finns i [vanliga frågor och svar](vpn-gateway-vpn-faq.md#P2S).

Använd följande steg för att konfigurera den inbyggda Windows VPN-klienten för certifikatautentisering:

1. Välj de VPN-klientkonfigurationsfiler som motsvarar Windows-datorns arkitektur. För en 64-bitars processorarkitektur väljer du **installationspaketet vpnclientSetupAmd64.** För en 32-bitars processorarkitektur väljer du installationspaketet **vpnclientSetupX86.** 
2. Om du vill installera paketet dubbelklickar du på det. Om du ser ett SmartScreen-popup-fönster väljer du **Mer information** > **Kör ändå**.
3. På klientdatorn bläddrar du till **Nätverksinställningar** och väljer **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till. 

#### <a name="mac-os-x-vpn-client-setup"></a><a name="admaccli"></a>Installation av MAC (OS X) VPN-klient

1. Välj **filen VpnClientSetup mobileconfig** och skicka den till var och en av användarna. Du kan använda e-post eller annan metod.

2. Leta upp **mobileconfig-filen** på Mac.Locate the mobileconfig file on the Mac.

   ![Plats för mobileconfig-filen](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Valfritt steg - Om du vill ange en anpassad DNS lägger du till följande rader i **mobileconfig-filen:**

   ```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        </array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
   ```
4. Dubbelklicka på profilen för att installera den och välj **Fortsätt**. Profilnamnet är detsamma som namnet på det virtuella nätverket.

   ![Installationsmeddelande](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Välj **Fortsätt** om du vill lita på profilens avsändare och fortsätta med installationen.

   ![Bekräftelsemeddelande](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Under profilinstallationen har du möjlighet att ange användarnamn och lösenord för VPN-autentisering. Det är inte obligatoriskt att ange den här informationen. Om du gör det sparas och används informationen automatiskt när du startar en anslutning.Välj **Installera** för att fortsätta.

   ![Användarnamn och lösenordsrutor för VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Ange ett användarnamn och lösenord för de privilegier som krävs för att installera profilen på datorn. Välj **OK**.

   ![Rutor för användarnamn och lösenord för profilinstallation](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. När profilen har installerats visas den i dialogrutan **Profiler.** Du kan också öppna den här dialogrutan senare från **Systeminställningar**.

   ![Dialogrutan "Profiler"](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. Om du vill komma åt VPN-anslutningen öppnar du dialogrutan **Nätverk** från **Systeminställningar**.

   ![Ikoner i systeminställningar](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. VPN-anslutningen visas som **IkeV2-VPN**. Du kan ändra namnet genom att uppdatera **mobileconfig-filen.**

    ![Information om VPN-anslutningen](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Välj **autentiseringsinställningar**. Välj **Användarnamn** i listan och ange dina autentiseringsuppgifter. Om du angav autentiseringsuppgifterna tidigare **väljs användarnamn** automatiskt i listan och användarnamnet och lösenordet förifylls. Välj **OK** för att spara inställningarna.

    ![Inställningar för autentisering](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. I dialogrutan **Nätverk** väljer du **Använd** för att spara ändringarna. Om du vill initiera anslutningen väljer du **Anslut**.

#### <a name="linux-vpn-client-setup-through-strongswan"></a><a name="adlinuxcli"></a>Linux VPN-klientinstallation genom strongSwan

Följande instruktioner skapades genom strongSwan 5.5.1 på Ubuntu 17.0.4. Faktiska skärmar kan vara olika, beroende på din version av Linux och strongSwan.

1. Öppna **terminalen** för att installera **strongSwan** och dess Network Manager genom att köra kommandot i exemplet. Om du får ett felmeddelande `libcharon-extra-plugins`som är `strongswan-plugin-eap-mschapv2`relaterat till ersätter du det med .

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Välj **ikonen Nätverkshanteraren** (upp-pil/nedpil) och välj **Redigera anslutningar**.

   !["Redigera anslutningar" i Nätverkshanteraren](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Välj knappen **Lägg** till om du vill skapa en ny anslutning.

   ![Knappen "Lägg till" för en anslutning](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Välj **IPsec/IKEv2 (strongswan)** på rullgardinsmenyn och välj sedan **Skapa**. Du kan byta namn på anslutningen i det här steget.

   ![Välja anslutningstyp](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Öppna filen **VpnSettings.xml** från mappen **Generisk** för de hämtade klientkonfigurationsfilerna. Leta reda `VpnServer` på taggen som `azuregateway` anropas `.cloudapp.net`och kopiera namnet, som börjar med och slutar med .

   ![Innehållet i filen VpnSettings.xml](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Klistra in det här namnet i **adressfältet** för din nya VPN-anslutning i avsnittet **Gateway.** Välj sedan mappikonen i slutet av **fältet Certifikat,** bläddra till mappen **Allmänt** och välj filen **VpnServerRoot.**
7. I avsnittet **Klient** i anslutningen väljer du **EAP** för **autentisering**och anger ditt användarnamn och lösenord. Du kanske måste välja låsikonen till höger för att spara den här informationen. Välj sedan **Spara**.

   ![Redigera anslutningsinställningar](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Välj **ikonen Nätverkshanteraren** (upp-pil/nedpil) och hovra över **VPN-anslutningar**. Du ser VPN-anslutningen som du skapade. Om du vill initiera anslutningen markerar du den.

   !["VPN Radius"-anslutning i Network Manager](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certificate-authentication"></a><a name="certeap"></a>Certifikatsautentisering
 
Du kan skapa VPN-klientkonfigurationsfiler för RADIUS-certifikatautentisering som använder EAP-TLS-protokollet. Vanligtvis används ett företagsutfärdat certifikat för att autentisera en användare för VPN. Kontrollera att alla anslutande användare har ett certifikat installerat på sina enheter och att RADIUS-servern kan validera certifikatet.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

I kommandona `-AuthenticationMethod` är `EapTls`. Under certifikatautentisering validerar klienten RADIUS-servern genom att validera dess certifikat. `-RadiusRootCert`är FILEN .cer som innehåller rotcertifikatet som används för att validera RADIUS-servern.

Varje VPN-klientenhet kräver ett installerat klientcertifikat. Ibland har en Windows-enhet flera klientcertifikat. Under autentiseringen kan detta resultera i en popup-dialogruta med alla certifikat. Användaren måste sedan välja det certifikat som ska användas. Rätt certifikat kan filtreras bort genom att ange det rotcertifikat som klientcertifikatet ska kedjas till. 

`-ClientRootCert`är filfilen .cer som innehåller rotcertifikatet. Det är en valfri parameter. Om enheten som du vill ansluta från bara har ett klientcertifikat behöver du inte ange den här parametern.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="certfiles"></a>1. Generera konfigurationsfiler för VPN-klient

Generera VPN-klientkonfigurationsfiler för användning med certifikatautentisering. Du kan generera konfigurationsfilerna för VPN-klienten med följande kommando:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Om du kör kommandot returneras en länk. Kopiera och klistra in länken till en webbläsare för att ladda ner VpnClientConfiguration.zip. Packa upp filen för att visa följande mappar:

* **WindowsAmd64** och **WindowsX86**: Dessa mappar innehåller Windows 64-bitars- respektive 32-bitars installationspaket. 
* **GenericDevice**: Den här mappen innehåller allmän information som används för att skapa din egen VPN-klientkonfiguration.

Om du redan har skapat klientkonfigurationsfiler `Get-AzVpnClientConfiguration` kan du hämta dem med hjälp av cmdleten. Men om du gör några ändringar i din P2S VPN-konfiguration, till exempel VPN-protokolltyp eller autentiseringstyp, uppdateras inte konfigurationen automatiskt. Du måste `New-AzVpnClientConfiguration` köra cmdlet för att skapa en ny konfigurationshämtning.

Om du vill hämta tidigare genererade klientkonfigurationsfiler använder du följande kommando:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a>2. Konfigurera VPN-klienter

Du kan konfigurera följande VPN-klienter:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (stöds, ingen artikel steg ännu)

#### <a name="windows-vpn-client-setup"></a><a name="certwincli"></a>Installation av Windows VPN-klient

1. Välj ett konfigurationspaket och installera det på klientenheten. För en 64-bitars processorarkitektur väljer du **installationspaketet vpnclientSetupAmd64.** För en 32-bitars processorarkitektur väljer du installationspaketet **vpnclientSetupX86.** Om du ser ett SmartScreen-popup-fönster väljer du **Mer information** > **Kör ändå**. Du kan också spara paketet om du vill installera det på andra klientdatorer.
2. Varje klient kräver ett klientcertifikat för autentisering. Installera klientcertifikatet. Information om klientcertifikat finns i [Klientcertifikat för punkt-till-plats](vpn-gateway-certificates-point-to-site.md). Om du vill installera ett certifikat som har genererats finns i [Installera ett certifikat på Windows-klienter](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. På klientdatorn bläddrar du till **Nätverksinställningar** och väljer **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till.

#### <a name="mac-os-x-vpn-client-setup"></a><a name="certmaccli"></a>Installation av MAC (OS X) VPN-klient

Du måste skapa en separat profil för varje Mac-enhet som ansluter till det virtuella Azure-nätverket. Detta beror på att dessa enheter kräver att användarcertifikatet för autentisering anges i profilen. Den **allmänna** mappen har all information som krävs för att skapa en profil:

* **VpnSettings.xml** innehåller viktiga inställningar som serveradress och tunneltyp.
* **VpnServerRoot.cer** innehåller rotcertifikatet som krävs för att validera VPN-gatewayen under installationen av P2S-anslutningen.
* **RadiusServerRoot.cer** innehåller rotcertifikatet som krävs för att validera RADIUS-servern under autentiseringen.

Använd följande steg för att konfigurera den inbyggda VPN-klienten på en Mac för certifikatautentisering:

1. Importera **rotcertifikaten VpnServerRoot** och **RadiusServerRoot** till din Mac. Kopiera varje fil till din Mac, dubbelklicka på den och välj sedan **Lägg till**.

   ![Lägga till VpnServerRoot-certifikatet](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Lägga till RadiusServerRoot-certifikatet](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Varje klient kräver ett klientcertifikat för autentisering. Installera klientcertifikatet på klientenheten.
3. Öppna dialogrutan **Nätverk** under **Nätverksinställningar**. Välj **+** det här om du vill skapa en ny VPN-klientanslutningsprofil för en P2S-anslutning till det virtuella Azure-nätverket.

   **Gränssnittets** värde är **VPN**och **VPN-typvärdet** är **IKEv2**. Ange ett namn för profilen i rutan **Tjänstnamn** och välj sedan **Skapa** för att skapa VPN-klientanslutningsprofilen.

   ![Information om gränssnitts- och tjänstnamn](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. Kopiera **vpnserver-taggvärdet** från filen **VpnSettings.xml** i mappen **Allmänt.** Klistra in det här värdet i rutorna **Serveradress** och **Fjärr-ID** i profilen. Lämna rutan **Lokalt ID** tomt.

   ![Serverinformation](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Välj **Autentiseringsinställningar**och välj **Certifikat**. 

   ![Inställningar för autentisering](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Klicka på **Välj** om du vill välja det certifikat som du vill använda för autentisering.

   ![Välja ett certifikat för autentisering](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Välj En identitet** visar en lista över certifikat som du kan välja mellan. Markera rätt certifikat och välj sedan **Fortsätt**.

   !["Välj en identitetslista"](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. I rutan **Lokalt ID** anger du namnet på certifikatet (från steg 6). I det här exemplet är det **ikev2Client.com**. Välj sedan knappen **Använd** för att spara ändringarna.

   ![Rutan "Lokalt ID"](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. Välj **Använd** för att spara alla ändringar i dialogrutan **Nätverk.** Välj sedan **Anslut** för att starta P2S-anslutningen till det virtuella Azure-nätverket.

## <a name="working-with-other-authentication-types-or-protocols"></a><a name="otherauth"></a>Arbeta med andra autentiseringstyper eller protokoll

Om du vill använda en annan autentiseringstyp (till exempel OTP) eller använda ett annat autentiseringsprotokoll (till exempel PEAP-MSCHAPv2 i stället för EAP-MSCHAPv2) måste du skapa en egen KONFIGURATIONSPROFIL för VPN-klienten. För att skapa profilen behöver du information som IP-adress för virtuell nätverksgateway, tunneltyp och delningstunnelvägar. Du kan hämta den här informationen med hjälp av följande steg:

1. Använd `Get-AzVpnClientConfiguration` cmdlet för att generera VPN-klientkonfigurationen för EapMSChapv2.

2. Packa upp filen VpnClientConfiguration.zip och leta efter mappen **GenericDevice.** Ignorera mapparna som innehåller Windows-installationsprogrammet för 64- och 32-bitarsarkitekturer.
 
3. **Mappen GenericDevice** innehåller en XML-fil som heter **VpnSettings**. Den här filen innehåller all information som krävs:

   * **VpnServer**: FQDN för Azure VPN-gatewayen. Det här är adressen som klienten ansluter till.
   * **VpnType**: Tunneltyp som du använder för att ansluta.
   * **Vägar**: Vägar som du måste konfigurera i din profil så att endast trafik som är på väg till det virtuella Azure-nätverket skickas över P2S-tunneln.
   
   **GenericDevice-mappen** innehåller också en .cer-fil som heter **VpnServerRoot**. Den här filen innehåller rotcertifikatet som krävs för att validera Azure VPN-gatewayen under P2S-anslutningskonfiguration. Installera certifikatet på alla enheter som ansluter till det virtuella Azure-nätverket.

## <a name="next-steps"></a>Nästa steg

Gå tillbaka till artikeln för att [slutföra din P2S-konfiguration](point-to-site-how-to-radius-ps.md).

Felsökningsinformation för P2S finns i [Felsöka Azure point-to-site-anslutningar](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
