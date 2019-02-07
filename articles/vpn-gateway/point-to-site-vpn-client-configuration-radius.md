---
title: 'Skapa och installera VPN-klientkonfigurationsfiler för P2S RADIUS-anslutningar: PowerShell: Azure | Microsoft Docs'
description: Skapa Windows, Mac OS X och Linux VPN client configuration-filer för anslutningar som använder RADIUS-autentisering.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/07/2018
ms.author: cherylmc
ms.openlocfilehash: 8fc2c487a374a34cd9a7642a45fd59c04061b398
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55817826"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Skapa och installera VPN-klientkonfigurationsfiler för P2S RADIUS-autentisering

Om du vill ansluta till ett virtuellt nätverk via punkt-till-plats (P2S), måste du konfigurera klientenheten som du ansluter från. Du kan skapa P2S VPN-anslutningar från Windows, Mac OS X och Linux-klientenheter. 

När du använder RADIUS-autentisering, det finns flera autentiseringsalternativ för: autentisering med användarnamn/lösenord autentisering med datorcertifikat och andra typer av autentisering. VPN-klientkonfiguration är olika för varje typ av autentisering. För att konfigurera VPN-klienten, kan du använda klientkonfigurationsfiler som innehåller inställningarna som krävs. Den här artikeln hjälper dig att skapa och installera VPN-klientkonfiguration för RADIUS-autentiseringstyp som du vill använda.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Konfiguration av arbetsflödet för P2S RADIUS-autentisering är följande:

1. [Konfigurera Azure VPN-gateway för P2S-anslutning](point-to-site-how-to-radius-ps.md).
2. [Konfigurera RADIUS-servern för autentisering](point-to-site-how-to-radius-ps.md#radius). 
3. **Hämta VPN-klientkonfiguration för autentiseringsalternativet för ditt val och använda den för att konfigurera VPN-klienten** (den här artikeln).
4. [Slutför din P2S-konfiguration och ansluta](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Om det finns några ändringar i konfigurationen för punkt-till-plats-VPN när du har genererat, till exempel typ av VPN-protokoll eller autentiseringstyp Konfigurationsprofil VPN-klienten, måste du generera och installera en ny VPN-klientkonfiguration på användarnas enheter.
>
>

Om du vill använda avsnitten i den här artikeln måste börja med att bestämma vilken typ av autentisering som du vill använda: användarnamn/lösenord, certifikat eller andra typer av autentisering. Varje avsnitt innehåller steg för Windows, Mac OS X och Linux (begränsad steg som är tillgängliga just nu).

## <a name="adeap"></a>Autentisering med användarnamn/lösenord

Du kan konfigurera autentisering med användarnamn/lösenord för att antingen använda Active Directory eller inte använda Active Directory. Med båda fallen måste du se till att alla användare som ansluter har autentiseringsuppgifter som går att autentisera via RADIUS.

När du konfigurerar autentisering med användarnamn/lösenord kan skapa du bara en konfiguration för autentiseringsprotokollet EAP-MSCHAPv2 användarnamn/lösenord. I kommandon, `-AuthenticationMethod` är `EapMSChapv2`.

### <a name="usernamefiles"></a> 1. Generera VPN-klientkonfigurationsfiler

Generera VPN-klientkonfigurationsfiler för användning med autentisering med användarnamn/lösenord. Du kan generera VPN-klientkonfigurationsfiler med hjälp av följande kommando:

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Kör kommandot returnerar en länk. Kopiera och klistra in en länk till en webbläsare för att ladda ned **VpnClientConfiguration.zip**. Packa upp filen om du vill visa följande mappar: 
 
* **WindowsAmd64** och **WindowsX86**: Dessa mappar innehåller installationsprogrammet för Windows 64-bitars och 32-bitars-paket. 
* **Allmän**: Den här mappen innehåller allmän information som används för att skapa egna VPN-klientkonfiguration. Du behöver inte den här mappen för konfigurationer med användarnamn/lösenord autentisering.
* **Mac**: Om du har konfigurerat IKEv2 när du skapade den virtuella nätverksgatewayen finns i en mapp med namnet **Mac** som innehåller en **mobileconfig** fil. Du kan använda den här filen för att konfigurera Mac-klienter.

Om du redan har skapat klienten konfigurationsfiler, kan du hämta dem med hjälp av den `Get-AzureRmVpnClientConfiguration` cmdlet. Men om du gör några ändringar i dina P2S VPN-konfiguration, till exempel typ av VPN-protokoll eller autentiseringstyp, konfigurationen uppdateras inte automatiskt. Du måste köra den `New-AzureRmVpnClientConfiguration` cmdlet för att skapa en ny nedladdning.

Om du vill hämta tidigare genererade klientkonfigurationsfiler, använder du följande kommando:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="setupusername"></a> 2. Konfigurera VPN-klienter

Du kan konfigurera följande VPN-klienter:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux med hjälp av strongSwan](#adlinuxcli)
 
#### <a name="adwincli"></a>Windows VPN-klientkonfiguration

Du kan använda samma konfigurationspaketet för VPN-klienten på varje Windows-klientdator, förutsatt att versionen matchar arkitekturen för klienten. Lista över klientoperativsystem som stöds finns i den [vanliga frågor och svar](vpn-gateway-vpn-faq.md#P2S).

Använd följande steg för att konfigurera den inbyggda Windows VPN-klienten för autentisering med datorcertifikat:

1. Välj de VPN-klientkonfigurationsfiler som motsvarar Windows-datorns arkitektur. För en 64-bitars processorarkitektur väljer du den **VpnClientSetupAmd64** installer-paketet. För en 32-bitars processorarkitektur väljer du den **VpnClientSetupX86** installer-paketet. 
2. Dubbelklicka på den för att installera paketet. Om du ser ett SmartScreen-popup-fönster väljer **mer info** > **kör ändå**.
3. På klientdatorn, bläddrar du till **nätverksinställningar** och välj **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till. 

#### <a name="admaccli"></a>Mac (OS X) VPN-klientkonfiguration

1. Välj den **VpnClientSetup mobileconfig** fil och skicka den till varje användare. Du kan använda e-post eller någon annan metod.

2. Leta upp den **mobileconfig** fil för Mac.

   ![Platsen för den mobileconfig-fil](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Valfritt steg - om du vill ange en anpassad DNS, Lägg till följande rader till den **mobileconfig** fil:
```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        <array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
```
4. Dubbelklicka på profilen du vill installera den och välj **Fortsätt**. Namnet på profilen är samma som namnet på det virtuella nätverket.

   ![Meddelande om att installationen](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Välj **Fortsätt** och litar på avsändaren på profilen som du kan fortsätta med installationen.

   ![Bekräftelsemeddelande](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Under profilinstallationen av har möjlighet att ange användarnamn och lösenord för VPN-autentisering. Det är inte obligatoriskt att ange den här informationen. Om du gör informationen sparas och används automatiskt när du initierar en anslutning. Välj **installera** att fortsätta.

   ![Användarnamn och lösenord rutorna för VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Ange ett användarnamn och lösenord för de privilegier som krävs för att installera profilen på datorn. Välj **OK**.

   ![Användarnamn och lösenord rutorna för profilinstallationen](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. När profilen har installerats måste den syns i den **profiler** dialogrutan. Du kan också öppna den här dialogrutan senare från **systeminställningar**.

   ![Dialogrutan för ”profiler”](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. För att komma åt VPN-anslutningen, öppna den **nätverk** dialogrutan från **systeminställningar**.

   ![Ikonerna i Systeminställningar](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. VPN-anslutningen visas som **IkeV2 VPN**. Du kan ändra namnet genom att uppdatera den **mobileconfig** fil.

   ![Information om VPN-anslutningen](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Välj **autentiseringsinställningar**. Välj **användarnamn** i listan och ange dina autentiseringsuppgifter. Om du har angett autentiseringsuppgifterna tidigare, sedan **användarnamn** automatiskt vald i listan och det användarnamn och lösenord är innehåller. Välj **OK** att spara inställningarna.

    ![Autentiseringsinställningar](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. I den **nätverk** dialogrutan **tillämpa** att spara ändringarna. För att initiera anslutningen, Välj **Connect**.

#### <a name="adlinuxcli"></a>Linux VPN-klientkonfiguration via strongSwan

Följande instruktioner har skapats via strongSwan 5.5.1 på Ubuntu 17.0.4. Faktiska skärmar kan vara olika, beroende på din version av Linux- och strongSwan.

1. Öppna den **Terminal** installera **strongSwan** och dess Nätverkshanteraren genom att köra kommandot i det här exemplet. Om du får ett fel som rör `libcharon-extra-plugins`, Ersätt den med `strongswan-plugin-eap-mschapv2`.

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Välj den **nätverkshanterare** ikonen (pilen/ned-uppilen) och välj **redigera anslutningar**.

   ![”Redigera anslutningar” val av i hanteraren för Utjämning](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Välj den **Lägg till** för att skapa en ny anslutning.

   ![”Lägg till”-knappen för en anslutning](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Välj **IPsec/IKEv2 (strongswan)** från den nedrullningsbara menyn och välj sedan **skapa**. Du kan byta namn på anslutningen i det här steget.

   ![Välja anslutningstyp](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Öppna den **VpnSettings.xml** fil från den **allmän** mappen för nedladdade klienten configuration-filer. Hitta taggen kallas `VpnServer` och kopiera namnet, från och med `azuregateway` och slutar med `.cloudapp.net`.

   ![Innehållet i filen VpnSettings.xml](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Klistra in det här namnet i den **adress** fältet för din nya VPN-anslutningen i den **Gateway** avsnittet. Välj mappikonen i slutet av den **certifikat** fältet, bläddra till den **allmän** mapp och välj den **VpnServerRoot** fil.
7. I den **klienten** avsnittet för anslutningen, väljer **EAP** för **autentisering**, och ange ditt användarnamn och lösenord. Du kan behöva välja på låsikonen för att spara den här informationen till höger. Välj sedan **Spara**.

   ![Redigera anslutningsinställningarna](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Välj den **nätverkshanterare** ikon (uppåt-pilen/nedpilen) och hovra över **VPN-anslutningar**. Du ser VPN-anslutningen som du skapade. Välj den för att initiera anslutningen.

   ![”VPN med Radius”-anslutning i Nätverkshanteraren](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>Autentisering med datorcertifikat
 
Du kan skapa VPN-klienten konfigurationsfilerna för RADIUS-autentisering som använder EAP-TLS-protokollet. Ett enterprise-utfärdade certifikat används vanligtvis för att autentisera en användare för VPN. Se till att alla anslutande användare har ett certifikat installerat på sina enheter och att RADIUS-servern kan verifiera certifikatet.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

I kommandon, `-AuthenticationMethod` är `EapTls`. Under certifikatautentisering verifierar klienten RADIUS-servern genom att verifiera dess certifikat. `-RadiusRootCert` är den .cer-fil som innehåller det rotcertifikat som används för att validera RADIUS-servern.

Varje enhet för VPN-klienten kräver ett installerat klientcertifikat. Ibland har en Windows-enhet flera klientcertifikat. Detta kan resultera i ett popup-dialogruta som visar en lista över alla certifikat under autentiseringen. Användaren kan sedan välja certifikatet som ska användas. Rätt certifikat kan filtreras genom att ange det rotcertifikat som klientcertifikatet ska kedja till. 

`-ClientRootCert` är .cer-fil som innehåller servercertifikatets rotcertifikat. Det är en valfri parameter. Om den enhet som du vill ansluta från har bara ett klientcertifikat, har du inte anger den här parametern.

### <a name="certfiles"></a>1. Generera VPN-klientkonfigurationsfiler

Generera VPN-klientkonfigurationsfiler för användning med certifikatautentisering. Du kan generera VPN-klientkonfigurationsfiler med hjälp av följande kommando:
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Kör kommandot returnerar en länk. Kopiera och klistra in en länk till en webbläsare för att hämta VpnClientConfiguration.zip. Packa upp filen om du vill visa följande mappar:

* **WindowsAmd64** och **WindowsX86**: Dessa mappar innehåller installationsprogrammet för Windows 64-bitars och 32-bitars-paket. 
* **GenericDevice**: Den här mappen innehåller allmän information som används för att skapa egna VPN-klientkonfiguration.

Om du redan har skapat klienten konfigurationsfiler, kan du hämta dem med hjälp av den `Get-AzureRmVpnClientConfiguration` cmdlet. Men om du gör några ändringar i dina P2S VPN-konfiguration, till exempel typ av VPN-protokoll eller autentiseringstyp, konfigurationen uppdateras inte automatiskt. Du måste köra den `New-AzureRmVpnClientConfiguration` cmdlet för att skapa en ny nedladdning.

Om du vill hämta tidigare genererade klientkonfigurationsfiler, använder du följande kommando:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="setupusername"></a> 2. Konfigurera VPN-klienter

Du kan konfigurera följande VPN-klienter:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (stöds, ingen artikel steg ännu)

#### <a name="certwincli"></a>Windows VPN-klientkonfiguration

1. Välj ett konfigurationspaket och installera den på klientenheten. För en 64-bitars processorarkitektur väljer du den **VpnClientSetupAmd64** installer-paketet. För en 32-bitars processorarkitektur väljer du den **VpnClientSetupX86** installer-paketet. Om du ser ett SmartScreen-popup-fönster väljer **mer info** > **kör ändå**. Du kan också spara paketet om du vill installera det på andra klientdatorer.
2. Varje klient kräver ett klientcertifikat för autentisering. Installera klientcertifikatet. Läs om hur klientcertifikat [klientcertifikat för punkt-till-plats](vpn-gateway-certificates-point-to-site.md). Om du vill installera ett certifikat som har genererats Se [installera ett certifikat på Windows-klienter](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. På klientdatorn, bläddrar du till **nätverksinställningar** och välj **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till.

#### <a name="certmaccli"></a>Mac (OS X) VPN-klientkonfiguration

Du måste skapa en separat profil för varje Mac-enhet som ansluter till Azure-nätverket. Det beror på att dessa enheter kräver användarcertifikatet för autentisering måste anges i profilen. Den **allmän** mappen innehåller all information som krävs för att skapa en profil:

* **VpnSettings.xml** innehåller viktiga inställningar, till exempel typ av adress och tunnel.
* **VpnServerRoot.cer** innehåller rotcertifikat som krävs för att verifiera VPN-gatewayen under installationen av P2S-anslutning.
* **RadiusServerRoot.cer** innehåller rotcertifikat som krävs för att validera RADIUS-servern under autentiseringen.

Använd följande steg för att konfigurera den inbyggda VPN-klienten på en Mac för autentisering med datorcertifikat:

1. Importera den **VpnServerRoot** och **RadiusServerRoot** rotcertifikat till din Mac. Kopierar filer till en Mac, dubbelklicka på den och välj sedan **Lägg till**.

   ![Lägger till VpnServerRoot certifikatet](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Lägger till RadiusServerRoot certifikatet](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Varje klient kräver ett klientcertifikat för autentisering. Installera klientcertifikatet på klientenheten.
3. Öppna den **nätverk** dialogrutan under **nätverksinställningar**. Välj **+** att skapa en ny klient för VPN-anslutningsprofil för en P2S-anslutning till Azure-nätverket.

   Den **gränssnittet** värdet är **VPN**, och **VPN-typ** värdet är **IKEv2**. Ange ett namn för profilen i det **tjänstnamn** och väljer sedan **skapa** att skapa VPN-klienten anslutningsprofilen.

   ![Information om gränssnittet och namn](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. I den **allmän** mappen från den **VpnSettings.xml** fil, kopiera den **VpnServer** taggvärde. Klistra in det här värdet i den **serveradress** och **fjärr-ID för** rutorna för profilen. Lämna den **lokala ID: T** lämnas omarkerad.

   ![Serverinformation](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Välj **autentiseringsinställningar**, och välj **certifikat**. 

   ![Autentiseringsinställningar](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Klicka på **Välj** att välja det certifikat som du vill använda för autentisering.

   ![Att välja ett certifikat för autentisering](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Välj en identitet** visar en lista över certifikat som du kan välja bland. Välj rätt certifikat och välj sedan **Fortsätt**.

   ![”Välj en identitet” lista](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. I den **lokala ID: T** anger du namnet på certifikatet (från steg 6). I det här exemplet har **ikev2Client.com**. Välj den **tillämpa** för att spara ändringarna.

   ![”Lokala ID”-rutan](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. I den **nätverk** dialogrutan **tillämpa** att spara alla ändringar. Välj **Connect** att starta P2S-anslutning till Azure-nätverket.

## <a name="otherauth"></a>Arbeta med andra typer av autentisering eller protokoll

Att använda olika autentiseringstyper (till exempel OTP), eller Använd en annan autentiseringsprotokoll (till exempel PEAP-MSCHAPv2 i stället för EAP-MSCHAPv2), måste du skapa din egen profil för VPN-klienten. Du behöver information som virtuellt nätverk gatewayens IP-adress, Tunneltyp och vägar i delade tunnlar för att skapa profilen. Du kan hämta den här informationen med hjälp av följande steg:

1. Använd den `Get-AzureRmVpnClientConfiguration` cmdlet för att generera VPN-klientkonfiguration för EapMSChapv2. Anvisningar finns i det här avsnittet av artikeln.

2. Packa upp filen VpnClientConfiguration.zip och leta efter den **GenericDevice** mapp. Ignorera mapparna som innehåller de Windows-installationsprogram för 64-bitars och 32-bitars arkitektur.
 
3. Den **GenericDevice** mappen innehåller en XML-fil som heter **VpnSettings**. Den här filen innehåller informationen som krävs:

   * **VpnServer**: FQDN för Azure VPN-gatewayen. Det här är den adress som klienten ansluter till.
   * **VpnType**: Tunneltyp som används för att ansluta.
   * **Vägar**: Vägar som du måste konfigurera i din profil så att endast trafik som är bunden till Azure-nätverk skickas via P2S-tunnel.
   
   Den **GenericDevice** mappen innehåller också en .cer-fil som heter **VpnServerRoot**. Den här filen innehåller rotcertifikat som krävs för att verifiera Azure VPN-gatewayen under installationen av P2S-anslutning. Installera certifikatet på alla enheter som ska ansluta till virtuella Azure-nätverket.

## <a name="next-steps"></a>Nästa steg

Gå tillbaka till artikeln om du vill [Slutför konfigurationen P2S](point-to-site-how-to-radius-ps.md).

P2S felsökningsinformation finns i [punkt-till-plats-anslutningar i felsökning av Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
