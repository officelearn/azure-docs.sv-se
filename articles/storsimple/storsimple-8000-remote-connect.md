---
title: Ansluta på distans till din StorSimple-enhet
description: I artikeln beskrivs hur du konfigurerar enheten fÃ¶r fjärrhantering och anjÃ¤nder du anslutning till Windows PowerShell fÃ¶r StorSimple via HTTP eller HTTPS.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 58d61df932da06e32bb4c8f21a3a296b185f02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80299007"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Ansluta på distans till din StorSimple 8000-serieenhet

## <a name="overview"></a>Översikt

Du kan fjärransluta till enheten via Windows PowerShell. När du ansluter på det här sättet visas ingen meny. (Du ser bara en meny om du använder seriekonsolen på enheten för att ansluta.) Med Windows PowerShell-ommotning ansluter du till ett visst runspace. Du kan också ange visningsspråket.

Mer information om hur du använder Windows PowerShell-ommotning för att hantera enheten finns i [Använda Windows PowerShell för StorSimple för att administrera din StorSimple-enhet](storsimple-8000-windows-powershell-administration.md).

I den här självstudien beskrivs hur du konfigurerar enheten för fjärrhantering och sedan hur du ansluter till Windows PowerShell för StorSimple. Du kan använda HTTP eller HTTPS för att fjärransluta via Windows PowerShell. Men när du bestämmer hur du ansluter till Windows PowerShell för StorSimple bör du tänka på följande information:

* Det är säkert att ansluta direkt till enhetens seriekonsol, men det är inte säkert att ansluta till den seriella konsolen via nätverksväxlar. Var försiktig med säkerhetsrisken när du ansluter till enhetens seriella konsol via nätverksväxlar.
* Att ansluta via en HTTP-session kan ge större säkerhet än att ansluta via seriekonsolen via nätverket. Även om detta inte är den säkraste metoden är det acceptabelt i betrodda nätverk.
* Att ansluta via en HTTPS-session med ett självsignerat certifikat är det säkraste och rekommenderade alternativet.

Du kan fjärransluta till Windows PowerShell-gränssnittet. Fjärråtkomst till Din StorSimple-enhet via Windows PowerShell-gränssnittet är dock inte aktiverat som standard. Du måste aktivera fjärrhantering på enheten först och sedan på klienten som används för att komma åt enheten.

Stegen som beskrivs i den här artikeln utfördes på ett värdsystem som kör Windows Server 2012 R2.

## <a name="connect-through-http"></a>Ansluta via HTTP

Att ansluta till Windows PowerShell för StorSimple via en HTTP-session ger mer säkerhet än att ansluta via den seriella konsolen på Din StorSimple-enhet. Även om detta inte är den säkraste metoden är det acceptabelt i betrodda nätverk.

Du kan använda antingen Azure-portalen eller seriekonsolen för att konfigurera fjärrhantering. Välj bland följande procedurer:

* Använda Azure-portalen för att aktivera fjärrhantering via HTTP
* [Använd seriekonsolen för att aktivera fjärrhantering via HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

När du har aktiverat fjärrhantering använder du följande procedur för att förbereda klienten för en fjärranslutning.

* [Förbereda klienten för fjärranslutning](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Använda Azure-portalen för att aktivera fjärrhantering via HTTP

Utför följande steg i Azure-portalen för att aktivera fjärrhantering via HTTP.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Så här aktiverar du fjärrhantering via Azure-portalen

1. Gå till StorSimple Device Manager-tjänsten. Välj **Enheter** och välj och klicka sedan på den enhet som du vill konfigurera för fjärrhantering. Gå till **Enhetsinställningar > Säkerhet**.
2. Klicka på **Fjärrhantering**i bladet **Säkerhetsinställningar** .
3. I **bladet Fjärrhantering** ställer du in **Aktivera fjärrhantering** på **Ja**.
4. Nu kan du välja att ansluta med HTTP. (Standardär att ansluta via HTTPS.) Kontrollera att HTTP är markerat.
   
   > [!NOTE]
   > Det är bara acceptabelt att ansluta över HTTP på betrodda nätverk.
   
5. Klicka på **Spara** och välj **Ja**när du uppmanas att bekräfta .

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Använd seriekonsolen för att aktivera fjärrhantering via HTTP
Utför följande steg på enhetens seriekonsol för att aktivera fjärrhantering.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Så här aktiverar du fjärrhantering via enhetens seriekonsol
1. Välj alternativ 1 på menyn seriell konsol. Mer information om hur du använder seriekonsolen på enheten finns i [Anslut till Windows PowerShell för StorSimple via seriell konsol](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)för enheten .
2. Skriv vid prompten:`Enable-HcsRemoteManagement –AllowHttp`
3. Du får ett meddelande om säkerhetsproblemen med att använda HTTP för att ansluta till enheten. Bekräfta genom att skriva **Y**.
4. Kontrollera att HTTP är aktiverat genom att skriva:`Get-HcsSystem`
5. Kontrollera att fältet **RemoteManagementMode** visar **HttpsAndHttpEnabled**. Följande bild visar dessa inställningar i PuTTY.
   
     ![Seriell HTTPS- och HTTP-aktiverad](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Förbereda klienten för fjärranslutning
Utför följande steg på klienten för att aktivera fjärrhantering.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Så här förbereder du klienten för fjärranslutning
1. Starta en Windows PowerShell-session som administratör. Om du använder en Windows 10-klient är tjänsten Windows Remote Management som standard manuell. Du kan behöva starta tjänsten genom att skriva:

    `Start-Service WinRM`
    
2. Skriv följande kommando om du vill lägga till IP-adressen för StorSimple-enheten i klientens lista över betrodda värdar:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Ersätt <*device_ip*> med enhetens IP-adress. till exempel: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Skriv följande kommando för att spara enhetsautentiseringsuppgifterna i en variabel: 
   
    ```
    $cred = Get-Credential
    ```
    
4. I dialogrutan som visas:
   
   1. Skriv användarnamnet i det här formatet: *device_ip\SSAdmin*.
   2. Skriv enhetsadministratörens lösenord som angavs när enheten konfigurerades med installationsguiden. Standardlösenordet är *Password1*.
5. Starta en Windows PowerShell-session på enheten genom att skriva det här kommandot:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Om du vill skapa en Windows PowerShell-session som ska `–Port` användas med den virtuella StorSimple-enheten lägger du till parametern och anger den offentliga port som du har konfigurerat i Remoting för StorSimple Virtual Appliance.
   
   
Nu bör du ha en aktiv fjärr-Windows PowerShell-session till enheten.
   
![PowerShell-ommotning med HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Ansluta via HTTPS

Att ansluta till Windows PowerShell för StorSimple via en HTTPS-session är den säkraste och rekommenderade metoden för fjärranslutning till din Microsoft Azure StorSimple-enhet. Följande procedurer förklarar hur du konfigurerar seriekonsolen och klientdatorerna så att du kan använda HTTPS för att ansluta till Windows PowerShell för StorSimple.

Du kan använda antingen Azure-portalen eller seriekonsolen för att konfigurera fjärrhantering. Välj bland följande procedurer:

* Använda Azure-portalen för att aktivera fjärrhantering via HTTPS
* [Använd seriekonsolen för att aktivera fjärrhantering via HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

När du har aktiverat fjärrhantering använder du följande procedurer för att förbereda värden för en fjärrhantering och ansluta till enheten från fjärrvärden.

* [Förbereda värden för fjärrhantering](#prepare-the-host-for-remote-management)
* [Anslut till enheten från fjärrvärden](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Använda Azure-portalen för att aktivera fjärrhantering via HTTPS

Utför följande steg i Azure-portalen för att aktivera fjärrhantering via HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Så här aktiverar du fjärrhantering via HTTPS från Azure-portalen

1. Gå till StorSimple Device Manager-tjänsten. Välj **Enheter** och välj och klicka sedan på den enhet som du vill konfigurera för fjärrhantering. Gå till **Enhetsinställningar > Säkerhet**.
2. Klicka på **Fjärrhantering**i bladet **Säkerhetsinställningar** .
3. Ställ in **Aktivera fjärrhantering** på **Ja**.
4. Du kan nu välja att ansluta med HTTPS. (Standardär att ansluta via HTTPS.) Kontrollera att HTTPS är markerat.
5. Klicka... och klicka sedan på **Hämta fjärrhanteringscertifikat**. Ange en plats för att spara filen. Du måste installera certifikatet på klienten eller värddatorn som du ska använda för att ansluta till enheten.
6. Klicka på **Spara** och sedan på **Ja** när du uppmanas att bekräfta.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Använd seriekonsolen för att aktivera fjärrhantering via HTTPS

Utför följande steg på enhetens seriekonsol för att aktivera fjärrhantering.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Så här aktiverar du fjärrhantering via enhetens seriekonsol
1. Välj alternativ 1 på menyn seriell konsol. Mer information om hur du använder seriekonsolen på enheten finns i [Anslut till Windows PowerShell för StorSimple via seriell konsol](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)för enheten .
2. Skriv vid prompten:
   
     `Enable-HcsRemoteManagement`
   
    Detta bör aktivera HTTPS på enheten.
3. Kontrollera att HTTPS har aktiverats genom att skriva: 
   
     `Get-HcsSystem`
   
    Kontrollera att fältet **RemoteManagementMode** visar **HttpsEnabled**. Följande bild visar dessa inställningar i PuTTY.
   
     ![Seriell HTTPS aktiverad](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Kopiera enhetens `Get-HcsSystem`serienummer från utdata från och spara det för senare användning.
   
   > [!NOTE]
   > Serienumret mappas till CN-namnet i certifikatet.
   
5. Skaffa ett fjärrhanteringscertifikat genom att skriva: 
   
     `Get-HcsRemoteManagementCert`
   
    Ett certifikat som liknar följande visas.
   
    ![Hämta certifikat för fjärrhantering](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Kopiera informationen i certifikatet från **-----BEGIN-certifikat-----** till **-----END CERTIFIKAT-----** till en textredigerare som Anteckningar och spara den som en .cer-fil. (Du kopierar filen till fjärrvärden när du förbereder värden.)
   
   > [!NOTE]
   > Om du vill generera `Set-HcsRemoteManagementCert` ett nytt certifikat använder du cmdleten.
   
### <a name="prepare-the-host-for-remote-management"></a>Förbereda värden för fjärrhantering

Så här förbereder du värddatorn för en fjärranslutning som använder en HTTPS-session:

* [Importera .cer-filen till klientens eller fjärrvärdens rotarkiv](#to-import-the-certificate-on-the-remote-host).
* [Lägg till enhetens serienummer i hosts-filen på fjärrvärden](#to-add-device-serial-numbers-to-the-remote-host).

Var och en av de föregående procedurerna beskrivs nedan.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Så här importerar du certifikatet på fjärrvärden
1. Högerklicka på .cer-filen och välj **Installera certifikat**. Då startas guiden för att importera certifikat.
   
    ![Guiden Importera certifikat 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. För **Store location** (Lagringsplats) väljer du **Lokal dator** och klickar sedan på **Nästa**.
3. Välj **Place all certificates in the following store** (Placera alla certifikat i följande lagringsplats) och klicka sedan på **Bläddra**. Gå till rotcertifikatarkivet på fjärrvärden och klicka på **Nästa**.
   
    ![Guiden Importera certifikat 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Klicka på **Slutför**. Ett meddelande visas där det står att importen lyckades.
   
    ![Guiden Importera certifikat 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Så här lägger du till enhetsserienummer i fjärrvärden
1. Starta Anteckningar som administratör och öppna sedan hosts-filen som finns på \Windows\System32\Drivers\etc.
2. Lägg till följande tre poster i hosts-filen: **DATA 0 IP-adress,** **Controller 0 Fast IP-adress**och **Controller 1 Fast IP-adress**.
3. Ange enhetens serienummer som du sparade tidigare. Mappa detta till IP-adressen som visas i följande bild. För Controller 0 och Controller 1 lägger du till **Controller0** och **Controller1** i slutet av serienumret (KN-namn).
   
    ![Lägga till CN-namn i hosts-filen](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Spara värdens fil.

### <a name="connect-to-the-device-from-the-remote-host"></a>Anslut till enheten från fjärrvärden

Använd Windows PowerShell och TLS för att ange en SSAdmin-session på enheten från en fjärrvärd eller klient. SSAdmin-sessionen mappar till alternativ 1 i [enhetens seriella](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) konsolmeny.

Utför följande procedur på den dator som du vill göra fjärranslutningen till Windows PowerShell.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-tls"></a>Så här anger du en SSAdmin-session på enheten med hjälp av Windows PowerShell och TLS
1. Starta en Windows PowerShell-session som administratör. Om du använder en Windows 10-klient är tjänsten Windows Remote Management som standard manuell. Du kan behöva starta tjänsten genom att skriva:

    `Start-Service WinRM`

2. Lägg till enhetens IP-adress i klientens betrodda värdar genom att skriva:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Där <*device_ip*> är enhetens IP-adress. till exempel: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Om du vill skapa en ny autentiseringstillstånd skriver du:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Där <*IP för målenhet*> är IP-adressen för DATA 0 för din enhet; till exempel **10.126.173.90** som visas i föregående bild av hosts-filen. Ange också administratörslösenordet för enheten.
4. Skapa en session genom att skriva:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    För parametern -ComputerName i cmdleten anger du <*serienummer för målenhet*>. Det här serienumret mappades till IP-adressen för DATA 0 i hosts-filen på fjärrvärden. till exempel **SHX0991003G44MT** som visas i följande bild.
5. Ange:
   
     `Enter-PSSession $session`
6. Du måste vänta några minuter, och sedan kommer du att vara ansluten till din enhet via HTTPS över TLS. Ett meddelande visas som anger att du är ansluten till enheten.
   
    ![PowerShell-ommotning med HTTPS och TLS](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [hur du använder Windows PowerShell för att administrera Din StorSimple-enhet](storsimple-8000-windows-powershell-administration.md).
* Läs mer om [hur du använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

