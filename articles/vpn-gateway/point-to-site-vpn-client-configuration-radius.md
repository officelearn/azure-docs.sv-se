---
title: "Skapa och installera VPN-klientkonfigurationsfiler för RADIUS-P2S-anslutningar: PowerShell: Azure | Microsoft Docs"
description: "Skapa Windows-, Mac OS X- och Linux VPN-klienten konfigurationsfiler för anslutningar som använder RADIUS-autentisering."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: 1d57537428f5ac1085b6cbae93be6f77c71b12e7
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Skapa och installera VPN-klientkonfigurationsfiler för P2S RADIUS-autentisering

Om du vill ansluta till ett virtuellt nätverk via punkt-till-plats (P2S), måste du konfigurera klientenheten som du ska ansluta från. Du kan skapa P2S VPN-anslutningar från Windows-, Mac OS X- och Linux-klientenheter. 

När du använder RADIUS-autentisering, det finns flera alternativ för Webbplatsautentisering: autentisering med användarnamn/lösenord, autentisering med datorcertifikat och andra typer av autentisering. Konfigurationen för VPN-klienten är olika för varje typ av autentisering. Om du vill konfigurera VPN-klienten, kan du använda klientkonfigurationsfiler som innehåller inställningarna som krävs. Den här artikeln hjälper dig att skapa och installera VPN-klientkonfiguration för RADIUS-autentisering-typ som du vill använda.

Konfiguration av arbetsflödet för P2S RADIUS-autentisering är följande:

1. [Konfigurera Azure VPN-gateway för P2S-anslutning](point-to-site-how-to-radius-ps.md).
2. [Konfigurera RADIUS-servern för autentisering](point-to-site-how-to-radius-ps.md#radius). 
3. **Hämta konfigurationen för VPN-klienten för autentiseringsalternativet som du väljer och använda den för att ställa in VPN-klienten** (den här artikeln).
4. [Slutför konfigurationen P2S och ansluta](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Om det finns ändringar i punkt-till-plats VPN-konfiguration när du skapar VPN-klienten konfigurationsprofilen, till exempel den VPN-protokollet eller autentiseringstyp, måste du skapa och installera en ny konfiguration för VPN-klienten på användarnas enheter.
>
>

Om du vill använda avsnitten i den här artikeln först bestämma vilken typ av autentisering som du vill använda: användarnamn/lösenord, certifikat eller andra typer av autentisering. Varje avsnitt innehåller steg för Windows, Mac OS X och Linux (begränsad steg som är tillgängliga just nu).

## <a name="adeap"></a>Autentisering med användarnamn/lösenord

Du kan konfigurera autentisering av användarnamn/lösenord för att använda Active Directory eller inte använda Active Directory. Med båda fallen måste du se till att alla användare som ansluter har autentiseringsuppgifter som kan autentiseras via RADIUS.

När du konfigurerar autentisering med användarnamn/lösenord kan skapa du bara en konfiguration för autentiseringsprotokollet EAP-MSCHAPv2 användarnamn/lösenord. I kommandona, `-AuthenticationMethod` är `EapMSChapv2`.

### <a name="usernamefiles"></a> 1. Generera konfigurationsfiler för VPN-klienten

Generera VPN-klientkonfigurationsfiler för användning med autentisering med användarnamn/lösenord. Du kan generera konfigurationsfiler för VPN-klienten med hjälp av följande kommando:

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Kör kommandot returnerar en länk. Kopiera och klistra in en länk till en webbläsare för att hämta **VpnClientConfiguration.zip**. Packa upp filen om du vill visa följande mappar: 
 
* **WindowsAmd64** och **WindowsX86**: mapparna innehåller Windows 64-bitars och 32-bitars installer-paket. 
* **Allmän**: den här mappen innehåller allmän information som används för att skapa din egen konfiguration för VPN-klienten. Du behöver den här mappen för konfigurationer för autentisering av användarnamn/lösenord.
* **Mac**: Om du konfigurerat IKEv2 när du skapade den virtuella nätverksgatewayen kan du se en mapp med namnet **Mac** som innehåller en **mobileconfig** fil. Du kan använda den här filen för att konfigurera Mac-klienter.

Om du redan skapat klienten konfigurationsfiler, du kan hämta dem med hjälp av den `Get-AzureRmVpnClientConfiguration` cmdlet. Men om du gör några ändringar i konfigurationen för P2S VPN, till exempel den VPN-protokollet eller autentiseringstyp, konfigurationen uppdateras inte automatiskt. Du måste köra den `New-AzureRmVpnClientConfiguration` för att skapa en ny konfiguration hämtning.

Om du vill hämta konfigurationsfiler för tidigare genererade klienten använder du följande kommando:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="setupusername"></a> 2. Konfigurera VPN-klienter

Du kan konfigurera följande VPN-klienter:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux med strongSwan](#adlinuxcli)
 
#### <a name="adwincli"></a>Windows VPN-klientkonfiguration

Du kan använda samma konfigurationspaketet för VPN-klienten på alla Windows-klientdatorer, förutsatt att versionen matchar arkitekturen för klienten. Lista över klientoperativsystem som stöds, finns det [vanliga frågor och svar](vpn-gateway-vpn-faq.md#P2S).

Använd följande steg för att konfigurera den inbyggda Windows VPN-klienten för autentisering med datorcertifikat:

1. Välj VPN-klienten konfigurationsfilerna som motsvarar arkitekturen på Windows-dator. En 64-bitars processorarkitektur väljer den **VpnClientSetupAmd64** installer-paketet. En 32-bitars processorarkitektur väljer den **VpnClientSetupX86** installer-paketet. 
2. Dubbelklicka på den för att installera paketet. Om du ser ett SmartScreen popup-fönster, Välj **mer info** > **kör ändå**.
3. På klientdatorn, bläddrar du till **nätverksinställningar** och välj **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till. 

#### <a name="admaccli"></a>Mac (OS X) VPN-klientkonfiguration

1. Välj den **VpnClientSetup mobileconfig** filen och skicka den till varje användare. Du kan använda e-post eller någon annan metod.

2. Leta upp den **mobileconfig** fil för Mac.

   ![Platsen för filen mobilconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. Dubbelklicka på profilen för att installera den och välja **Fortsätt**. Namnet på profilen är samma som namnet på det virtuella nätverket.

   ![Meddelande om att installationen](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. Välj **Fortsätt** litar på avsändaren av profilen och fortsätta med installationen.

   ![Bekräftelsemeddelande](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. Under profilinstallationen av har möjlighet att ange användarnamn och lösenord för VPN-autentisering. Det är inte obligatoriskt att ange den här informationen. Om du vill spara informationen och används automatiskt när du initierar en anslutning. Välj **installera** att fortsätta.

   ![Användarnamn och lösenord kryssrutorna för VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. Ange ett användarnamn och lösenord för de behörigheter som krävs för att installera profilen på datorn. Välj **OK**.

   ![Användarnamn och lösenord kryssrutorna för att installera profilen](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. När profilen har installerats kan den visas i den **profiler** dialogrutan. Du kan också öppna den här dialogrutan senare från **systeminställningar**.

   ![Dialogrutan ”användarprofiler”](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. Om du vill komma åt VPN-anslutningen, öppna den **nätverk** dialogruta från **systeminställningar**.

   ![Ikonerna i Systeminställningar](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. VPN-anslutningen visas som **IkeV2 VPN**. Du kan ändra namnet genom att uppdatera den **mobileconfig** fil.

   ![Information om VPN-anslutningen](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. Välj **autentiseringsinställningar**. Välj **användarnamn** i listan och ange dina autentiseringsuppgifter. Om du angav autentiseringsuppgifterna tidigare, sedan **användarnamn** automatiskt som väljs i listan och det användarnamn och lösenord är förinställd. Välj **OK** spara inställningarna.

    ![Autentiseringsinställningar](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. I den **nätverk** dialogrutan **tillämpa** spara ändringarna. Om du vill upprätta anslutningen, Välj **Anslut**.

#### <a name="adlinuxcli"></a>Linux VPN-klientkonfiguration via strongSwan

Följande instruktioner har skapats via strongSwan 5.5.1 på Ubuntu 17.0.4. Faktiska skärmar kan vara olika beroende på din version av Linux- och strongSwan.

1. Öppna den **Terminal** installera **strongSwan** och dess Nätverkshanteraren genom att köra kommandot i exemplet. Om du får ett fel som rör `libcharon-extra-plugins`, Ersätt den med `strongswan-plugin-eap-mschapv2`.

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Välj den **Nätverkshanteraren** (uppåt-pilen/nedåtpilen), och välj **redigera anslutningar**.

   ![”Redigera anslutningar” val i Network Manager](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Välj den **Lägg till** för att skapa en ny anslutning.

   ![”Knappen Lägg till” för en anslutning](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Välj **IPsec/IKEv2 (strongswan)** från listrutan och välj sedan **skapa**. Du kan byta namn på anslutningen i det här steget.

   ![Att välja vilken typ av anslutning](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Öppna den **VpnSettings.xml** filen från den **allmänna** mapp för hämtade client configuration-filer. Taggen kallas `VpnServer` och kopiera namn, från och med `azuregateway` och slutar med `.cloudapp.net`.

   ![Innehållet i filen VpnSettings.xml](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Klistra in det här namnet i den **adress** för din nya VPN-anslutningen i den **Gateway** avsnitt. Sedan väljer du mappikonen i slutet av den **certifikat** fältet, bläddra till den **allmänna** och välj den **VpnServerRoot** fil.
7. I den **klienten** delen av anslutningen, Välj **EAP** för **autentisering**, och ange ditt användarnamn och lösenord. Du kan behöva Välj låsikonen på behörighet att spara den här informationen. Välj sedan **Spara**.

   ![Redigera anslutningsinställningar](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Välj den **Nätverkshanteraren** ikonen (pilen/ned-uppilen) och över **VPN-anslutningar**. Du ser VPN-anslutningen som du skapade. Välj den för att upprätta anslutningen.

   ![”VPN Radius” anslutning i Network Manager](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>Certifikatautentisering
 
Du kan skapa VPN-klienten konfigurationsfilerna för RADIUS-autentisering som använder EAP-TLS-protokollet. Ett enterprise-utfärdade certifikat används vanligtvis för att autentisera en användare för VPN. Kontrollera att alla anslutande användare ha ett certifikat installerat på sina enheter och att RADIUS-server kan verifiera certifikatet.

I kommandona, `-AuthenticationMethod` är `EapTls`. Under certifikatautentisering verifierar klienten RADIUS-servern genom att verifiera certifikatet. `-RadiusRootCert` är den .cer-fil som innehåller rotcertifikatet som används för att verifiera RADIUS-servern.

Varje enhet för VPN-klienten kräver ett installerat klientcertifikat. En windowsenhet har ibland flera klientcertifikat. Detta kan resultera i en dialogruta som visar en lista över alla certifikat vid autentisering. Användaren kan sedan välja certifikatet som ska användas. Rätt certifikat kan filtreras genom att ange det rotcertifikat som klientcertifikatet ska kedja till. 

`-ClientRootCert` är den .cer-fil som innehåller rotcertifikatet. Det är en valfri parameter. Om enheten som du vill ansluta från har bara ett klientcertifikat kan du inte ange den här parametern.

### <a name="certfiles"></a>1. Generera konfigurationsfiler för VPN-klienten

Generera VPN-klientkonfigurationsfiler för användning med certifikatautentisering. Du kan generera konfigurationsfiler för VPN-klienten med hjälp av följande kommando:
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Kör kommandot returnerar en länk. Kopiera och klistra in en länk till en webbläsare för att hämta VpnClientConfiguration.zip. Packa upp filen om du vill visa följande mappar:

* **WindowsAmd64** och **WindowsX86**: mapparna innehåller Windows 64-bitars och 32-bitars installer-paket. 
* **GenericDevice**: den här mappen innehåller allmän information som används för att skapa din egen konfiguration för VPN-klienten.

Om du redan skapat klienten konfigurationsfiler, du kan hämta dem med hjälp av den `Get-AzureRmVpnClientConfiguration` cmdlet. Men om du gör några ändringar i konfigurationen för P2S VPN, till exempel den VPN-protokollet eller autentiseringstyp, konfigurationen uppdateras inte automatiskt. Du måste köra den `New-AzureRmVpnClientConfiguration` för att skapa en ny konfiguration hämtning.

Om du vill hämta konfigurationsfiler för tidigare genererade klienten använder du följande kommando:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="setupusername"></a> 2. Konfigurera VPN-klienter

Du kan konfigurera följande VPN-klienter:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (stöds, ingen artikel steg ännu)

#### <a name="certwincli"></a>Windows VPN-klientkonfiguration

1. Välj ett konfigurationspaket och installera den på klientenheten. En 64-bitars processorarkitektur väljer den **VpnClientSetupAmd64** installer-paketet. En 32-bitars processorarkitektur väljer den **VpnClientSetupX86** installer-paketet. Om du ser ett SmartScreen popup-fönster, Välj **mer info** > **kör ändå**. Du kan också spara paketet om du vill installera det på andra klientdatorer.
2. Varje klient kräver ett klientcertifikat för autentisering. Installera klientcertifikatet. Information om klientcertifikat finns [klientcertifikat för punkt-till-plats](vpn-gateway-certificates-point-to-site.md). Om du vill installera ett certifikat som har genererats [installera ett certifikat på Windows-klienter](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. På klientdatorn, bläddrar du till **nätverksinställningar** och välj **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till.

#### <a name="certmaccli"></a>Mac (OS X) VPN-klientkonfiguration

Du måste skapa en separat profil för alla Mac-enheter som ansluter till det virtuella Azure-nätverket. Det beror på att dessa enheter kräver användarcertifikat för att autentisering ska anges i profilen. Den **allmänna** mappen innehåller all information som krävs för att skapa en profil:

* **VpnSettings.xml** innehåller viktiga inställningar, till exempel adress och tunnel servertyp.
* **VpnServerRoot.cer** innehåller rotcertifikat som krävs för att verifiera VPN-gatewayen under installationen av P2S-anslutning.
* **RadiusServerRoot.cer** innehåller rotcertifikat som krävs för att verifiera RADIUS-servern under autentiseringen.

Använd följande steg för att konfigurera den inbyggda VPN-klienten på en Mac för autentisering med datorcertifikat:

1. Importera den **VpnServerRoot** och **RadiusServerRoot** root certifikat till datorn. Kopierar filer till en Mac, dubbelklicka på den och väljer sedan **Lägg till**.

   ![Lägger till certifikatet VpnServerRoot](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Lägger till certifikatet RadiusServerRoot](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Varje klient kräver ett klientcertifikat för autentisering. Installera klientcertifikatet på klientenheten.
3. Öppna den **nätverk** dialogrutan under **nätverksinställningar**. Välj  **+**  att skapa en ny klient för VPN-anslutningsprofil för en P2S-anslutning till Azure-nätverket.

   Den **gränssnittet** värdet är **VPN**, och **VPN-typ** värdet är **IKEv2**. Ange ett namn för profilen i den **tjänstnamnet** och välj sedan **skapa** att skapa VPN-anslutningsprofilen för klienten.

   ![Information om gränssnitt och tjänsten namn](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. I den **allmänna** mappen från den **VpnSettings.xml** fil, kopiera den **VpnServer** taggvärde. Klistra in det här värdet i den **serveradress** och **fjärr-ID** kryssrutorna för profilen. Lämna den **lokalt ID** tom.

   ![Information om Server](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Välj **autentiseringsinställningar**, och välj **certifikat**. 

   ![Autentiseringsinställningar](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Klicka på **Välj** att välja det certifikat som du vill använda för autentisering.

   ![Att välja ett certifikat för autentisering](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Välj en identitet** visar en lista över certifikat som du kan välja från. Välj rätt certifikat och välj sedan **Fortsätt**.

   ![”Välj en identitet” lista](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. I den **lokalt ID** anger du namnet på certifikatet (från steg 6). I det här exemplet har **ikev2Client.com**. Markera den **tillämpa** för att spara ändringarna.

   ![”Lokala ID”-rutan](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. I den **nätverk** dialogrutan **tillämpa** spara alla ändringar. Markera **Anslut** att starta P2S-anslutning till Azure-nätverket.

## <a name="otherauth"></a>Arbeta med andra typer av autentisering eller protokoll

Att använda en annan autentiseringstyp (till exempel OTP), eller Använd en annan autentiseringsprotokoll (till exempel PEAP-MSCHAPv2 i stället för EAP-MSCHAPv2), måste du skapa dina egna Konfigurationsprofil för VPN-klienten. Du behöver information om virtuellt IP-adressen för gateway, Tunneltyp och delad tunnel vägar för att skapa profilen. Du kan hämta den här informationen med hjälp av följande steg:

1. Använd den `Get-AzureRmVpnClientConfiguration` för att generera VPN-klientkonfiguration för EapMSChapv2. Instruktioner finns i [i det här avsnittet](#ccradius) av artikeln.

2. Packa upp VpnClientConfiguration.zip-filen och leta efter den **GenenericDevice** mapp. Ignorera mapparna som innehåller Windows-installationsprogram för 64-bitars och 32-bitars arkitekturerna.
 
3. Den **GenenericDevice** mappen innehåller en XML-fil som kallas **VpnSettings**. Den här filen innehåller informationen som krävs:

   * **VpnServer**: FQDN för Azure VPN-gatewayen. Det här är den adress som klienten ansluter till.
   * **VpnType**: Tunnel som du använder för att ansluta.
   * **Vägar**: vägar som du måste konfigurera i profilen så att endast trafik som är bunden till virtuella Azure-nätverket skickas över P2S-tunnel.
   
   Den **GenenericDevice** mappen innehåller också en .cer-fil som kallas **VpnServerRoot**. Den här filen innehåller rotcertifikat som krävs för att validera Azure VPN-gatewayen under installationen av P2S-anslutning. Installera certifikatet på alla enheter som ska ansluta till virtuella Azure-nätverket.

## <a name="next-steps"></a>Nästa steg

Gå tillbaka till artikeln [Slutför konfigurationen P2S](point-to-site-how-to-radius-ps.md).

P2S felsökningsinformation finns i [felsöka Azure punkt-till-plats-anslutningar](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).