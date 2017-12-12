---
title: Ansluta till Azure-stacken | Microsoft Docs
description: "Lär dig hur du ansluter till Azure-stacken."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: d9d7beae9ea81f2568377ee3593362871aae98fe
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="connect-to-azure-stack"></a>Anslut till Azure Stack

*Gäller för: Azure stacken Development Kit*

För att hantera resurser, måste du först ansluta till Azure-stacken Development Kit. I den här artikeln beskrivs de steg som du utför för att ansluta till development kit. Du kan använda något av följande anslutningsalternativ för:

* [Anslutning till fjärrskrivbord](#connect-with-remote-desktop). När du ansluter med hjälp av anslutning till fjärrskrivbord kan snabbt ansluta till en enskild användare från development kit.
* [Virtuellt privat nätverk (VPN)](#connect-with-vpn). När du ansluter via en VPN-anslutning ansluta flera användare samtidigt från klienter utanför Azure Stack-infrastruktur (kräver installationen).

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Ansluta till Azure-stacken genom att använda anslutning till fjärrskrivbord
En enskild samtidiga användare kan hantera resurser på den operator-portalen eller användarportalen via anslutning till fjärrskrivbord.

1. Öppna anslutning till fjärrskrivbord och Anslut development Kit. Användarnamnet, ange **AzureStack\AzureStackAdmin**. Använd operatorn lösenord som du angav när du ställer in Azure-stacken.  

2. Öppna Serverhanteraren på utvecklingsdatorn kit. Välj **lokal Server**avmarkerar den **Förbättrad säkerhetskonfiguration i Internet Explorer** kryssrutan och Stäng Server Manager.

3. Öppna den [användarportalen](azure-stack-key-features.md#portal), gå till https://portal.local.azurestack.external/. Logga in med autentiseringsuppgifterna för användaren. Öppna Azure-stacken [operatorn portal](azure-stack-key-features.md#portal), gå till https://adminportal.local.azurestack.external/. Logga in med hjälp av Azure Active Directory (Azure AD)-autentiseringsuppgifterna som du angav under installationen.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Ansluta till Azure-stacken med hjälp av VPN

Du kan upprätta en VPN-anslutning till en Azure-stacken Development Kit för delad tunnel. Du kan använda en VPN-anslutning för att få åtkomst till Azure-stacken operatorn portal, användarportalen och lokalt installerade verktyg som Visual Studio och PowerShell för att hantera Azure-stacken resurser. VPN-anslutningar stöds både i Azure AD och i Active Directory Federation Services (AD FS) distributioner. VPN-anslutningar gör det möjligt för flera klienter att ansluta till Azure-stacken på samma gång. 

> [!NOTE] 
> En VPN-anslutning innehåller inte anslutningen till Azure-stacken infrastruktur virtuella datorer. 

### <a name="prerequisites"></a>Krav

1. Installera [Azure Stack-kompatibla Azure PowerShell](azure-stack-powershell-install.md) på den lokala datorn.  
2. Hämta den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md). 

### <a name="set-up-vpn-connectivity"></a>Konfigurera VPN-anslutningar

Öppna Windows PowerShell som administratör på den lokala Windows-baserade datorn för att skapa en VPN-anslutning i development Kit. Kör följande skript (uppdatera IP-adress och lösenord som värden för din miljö):

```PowerShell 
# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address and certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment. 

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

![Nätverksanslutningar](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Anslut till Azure Stack

Anslut till Azure Stack-instans med någon av följande metoder:  

* Använd den `Connect-AzsVpn ` kommando: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  När du uppmanas, litar Azure Stack-värden och installera certifikatet från **AzureStackCertificateAuthority** i certifikatarkivet för den lokala datorn. (Frågan kanske döljs av PowerShell-fönstret.) 

* På den lokala datorn, väljer **nätverksinställningar** > **VPN** > **azurestack** > **ansluta**. Ange användarnamnet i Kommandotolken inloggning (**AzureStack\AzureStackAdmin**) och lösenordet.

### <a name="test-vpn-connectivity"></a>Testa en VPN-anslutning

Öppna en webbläsare för att testa anslutningen portal och går till användarportalen (https://portal.local.azurestack.external/) eller operatorn portal (https://adminportal.local.azurestack.external/). Logga in och skapa resurser.  

## <a name="next-steps"></a>Nästa steg

[Tillhandahålla virtuella datorer till Azure Stack-användare](azure-stack-tutorial-tenant-vm.md)

