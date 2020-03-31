---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e85dc8c079205484db9b7b7c43a0086f69feb3be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059922"
---
## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Skapa ett självsignerat rotcertifikat

Använd cmdleten New-SelfSignedCertificate för att skapa ett självsignerat rotcertifikat. Mer information om parametrar finns i [Ny-SjälvsignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Öppna en Windows PowerShell-konsol med förhöjda privilegier från en dator med Windows 10 eller Windows Server 2016. Dessa exempel fungerar inte i Azure Cloud Shell "Prova det". Du måste köra dessa exempel lokalt.
2. Använd följande exempel för att skapa det självsignerade rotcertifikatet. I följande exempel skapas ett självsignerat rotcertifikat med namnet "P2SRootCert" som installeras automatiskt i "Certifikat-aktuell användare\Personal\Certifikat". Du kan visa certifikatet genom att öppna *certmgr.msc*eller *Hantera användarcertifikat*.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```
 3. Låt PowerShell-konsolen vara öppen om du vill skapa ett klientcertifikat direkt efter att du har skapat det här rotcertifikatet.

## <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Generera ett klientcertifikat

Varje klientdator som ansluter till ett virtuellt nätverk med punkt-till-plats måste ha ett klientcertifikat installerat. Du kan generera ett klientcertifikat från det självsignerade rotcertifikatet och sedan exportera och installera klientcertifikatet. Om klientcertifikatet inte är installerat misslyckas autentiseringen. 

I följande steg går du igenom att generera ett klientcertifikat från ett självsignerat rotcertifikat. Du kan generera flera klientcertifikat från samma rotcertifikat. När du genererar klientcertifikat med hjälp av stegen nedan installeras klientcertifikatet automatiskt på den dator som du använde för att generera certifikatet. Om du vill installera ett klientcertifikat på en annan klientdator kan du exportera certifikatet.

Exemplen använder cmdleten New-SelfSignedCertificate för att generera ett klientcertifikat som upphör att gälla om ett år. Ytterligare parameterinformation, till exempel ange ett annat förfallodatumvärde för klientcertifikatet, finns i [Ny-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1---powershell-console-session-still-open"></a>Exempel 1 - PowerShell-konsolsession fortfarande öppen

Använd det här exemplet om du inte har stängt PowerShell-konsolen när du har skapat det självsignerade rotcertifikatet. Det här exemplet fortsätter från föregående avsnitt och använder variabeln deklarerad $cert. Om du stängde PowerShell-konsolen efter att ha skapat det självsignerade rotcertifikatet eller skapar ytterligare klientcertifikat i en ny PowerShell-konsolsession följer du stegen i [exempel 2](#ex2).

Ändra och kör exemplet för att generera ett klientcertifikat. Om du kör följande exempel utan att ändra det blir resultatet ett klientcertifikat med namnet "P2SChildCert".  Om du vill namnge det underordnade certifikatet något annat ändrar du CN-värdet. Ändra inte TextExtension när du kör det här exemplet. Klientcertifikatet som du genererar installeras automatiskt i Certifikat - Aktuell användare\Personliga\Certifikat på datorn.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="example-2---new-powershell-console-session"></a><a name="ex2"></a>Exempel 2 - Ny PowerShell-konsolsession

Om du skapar ytterligare klientcertifikat eller inte använder samma PowerShell-session som du använde för att skapa ditt självsignerade rotcertifikat gör du så här:

1. Identifiera det självsignerade rotcertifikat som är installerat på datorn. Den här cmdleten returnerar en lista över certifikat som är installerade på datorn.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```
2. Leta reda på ämnesnamnet från den returnerade listan och kopiera sedan tumavtrycket som finns bredvid det till en textfil. I följande exempel finns det två certifikat. CN-namnet är namnet på det självsignerade rotcertifikat som du vill generera ett underordnat certifikat från. I det här fallet "P2SRootCert".

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. Deklarera en variabel för rotcertifikatet med tumavtrycket från föregående steg. Ersätt TUMAVTRYCK med tumavtrycket för det rotcertifikat som du vill generera ett underordnat certifikat från.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Om du till exempel använder tumavtrycket för P2SRootCert i föregående steg ser variabeln ut så här:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. Ändra och kör exemplet för att generera ett klientcertifikat. Om du kör följande exempel utan att ändra det blir resultatet ett klientcertifikat med namnet "P2SChildCert". Om du vill namnge det underordnade certifikatet något annat ändrar du CN-värdet. Ändra inte TextExtension när du kör det här exemplet. Klientcertifikatet som du genererar installeras automatiskt i Certifikat - Aktuell användare\Personliga\Certifikat på datorn.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="export-the-root-certificate-public-key-cer"></a><a name="cer"></a>Exportera den offentliga nyckeln för rotcertifikat (.cer)

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Exportera det självsignerade rotcertifikatet och den privata nyckeln för att lagra det (valfritt)

Du kanske vill exportera det självsignerade rotcertifikatet och lagra det på ett säkert sätt som säkerhetskopia. Om det behövs kan du senare installera det på en annan dator och generera fler klientcertifikat. Om du vill exportera det självsignerade rotcertifikatet som en PFX markerar du rotcertifikatet och använder samma steg som beskrivs i [Exportera ett klientcertifikat](#clientexport).

## <a name="export-the-client-certificate"></a><a name="clientexport"></a>Exportera klientcertifikat

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]
