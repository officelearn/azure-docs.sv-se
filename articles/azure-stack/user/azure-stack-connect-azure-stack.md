---
title: Ansluta till Azure-stacken | Microsoft Docs
description: Lär dig hur du ansluter Azure Stack
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
ms.date: 08/22/2017
ms.author: mabrigg
ms.openlocfilehash: 7479202a8afabf5a84560691a2bccf849206c077
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
ms.locfileid: "26636185"
---
# <a name="connect-to-azure-stack"></a>Anslut till Azure Stack

För att hantera resurser, måste du ansluta till Azure-stacken Development Kit. Det här artikeln beskriver steg som krävs för att ansluta development Kit. Du kan använda något av följande anslutningsalternativ för:

* [Fjärrskrivbord](#connect-with-remote-desktop): tillåter att en enskild samtidiga användare enkelt kan ansluta development Kit.
* [Virtuella privata nätverk (VPN)](#connect-with-vpn): tillåter att flera samtidiga användare ansluta från klienter utanför Azure Stack-infrastruktur (kräver konfiguration).

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Ansluta till Azure-stacken med fjärrskrivbord
En enskild samtidiga användare kan arbeta med portalen för att hantera resurser med en fjärrskrivbordsanslutning.

1. Öppna en anslutning till fjärrskrivbord och Anslut development Kit. Ange **AzureStack\AzureStackAdmin** som användarnamnet och lösenordet för administratörer som du angav under installationen av Azure-stacken.  

2. Från development kit datorn, öppna Serverhanteraren klickar du på **lokal Server**, inaktivera Förbättrad säkerhetskonfiguration i Internet Explorer och sedan stänger du Server Manager.

3. Navigera till (https://portal.local.azurestack.external/) om du vill öppna portalen och logga in med autentiseringsuppgifterna för användaren.


## <a name="connect-to-azure-stack-with-vpn"></a>Ansluta till Azure-stacken med VPN

Du kan upprätta en delad tunnel virtuella privata nätverk (VPN)-anslutning till en Azure-stacken Development Kit. Via VPN-anslutningen kan komma åt administratörsportal användarportalen, och verktyg som Visual Studio och PowerShell för att hantera Azure-stacken resurser har installerats lokalt. VPN-anslutningar stöds i både aktiva Directory(AAD) Azure och Active Directory Federation Services(AD FS) baserade distributioner. VPN-anslutningar kan flera klienter att ansluta till Azure-stacken på samma gång. 

> [!NOTE] 
> Den här VPN-anslutningen ger inte anslutningen till Azure-stacken infrastruktur virtuella datorer. 

### <a name="prerequisites"></a>Krav

* Installera [Azure Stack kompatibla Azure PowerShell](azure-stack-powershell-install.md) på den lokala datorn.  
* Hämta den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>Konfigurera VPN-anslutning

Öppna en upphöjd PowerShell-session från den lokala Windows-baserade datorn och kör följande skript (se till att uppdatera värdena för IP-adress och lösenord för din miljö) för att skapa en VPN-anslutning i development Kit:

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Om inställningen lyckas, bör du se **azurestack** i listan över VPN-anslutningar.

![Nätverksanslutningar](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Anslut till Azure Stack

Anslut till Azure Stack-instans med någon av följande två metoder:  

* Med hjälp av den `Connect-AzsVpn ` kommando: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  När du uppmanas, litar Azure Stack-värden och installera certifikatet från **AzureStackCertificateAuthority** till den lokala datorns certifikatarkiv. (uppmaningen visas bakom fönstret PowerShell-session). 

* Öppna den lokala datorn **nätverksinställningar** > **VPN** > klickar du på **azurestack** > **ansluta**. Ange användarnamnet (AzureStack\AzureStackAdmin) och lösenordet i Kommandotolken inloggning.

### <a name="test-the-vpn-connectivity"></a>Testa VPN-anslutningen

Om du vill testa anslutningen portal, öppna en webbläsare och gå till användarportalen (https://portal.local.azurestack.external/), logga in och skapa resurser.  

## <a name="next-steps"></a>Nästa steg



