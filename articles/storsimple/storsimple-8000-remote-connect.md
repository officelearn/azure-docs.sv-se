---
title: Fjärransluta till din StorSimple-enhet | Microsoft Docs
description: Beskriver hur du konfigurerar din enhet för fjärrhantering och hur du ansluter till Windows PowerShell för StorSimple via HTTP eller HTTPS.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05bec60f4c56c98e9b910b50e858656a2e5554b2
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816500"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Fjärransluta till din enhet i StorSimple 8000-serien

## <a name="overview"></a>Översikt

Du kan fjärransluta till din enhet via Windows PowerShell. När du ansluter på så sätt kan ser du inte en meny. (Du se en meny bara om du använder seriekonsolen på enheten för att ansluta.) Med Windows PowerShell-fjärrkommunikation ansluta till en specifik körningsutrymme. Du kan också ange språk för visning.

Mer information om hur du använder Windows PowerShell-fjärrkommunikation för att hantera din enhet går du till [Använd Windows PowerShell för StorSimple att administrera din StorSimple-enhet](storsimple-8000-windows-powershell-administration.md).

Den här självstudien beskrivs hur du konfigurerar din enhet för fjärrhantering och sedan hur du ansluter till Windows PowerShell för StorSimple. Du kan använda HTTP eller HTTPS för att fjärransluta via Windows PowerShell. Men när du bestämmer hur du ansluter till Windows PowerShell för StorSimple, Tänk på följande:

* Ansluta direkt till enhetens seriekonsol är säkra, men är ansluta till seriekonsol nätverksväxlar inte. Se upp för säkerhetsrisken när du ansluter till enhetens seriekonsol över nätverksväxlar.
* Ansluta via en HTTP-session kan erbjuda mer säkerhet än att ansluta via seriekonsolen över nätverket. Även om detta inte är den säkraste metoden är det acceptabelt på betrodda nätverk.
* Ansluta via en HTTPS-session med ett självsignerat certifikat är det säkraste alternativet och det rekommenderade alternativet.

Du kan fjärransluta till Windows PowerShell-gränssnittet. Fjärråtkomst till din StorSimple-enhet via Windows PowerShell-gränssnittet är inte aktiverad som standard. Du måste aktivera fjärrhantering på enheten först och sedan på klienten som används för att komma åt enheten.

Stegen som beskrivs i den här artikeln utfördes på ett värdsystem som kör Windows Server 2012 R2.

## <a name="connect-through-http"></a>Ansluta via HTTP

Ansluta till Windows PowerShell för StorSimple via en HTTP-session ger högre säkerhet än Anslut via seriekonsol för StorSimple-enheten. Även om detta inte är den säkraste metoden är det acceptabelt på betrodda nätverk.

Du kan använda Azure portal eller seriell konsol för att konfigurera fjärrhantering. Välj följande procedurer:

* Använd Azure portal för att aktivera fjärrhantering över HTTP
* [Använd seriekonsol för att aktivera fjärrhantering över HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

När du har aktiverat fjärrhantering, följ anvisningarna för att förbereda klienten för en fjärranslutning.

* [Förbered för fjärranslutning](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Använd Azure portal för att aktivera fjärrhantering över HTTP

Utför följande steg i Azure portal för att aktivera fjärrhantering via HTTP.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Aktivera fjärrhantering via Azure portal

1. Gå till StorSimple Device Manager-tjänsten. Välj **enheter** och markera och klicka på den enhet som du vill konfigurera för fjärrhantering. Gå till **Enhetsinställningar > säkerhet**.
2. I den **säkerhetsinställningar** bladet klickar du på **fjärrhantering**.
3. I den **fjärrhantering** , anger du **aktivera fjärrhantering** till **Ja**.
4. Nu kan du välja att ansluta med HTTP. (Standard är att ansluta via HTTPS.) Se till att HTTP är markerad.
   
   > [!NOTE]
   > Det är bara acceptabelt att ansluta över HTTP på betrodda nätverk.
   
5. Klicka på **spara** och när du uppmanas att bekräfta väljer **Ja**.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Använd seriekonsol för att aktivera fjärrhantering över HTTP
Utför följande steg på enhetens seriekonsol vill aktivera fjärrhantering.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Aktivera fjärrhantering via enhetens seriekonsol
1. På menyn för seriekonsolen väljer du alternativ 1. Mer information om hur du använder seriekonsolen på enheten går du till [Anslut till Windows PowerShell för StorSimple via enhetens seriekonsol](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. I Kommandotolken skriver du: `Enable-HcsRemoteManagement –AllowHttp`
3. Du får ett meddelande om säkerhetsrisker med att använda HTTP för att ansluta till enheten. När du uppmanas bekräfta genom att skriva **Y**.
4. Kontrollera att HTTP är aktiverat genom att skriva: `Get-HcsSystem`
5. Kontrollera att den **RemoteManagementMode** fältet visar **HttpsAndHttpEnabled**. Följande bild visar de här inställningarna i PuTTY.
   
     ![Seriell HTTPS och HTTP som aktiverats](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Förbered för fjärranslutning
Utför följande steg på klienten för att aktivera fjärrhantering.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Förbereda klienten för fjärranslutning
1. Starta en Windows PowerShell-session som administratör. Om du använder en Windows 10-klient som standard, är Windows Remote Management-tjänsten inställd till manuell. Du kan behöva starta tjänsten genom att skriva:

    `Start-Service WinRM`
    
2. Skriv följande kommando för att lägga till IP-adressen för StorSimple-enheten i listan över betrodda värdar klientens:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Ersätt <*device_ip*> med IP-adressen för din enhet, till exempel: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Skriv följande kommando för att spara enhetsautentiseringsuppgifterna i en variabel: 
   
    ```
    $cred = Get-Credential
    ```
    
4. I dialogrutan som visas:
   
   1. Skriv användarnamnet i följande format: *device_ip\SSAdmin*.
   2. Ange enhetens administratörslösenord som angavs när enheten konfigurerades med installationsguiden. Standardlösenordet är *Password1*.
5. Starta en Windows PowerShell-session på enheten genom att skriva följande kommando:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Skapa en Windows PowerShell-session för användning med den virtuella StorSimple-enheten genom att lägga till den `–Port` parametern och ange den offentliga porten som du konfigurerade i fjärrkommunikation för StorSimple Virtual Appliance.
   
   
Du bör nu ha en aktiv fjärransluten Windows PowerShell-session till enheten.
   
![PowerShell-fjärrkommunikation med HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Ansluta via HTTPS

Ansluta till Windows PowerShell för StorSimple via en HTTPS-session är den mest säkra och rekommenderade metoden för att fjärransluta till din Microsoft Azure StorSimple-enhet. Följande procedurer beskriver hur du ställer in de seriella konsolen och klientdatorerna så att du kan använda HTTPS för att ansluta till Windows PowerShell för StorSimple.

Du kan använda Azure portal eller seriell konsol för att konfigurera fjärrhantering. Välj följande procedurer:

* Använd Azure portal för att aktivera fjärrhantering via HTTPS
* [Använd seriekonsol för att aktivera fjärrhantering via HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

När du har aktiverat fjärrhantering kan du använda följande procedurer för att förbereda värden för en fjärrhantering och ansluta till enheten från fjärrvärden.

* [Förbereda värden för fjärrhantering](#prepare-the-host-for-remote-management)
* [Ansluta till enheten från fjärrvärden](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Använd Azure portal för att aktivera fjärrhantering via HTTPS

Utför följande steg i Azure portal för att aktivera fjärrhantering via HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Aktivera fjärrhantering via HTTPS från Azure portal

1. Gå till StorSimple Device Manager-tjänsten. Välj **enheter** och markera och klicka på den enhet som du vill konfigurera för fjärrhantering. Gå till **Enhetsinställningar > säkerhet**.
2. I den **säkerhetsinställningar** bladet klickar du på **fjärrhantering**.
3. Ställ in **Aktivera fjärrhantering** på **Ja**.
4. Du kan nu välja att ansluta med hjälp av HTTPS. (Standard är att ansluta via HTTPS.) Kontrollera att HTTPS har valts.
5. Klicka på... och klicka sedan på **ladda ned Fjärrhanteringscertifikatet**. Ange en plats för att spara den här filen. Du måste installera detta certifikat på klienten eller värddatorn datorn som du använder för att ansluta till enheten.
6. Klicka på **spara** och klicka sedan på **Ja** när du uppmanas att bekräfta.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Använd seriekonsol för att aktivera fjärrhantering via HTTPS

Utför följande steg på enhetens seriekonsol vill aktivera fjärrhantering.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Aktivera fjärrhantering via enhetens seriekonsol
1. På menyn för seriekonsolen väljer du alternativ 1. Mer information om hur du använder seriekonsolen på enheten går du till [Anslut till Windows PowerShell för StorSimple via enhetens seriekonsol](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. I Kommandotolken skriver du:
   
     `Enable-HcsRemoteManagement`
   
    Detta bör aktivera HTTPS på din enhet.
3. Kontrollera att HTTPS har aktiverats genom att skriva: 
   
     `Get-HcsSystem`
   
    Se till att den **RemoteManagementMode** fältet visar **HttpsEnabled**. Följande bild visar de här inställningarna i PuTTY.
   
     ![Seriell HTTPS-aktiverade](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Från utdata från `Get-HcsSystem`, kopiera serienumret för enheten och spara den för senare användning.
   
   > [!NOTE]
   > Serienumret mappar till CN-namnet i certifikatet.
   
5. Hämta ett certifikat för fjärrhantering genom att skriva: 
   
     `Get-HcsRemoteManagementCert`
   
    Ett certifikat som liknar följande visas.
   
    ![Hämta certifikat för fjärrhantering](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Kopiera informationen i certifikatet från **---BEGIN CERTIFICATE---** till **---END CERTIFICATE---** i en textredigerare, till exempel Anteckningar och spara den som en .cer-fil. (Du kommer att kopiera den här filen till din fjärrvärd när du förbereder värden.)
   
   > [!NOTE]
   > Generera ett nytt certifikat genom att använda den `Set-HcsRemoteManagementCert` cmdlet.
   
### <a name="prepare-the-host-for-remote-management"></a>Förbereda värden för fjärrhantering

Utför följande procedurer för att förbereda värddatorn för en anslutning som använder en HTTPS-session:

* [Importera CER-filen till rotcertifikatutfärdare på klienten eller fjärrvärden](#to-import-the-certificate-on-the-remote-host).
* [Lägga till enhetens serienummer i hosts-filen på din fjärrvärden](#to-add-device-serial-numbers-to-the-remote-host).

Var och en av de föregående procedurerna beskrivs nedan.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Importera certifikatet på fjärrvärden
1. Högerklicka på .cer-filen och välj **Installera certifikat**. Då startas guiden för att importera certifikat.
   
    ![Certifikatimport 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. För **Store location** (Lagringsplats) väljer du **Lokal dator** och klickar sedan på **Nästa**.
3. Välj **Place all certificates in the following store** (Placera alla certifikat i följande lagringsplats) och klicka sedan på **Bläddra**. Gå till rotcertifikatarkivet på fjärrvärden och klicka på **Nästa**.
   
    ![Certifikatimport 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Klicka på **Slutför**. Ett meddelande visas där det står att importen lyckades.
   
    ![Certifikatimport 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Att lägga till Enhetsserienummer fjärrvärden
1. Starta Anteckningar som administratör och sedan öppna värdfilen som finns på \Windows\System32\Drivers\etc.
2. Lägg till följande tre poster i hosts-filen: **DATA 0 IP-adress**, **fasta IP-adress för kontrollenhet 0**, och **styrenhet 1 fasta IP-adress**.
3. Ange enhetens serienummer som du sparade tidigare. Mappa detta till IP-adressen som visas i följande bild. Lägg till för styrenhet 0 och 1 **Controller0** och **Controller1** i slutet av serienummer (CN-namn).
   
    ![Att lägga till CN-namnet i hosts-filen](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Spara i hosts-filen.

### <a name="connect-to-the-device-from-the-remote-host"></a>Ansluta till enheten från fjärrvärden

Ange en SSAdmin-session på din enhet från en fjärrvärd eller -klient med hjälp av Windows PowerShell och SSL. Sessionen SSAdmin mappar till alternativ 1 i den [seriekonsolen](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) menyn på din enhet.

Utför följande procedur på den dator som du vill skapa fjärranslutning i Windows PowerShell.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Ange en SSAdmin-session på enheten med hjälp av Windows PowerShell och SSL
1. Starta en Windows PowerShell-session som administratör. Om du använder en Windows 10-klient som standard, är Windows Remote Management-tjänsten inställd till manuell. Du kan behöva starta tjänsten genom att skriva:

    `Start-Service WinRM`

2. Lägg till enhetens IP-adress i klientens betrodda värdar genom att skriva:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Där <*device_ip*> är IP-adressen för din enhet, till exempel: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Om du vill skapa en ny autentiseringsuppgift, skriver du:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Där <*IP-Adressen för målenheten*> är IP-adressen för DATA 0 för din enhet, till exempel **10.126.173.90** som visas i föregående bild av hosts-filen. Dessutom ange ett administratörslösenord för enheten.
4. Skapa en session genom att skriva:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    -ComputerName-parameter i cmdleten, ange den <*target enhetens serienummer*>. Det här serienumret mappades till IP-adressen för DATA 0 i hosts-filen på din fjärrvärden; till exempel **SHX0991003G44MT** enligt följande bild.
5. Ange:
   
     `Enter-PSSession $session`
6. Du kommer att behöva vänta några minuter och sedan du ansluts till din enhet via HTTPS via SSL. Du ser ett meddelande som anger att du är ansluten till din enhet.
   
    ![PowerShell-fjärrkommunikation med hjälp av HTTPS och SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [använder Windows PowerShell för att administrera din StorSimple-enhet](storsimple-8000-windows-powershell-administration.md).
* Läs mer om [med StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

