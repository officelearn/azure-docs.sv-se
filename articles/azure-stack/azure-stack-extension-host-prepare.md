---
title: Förbereda för tillägget för värd för Azure Stack | Microsoft Docs
description: Lär dig att förbereda för tillägget värden, vilket aktiveras automatiskt via ett framtida uppdateringspaket för Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 02/07/2019
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: b0d3b3e4901fbcece13c201938be8bccb1bb9c82
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962374"
---
# <a name="prepare-for-extension-host-for-azure-stack"></a>Förbereda för tillägget för värd för Azure Stack

Tillägget värden skyddar Azure Stack genom att minska antalet TCP/IP-portar som krävs. Den här artikeln tittar på förbereder Azure Stack för tillägg som aktiveras automatiskt via ett Azure Stack uppdatera paket efter 1808 uppdateringen. Den här artikeln gäller för Azure Stack-uppdateringar 1808 1809 och 1811.

## <a name="certificate-requirements"></a>Certifikatkrav

Värden för tillägget implementerar två nya domäner namnområden för att garantera unika värdposter för varje portaltillägg. De nya domänen namnområden kräver två ytterligare-jokertecken-certifikat för att garantera en säker kommunikation.

Tabellen visar nya namnområden och de associera certifikat:

| Distributionsmappen | Nödvändiga certifikatämnet och Alternativt ämnesnamn (SAN) | Omfång (per region) | SubDomain namespace |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Admin-tillägg-värd | *.adminhosting. \<region >. \<fqdn > (SSL-certifikat med jokertecken) | Admin-tillägg-värd | adminhosting.\<region>.\<fqdn> |
| Offentliga tillägget värd | * .hosting. \<region >. \<fqdn > (SSL-certifikat med jokertecken) | Offentliga tillägget värd | hosting.\<region>.\<fqdn> |

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

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ```

5. Placera ditt certifikat i lämplig kataloger.

6. Kontrollera utdata och certifikat klara alla tester.


## <a name="import-extension-host-certificates"></a>Importera certifikat för tillägget för värd

Använda en dator som kan ansluta till Azure Stack privilegierad slutpunkt för nästa steg. Kontrollera att du har åtkomst till de nya certifikatfilerna från datorn.

1. Använda en dator som kan ansluta till Azure Stack privilegierad slutpunkt för nästa steg. Kontrollera att du får tillgång till nya certifikatfiler från datorn.
2. Öppna PowerShell ISE för att köra nästa skriptblock
3. Importera certifikatet för administratören som är värd för slutpunkten.

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
4. Importera certifikat för värdbaserade slutpunkten.
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

| IP-adress | Värdnamn | Type |
|----|------------------------------|------|
| \<IP> | *.Adminhosting.\<Region>.\<FQDN> | A |
| \<IP> | *.Hosting.\<Region>.\<FQDN> | A |

Allokerade IP-adresser kan hämtas med hjälp av privilegierad slutpunkt genom att köra cmdlet **Get-AzureStackStampInformation**.

### <a name="ports-and-protocols"></a>Portar och protokoll

I artikeln [datacenter-integrering med Azure Stack - publicera slutpunkter](azure-stack-integrate-endpoints.md), beskriver de portar och protokoll som kräver inkommande kommunikation att publicera Azure Stack före tillägget värd-distribution.

### <a name="publish-new-endpoints"></a>Publicera nya slutpunkter

Det finns två nya slutpunkter som krävs för att publiceras via brandväggen. Den allokerade IP-adresser från den offentliga VIP-poolen kan hämtas med följande kod som måste köras via Azure Stack [miljö är privilegierad slutpunkt](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).

```PowerShell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Obtain DNS Servers and Extension Host information from Azure Stack Stamp Information and find the IPs for the Host Extension Endpoints
$StampInformation = Invoke-Command $PEPSession {Get-AzureStackStampInformation} | Select-Object -Property ExternalDNSIPAddress01, ExternalDNSIPAddress02, @{n="TenantHosting";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://*.","testdnsentry"-replace "/"}},  @{n="AdminHosting";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://*.","testdnsentry"-replace "/"}},@{n="TenantHostingDNS";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://",""-replace "/"}},  @{n="AdminHostingDNS";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://",""-replace "/"}}
If (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting -ErrorAction SilentlyContinue) {
    Write-Host "Can access AZS DNS" -ForegroundColor Green
    $AdminIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress02 -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Else {
    Write-Host "Cannot access AZS DNS" -ForegroundColor Yellow
    $AdminIP = (Resolve-DnsName -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Remove-PSSession -Session $PEPSession
```

#### <a name="sample-output"></a>Exempel på utdata

```PowerShell
Can access AZS DNS
The IP for the Admin Extension Host is: *.adminhosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.adminhosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
The IP address for the Tenant Extension Host is *.hosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.hosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
```

> [!Note]  
> Se den här ändringen innan du aktiverar tillägget värden. På så sätt kan Azure Stack-portaler för att komma åt kontinuerligt.

| Slutpunkt (VIP) | Protokoll | Portar |
|----------------|----------|-------|
| Admin Hosting | HTTPS | 443 |
| Värd | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>Uppdatera befintliga publiceringsregler (Post-aktivering av tillägget värd)

> [!Note]  
> 1808 uppdateringspaket för Azure Stack har **inte** aktivera tillägget värd ännu. Det gör för att förbereda för tillägget värden genom att importera certifikat som krävs. Stäng inte alla portar innan tillägget värden aktiveras automatiskt via ett Azure Stack uppdatera paket efter 1808 uppdateringen.

Följande befintliga slutpunktsportar måste stängas i din befintliga brandväggsregler.

> [!Note]  
> Vi rekommenderar att stänga portarna efter lyckad validering.

| Slutpunkt (VIP) | Protokoll | Portar |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| Portal (administratör) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015 |
| Portal (användare) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003 |
| Azure Resource Manager (administratör) | HTTPS | 30024 |
| Azure Resource Manager (user) | HTTPS | 30024 |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [integrering i brandväggen](azure-stack-firewall.md).
- Lär dig mer om [signering begäran generation av Azure Stack-certifikat](azure-stack-get-pki-certs.md)
