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
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: knithinc
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 31025582516198bdfe9da9312bae33852986a423
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884864"
---
# <a name="connect-to-the-asdk"></a>Ansluta till ASDK

För att hantera resurser, måste du först ansluta till Azure Stack Development Kit (ASDK). I den här artikeln beskrivs de steg som du utför för att ansluta till ASDK med hjälp av följande alternativ för anslutning:

* [Anslutning till fjärrskrivbord (RDP)](#connect-with-rdp). När du ansluter med hjälp av anslutning till fjärrskrivbord kan snabbt ansluta till en enskild användare i development Kit.
* [Virtuellt privat nätverk (VPN)](#connect-with-vpn). När du ansluter med hjälp av en VPN-anslutning kan kan flera användare samtidigt ansluta till Azure Stack-portaler från klienter utanför Azure Stack-infrastruktur. En VPN-anslutning kräver viss konfigurering.

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>Ansluta till Azure Stack med RDP

En enskild samtidiga användare kan hantera resurser i Azure Stack-administrationsportalen eller användarportalen via anslutning till fjärrskrivbord direkt från värddatorn ASDK. 

> [!TIP]
> Det här alternativet kan du använda RDP igen när du loggat in på värddatorn ASDK för att logga in på virtuella datorer som skapats på värddatorn ASDK. 

1. Öppna anslutning till fjärrskrivbord (mstc.exe) och Anslut till development kit värdens datorn IP-adress med ett konto som har behörighet att logga in via en fjärranslutning till värddatorn ASDK. Som standard **AzureStack\AzureStackAdmin** har behörigheter för fjärråtkomst i till värddatorn ASDK.  

2. Öppna Serverhanteraren (ServerManager.exe) på värddatorn för development kit. Välj **lokal Server**, inaktivera **Förbättrad säkerhetskonfiguration**, och Stäng Serverhanteraren.

3. Logga in på administrationsportalen som **AzureStack\CloudAdmin** eller använda andra autentiseringsuppgifter för Azure Stack-operatör. ASDK administration Portaladress är [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

4. Logga in på användarportalen som **AzureStack\CloudAdmin** eller använda andra autentiseringsuppgifter för Azure Stack-användare. ASDK användaren Portaladress är [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

> [!NOTE]
> Mer information om när du ska använda vilket konto som finns i [grundläggande om administration av ASDK](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>Ansluta till Azure Stack med hjälp av VPN

Du kan skapa en delad tunnel VPN-anslutning till en värddator för ASDK för att komma åt Azure Stack-portaler och lokalt installerade verktyg som Visual Studio och PowerShell. Med VPN-anslutningar kan ansluta flera användare samtidigt till Azure Stack-resurser som finns i ASDK.

VPN-anslutningar stöds för både Azure AD och Active Directory Federation Services (AD FS)-distributioner.

> [!NOTE]
> En VPN-anslutning *inte* tillhandahålla anslutningar till virtuella datorer i Azure Stack. Du kommer inte att kunna använda RDP med Azure Stack virtuella datorer när du är ansluten via VPN.

### <a name="prerequisites"></a>Förutsättningar
Kontrollera att du uppfyller följande krav innan du konfigurerar en VPN-anslutning till ASDK.

- Installera [Azure Stack-kompatibla Azure PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) på den lokala datorn.  
- Ladda ned den [verktyg som krävs för att arbeta med Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools).

### <a name="set-up-vpn-connectivity"></a>Konfigurera VPN-anslutning

Öppna PowerShell som administratör på den lokala Windows-baserade datorn för att skapa en VPN-anslutning till ASDK. Kör följande skript (uppdatera IP-adress och lösenord som värden för din miljö):

```powershell
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

![Nätverksanslutningar](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>Anslut till Azure Stack

  Anslut till Azure Stack-instans med hjälp av något av följande metoder:  

  * Använd den `Connect-AzsVpn` kommando:
      
    ```powershell
    Connect-AzsVpn `
      -Password $Password
    ```

  * På den lokala datorn, Välj **nätverksinställningar** > **VPN** > **azurestack** > **ansluta**. Ange användarnamnet i Kommandotolken inloggning (**AzureStack\AzureStackAdmin**) och lösenordet.

Första gången du ansluter, uppmanas du att installera Azure Stack-rotcertifikat från **AzureStackCertificateAuthority** i den lokala datorns certifikatarkiv. Det här steget lägger till ASDK certifikatutfärdaren (CA) i listan över betrodda värdar. Klicka på **Ja** att installera certifikatet.

![Rotcertifikat](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > Meddelandet kan vara dolt av PowerShell-fönstret eller andra program.

### <a name="test-vpn-connectivity"></a>Testa VPN-anslutningen

Om du vill testa portal anslutningen, öppna en webbläsare och gå sedan till antingen användarportalen (https://portal.local.azurestack.external/) eller administrationsportalen (https://adminportal.local.azurestack.external/). 

Logga in med autentiseringsuppgifter för lämplig prenumeration att skapa och hantera resurser.  

## <a name="next-steps"></a>Nästa steg

[Felsökning](asdk-troubleshooting.md)
