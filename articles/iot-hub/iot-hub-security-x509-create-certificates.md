---
title: "Hur du skapar X.509-certifikat med hjälp av PowerShell | Microsoft Docs"
description: "Hur du använder PowerShell för att skapa lokalt X.509-certifikat och aktivera X.509 baserat säkerheten i din Azure IoT-hubb i en simulerad miljö."
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2017
ms.author: dkshir
ms.openlocfilehash: b2f78e8debd367f86ee9bb06bf7de50590c61ad7
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2018
---
# <a name="powershell-scripts-to-manage-ca-signed-x509-certificates"></a>PowerShell-skript för att hantera CA-signerat X.509-certifikat

X.509 certifikatbaserad säkerhet i IoT-hubben måste du börja med en [X.509 certifikatkedja](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), som innehåller rotcertifikatet, liksom alla mellanliggande certifikat fram till lövcertifikatet. Detta *så* guiden går du igenom exempel PowerShell-skript som använder [OpenSSL](https://www.openssl.org/) att skapa och signera X.509-certifikat. Vi rekommenderar att du kan använda den här guiden för experimentering, eftersom många av de här stegen sker under tillverkar processen i verkligheten. Du kan använda dessa certifikat för att simulera säkerheten i din Azure IoT-hubb med den *X.509 certifikatautentisering*. Stegen i den här guiden Skapa certifikat lokalt på din Windows-dator. 

## <a name="prerequisites"></a>Förutsättningar
Den här kursen förutsätter att du har köpt OpenSSL-binärfilerna. Du kan antingen
    - Hämta OpenSSL källkoden och skapa de binära filerna på din dator eller 
    - Hämta och installera någon [från tredje part OpenSSL binärfiler](https://wiki.openssl.org/index.php/Binaries), till exempel från [projektet på SourceForge](https://sourceforge.net/projects/openssl/).

<a id="createcerts"></a>

## <a name="create-x509-certificates"></a>Skapa X.509-certifikat
Följande steg visar ett exempel på hur du skapar X.509 rotcertifikat lokalt. 

1. Öppna ett PowerShell-fönster som en *administratör*. 
2. Gå till arbetskatalogen. Kör följande skript för att ställa in de globala variablerna. 
    ```PowerShell
    $openSSLBinSource = "<full_path_to_the_binaries>\OpenSSL\bin"
    $errorActionPreference    = "stop"

    # Note that these values are for test purpose only
    $_rootCertSubject         = "CN=Azure IoT Root CA"
    $_intermediateCertSubject = "CN=Azure IoT Intermediate {0} CA"
    $_privateKeyPassword      = "123"

    $rootCACerFileName          = "./RootCA.cer"
    $rootCAPemFileName          = "./RootCA.pem"
    $intermediate1CAPemFileName = "./Intermediate1.pem"
    $intermediate2CAPemFileName = "./Intermediate2.pem"
    $intermediate3CAPemFileName = "./Intermediate3.pem"

    $openSSLBinDir              = Join-Path $ENV:TEMP "openssl-bin"

    # Whether to use ECC or RSA.
    $useEcc                     = $true
    ```
3. Kör följande skript som kopierar OpenSSL-binärfiler till arbetskatalogen och ställer in miljövariablerna:

    ```PowerShell
    function Initialize-CAOpenSSL()
    {
        Write-Host ("Beginning copy of openssl binaries to {0} (and setting up env variables...)" -f $openSSLBinDir)
        if (-not (Test-Path $openSSLBinDir))
        {
            mkdir $openSSLBinDir | Out-Null
        }

        robocopy $openSSLBinSource $openSSLBinDir * /s 
        robocopy $openSSLBinSource . * /s 

        Write-Host "Setting up PATH and other environment variables."
        $ENV:PATH += "; $openSSLBinDir"
        $ENV:OPENSSL_CONF = Join-Path $openSSLBinDir "openssl.cnf"

        Write-Host "Success"
    }
    Initialize-CAOpenSSL
    ```
4. Kör följande skript söker om ett certifikat med det angivna *ämnesnamn* redan är installerad, och om OpenSSL har konfigurerats korrekt på datorn:
    ```PowerShell
    function Get-CACertBySubjectName([string]$subjectName)
    {
        $certificates = gci -Recurse Cert:\LocalMachine\ |? { $_.gettype().name -eq "X509Certificate2" }
        $cert = $certificates |? { $_.subject -eq $subjectName -and $_.PSParentPath -eq "Microsoft.PowerShell.Security\Certificate::LocalMachine\My" }
        if ($NULL -eq $cert)
        {
            throw ("Unable to find certificate with subjectName {0}" -f $subjectName)
        }
    
        write $cert
    }
    function Test-CAPrerequisites()
    {
        $certInstalled = $null
        try
        {
            $certInstalled = Get-CACertBySubjectName $_rootCertSubject
        }
        catch {}

        if ($NULL -ne $certInstalled)
        {
            throw ("Certificate {0} already installed.  Cleanup CA certs 1st" -f $_rootCertSubject)
        }

        if ($NULL -eq $ENV:OPENSSL_CONF)
        {
            throw ("OpenSSL not configured on this system.  Run 'Initialize-CAOpenSSL' (even if you've already done so) to set everything up.")
        }
        Write-Host "Success"
    }
    Test-CAPrerequisites
    ```
    Om allt är korrekt konfigurerad, bör du se ”klar” visas. 

<a id="createcertchain"></a>

## <a name="create-x509-certificate-chain"></a>Skapa kedja för X.509-certifikat
Skapa en certifikatkedja med en rot-CA, till exempel ”CN = Azure IoT Root CA” att det här exemplet använder genom att köra följande PowerShell-skript. Det här skriptet uppdateras också ditt Windows OS-certifikatarkiv, skapar samt certifikatfiler i arbetskatalogen. 
    1. Följande skript skapar ett PowerShell-funktionen om du vill skapa ett självsignerat certifikat, för en given *ämnesnamn* och signering utfärdare. 
    ```PowerShell
    function New-CASelfsignedCertificate([string]$subjectName, [object]$signingCert, [bool]$isASigner=$true)
    {
        # Build up argument list
        $selfSignedArgs =@{"-DnsName"=$subjectName; 
                           "-CertStoreLocation"="cert:\LocalMachine\My";
                           "-NotAfter"=(get-date).AddDays(30); 
                          }

        if ($isASigner -eq $true)
        {
            $selfSignedArgs += @{"-KeyUsage"="CertSign"; }
            $selfSignedArgs += @{"-TextExtension"= @(("2.5.29.19={text}ca=TRUE&pathlength=12")); }
        }
        else
        {
            $selfSignedArgs += @{"-TextExtension"= @("2.5.29.37={text}1.3.6.1.5.5.7.3.2,1.3.6.1.5.5.7.3.1", "2.5.29.19={text}ca=FALSE&pathlength=0")  }
        }

        if ($signingCert -ne $null)
        {
            $selfSignedArgs += @{"-Signer"=$signingCert }
        }

        if ($useEcc -eq $true)
        {
            $selfSignedArgs += @{"-KeyAlgorithm"="ECDSA_nistP256";
                             "-CurveExport"="CurveName" }
        }

        # Now use splatting to process this
        Write-Host ("Generating certificate {0} which is for prototyping, NOT PRODUCTION.  It will expire in 30 days." -f $subjectName)
        write (New-SelfSignedCertificate @selfSignedArgs)
    }
    ``` 
    2. Följande PowerShell-funktionen skapar mellanliggande X.509-certifikat med hjälp av funktionen föregående samt OpenSSL-binärfilerna. 
    ```PowerShell
    function New-CAIntermediateCert([string]$subjectName, [Microsoft.CertificateServices.Commands.Certificate]$signingCert, [string]$pemFileName)
    {
        $certFileName = ($subjectName + ".cer")
        $newCert = New-CASelfsignedCertificate $subjectName $signingCert
        Export-Certificate -Cert $newCert -FilePath $certFileName -Type CERT | Out-Null
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\CA" -FilePath $certFileName | Out-Null

        # Store public PEM for later chaining
        openssl x509 -inform deer -in $certFileName -out $pemFileName

        del $certFileName
   
        write $newCert
    }  
    ```
    3. Följande PowerShell-funktionen skapar kedja för X.509-certifikat. Läs [certifikat kedjor](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification) för mer information.
    ```PowerShell
    function New-CACertChain()
    {
        Write-Host "Beginning to install certificate chain to your LocalMachine\My store"
        $rootCACert =  New-CASelfsignedCertificate $_rootCertSubject $null
    
        Export-Certificate -Cert $rootCACert -FilePath $rootCACerFileName  -Type CERT
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\Root" -FilePath $rootCACerFileName

        openssl x509 -inform der -in $rootCACerFileName -out $rootCAPemFileName

        $intermediateCert1 = New-CAIntermediateCert ($_intermediateCertSubject -f "1") $rootCACert $intermediate1CAPemFileName
        $intermediateCert2 = New-CAIntermediateCert ($_intermediateCertSubject -f "2") $intermediateCert1 $intermediate2CAPemFileName
        $intermediateCert3 = New-CAIntermediateCert ($_intermediateCertSubject -f "3") $intermediateCert2 $intermediate3CAPemFileName
        Write-Host "Success"
    }    
    ```
    Det här skriptet skapar en fil med namnet *RootCA.cer* i arbetskatalogen. 
    4. Slutligen använda funktionerna PowerShell för att skapa certifikatkedja X.509 genom att köra kommandot `New-CACertChain` i PowerShell-fönster. 


<a id="signverificationcode"></a>

## <a name="proof-of-possession-of-your-x509-ca-certificate"></a>Konceptbevis tillgång för din CA X.509-certifikat

Det här skriptet utför den *bevis tillgång* flödet för X.509-certifikat. 

Kör följande kod i PowerShell-fönster på skrivbordet:
   
   ```PowerShell
   function New-CAVerificationCert([string]$requestedSubjectName)
   {
       $cnRequestedSubjectName = ("CN={0}" -f $requestedSubjectName)
       $verifyRequestedFileName = ".\verifyCert4.cer"
       $rootCACert = Get-CACertBySubjectName $_rootCertSubject
       Write-Host "Using Signing Cert:::" 
       Write-Host $rootCACert
   
       $verifyCert = New-CASelfsignedCertificate $cnRequestedSubjectName $rootCACert $false

       Export-Certificate -cert $verifyCert -filePath $verifyRequestedFileName -Type Cert
       if (-not (Test-Path $verifyRequestedFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $verifyRequestedFileName)
       }
   
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnRequestedSubjectName, (Join-Path (get-location).path $verifyRequestedFileName)) 
   }
   New-CAVerificationCert "<your verification code>"
   ```

Den här koden skapar ett certifikat med den angivna ämnesnamn signerats av CA: N, som en fil med namnet *VerifyCert4.cer* i arbetskatalogen. Den här certifikatfilen hjälper dig att kontrollera med din IoT-hubb som du har behörigheten signering (det vill säga den privata nyckeln) för denna Certifikatutfärdare.


<a id="createx509device"></a>

## <a name="create-leaf-x509-certificate-for-your-device"></a>Skapa lövmedlemmar X.509-certifikat för din enhet

Det här avsnittet visar du kan använda ett PowerShell-skript som skapar en enhet lövcertifikatet och motsvarande certifikatkedjan. 

Kör följande skript för att skapa en CA-signerat X.509-certifikat för den här enheten i PowerShell-fönstret på den lokala datorn:

   ```PowerShell
   function New-CADevice([string]$deviceName, [string]$signingCertSubject=$_rootCertSubject)
   {
       $cnNewDeviceSubjectName = ("CN={0}" -f $deviceName)
       $newDevicePfxFileName = ("./{0}.pfx" -f $deviceName)
       $newDevicePemAllFileName      = ("./{0}-all.pem" -f $deviceName)
       $newDevicePemPrivateFileName  = ("./{0}-private.pem" -f $deviceName)
       $newDevicePemPublicFileName   = ("./{0}-public.pem" -f $deviceName)
   
       $signingCert = Get-CACertBySubjectName $signingCertSubject ## "CN=Azure IoT CA Intermediate 1 CA"

       $newDeviceCertPfx = New-CASelfSignedCertificate $cnNewDeviceSubjectName $signingCert $false
   
       $certSecureStringPwd = ConvertTo-SecureString -String $_privateKeyPassword -Force -AsPlainText

       # Export the PFX of the cert we've just created.  The PFX is a format that contains both public and private keys.
       Export-PFXCertificate -cert $newDeviceCertPfx -filePath $newDevicePfxFileName -password $certSecureStringPwd
       if (-not (Test-Path $newDevicePfxFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $newDevicePfxFileName)
       }

       # Begin the massaging.  First, turn the PFX into a PEM file which contains public key, private key, and other attributes.
       Write-Host ("When prompted for password by openssl, enter the password as {0}" -f $_privateKeyPassword)
       openssl pkcs12 -in $newDevicePfxFileName -out $newDevicePemAllFileName -nodes

       # Convert the PEM to get formats we can process
       if ($useEcc -eq $true)
       {
           openssl ec -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       else
       {
           openssl rsa -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       openssl x509 -in $newDevicePemAllFileName -out $newDevicePemPublicFileName
 
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnNewDeviceSubjectName, (Join-Path (get-location).path $newDevicePemPublicFileName)) 
   }
   ```

Kör sedan `New-CADevice "<yourTestDevice>"` i PowerShell-fönster med hjälp av det egna namnet som du använde för att skapa din enhet. Ange ”123” när du uppmanas att ange lösenordet för CA: ns privata nyckel. Detta skapar en  _<yourTestDevice>.pfx_ filen i arbetskatalogen.

## <a name="clean-up-certificates"></a>Rensa certifikat

I startfältet eller **inställningar** app, Sök efter och välj **hantera datorcertifikat**. Ta bort eventuella certifikat som utfärdats av ** Azure IoT CA TestOnly ***. Dessa certifikat ska finnas på följande tre platser: 

* Certifikat - lokal dator > personliga > certifikat
* Certifikat - lokal dator > betrodda rotcertifikatutfärdare > certifikat
* Certifikat - lokal dator > mellanliggande certifikatutfärdare > certifikat

   ![Ta bort Azure IoT CA TestOnly certifikat](./media/iot-hub-security-x509-create-certificates/cleanup.png)