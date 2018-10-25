---
title: Anslut till Azure Stack | Microsoft Docs
description: Lär dig hur du ansluter till ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 55be312046f5cdea2c1481ed435b5859ab2c2540
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026909"
---
# <a name="connect-to-the-azure-stack-development-kit"></a>Ansluta till Azure Stack Development Kit

För att hantera resurser, måste du först ansluta till Azure Stack Development Kit (ASDK). I den här artikeln beskrivs de steg som du utför för att ansluta till ASDK. Du kan använda någon av följande anslutningsalternativ för:

* [Anslutning till fjärrskrivbord](#connect-with-remote-desktop). När du ansluter med hjälp av anslutning till fjärrskrivbord kan snabbt ansluta till en enskild användare i development Kit.
* [Virtuellt privat nätverk (VPN)](#connect-with-vpn). När du ansluter med hjälp av en VPN-anslutning kan ansluta flera användare samtidigt från klienter utanför Azure Stack-infrastruktur. En VPN-anslutning kräver viss konfigurering.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Ansluta till Azure Stack med anslutning till fjärrskrivbord

En enskild samtidiga användare kan hantera resurser i portalen operator eller användarportalen via anslutning till fjärrskrivbord.

1. Öppna anslutning till fjärrskrivbord (mstc.exe) och Anslut till värddatorn som development kit **AzureStack\AzureStackAdmin** med hjälp av lösenordet som angavs vid installationen av ASDK.  

2. Öppna Serverhanteraren (ServerManager.exe) på värddatorn för development kit. Välj **lokal Server**, inaktivera **Förbättrad säkerhetskonfiguration**, och Stäng Serverhanteraren.

3. Logga in på administrationsportalen som **AzureStack\CloudAdmin** eller använda andra autentiseringsuppgifter för Azure Stack-operatör. ASDK administration Portaladress är [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

4. Logga in på användarportalen som **AzureStack\CloudAdmin** eller använda andra autentiseringsuppgifter för Azure Stack-användare. ASDK användaren Portaladress är [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

> [!NOTE]
> Mer information om när du ska använda vilket konto som finns i [grundläggande om administration av ASDK](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Ansluta till Azure Stack via VPN

Du kan skapa en delad tunnel VPN-anslutning till en ASDK åtkomst till Azure Stack-portaler och lokalt installerade verktyg som Visual Studio och PowerShell. Med VPN-anslutningar kan ansluta flera användare samtidigt till Azure Stack-resurser som finns i ASDK.

VPN-anslutningar stöds för både Azure AD och Active Directory Federation Services (AD FS)-distributioner.

> [!NOTE]
> En VPN-anslutning tillhandahåller inte anslutningen till Azure Stack-infrastrukturens virtuella datorer.

### <a name="prerequisites"></a>Förutsättningar
Kontrollera att du uppfyller följande krav innan du konfigurerar en VPN-anslutning till ASDK.

- Installera [Azure Stack-kompatibla Azure PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) på den lokala datorn.  
- Ladda ned den [verktyg som krävs för att arbeta med Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools).

### <a name="set-up-vpn-connectivity"></a>Konfigurera VPN-anslutning

Öppna PowerShell som administratör på den lokala Windows-baserade datorn för att skapa en VPN-anslutning till ASDK. Kör följande skript (uppdatera IP-adress och lösenord som värden för din miljö):

```PowerShell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer’s IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Om installationen lyckas, **azurestack** visas i listan över VPN-anslutningar.

![Nätverksanslutningar](media/asdk-connect/image3.png)  

### <a name="connect-to-azure-stack"></a>Anslut till Azure Stack

Anslut till Azure Stack-instans med hjälp av något av följande metoder:  

* Använd den `Connect-AzsVpn ` kommando:
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  När du uppmanas, litar på Azure Stack-värden och installera certifikat från **AzureStackCertificateAuthority** i den lokala datorns certifikatarkiv. 
  
  > [!IMPORTANT]
  > Meddelandet kan vara dolt av PowerShell-fönstret.

* På den lokala datorn, Välj **nätverksinställningar** > **VPN** > **azurestack** > **ansluta**. Ange användarnamnet i Kommandotolken inloggning (**AzureStack\AzureStackAdmin**) och lösenordet.

### <a name="test-vpn-connectivity"></a>Testa VPN-anslutningen

Om du vill testa portal anslutningen, öppna en webbläsare och gå sedan till antingen användarportalen (https://portal.local.azurestack.external/) eller administrationsportalen (https://adminportal.local.azurestack.external/). Logga in och skapa resurser.  

## <a name="next-steps"></a>Nästa steg

[Felsökning](asdk-troubleshooting.md)
