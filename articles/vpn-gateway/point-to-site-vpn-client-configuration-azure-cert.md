---
title: "Skapa och installera P2S VPN-klientkonfigurationsfiler för Azure certifikatautentisering: PowerShell: Azure | Microsoft Docs"
description: "Skapa och installera Windows och Mac OS X VPN-klientkonfigurationsfiler för P2S certifikatautentisering."
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
ms.openlocfilehash: b18e01a780f6371ecae3298a6f3f41949090b9e8
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-point-to-site-configurations"></a>Skapa och installera VPN klientkonfigurationsfiler för interna Azure certifikat autentisering punkt-till-plats-konfigurationer

Konfigurationsfiler för VPN-klienten finns i en zip-fil. Konfigurationsfiler ange inställningar som krävs för en ursprunglig Windows eller Mac IKEv2 VPN-klient att ansluta till ett virtuellt nätverk via punkt-till-plats-anslutningar som använder interna Azure certifikatautentisering.

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
  * **Allmän**, som innehåller allmän information som används för att skapa din egen konfiguration för VPN-klienten. Ignorera den här mappen. Den generiska mappen tillhandahålls om IKEv2 eller SSTP + IKEv2 konfigurerades på gateway. Den generiska mappen är inte installerat om endast SSTP har konfigurerats.

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

## <a name="installwin"></a>Installera en konfiguration för Windows VPN-klientpaketet

Du kan använda samma konfigurationspaketet för VPN-klienten på alla Windows-klientdatorer, förutsatt att versionen matchar arkitekturen för klienten. Lista över klientoperativsystem som stöds finns i avsnittet punkt-till-plats för den [VPN-Gateway FAQ](vpn-gateway-vpn-faq.md#P2S).

Använd följande steg för att konfigurera den inbyggda Windows VPN-klienten för autentisering med datorcertifikat:

1. Välj VPN-klienten konfigurationsfilerna som motsvarar arkitekturen på Windows-dator. Välj installationspaketet 'VpnClientSetupAmd64' för en 64-bitars processorarkitektur. Välj installationspaketet 'VpnClientSetupX86' för en 32-bitars processorarkitektur. 
2. Dubbelklicka på paketet du vill installera den. Om du ser ett SmartScreen-popup-fönster klickar du på **Mer information** och sedan på **Kör ändå**.
3. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till. 
4. Innan du försöker ansluta och kontrollera att har du installerat ett klientcertifikat på klientdatorn. Ett klientcertifikat krävs för autentisering när du använder inbyggd Azure authentication certifikattyp. Mer information om hur du genererar certifikat finns [generera certifikat](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Information om hur du installerar ett klientcertifikat finns [installera ett klientcertifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>VPN-klientkonfiguration på Mac-datorer (OS X)

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

  ![använd](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. På den **nätverk** dialogrutan klickar du på **tillämpa** spara alla ändringar. Klicka på **Anslut** att starta P2S-anslutning till Azure VNet.

## <a name="next-steps"></a>Nästa steg

Gå tillbaka till artikeln [Slutför konfigurationen P2S](vpn-gateway-howto-point-to-site-rm-ps.md).

P2S felsökningsinformation [felsöka Azure punkt-till-plats-anslutningar](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).