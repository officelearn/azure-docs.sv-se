---
title: "Fjärransluta till din StorSimple-enhet | Microsoft Docs"
description: "Beskriver hur du konfigurerar enheten för fjärrhantering och hur du ansluter till Windows PowerShell för StorSimple via HTTP eller HTTPS."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 923377aa-f451-4656-87de-5e95a34a6a2a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ec625a604660489812b1a8c133fa0156a422b605
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Fjärransluta till enheten StorSimple 8000-serien
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Om du vill visa versionen av den här artikeln för den nya Azure portalen, gå till [Anslut via en fjärranslutning till enheten StorSimple 8000-serien](storsimple-8000-remote-connect.md). Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Översikt
Du kan använda Windows PowerShell-fjärrkommunikation för att ansluta till din StorSimple-enhet. När du ansluter det här sättet kan se du inte en meny. (Du se en meny endast om du använder seriekonsolen på enheten för att ansluta.) Med Windows PowerShell-fjärrkommunikation ansluta till en specifik runspace. Du kan också ange språk för visning. 

Mer information om hur du använder Windows PowerShell-fjärrkommunikation för att hantera enheten, gå till [använda Windows PowerShell för StorSimple att administrera din StorSimple-enhet](storsimple-windows-powershell-administration.md).

Den här självstudiekursen beskrivs hur du konfigurerar enheten för fjärrhantering och hur du ansluter till Windows PowerShell för StorSimple. Du kan använda HTTP eller HTTPS för att ansluta via Windows PowerShell-fjärrkommunikation. Men när du bestämmer hur du ansluter till Windows PowerShell för StorSimple, Tänk på följande: 

* Ansluta direkt till enhetens seriekonsol är säker och ansluter till seriekonsol via nätverksväxlar inte. Var försiktig för säkerhetsrisken när du ansluter till enhetens seriekonsol via nätverksväxlar. 
* Ansluter via en HTTP-session kan erbjuda mer säkerhet än att ansluta via seriekonsolen över nätverket. Även om detta inte är den säkraste metoden är är det acceptabelt på betrodda nätverk. 
* Ansluter via en HTTPS-session med ett självsignerat certifikat är den säkraste och det rekommenderade alternativet.

Du kan fjärransluta till Windows PowerShell-gränssnittet. Fjärråtkomst till din StorSimple-enhet via Windows PowerShell-gränssnittet är inte aktiverad som standard. Du måste först aktivera fjärrhantering på enheten och sedan på klienten som används för åtkomst till din enhet.

Stegen som beskrivs i den här artikeln utfördes på ett värdsystem som kör Windows Server 2012 R2.

## <a name="connect-through-http"></a>Ansluta via HTTP
Ansluter till Windows PowerShell för StorSimple via en HTTP-session är säkrare än att ansluta via seriekonsolen av StorSimple-enheten. Även om detta inte är den säkraste metoden är är det acceptabelt på betrodda nätverk.

Du kan använda den klassiska Azure-portalen eller seriekonsolen för att konfigurera fjärrhantering. Välj följande procedurer:

* [Använd den klassiska Azure-portalen för att aktivera fjärrhantering över HTTP](#use-the-azure-classic-portal-to-enable-remote-management-over-http)
* [Använd seriekonsolen för att aktivera fjärrhantering över HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

När du aktiverar fjärrhantering, Använd följande procedur för att Förbered för en fjärranslutning.

* [Förbered för fjärranslutning](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-http"></a>Använd den klassiska Azure-portalen för att aktivera fjärrhantering över HTTP
Utför följande steg i den klassiska Azure-portalen för att aktivera fjärrhantering över HTTP.

#### <a name="to-enable-remote-management-through-the-azure-classic-portal"></a>Så här aktiverar du fjärrhantering via den klassiska Azure-portalen
1. Åtkomst **enheter** > **konfigurera** för din enhet.
2. Bläddra ned till avsnittet **Fjärrhantering**.
3. Ställ in **Aktivera fjärrhantering** på **Ja**.
4. Nu kan du välja att ansluta med HTTP. (Standard är att ansluta via HTTPS.) Se till att HTTP har valts.
   
   > [!NOTE]
   > Det är bara acceptabelt att ansluta över HTTP på betrodda nätverk.
   > 
   > 
5. Klicka på **Spara** längst ned på sidan.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Använd seriekonsolen för att aktivera fjärrhantering över HTTP
Utför följande steg på enhetens seriekonsol för att aktivera fjärrhantering.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Så här aktiverar du fjärrhantering via enhetens seriekonsol
1. Välj alternativ 1 på menyn för seriekonsolen. Mer information om hur du använder seriekonsolen på enheten, gå till [Anslut till Windows PowerShell för StorSimple via enhetens seriekonsol](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Skriv följande i Kommandotolken:`Enable-HcsRemoteManagement –AllowHttp`
3. Du meddelas om säkerhetsrisker med att använda HTTP för att ansluta till enheten. När du uppmanas bekräfta genom att skriva **Y**.
4. Kontrollera att HTTP har aktiverats genom att skriva:`Get-HcsSystem`
5. Kontrollera att den **RemoteManagementMode** fältet visar **HttpsAndHttpEnabled**. Följande bild visar de här inställningarna i PuTTY.
   
     ![Seriell HTTPS och HTTP-aktiverad](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Förbered för fjärranslutning
Utför följande steg på klienten för att aktivera fjärrhantering.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Så här förbereder du klienten för fjärranslutning
1. Starta en Windows PowerShell-session som administratör.
2. Skriv följande kommando för att lägga till IP-adressen för StorSimple-enhet i listan över betrodda värdar klientens: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Ersätt <*device_ip*> med IP-adressen för din enhet, till exempel: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Skriv följande kommando för att spara autentiseringsuppgifter för enheten i en variabel: 
   
    ```
    $cred = Get-Credential
    ```
    
4. I dialogrutan som visas:
   
   1. Ange användarnamnet i formatet: *device_ip\SSAdmin*.
   2. Ange enhetens administratörslösenord som angavs när enheten konfigurerades med installationsguiden. Standardlösenordet är *Password1*.
5. Starta Windows PowerShell-sessionen på enheten genom att skriva följande kommando:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Om du vill skapa en Windows PowerShell-session för användning med den virtuella enheten StorSimple, bifoga den `–Port` parametern och ange den offentliga porten som du konfigurerade i fjärrkommunikation för virtuell StorSimple-enhet.
   > 
   > 
   
     Du bör nu ha en aktiv Windows PowerShell-fjärrsession till enheten.
   
    ![PowerShell-fjärrkommunikation med HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Ansluta via HTTPS
Ansluter till Windows PowerShell för StorSimple via en HTTPS-session är den mest säkra och rekommenderade metoden för att fjärransluta till din Microsoft Azure StorSimple-enhet. Följande procedurer beskriver hur du ställer in serial-konsolen och klienten datorerna så att du kan använda HTTPS för att ansluta till Windows PowerShell för StorSimple.

Du kan använda den klassiska Azure-portalen eller seriekonsolen för att konfigurera fjärrhantering. Välj följande procedurer:

* [Använd den klassiska Azure-portalen för att aktivera fjärrhantering över HTTPS](#use-the-azure-classic-portal-to-enable-remote-management-over-https)
* [Använd seriekonsolen för att aktivera fjärrhantering över HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

När du aktiverar fjärrhantering, Använd följande procedurer för att förbereda värden för en fjärrhantering och ansluter till enheten från fjärrvärden.

* [Förbereda värden för fjärrhantering](#prepare-the-host-for-remote-management)
* [Ansluta till enheten från fjärrvärden](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-https"></a>Använd den klassiska Azure-portalen för att aktivera fjärrhantering över HTTPS
Utför följande steg i den klassiska Azure-portalen för att aktivera fjärrhantering över HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-classic-portal"></a>Att aktivera fjärrhantering via HTTPS från den klassiska Azure-portalen
1. Åtkomst **enheter** > **konfigurera** för din enhet.
2. Bläddra ned till avsnittet **Fjärrhantering**.
3. Ställ in **Aktivera fjärrhantering** på **Ja**.
4. Du kan nu välja att ansluta med HTTPS. (Standard är att ansluta via HTTPS.) Kontrollera att HTTPS är markerad. 
5. Klicka på **hämta certifikat för fjärrhantering**. Ange en plats om du vill spara den här filen. Du behöver installera certifikatet på klienten eller värddatorn datorn som du använder för att ansluta till enheten.
6. Klicka på **Spara** längst ned på sidan.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Använd seriekonsolen för att aktivera fjärrhantering över HTTPS
Utför följande steg på enhetens seriekonsol för att aktivera fjärrhantering.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Så här aktiverar du fjärrhantering via enhetens seriekonsol
1. Välj alternativ 1 på menyn för seriekonsolen. Mer information om hur du använder seriekonsolen på enheten, gå till [Anslut till Windows PowerShell för StorSimple via enhetens seriekonsol](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Skriv följande i Kommandotolken: 
   
     `Enable-HcsRemoteManagement`
   
    Detta bör aktivera HTTPS på enheten.
3. Kontrollera att HTTPS har aktiverats genom att skriva: 
   
     `Get-HcsSystem`
   
    Se till att den **RemoteManagementMode** fältet visar **HttpsEnabled**. Följande bild visar de här inställningarna i PuTTY.
   
     ![Seriell HTTPS-aktiverade](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Från utdata från `Get-HcsSystem`, kopiera serienumret på enheten och spara den för senare användning.
   
   > [!NOTE]
   > Serienumret mappas till CN-namnet i certifikatet.
   > 
   > 
5. Hämta ett certifikat för fjärrhantering genom att skriva: 
   
     `Get-HcsRemoteManagementCert`
   
    Ett certifikat som liknar följande visas.
   
    ![Hämta certifikat för fjärrhantering](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Kopiera informationen i certifikatet från **---BEGIN CERTIFICATE---** till **---END CERTIFICATE---** i en textredigerare, till exempel Anteckningar och spara den som en .cer-fil. (Du kopierar filen till din fjärrvärden när du förbereder värden.)
   
   > [!NOTE]
   > Om du vill skapa ett nytt certifikat, använder den `Set-HcsRemoteManagementCert` cmdlet.
   > 
   > 

### <a name="prepare-the-host-for-remote-management"></a>Förbereda värden för fjärrhantering
Utför följande procedurer för att förbereda värddatorn för en anslutning som använder en HTTPS-session:

* [Importera CER-filen till rotarkivet på klienten eller fjärrvärden](#to-import-the-certificate-on-the-remote-host).
* [Lägg till enhetens serienummer i hosts-filen på din fjärrvärden](#to-add-device-serial-numbers-to-the-remote-host).

Var och en av de här procedurerna beskrivs nedan.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Importera certifikatet på fjärrvärden
1. Högerklicka på .cer-filen och välj **installera certifikat**. Detta startar guiden Importera certifikat.
   
    ![Guiden Importera 1 certifikat](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. För **plats**väljer **lokal dator**, och klicka sedan på **nästa**.
3. Välj **placera alla certifikat i nedanstående arkiv**, och klicka sedan på **Bläddra**. Navigera till rotarkivet för din fjärrvärden och klicka sedan på **nästa**.
   
    ![Guiden Importera 2 certifikat](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Klicka på **Slutför**. Visas ett meddelande som talar om att importen lyckades.
   
    ![Guiden Importera 3 certifikat](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Att lägga till Enhetsserienummer fjärrvärden
1. Starta Anteckningar som administratör och öppna värdfilen som finns på \Windows\System32\Drivers\etc.
2. Lägg till följande tre poster i hosts-filen: **DATA 0 IP-adress**, **fasta IP-adressen för styrenhet 0**, och **domänkontrollant 1 fasta IP-adressen**.
3. Ange serienumret för enheten som du sparade tidigare. Mappa till IP-adressen som visas i följande bild. Lägg till för styrenhet 0 och 1 **Controller0** och **Controller1** i slutet av serienumret (CN-namn).
   
    ![Lägger till CN-namn i hosts-filen](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Spara hosts-filen.

### <a name="connect-to-the-device-from-the-remote-host"></a>Ansluta till enheten från fjärrvärden
Använda Windows PowerShell och SSL för att ange en SSAdmin session på din enhet från en fjärrvärd eller klienten. Sessionen SSAdmin mappar till alternativ 1 i den [seriekonsolen](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) -menyn i enheten.

Utför följande procedur på den dator som du vill göra fjärranslutning i Windows PowerShell.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Ange en SSAdmin session på enheten med hjälp av Windows PowerShell och SSL
1. Starta en Windows PowerShell-session som administratör.
2. Lägg till enhetens IP-adress i klientens betrodda värdar genom att skriva:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Där <*device_ip*> är IP-adressen för din enhet, till exempel: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Skapa en ny autentiseringsuppgift genom att skriva: 
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Där <*IP målenhet*> är IP-adressen för DATA 0 för din enhet, till exempel **10.126.173.90** som visas i föregående bild av hosts-filen. Dessutom ange administratörslösenordet för enheten.
4. Skapa en session genom att skriva:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Parametern - ComputerName i cmdleten, ange den <*serienumret för målenhet*>. Det här serienumret mappades till IP-adressen för DATA 0 i värdfilen på fjärrvärden; till exempel **SHX0991003G44MT** som visas i följande bild.
5. Ange: 
   
     `Enter-PSSession $session`
6. Du kommer att behöva vänta några minuter och sedan ansluts du till din enhet via HTTPS via SSL. Du ser ett meddelande som anger att du är ansluten till din enhet.
   
    ![PowerShell-fjärrkommunikation med hjälp av HTTPS och SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [använder Windows PowerShell för att administrera din StorSimple-enhet](storsimple-windows-powershell-administration.md).
* Lär dig mer om [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

