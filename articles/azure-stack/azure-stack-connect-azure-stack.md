---
title: Anslut till Azure Stack | Microsoft Docs
description: Lär dig hur du ansluter till Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2018
ms.author: mabrigg
ms.openlocfilehash: 1cdf013325afe4b217f5f56043e06f60a4933419
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49993178"
---
# <a name="connect-to-azure-stack-development-kit"></a>Ansluta till Azure Stack Development Kit

*Gäller för: Azure Stack Development Kit*

För att hantera resurser, måste du först ansluta till Azure Stack Development Kit. I den här artikeln beskrivs de steg som du utför för att ansluta till i development kit. Du kan använda någon av följande anslutningsalternativ för:

* [Anslutning till fjärrskrivbord](#connect-with-remote-desktop). När du ansluter med hjälp av anslutning till fjärrskrivbord kan snabbt ansluta till en enskild användare i development Kit.
* [Virtuellt privat nätverk (VPN)](#connect-with-vpn). När du ansluter med hjälp av en VPN-anslutning kan ansluta flera användare samtidigt från klienter utanför Azure Stack-infrastruktur. En VPN-anslutning kräver viss konfigurering.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Ansluta till Azure Stack med anslutning till fjärrskrivbord

En enskild samtidiga användare kan hantera resurser i portalen operator eller användarportalen via anslutning till fjärrskrivbord.

1. Öppna anslutning till fjärrskrivbord och Anslut development Kit. Användarnamnet, ange **AzureStack\AzureStackAdmin**. Använd operatorn lösenordet som du angav när du ställer in Azure Stack.  

2. Öppna Serverhanteraren på utvecklingsdatorn kit. Välj **lokal Server**, avmarkera de **förbättrad säkerhet i Internet Explorer** kryssrutan och Stäng Serverhanteraren.

3. Öppna den [användarportalen](azure-stack-key-features.md#portal)går du till https://portal.local.azurestack.external/. Logga in med autentiseringsuppgifter för användare. Öppna Azure Stack [operatorn portal](azure-stack-key-features.md#portal)går du till https://adminportal.local.azurestack.external/. Logga in med hjälp av Azure Active Directory (Azure AD)-autentiseringsuppgifterna som du angav under installationen.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Ansluta till Azure Stack via VPN

Du kan upprätta en VPN-anslutning till en Azure Stack Development Kit för delade tunnlar. Du kan använda en VPN-anslutning för att få åtkomst till portalen för Azure Stack-operator, användarportalen och lokalt installerade verktyg som Visual Studio och PowerShell för att hantera Azure Stack-resurser. VPN-anslutning stöds i Azure AD och Active Directory Federation Services (AD FS)-distributioner. VPN-anslutningar som gör det möjligt för flera klienter att ansluta till Azure Stack på samma gång.

> [!NOTE]
> En VPN-anslutning tillhandahåller inte anslutningen till Azure Stack-infrastrukturens virtuella datorer.

### <a name="prerequisites"></a>Förutsättningar

1. Installera [Azure Stack-kompatibla Azure PowerShell](azure-stack-powershell-install.md) på den lokala datorn.  
2. Ladda ned den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md).

### <a name="set-up-vpn-connectivity"></a>Konfigurera VPN-anslutning

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

Anslut till Azure Stack-instans med hjälp av något av följande metoder:  

* Använd den `Connect-AzsVpn ` kommando:
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  När du uppmanas, litar på Azure Stack-värden och installera certifikat från **AzureStackCertificateAuthority** i den lokala datorns certifikatarkiv. (Det kan vara dolt prompten av PowerShell-fönstret.)

* På den lokala datorn, Välj **nätverksinställningar** > **VPN** > **azurestack** > **ansluta**. Ange användarnamnet i Kommandotolken inloggning (**AzureStack\AzureStackAdmin**) och lösenordet.

### <a name="test-vpn-connectivity"></a>Testa VPN-anslutningen

Om du vill testa portal anslutningen, öppna en webbläsare och gå sedan till antingen användarportalen (https://portal.local.azurestack.external/) eller operator-portal (https://adminportal.local.azurestack.external/). Logga in och skapa resurser.  

## <a name="next-steps"></a>Nästa steg

[Tillgängliggör virtuella datorer för Azure Stack-användare](azure-stack-tutorial-tenant-vm.md)
