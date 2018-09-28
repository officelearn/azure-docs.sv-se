---
title: Förbereda för tillägget för värd för Azure Stack | Microsoft Docs
description: Lär dig att förbereda för tillägget värden, vilket aktiveras automatiskt via ett framtida uppdateringspaket för Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 09/26/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.openlocfilehash: 3e35b0d9ba697b54b0fb85096caceeaa024b1f3d
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405248"
---
# <a name="prepare-for-extension-host-for-azure-stack"></a>Förbereda för tillägget för värd för Azure Stack

Tillägget värden skyddar Azure Stack genom att minska antalet TCP/IP-portar som krävs. Den här artikeln tittar på förbereder Azure Stack för tillägg som aktiveras automatiskt via ett Azure Stack uppdatera paket efter 1808 uppdateringen.

## <a name="certificate-requirements"></a>Certifikatkrav

Värden för tillägget implementerar två nya domäner namnområden för att garantera unika värdposter för varje portaltillägg. De nya domänen namnområden kräver två ytterligare-jokertecken-certifikat för att garantera en säker kommunikation.

Tabellen visar nya namnområden och de associera certifikat:

| Distributionsmappen | Nödvändiga certifikatämnet och Alternativt ämnesnamn (SAN) | Omfång (per region) | Underdomän namnområde |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Admin-tillägg-värd | *.adminhosting. \<region >. \<fqdn > (SSL-certifikat med jokertecken) | Admin-tillägg-värd | adminhosting. \<region >. \<fqdn > |
| Offentliga tillägget värd | * .hosting. \<region >. \<fqdn > (SSL-certifikat med jokertecken) | Offentliga tillägget värd | som är värd för. \<region >. \<fqdn > |

Detaljerad certifikatkraven finns i den [certifikatkrav för Azure Stack-infrastruktur för offentliga nycklar](azure-stack-pki-certs.md) artikeln.

## <a name="create-certificate-signing-request"></a>Skapa förfrågan om Certifikatsignering

Azure Stack-beredskap för installation-verktyget erbjuder möjligheten att skapa en certifikatsigneringsförfrågan för de två nya, krävs SSL-certifikat. Följ stegen i artikeln [Azure Stack certifikat signering begäran generation](azure-stack-get-pki-certs.md).

> [!Note]  
> Du kan hoppa över det här steget beroende på hur du har begärt SSL-certifikat.

## <a name="validate-new-certificates"></a>Verifiera nya certifikat

1. Öppna PowerShell med förhöjd behörighet på maskinvara livscykel värd eller arbetsstationen för hantering av Azure Stack.
2. Kör följande cmdlet för att installera verktyget Azure Stack-beredskap för installation.

    ```PowerShell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. Kör följande skript för att skapa nödvändiga mappstrukturen:

    ```PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > Om du distribuerar med Azure Active Directory Federation Services (AD FS) till följande kataloger måste läggas till **$directories** i skriptet: `ADFS`, `Graph`.

4. Kör följande cmdlets för att starta kontrollen certifikat:

    ```PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD -ExtensionHostFeature
    ```

5. Placera ditt certifikat i lämplig kataloger.

6. Kontrollera utdata och certifikat klara alla tester.


## <a name="import-extension-host-certificates"></a>Importera certifikat för tillägget för värd

Använda en dator som kan ansluta till Azure Stack privilegierad slutpunkt för nästa steg. Kontrollera att du har åtkomst till de nya certifikatfilerna från datorn.

1. Använda en dator som kan ansluta till Azure Stack privilegierad slutpunkt för nästa steg. Kontrollera att du får tillgång till nya certifikatfiler från datorn.
2. Öppna PowerShell ISE för att köra nästa skriptblock
3. Importera certifikatet för som är värd för slutpunkten. Justera skriptet som matchar din miljö.
4. Importera certifikatet för administratören som är värd för slutpunkten.

    ```PowerShell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
5. Importera certifikat för värdbaserade slutpunkten.
    ```PowerShell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```



### <a name="update-dns-configuration"></a>Uppdatera DNS-konfiguration

> [!Note]  
> Det här steget krävs inte om du använde DNS-zondelegering för DNS-integrering.
Om enskilda värd A-poster har konfigurerats för att publicera Azure Stack-slutpunkter, måste du skapa två ytterligare värd A-poster:

| IP-adress | Värdnamn | Typ |
|----|------------------------------|------|
| \<IP &GT; | Adminhosting. <Region>.<FQDN> | A |
| \<IP &GT; | Som är värd för. <Region>.<FQDN> | A |

Allokerade IP-adresser kan hämtas med hjälp av privilegierad slutpunkt genom att köra cmdlet **Get-AzureStackStampInformation**.

### <a name="ports-and-protocols"></a>Portar och protokoll

I artikeln [datacenter-integrering med Azure Stack - publicera slutpunkter](azure-stack-integrate-endpoints.md), beskriver de portar och protokoll som kräver inkommande kommunikation att publicera Azure Stack före tillägget värd-distribution.

### <a name="publish-new-endpoints"></a>Publicera nya slutpunkter

Det finns två nya slutpunkter som krävs för att publiceras via brandväggen. Den allokerade IP-adresser från den offentliga VIP-poolen kan hämtas med hjälp av cmdleten **Get-AzureStackStampInformation**.

> [!Note]  
> Se den här ändringen innan du aktiverar tillägget värden. På så sätt kan Azure Stack-portaler för att komma åt kontinuerligt.

| Slutpunkt (VIP) | Protokoll | Portar |
|----------------|----------|-------|
| AdminHosting | HTTPS | 443 |
| Som är värd för | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>Uppdatera befintliga publiceringsregler (Post-aktivering av tillägget värd)

> [!Note]  
> 1808 uppdateringspaket för Azure Stack har **inte** aktivera tillägget värd ännu. Det gör för att förbereda för tillägget värden genom att importera certifikat som krävs. Stäng inte alla portar innan tillägget värden aktiveras automatiskt via ett Azure Stack uppdatera paket efter 1808 uppdateringen.

Följande befintliga slutpunktsportar måste stängas i din befintliga brandväggsregler.

> [!Note]  
> Vi rekommenderar att stänga portarna efter lyckad validering.

| Slutpunkt (VIP) | Protokoll | Portar |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| Portal (administratör) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015 |
| Portal (användare) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003 |
| Azure Resource Manager (administratör) | HTTPS | 30024 |
| Azure Resource Manager (användare) | HTTPS | 30024 |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [integrering i brandväggen](azure-stack-firewall.md).
- Lär dig mer om [signering begäran generation av Azure Stack-certifikat](azure-stack-get-pki-certs.md)
