---
title: 'Skapa och exportera certifikat för punkt-till-plats: PowerShell: Azure | Microsoft Docs'
description: Skapa ett självsignerat rotcertifikat, exportera den offentliga nyckeln och generera klientcertifikat med hjälp av PowerShell på Windows 10 eller Windows Server 2016.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: 74639dee6fb548e1c9067cae6fc22f6e3cc872c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764024"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Skapa och exportera certifikat för punkt-till-plats med hjälp av PowerShell

Punkt-till-plats-anslutningar använder certifikat för autentisering. Den här artikeln visar hur du skapar ett självsignerat rotcertifikat och generera klientcertifikat med hjälp av PowerShell på Windows 10 eller Windows Server 2016. Om du letar efter olika certifikat anvisningar Se [certifikat – Linux](vpn-gateway-certificates-point-to-site-linux.md) eller [certifikat - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Du måste utföra stegen i den här artikeln på en dator som kör Windows 10 eller Windows Server 2016. PowerShell-cmdlets som används för att generera certifikat är en del av operativsystemet och fungerar inte på andra versioner av Windows. Windows 10 eller Windows Server 2016-dator krävs endast för att generera certifikat. När certifikat som har genererats kan du överföra dem eller installera dem på alla operativsystem stöds för klientdatorer. 

Om du inte har tillgång till en Windows 10 eller Windows Server 2016-dator kan du använda [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) att generera certifikat. De certifikat som du genererar med hjälp av någon av metoderna kan installeras på någon [stöds](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) klientoperativsystem.

## <a name="rootcert"></a>1. Skapa ett självsignerat rotcertifikat

Använd cmdleten New-SelfSignedCertificate för att skapa ett självsignerat rotcertifikat. För ytterligare parameterinformation finns i [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Öppna en Windows PowerShell-konsol med utökade privilegier från en dator som kör Windows 10 eller Windows Server 2016. De här exemplen fungerar inte i Azure Cloud Shell ”prova”. Du måste köra dessa exempel lokalt.
2. Använd följande exempel för att skapa det självsignerade rotcertifikatet. I följande exempel skapas ett självsignerat rotcertifikat med namnet ”P2SRootCert” som installeras automatiskt i ”certifikat – aktuell användare\personligt\certifikat”. Du kan visa certifikatet genom att öppna *certmgr.msc*, eller *Hantera användarcertifikat*.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

## <a name="clientcert"></a>2. Generera ett klientcertifikat

Varje klientdator som ansluter till ett virtuellt nätverk med punkt-till-plats måste ha ett klientcertifikat installerat. Du skapar ett klientcertifikat från det självsignerade rotcertifikatet och sedan exportera och installera klientcertifikatet. Om klientcertifikatet inte är installerat, misslyckas autentiseringen. 

I följande steg vägleder dig genom att generera ett klientcertifikat från ett självsignerat rotcertifikat. Du kan skapa flera klientcertifikat från samma rotcertifikat. När du genererar klientcertifikat med nedanstående steg behöver installeras automatiskt klientcertifikatet på den dator som du använde för att generera certifikatet. Om du vill installera ett klientcertifikat på en annan klientdator kan du exportera certifikatet.

I exemplen används cmdleten New-SelfSignedCertificate för att generera ett klientcertifikat som upphör att gälla i ett år. Ytterligare parameterinformation, till exempel att ange ett värde för olika upphör att gälla för klientcertifikat, se [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Exempel 1

Använd det här exemplet om du inte har stängt PowerShell-konsolen när du har skapat det självsignerade rotcertifikatet. Det här exemplet fortsätter från föregående avsnitt och använder variabeln deklarerade '$cert'. Om du har stängt PowerShell-konsolen när du har skapat det självsignerade rotcertifikatet eller skapar ytterligare certifikat i en ny PowerShell-konsolsession, Följ stegen i [exempel 2](#ex2).

Ändra och köra exemplet för att generera ett klientcertifikat. Om du kör följande exempel utan att ändra den är resultatet ett klientcertifikat med namnet ”P2SChildCert”.  Om du vill namnge certifikatet underordnade något annat ändra CN-värde. Ändra inte TextExtension när du kör det här exemplet. Klientcertifikatet som du genererar installeras automatiskt under ”certifikat – aktuell användare\personligt\certifikat” på datorn.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Exempel 2

Om du skapar ytterligare klientcertifikat eller inte använder samma PowerShell-session som användes för att skapa ett självsignerat rotcertifikat certifikat, använder du följande steg:

1. Identifiera det självsignerade rotcertifikat som är installerad på datorn. Denna cmdlet returnerar en lista över certifikat som är installerade på datorn.

   ```powershell
   Get-ChildItem -Path “Cert:\CurrentUser\My”
   ```
2. Leta upp ämnesnamnet från den returnerade listan och kopiera tumavtrycket som finns bredvid den till en textfil. I följande exempel finns två certifikat. CN-namn är namnet på det självsignerade rotcertifikatet som du vill att generera ett underordnat certifikat. I det här fallet ”P2SRootCert”.

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. Deklarera en variabel för rot-certifikatet med tumavtrycket från föregående steg. Ersätt TUMAVTRYCK med tumavtrycket för rotcertifikatet som du vill att generera ett underordnat certifikat.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Till exempel använder tumavtrycket för P2SRootCert i föregående steg, variabeln ser ut så här:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. Ändra och köra exemplet för att generera ett klientcertifikat. Om du kör följande exempel utan att ändra den är resultatet ett klientcertifikat med namnet ”P2SChildCert”. Om du vill namnge certifikatet underordnade något annat ändra CN-värde. Ändra inte TextExtension när du kör det här exemplet. Klientcertifikatet som du genererar installeras automatiskt under ”certifikat – aktuell användare\personligt\certifikat” på datorn.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="cer"></a>3. Exportera rot certifikatets offentliga nyckel (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]


### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Exportera självsignerade rotcertifikat och lagra den (valfritt) den privata nyckeln

Kan du exportera det självsignerade rotcertifikatet och lagra den på ett säkert sätt som säkerhetskopiering. Om behöver vara du senare kan installera den på en annan dator och generera mer klientcertifikat. Välj rotcertifikat för att exportera det självsignerade rotcertifikatet som PFX-fil, och använda samma steg som beskrivs i [exportera ett klientcertifikat](#clientexport).

## <a name="clientexport"></a>4. Exportera klientcertifikat

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]


## <a name="install"></a>5. Installera ett exporterat klientcertifikat

Alla klienter som ansluter till det virtuella nätverket via en P2S-anslutning kräver ett klientcertifikat installeras lokalt.

Om du vill installera ett klientcertifikat, se [installera ett klientcertifikat för punkt-till-plats-anslutningar](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="install"></a>6. Fortsätt med steg för P2S-konfiguration

Fortsätt med punkt-till-plats-konfiguration.

* För **Resource Manager** modell distributionsstegen, se [konfigurera P2S med hjälp av Azures interna certifikatautentisering](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* För **klassiska** modell distributionsstegen, se [konfigurera en punkt-till-plats-VPN-anslutning till ett virtuellt nätverk (klassisk)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).
* P2S felsökningsinformation finns i [punkt-till-plats-anslutningar i felsökning av Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
