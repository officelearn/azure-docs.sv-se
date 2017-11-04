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
ms.date: 10/16/2017
ms.author: cherylmc
ms.openlocfilehash: d7d2dbff4bcd0d76b56c6f142afae4ce8359bb37
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication-preview"></a>Skapa och installera VPN-klientkonfigurationsfiler för P2S RADIUS-autentisering (förhandsgranskning)

Konfigurationsfiler för VPN-klienten finns i en zip-fil. Konfigurationsfiler ange de inställningar som krävs för en intern Windows- eller Mac IKEv2 VPN-klient ansluta till ett virtuellt nätverk via punkt-till-plats. RADIUS-servern tillhandahåller flera alternativ för autentisering och som sådan VPN-klientkonfiguration varierar för varje alternativ.

### <a name="workflow"></a>Arbetsflöde

  1. Ställ in Azure VPN-gateway för P2S-anslutning.
  2. Konfigurera RADIUS-servern för autentisering. 
  3. Hämta konfigurationen för VPN-klienten för autentiseringsalternativet som du väljer och använda den för att ställa in VPN-klienten på Windows-enheten. På så sätt kan du ansluta till virtuella Azure-nätverk via en P2S-anslutning.

>[!IMPORTANT]
>Om det finns ändringar i punkt-till-plats VPN-konfiguration när du skapar VPN-klienten konfigurationsprofilen, till exempel den VPN-protokollet eller autentiseringstyp, måste du skapa och installera en ny konfiguration för VPN-klienten på dina användarenheter.
>
>

## <a name="adeap"></a>Autentisering med användarnamn/lösenord

* **AD-verifiering:** ett populärt scenario är AD domänautentisering. I det här scenariot använder användarna sina domänautentiseringsuppgifter för att ansluta till virtuella Azure-nätverk. Du kan skapa VPN-klientkonfigurationsfiler för RADIUS AD-autentisering.

* **Autentisering utan AD:** du kan också konfigurera användarnamn/lösenord RADIUS-autentiseringsscenariot utan AD.

Kontrollera att alla användare som ansluter har autentiseringsuppgifter som kan autentiseras via RADIUS. Du kan bara skapa en konfiguration för EAP-MSCHAPv2 autentiseringsprotokoll för användarnamn/lösenord. '-AuthenticationMethod' har angetts som ”EapMSChapv2'.

### <a name="usernamefiles"></a>Generera konfigurationsfiler för VPN-klienten

Skapa VPN-klientkonfiguration med följande kommando:

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
När du har kört kommandot returneras en länk. Kopiera och klistra in en länk till en webbläsare för att hämta en komprimerad fil med namnet 'VpnClientConfiguration.zip'. Packa upp filen om du vill se följande mappar: 
 
* Mappar med namnet 'WindowsAmd64' och 'WindowsX86'. Dessa mappar innehåller Windows 64-bitars och 32-bitars installer-paket. 
* En mapp med namnet 'GenericDevice'. Den här mappen innehåller allmän information som används för att skapa din egen konfiguration för VPN-klienten. Ignorera den här mappen.
* Om IKEv2 konfigurerades när du skapade den virtuella nätverksgatewayen, kan du se en mapp med namnet 'Mac' som innehåller en fil med namnet 'mobileconfig'. Den här filen används för att konfigurera Mac-klienter.

Du kan hämta klientkonfigurationsfiler som du redan har skapat. 'Get-AzureRmVpnClientConfiguration-cmdlet returnerar en URL (länk) från där du kan hämta filen VpnClientConfiguration.zip. Om du gör några ändringar i konfigurationen för P2S VPN, till exempel den VPN-protokoll eller autentiseringstyp, uppdateras konfigurationen inte automatiskt. Du måste köra cmdleten 'Ny AzureRmVpnClientConfiguration' för att återskapa konfigurationen.

Om du vill hämta konfigurationsfiler för tidigare genererade klienten använder du följande kommando:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```
 
### <a name="adwincli"></a>Konfigurera Windows VPN-klienten

Du kan använda samma konfigurationspaketet för VPN-klienten på alla Windows-klientdatorer, förutsatt att versionen matchar arkitekturen för klienten. Lista över klientoperativsystem som stöds finns i avsnittet punkt-till-plats för den [vanliga frågor och svar](vpn-gateway-vpn-faq.md#P2S).

Använd följande steg för att konfigurera den inbyggda Windows VPN-klienten för autentisering med datorcertifikat:

1. Välj VPN-klienten konfigurationsfilerna som motsvarar arkitekturen på Windows-dator. Välj installationspaketet 'VpnClientSetupAmd64' för en 64-bitars processorarkitektur. Välj installationspaketet 'VpnClientSetupX86' för en 32-bitars processorarkitektur. 
2. Dubbelklicka på paketet du vill installera den. Om du ser ett SmartScreen-popup-fönster klickar du på **Mer information** och sedan på **Kör ändå**.
3. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till. 

### <a name="admaccli"></a>Konfigurera en Mac (OSX) VPN-klient

1. Välj den **VpnClientSetup mobileconfig** filen och skicka den till varje användare. Du kan använda e-post eller en annan metod för att göra detta.

2. Leta upp den **mobileconfig** fil för Mac.

  ![Leta upp filen mobilconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. Dubbelklicka på profilen som ska installera den, klickar du på **Fortsätt**. Namnet på profilen är samma som namnet på ditt VNet.

  ![Installera](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. Klicka på **Fortsätt** litar på avsändaren av profilen och fortsätta med installationen.

  ![Fortsätt](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. Under profilinstallationen av möjlighet du att ange användarnamn och lösenord som används för VPN-autentisering. Det är inte obligatoriskt att ange den här informationen. Om anges informationen sparas och används automatiskt när du initierar en anslutning. Klicka på **installera** att fortsätta.

  ![settings](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. Ange ett användarnamn och lösenord för den behörighet som krävs för att installera profilen på datorn. Klicka på **OK**.

  ![användarnamn och lösenord](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. När du har installerat profilen som är synliga i den **profiler** dialogrutan. Den här dialogrutan kan även öppna senare från **systeminställningar**.

  ! [systeminställningar]] (. / media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. Om du vill komma åt VPN-anslutningen, öppna den **nätverk** dialogrutan från **systeminställningar**.

  ![Nätverk](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. VPN-anslutningen visas som **IkeV2 VPN**. Namnet kan ändras genom att uppdatera den **mobileconfig** fil.

  ![anslutning](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. Klicka på **autentiseringsinställningar**. Välj **användarnamn** i listrutan och ange dina autentiseringsuppgifter. Om du angav autentiseringsuppgifterna tidigare, sedan **användarnamn** automatiskt är valt i listrutan och användarnamnet och lösenordet är i förväg. Spara inställningarna genom att klicka på **OK**. Detta tar dig tillbaka till dialogrutan nätverk.

  ![autentisera](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. Klicka på **tillämpa** spara ändringarna. Om du vill upprätta anslutningen klickar du på **Anslut**.

## <a name="certeap"></a>Certifikatautentisering
 
Du kan skapa VPN-klienten konfigurationsfilerna för RADIUS-autentisering som använder EAP-TLS-protokollet. Ett Enterprise-utfärdade certifikat används vanligtvis för att autentisera en användare för VPN. Kontrollera att alla anslutande användare ha ett certifikat installeras på användarnas enheter och att certifikatet som kan valideras av RADIUS-servern.
 
* '-AuthenticationMethod' är 'EapTls'.
* Under certifikatautentisering verifierar klienten RADIUS-servern genom att verifiera certifikatet. -RadiusRootCert är .cer-fil som innehåller rot-certifikat som används för att verifiera RADIUS-servern.  
* En windowsenhet har ibland flera klientcertifikat. Detta kan resultera i en popup-dialogruta som visar en lista över alla certifikat vid autentisering. Användaren kan sedan välja certifikatet som ska användas. Rätt certifikat kan filtreras genom att ange rotcertifikatet som klienten cert ska kedja. '-ClientRootCert' är .cer-fil som innehåller rot-certifikat. Det är en valfri parameter. Om du vill ansluta enheten har bara ett klientcertifikat, har inte den här parametern anges.

### <a name="certfiles"></a>Generera konfigurationsfiler för VPN-klienten

Skapa VPN-klientkonfiguration med följande kommando:
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root>
```

Cmdlet-resultatet returnerar en länk. Kopiera och klistra in en länk till en webbläsare för att hämta en komprimerad fil med namnet 'VpnClientConfiguration.zip'. Packa upp filen om du vill se följande mappar:

* Mappar med namnet 'WindowsAmd64' och 'WindowsX86'. Dessa mappar innehåller Windows 64-bitars och 32-bitars installer-paket. 
* En mapp med namnet 'GenericDevice'. Den här mappen innehåller allmän information som används för att skapa din egen konfiguration för VPN-klienten.

Du kan hämta klientkonfigurationsfiler som du redan har skapat. 'Get-AzureRmVpnClientConfiguration-cmdlet returnerar en URL (länk) från där du kan hämta filen VpnClientConfiguration.zip. Om du gör några ändringar i konfigurationen för P2S VPN, till exempel den VPN-protokoll eller autentiseringstyp, uppdateras konfigurationen inte automatiskt. Du måste köra cmdleten 'Ny AzureRmVpnClientConfiguration' för att återskapa konfigurationen.

Om du vill hämta konfigurationsfiler för tidigare genererade klienten använder du följande kommando:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```
 
### <a name="certwincli"></a>Konfigurera en VPN-klient för Windows

1. Välj ett konfigurationspaket och installera den på klientenheten. Välj installationspaketet 'VpnClientSetupAmd64' för en 64-bitars processorarkitektur. Välj installationspaketet 'VpnClientSetupX86' för en 32-bitars processorarkitektur. Om du ser ett SmartScreen-popup-fönster klickar du på **Mer information** och sedan på **Kör ändå**. Du kan också spara paketet om du vill installera det på andra klientdatorer.
2. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till.

### <a name="certmaccli"></a>Konfigurera en VPN-MAC-klienten

En separat profil måste skapas för varje Mac-enhet som ansluter till Azure VNet. Det beror på att dessa enheter kräver användarcertifikat för att autentisering ska anges i profilen. Den **allmänna** mappen innehåller all information som krävs för att skapa en profil.

  * **VpnSettings.xml** innehåller viktiga inställningar, till exempel adress och tunnel servertyp.
  * **VpnServerRoot.cer** innehåller rotcertifikat som krävs för att verifiera VPN-gatewayen under installationen av P2S-anslutning.
  * **RadiusServerRoot.cer** innehåller rotcertifikat som krävs för att validera RADIUS-servern under autentiseringen.

Använd följande steg för att konfigurera den inbyggda VPN-klienten på Mac för autentisering med datorcertifikat:

1. Importera den **VpnServerRoot** och **RadiusServerRoot** root certifikat till datorn. Detta kan göras genom att kopiera filen till din Mac och dubbelklicka på den.  
Klicka på **Lägg till** att importera.

  **Lägg till VpnServerRoot**

  ![Lägg till certifikat](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

  **Lägg till RadiusServerRoot**

  ![Lägg till certifikat](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Öppna den **nätverk** dialogrutan under **nätverksinställningar** och på **”+”** att skapa en ny klient för VPN-anslutningsprofil för en P2S-anslutning till Azure VNet.

  Den **gränssnittet** värdet är 'VPN- och **VPN-typ** värdet är 'IKEv2'. Ange ett namn för profilen i den **tjänstnamnet** fältet och klicka sedan på **skapa** att skapa VPN-anslutningsprofilen för klienten.

  ![Nätverk](./media/point-to-site-vpn-client-configuration-radius/network.png)
3. I den **allmänna** mappen från den **VpnSettings.xml** fil, kopiera den **VpnServer** taggvärde. Klistra in det här värdet i den **serveradress** och **fjärr-ID för** för profilen. Lämna den **lokalt ID** tomt.

  ![serverinformation](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
4. Klicka på **autentiseringsinställningar** och välj **certifikat**. 

  ![autentiseringsinställningar](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
5. Klicka på **Välj...** att välja det certifikat som du vill använda för autentisering.

  ![certifikat](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
6. **Välj en identitet** visar en lista över certifikat som du kan välja från. Välj rätt certifikat och klicka sedan på **Fortsätt**.

  ![identity](./media/point-to-site-vpn-client-configuration-radius/identity.png)
7. I den **lokalt ID** anger namnet på certifikatet (från steg 5). I det här exemplet är det ”ikev2Client.com”. Klicka på **tillämpa** för att spara ändringarna.

  ![tillämpa](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
8. På den **nätverk** dialogrutan klickar du på **tillämpa** spara alla ändringar. Klicka på **Anslut** att starta P2S-anslutning till Azure VNet.

## <a name="otherauth"></a>Andra typer av autentisering eller protokoll

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
