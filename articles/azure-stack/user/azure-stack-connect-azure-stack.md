---
title: Anslut till Azure Stack | Microsoft Docs
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 4bc4387dd56de96edd17fcd8e5672cfda96cf67b
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816415"
---
# <a name="connect-to-azure-stack"></a>Anslut till Azure Stack

För att hantera resurser, måste du ansluta till Azure Stack Development Kit. Den här artikeln beskriver steg behövs för att ansluta till i development kit. Du kan använda någon av följande anslutningsalternativ för:

* Fjärrskrivbord: låter en enskild samtidiga användare Anslut snabbt från i development kit.
* Virtuellt privat nätverk (VPN): kan flera samtidiga användare ansluta från klienter utanför Azure Stack-infrastruktur (kräver konfiguration).

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Anslut till Azure Stack med fjärrskrivbord
En enskild samtidiga användare kan arbeta med portalen för att hantera resurser med en fjärrskrivbordsanslutning.

1. Öppna en anslutning till fjärrskrivbord och Anslut till i development kit. Ange **AzureStack\AzureStackAdmin** som användarnamnet och lösenordet för administratörer som du angav under installationen av Azure Stack.  

2. Från development kit dator, öppna Serverhanteraren, klickar du på **lokal Server**, inaktivera Förbättrad säkerhet i Internet Explorer och Stäng Serverhanteraren.

3. För att öppna portalen, navigera till (https://portal.local.azurestack.external/) och logga in med autentiseringsuppgifter för användare.


## <a name="connect-to-azure-stack-with-vpn"></a>Anslut till Azure Stack med VPN

Du kan skapa en delad tunnel virtuella privata nätverk (VPN)-anslutning till en Azure Stack Development Kit. Via VPN-anslutningen har åtkomst till administratörsportalen användarportalen, och lokalt installerad verktyg som Visual Studio och PowerShell för att hantera Azure Stack-resurser. VPN-anslutningar stöds i både Azure Active Directory och Active Directory Federation Services(AD FS) baserade distributioner. VPN-anslutningar kan flera klienter att ansluta till Azure Stack på samma gång. 

> [!NOTE] 
> Den här VPN-anslutningen tillhandahåller inte anslutningen till Azure Stack-infrastrukturens virtuella datorer. 

### <a name="prerequisites"></a>Förutsättningar

* Installera [kompatibla Azure PowerShell för Azure Stack](azure-stack-powershell-install.md) på den lokala datorn.  
* Ladda ned den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>Konfigurera VPN-anslutning

Öppna en upphöjd PowerShell-session från din lokala Windows-baserad dator för att skapa en VPN-anslutning i development Kit och kör följande skript (se till att uppdatera IP-adress och lösenord värden för din miljö):

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the IP address and password values for your environment. 

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

Om inställningarna lyckas, bör du se **azurestack** i din lista över VPN-anslutningar.

![Nätverksanslutningar](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Anslut till Azure Stack

Anslut till Azure Stack-instans med hjälp av något av följande två metoder:  

* Med hjälp av den `Connect-AzsVpn ` kommando: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  När du uppmanas, litar på Azure Stack-värden och installera certifikat från **AzureStackCertificateAuthority** till den lokala datorns certifikatarkiv. (uppmaningen visas bakom sessionsfönstret PowerShell). 

* Öppna den lokala datorn **nätverksinställningar** > **VPN** > klickar du på **azurestack** > **ansluta**. Ange användarnamnet (AzureStack\AzureStackAdmin) och lösenordet i inloggning-Kommandotolken.

### <a name="test-the-vpn-connectivity"></a>Testa VPN-anslutningen

Testa portal anslutningen genom att öppna en webbläsare och gå till användarportalen (https://portal.local.azurestack.external/), logga in och skapa resurser.  

## <a name="next-steps"></a>Nästa steg



