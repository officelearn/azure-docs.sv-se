---
title: "Skapa och installera VPN-klientkonfigurationsfiler för RADIUS-P2S-anslutningar: PowerShell: Azure | Microsoft Docs"
description: "Den här artikeln hjälper dig att skapa VPN-klientkonfigurationsfil för punkt-till-plats-anslutningar som använder RADIUS-autentisering."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/05/2018
ms.author: cherylmc
ms.openlocfilehash: fb83bda50535dc002120ee4621cd4c8df71c141c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Skapa och installera VPN-klientkonfigurationsfiler för P2S RADIUS-autentisering

Om du vill ansluta till ett virtuellt nätverk via punkt-till-plats, måste du konfigurera klientenheten där du ska kunna ansluta. RADIUS-servern tillhandahåller flera autentiseringsalternativ: autentisering med användarnamn/lösenord, certifikat, samt andra typer av autentisering. Konfigurationen för VPN-klienten är olika för varje typ av autentisering. Om du vill konfigurera VPN-klienten, kan du använda klientkonfigurationsfiler som innehåller inställningarna som krävs. Den här artikeln hjälper dig att skapa och installera VPN-klientkonfiguration för RADIUS-autentisering-typ som du vill använda.

### <a name="workflow"></a>Arbetsflöde

1. [Konfigurera Azure VPN-gateway för P2S-anslutning](point-to-site-how-to-radius-ps.md).
2. [Konfigurera RADIUS-servern för autentisering](point-to-site-how-to-radius-ps.md#radius). 
3. **Hämta konfigurationen för VPN-klienten för autentiseringsalternativet som du väljer och använda den för att ställa in VPN-klienten**. (Den här artikeln)
4. [Slutför konfigurationen P2S och ansluta](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Om det finns ändringar i punkt-till-plats VPN-konfiguration när du skapar VPN-klienten konfigurationsprofilen, till exempel den VPN-protokollet eller autentiseringstyp, måste du skapa och installera en ny konfiguration för VPN-klienten på dina användarenheter.
>
>

## <a name="adeap"></a>Autentisering med användarnamn/lösenord

Det finns två sätt att konfigurera autentisering med användarnamn/lösenord. Du kan antingen konfigurera autentisering med AD-eller autentisera utan att använda AD. Med båda fallen måste du se till att alla användare som ansluter har autentiseringsuppgifter som kan autentiseras via RADIUS.

* När du konfigurerar autentisering med användarnamn/lösenord kan skapa du bara en konfiguration för autentiseringsprotokollet EAP-MSCHAPv2 användarnamn/lösenord.
* '-AuthenticationMethod' är 'EapMSChapv2'.

### <a name="usernamefiles"></a> 1. Generera konfigurationsfiler för VPN-klienten

Generera VPN-klientkonfigurationsfiler för användning med autentisering med användarnamn/lösenord. Du kan generera konfigurationsfiler för VPN-klienten med följande kommando:

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Kör kommandot returnerar en länk. Kopiera och klistra in en länk till en webbläsare för att hämta 'VpnClientConfiguration.zip'. Packa upp filen om du vill visa följande mappar: 
 
* **WindowsAmd64** och **WindowsX86** -mapparna innehåller Windows 64-bitars och 32-bitars installer-paket. 
* **Allmän** -den här mappen innehåller allmän information som används för att skapa din egen konfiguration för VPN-klienten. Den här mappen krävs inte för konfigurationer för autentisering av användarnamn/lösenord.
* **Mac** -om IKEv2 konfigurerades när du har skapat den virtuella nätverksgatewayen ser du en mapp med namnet 'Mac' som innehåller en **mobileconfig** fil. Den här filen används för att konfigurera Mac-klienter.

Om du redan skapat klienten konfigurationsfiler, kan du hämta dem med hjälp av cmdleten 'Get-AzureRmVpnClientConfiguration'. Men om du gör några ändringar i konfigurationen för P2S VPN, till exempel den VPN-protokoll eller autentiseringstyp, uppdateras konfigurationen inte automatiskt. Du måste köra cmdleten 'Ny AzureRmVpnClientConfiguration' om du vill skapa en ny konfiguration hämtning.

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

Du kan använda samma konfigurationspaketet för VPN-klienten på alla Windows-klientdatorer, förutsatt att versionen matchar arkitekturen för klienten. Lista över klientoperativsystem som stöds finns i avsnittet punkt-till-plats för den [vanliga frågor och svar](vpn-gateway-vpn-faq.md#P2S).

Använd följande steg för att konfigurera den inbyggda Windows VPN-klienten för autentisering med datorcertifikat:

1. Välj VPN-klienten konfigurationsfilerna som motsvarar arkitekturen på Windows-dator. Välj installationspaketet 'VpnClientSetupAmd64' för en 64-bitars processorarkitektur. Välj installationspaketet 'VpnClientSetupX86' för en 32-bitars processorarkitektur. 
2. Dubbelklicka på för att installera paketet. Om du ser ett SmartScreen-popup-fönster klickar du på **Mer information** och sedan på **Kör ändå**.
3. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till. 

#### <a name="admaccli"></a>Mac (OS X) VPN-klientkonfiguration

1. Välj den **VpnClientSetup mobileconfig** filen och skicka den till varje användare. Du kan använda e-post eller någon annan metod.

2. Leta upp den **mobileconfig** fil för Mac.

  ![Leta upp filen mobilconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. Dubbelklicka på profilen som ska installera den, klickar du på **Fortsätt**. Namnet på profilen är samma som namnet på ditt VNet.

  ![Installera](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. Klicka på **Fortsätt** litar på avsändaren av profilen och fortsätta med installationen.

  ![fortsätt](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. Under profilinstallationen av möjlighet du att ange användarnamn och lösenord som används för VPN-autentisering. Det är inte obligatoriskt att ange den här informationen. Om anges informationen sparas och används automatiskt när du initierar en anslutning. Klicka på **installera** att fortsätta.

  ![settings](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. Ange ett användarnamn och lösenord för den behörighet som krävs för att installera profilen på datorn. Klicka på **OK**.

  ![användarnamn och lösenord](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. När du har installerat profilen som är synliga i den **profiler** dialogrutan. Den här dialogrutan kan även öppna senare från **systeminställningar**.

  ![Systeminställningar](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. Om du vill komma åt VPN-anslutningen, öppna den **nätverk** dialogrutan från **systeminställningar**.

  ![Nätverk](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. VPN-anslutningen visas som **IkeV2 VPN**. Namnet kan ändras genom att uppdatera den **mobileconfig** fil.

  ![anslutning](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. Klicka på **autentiseringsinställningar**. Välj **användarnamn** i listrutan och ange dina autentiseringsuppgifter. Om du angav autentiseringsuppgifterna tidigare, sedan **användarnamn** automatiskt är valt i listrutan och användarnamnet och lösenordet är i förväg. Spara inställningarna genom att klicka på **OK**. Detta tar dig tillbaka till dialogrutan nätverk.

  ![autentisera](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. Klicka på **tillämpa** spara ändringarna. Om du vill upprätta anslutningen klickar du på **Anslut**.

#### <a name="adlinuxcli"></a>Linux VPN-klientkonfiguration med strongSwan

Följande instruktioner har skapats med strongSwan 5.5.1 på Ubuntu 17.0.4. Faktiska skärmar kan vara olika beroende på din version av Linux- och strongSwan.

1. Öppna den **Terminal** installera **strongSwan** och dess Nätverkshanteraren genom att köra följande kommando. Om du får ett fel som rör den ”libcharon-extra-plugin-program” kan ersätta det med ”strongswan-plugin-eap-mschapv2”.

  ```Terminal
  sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
  ```
2. Klicka på den **Nätverkshanteraren** (uppåt-pilen/nedåtpilen), och välj **redigera anslutningar**.

  ![Redigera anslutning](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Klicka på den **Lägg till** för att skapa en ny anslutning.

  ![Lägg till anslutning](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Välj **IPsec/IKEv2 (strongswan)** i den nedrullningsbara menyn Klicka **skapa**. Du kan byta namn på anslutningen i det här steget.

  ![Lägg till ikev2](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Öppna den **VpnSettings.xml** filen från den **allmänna** mapp för hämtade client configuration-filer. Taggen kallas **VpnServer** och kopiera namnet börjar med ”azuregateway” och slutar med ”. cloudapp.net”.

  ![VPN-inställningar](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Klistra in det här namnet i den **adress** för din nya VPN-anslutning under den **Gateway** avsnitt. Klicka sedan på mappikonen i slutet av den **certifikat** , bläddra till den generiska mappen och välj den **VpnServerRoot** finns det.
7. Under den **klienten** avsnittet för anslutningen väljer **EAP** för **autentisering**, och ange ditt användarnamn/lösenord. Du kan behöva Välj låsikonen på behörighet att spara den här informationen. Klicka på **spara**.

  ![Redigera anslutningsinställningar för](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Klicka på den **Nätverkshanteraren** ikonen (pilen/ned-uppilen) och över **VPN-anslutningar**. VPN-anslutningen som du har skapat visas. Välj den för att ansluta för att upprätta anslutningen.

  ![ansluta radius](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>Certifikatautentisering
 
Du kan skapa VPN-klienten konfigurationsfilerna för RADIUS-autentisering som använder EAP-TLS-protokollet. Ett Enterprise-utfärdade certifikat används vanligtvis för att autentisera en användare för VPN. Kontrollera att alla anslutande användare ha ett certifikat installeras på användarnas enheter och att certifikatet som kan valideras av RADIUS-servern.
 
* '-AuthenticationMethod' är 'EapTls'.
* Under certifikatautentisering verifierar klienten RADIUS-servern genom att verifiera certifikatet. -RadiusRootCert är .cer-fil som innehåller rot-certifikat som används för att verifiera RADIUS-servern.
* Varje enhet för VPN-klienten kräver ett installerat klientcertifikat.
* En windowsenhet har ibland flera klientcertifikat. Detta kan resultera i en popup-dialogruta som visar en lista över alla certifikat vid autentisering. Användaren kan sedan välja certifikatet som ska användas. Rätt certifikat kan filtreras genom att ange rotcertifikatet som klienten cert ska kedja. '-ClientRootCert' är .cer-fil som innehåller rot-certifikat. Det är en valfri parameter. Om du vill ansluta enheten har bara ett klientcertifikat, har inte den här parametern anges.

### <a name="certfiles"></a>1. Generera konfigurationsfiler för VPN-klienten

Generera VPN-klientkonfigurationsfiler för användning med certifikatautentisering. Du kan generera konfigurationsfiler för VPN-klienten med följande kommando:
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Kör kommandot returnerar en länk. Kopiera och klistra in en länk till en webbläsare för att hämta 'VpnClientConfiguration.zip'. Packa upp filen om du vill visa följande mappar:

* **WindowsAmd64** och **WindowsX86** -mapparna innehåller Windows 64-bitars och 32-bitars installer-paket. 
* **GenericDevice** -den här mappen innehåller allmän information som används för att skapa din egen konfiguration för VPN-klienten.

Om du redan skapat klienten konfigurationsfiler, kan du hämta dem med hjälp av cmdleten 'Get-AzureRmVpnClientConfiguration'. Men om du gör några ändringar i konfigurationen för P2S VPN, till exempel den VPN-protokoll eller autentiseringstyp, uppdateras konfigurationen inte automatiskt. Du måste köra cmdleten 'Ny AzureRmVpnClientConfiguration' om du vill skapa en ny konfiguration hämtning.

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

1. Välj ett konfigurationspaket och installera den på klientenheten. Välj installationspaketet 'VpnClientSetupAmd64' för en 64-bitars processorarkitektur. Välj installationspaketet 'VpnClientSetupX86' för en 32-bitars processorarkitektur. Om du ser ett SmartScreen-popup-fönster klickar du på **Mer information** och sedan på **Kör ändå**. Du kan också spara paketet om du vill installera det på andra klientdatorer.
2. Varje klient kräver ett klientcertifikat för att kunna autentisera. Installera klientcertifikatet. Information om klientcertifikat finns [klientcertifikat för punkt-till-plats](vpn-gateway-certificates-point-to-site.md). Om du vill installera ett certifikat som har genererats [installera ett certifikat på Windows-klienter](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till.

#### <a name="certmaccli"></a>Mac (OS X) VPN-klientkonfiguration

En separat profil måste skapas för varje Mac-enhet som ansluter till Azure VNet. Det beror på att dessa enheter kräver användarcertifikat för att autentisering ska anges i profilen. Den **allmänna** mappen innehåller all information som krävs för att skapa en profil.

  * **VpnSettings.xml** innehåller viktiga inställningar, till exempel adress och tunnel servertyp.
  * **VpnServerRoot.cer** innehåller rotcertifikat som krävs för att verifiera VPN-gatewayen under installationen av P2S-anslutning.
  * **RadiusServerRoot.cer** innehåller rotcertifikat som krävs för att validera RADIUS-servern under autentiseringen.

Använd följande steg för att konfigurera den inbyggda VPN-klienten på Mac för autentisering med datorcertifikat:

1. Importera den **VpnServerRoot** och **RadiusServerRoot** root certifikat till datorn. Detta kan göras genom att kopiera filen till din Mac och dubbelklicka på den.  
Klicka på **Lägg till** att importera.

  *Add VpnServerRoot*

  ![Lägg till certifikat](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

  *Add RadiusServerRoot*

  ![Lägg till certifikat](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Varje klient kräver ett klientcertifikat för att kunna autentisera. Installera klientcertifikatet på klientenheten.
3. Öppna den **nätverk** dialogrutan under **nätverksinställningar** och på **”+”** att skapa en ny klient för VPN-anslutningsprofil för en P2S-anslutning till Azure VNet.

  Den **gränssnittet** värdet är 'VPN- och **VPN-typ** värdet är 'IKEv2'. Ange ett namn för profilen i den **tjänstnamnet** fältet och klicka sedan på **skapa** att skapa VPN-anslutningsprofilen för klienten.

  ![Nätverk](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. I den **allmänna** mappen från den **VpnSettings.xml** fil, kopiera den **VpnServer** taggvärde. Klistra in det här värdet i den **serveradress** och **fjärr-ID för** för profilen. Lämna den **lokalt ID** tomt.

  ![server info](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Klicka på **autentiseringsinställningar** och välj **certifikat**. 

  ![autentiseringsinställningar](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Klicka på **Välj...** att välja det certifikat som du vill använda för autentisering.

  ![certifikat](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Välj en identitet** visar en lista över certifikat som du kan välja från. Välj rätt certifikat och klicka sedan på **Fortsätt**.

  ![identity](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. I den **lokalt ID** anger namnet på certifikatet (från steg 6). I det här exemplet är det ”ikev2Client.com”. Klicka på **tillämpa** för att spara ändringarna.

  ![använd](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. På den **nätverk** dialogrutan klickar du på **tillämpa** spara alla ändringar. Klicka på **Anslut** att starta P2S-anslutning till Azure VNet.

## <a name="otherauth"></a>Arbeta med andra typer av autentisering eller protokoll

Att använda en annan autentiseringstyp (till exempel OTP) och inte användarnamn/lösenord eller certifikat, eller Använd en annan autentiseringsprotokoll (till exempel PEAP-MSCHAPv2 i stället för EAP-MSCHAPv2), måste du skapa dina egna Konfigurationsprofil för VPN-klienten. Du behöver information om virtuellt IP-adressen för gateway, Tunneltyp och delad tunnel vägar för att skapa profilen. Du kan hämta den här informationen med hjälp av följande steg:

1. Använd cmdleten 'Get-AzureRmVpnClientConfiguration' för att generera VPN-klientkonfiguration för EapMSChapv2. Instruktioner finns i [i det här avsnittet](#ccradius) av artikeln.

2. Packa upp VpnClientConfiguration.zip-filen och leta efter mappen GenenericDevice. Ignorera mapparna som innehåller Windows-installationsprogram för 64-bitars och 32-bitars arkitekturerna.
 
3. Mappen GenenericDevice innehåller en XML-fil som kallas VpnSettings. Den här filen innehåller informationen som krävs.

  * VpnServer - FQDN för Azure VPN-gatewayen. Det här är den adress som klienten ansluter till.
  * VpnType - Tunneltyp som du använder för att ansluta.
  * Vägar - vägar som du måste konfigurera i profilen så att endast Azure VNet bunden trafik skickas via P2S-tunnel.
  * Mappen GenenericDevice innehåller också en .cer-fil som kallas ”VpnServerRoot'. Den här filen innehåller rotcertifikat som krävs för att validera Azure VPN-gatewayen under installationen av P2S-anslutning. Installera certifikatet på alla enheter som ansluter till Azure VNet.

## <a name="next-steps"></a>Nästa steg

Gå tillbaka till artikeln [Slutför konfigurationen P2S](point-to-site-how-to-radius-ps.md).