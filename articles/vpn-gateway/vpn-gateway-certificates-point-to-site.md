---
title: 'Generera och exporterar certifikat för plats-till-plats: PowerShell: Azure | Microsoft Docs'
description: Skapa ett självsignerat rotcertifikat, exportera den offentliga nyckeln och generera klientcertifikat med hjälp av PowerShell på Windows 10 eller Windows Server 2016.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: cherylmc
ms.openlocfilehash: 385b6ed2e8104fd2e15e6e55d46dcd12b963ec6b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Generera och exporterar certifikat för plats-till-plats med hjälp av PowerShell

Punkt-till-plats-anslutningar använder certifikat för autentisering. Den här artikeln visar hur du skapar ett självsignerat rotcertifikat och generera klientcertifikat med hjälp av PowerShell på Windows 10 eller Windows Server 2016. Om du letar efter konfigurationssteg för punkt-till-plats, till exempel hur du överför rotcertifikat, väljer du en av artiklarna Configure punkt-till-platsen från listan nedan:

> [!div class="op_single_selector"]
> * [Skapa självsignerat certifikat - PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Skapa självsignerat certifikat - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Konfigurera punkt-till-plats - Resource Manager - Azure-portalen](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Konfigurera punkt-till-plats - Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Konfigurera punkt-till-plats - klassisk - Azure-portalen](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Du måste utföra stegen i den här artikeln på en dator som kör Windows 10 eller Windows Server 2016. PowerShell-cmdlets som används för att generera certifikat är en del av operativsystemet och fungerar inte på andra versioner av Windows. Windows 10 eller Windows Server 2016-dator krävs endast för att generera certifikat. När certifikat som genereras kan du överför dem. eller installera dem på alla operativsystem stöds för klientdatorer. 

Om du inte har tillgång till en Windows 10 eller Windows Server 2016-dator kan du använda [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) att generera certifikat. De certifikat som du skapar med någon av metoderna kan installeras på något [stöds](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) klientoperativsystem.

## <a name="rootcert"></a>1. Skapa ett självsignerat rotcertifikat

Använd cmdleten New-SelfSignedCertificate för att skapa ett självsignerat rotcertifikat. Ytterligare parameterinformation finns [ny SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Öppna en Windows PowerShell-konsol från en dator som kör Windows 10 eller Windows Server 2016 med förhöjd behörighet.
2. Använd följande exempel för att skapa självsignerat rotcertifikatet. I följande exempel skapas ett självsignerat rotcertifikat med namnet 'P2SRootCert' som installeras automatiskt i 'Certifikat-aktuell User\Personal\Certificates'. Du kan visa certifikatet genom att öppna *certmgr.msc*, eller *Hantera användarcertifikat*.

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

## <a name="clientcert"></a>2. Generera ett klientcertifikat

Varje klientdator som ansluter till ett virtuellt nätverk med punkt-till-plats måste ha ett klientcertifikat installerat. Du genererar ett klientcertifikat från självsignerade rotcertifikatet, och sedan exportera och installera klientcertifikatet. Om klientcertifikatet inte är installerad, misslyckas autentiseringen. 

Följande steg vägleder dig genom att generera ett certifikat från ett självsignerat rotcertifikat. Du kan skapa flera klientcertifikat från samma rotcertifikat. När du skapar klientcertifikat med nedanstående steg installeras automatiskt klientcertifikatet på den dator som du använde för att generera certifikatet. Om du vill installera ett klientcertifikat på en annan dator, kan du exportera certifikatet.

Exemplen använder cmdleten New-SelfSignedCertificate för att generera ett certifikat som upphör att gälla i ett år. Parametern för ytterligare information, till exempel inställningsvärde olika giltighetstid för klientcertifikat, se [ny SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Exempel 1

Det här exemplet använder variabeln deklarerade '$cert' från föregående avsnitt. Om du stängt PowerShell-konsolen när du har skapat det självsignerade rotcertifikatet eller skapar ytterligare certifikat i en ny PowerShell-konsolsession, Följ stegen i [exempel 2](#ex2).

Ändra och köra exemplet för att generera ett klientcertifikat. Om du kör följande exempel utan att ändra den är resultatet ett klientcertifikat med namnet 'P2SChildCert'.  Om du vill att namnge certifikatet underordnade något annat, ändra värdet för Nätverksnamnet. Ändra inte TextExtension när du kör det här exemplet. Det klientcertifikat som du genererar installeras automatiskt i ”certifikat - aktuell User\Personal\Certificates” på datorn.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Exempel 2

Om du skapar ytterligare klientcertifikat eller använder inte samma PowerShell-session som du använde för att skapa din självsignerade rotcertifikat, använder du följande steg:

1. Identifiera det självsignerade rotcertifikat som är installerad på datorn. Denna cmdlet returnerar en lista över certifikat som är installerade på datorn.

  ```powershell
  Get-ChildItem -Path “Cert:\CurrentUser\My”
  ```
2. Hitta ämnesnamnet från listan returneras och sedan kopiera tumavtrycket bredvid den till en textfil. I följande exempel finns två certifikat. CN-namn är namnet på självsignerat rotcertifikatet som du vill skapa ett underordnat certifikat. I det här fallet 'P2SRootCert'.

  ```
  Thumbprint                                Subject
  
  AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
  7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
  ```
3. Deklarera en variabel för rotcertifikatet använder certifikatets tumavtryck från föregående steg. Ersätt TUMAVTRYCKET med tumavtrycket för rotcertifikatet som du vill skapa ett underordnat certifikat.

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
  ```

  Till exempel använder certifikatets tumavtryck för P2SRootCert i föregående steg, variabeln ser ut så här:

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
  ```
4.  Ändra och köra exemplet för att generera ett klientcertifikat. Om du kör följande exempel utan att ändra den är resultatet ett klientcertifikat med namnet 'P2SChildCert'. Om du vill att namnge certifikatet underordnade något annat, ändra värdet för Nätverksnamnet. Ändra inte TextExtension när du kör det här exemplet. Det klientcertifikat som du genererar installeras automatiskt i ”certifikat - aktuell User\Personal\Certificates” på datorn.

  ```powershell
  New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="cer"></a>3. Exportera rot certifikatets offentliga nyckel (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]


### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Exportera självsignerade rotcertifikat och privat nyckel för att lagra den (valfritt)

Du kanske vill exportera självsignerade rotcertifikat och lagra den på ett säkert sätt som säkerhetskopiering. Om behövs bör du senare kan installera den på en annan dator och skapa mer certifiates för klienten. Om du vill exportera självsignerade rotcertifikat som en PFX-fil, Välj rotcertifikatet och använda samma steg som beskrivs i [exportera ett certifikat för](#clientexport).

## <a name="clientexport"></a>4. Exportera klientcertifikat

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]


## <a name="install"></a>5. Installera ett exporterat klientcertifikat

Varje klient som ansluter till VNet via en P2S-anslutning kräver ett klientcertifikat installeras lokalt.

Om du vill installera ett klientcertifikat [installera ett klientcertifikat för punkt-till-plats-anslutningar](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="install"></a>6. Fortsätt med steg för P2S-konfiguration

Vill du fortsätta med konfigurationen punkt-till-plats.

* För **Resource Manager** modell distributionsstegen, se [konfigurera P2S med hjälp av inbyggda Azure certifikatautentisering](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* För **klassiska** modell distributionsstegen, se [konfigurerar en punkt-till-plats VPN-anslutning till ett virtuellt nätverk (klassiska)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).
* P2S felsökningsinformation finns i [felsöka Azure punkt-till-plats-anslutningar](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
