---
title: Hantera lokala fjärrresurser med hjälp av PowerShell-funktioner
description: Lär dig hur du konfigurerar hybridanslutningar i Azure Relay för att ansluta en PowerShell-funktionsapp till lokala resurser, som sedan kan användas för att fjärrhantera den lokala resursen.
author: eamono
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 36fc4c873dccfe9fa814bddccd829ed04207f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226941"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Hantera hybridmiljöer med PowerShell i Azure Functions och App Service Hybrid-anslutningar

Azure App Service Hybrid Connections-funktionen möjliggör åtkomst till resurser i andra nätverk. Du kan läsa mer om den här funktionen i [hybridanslutningsdokumentationen.](../app-service/app-service-hybrid-connections.md) I den här artikeln beskrivs hur du använder den här funktionen för att köra PowerShell-funktioner som riktar sig till en lokal server. Den här servern kan sedan användas för att hantera alla resurser i den lokala miljön från en Azure PowerShell-funktion.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Konfigurera en lokal server för PowerShell-ommotning

Följande skript aktiverar PowerShell-ommotning och skapar en ny brandväggsregel och en WinRM https-lyssnare. För testning används ett självsignerat certifikat. I en produktionsmiljö rekommenderar vi att du använder ett signerat certifikat.

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>Skapa en PowerShell-funktionsapp i portalen

Funktionen Hybridanslutningar för App Service är endast tillgänglig i grundläggande, standard och isolerade prisplaner. När du skapar funktionsappen med PowerShell skapar eller väljer du ett av dessa abonnemang.

1. Välj + Skapa **en resurs** på menyn till vänster i [Azure-portalen](https://portal.azure.com)och välj sedan **Funktionsapp**.

1. För **värdplan**väljer du **App Service-plan**och väljer sedan **App Service-plan/plats**.

1. Välj **Skapa nytt**, skriv ett namn på **apptjänstplan,** välj en **plats** i en [region](https://azure.microsoft.com/regions/) nära dig eller i närheten av andra tjänster som dina funktioner har åtkomst till och välj sedan **prisnivå**.

1. Välj S1 Standard-planen och välj sedan **Använd**.

1. Välj **OK** för att skapa planen och konfigurera sedan de återstående **funktionsappinställningarna** enligt tabellen omedelbart efter följande skärmbild:

    ![PowerShell Core-funktionsapp](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appnamn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z`, `0-9` och `-`.  | 
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. |
    | **Resursgrupp** |  myResourceGroup | Namnet på den nya resursgrupp där du vill skapa funktionsappen. Du kan också använda det föreslagna värdet. |
    | **Operativsystem** | Önskat operativsystem | Välj Windows. |
    | **Körningsstack** | Önskat språk | Välj PowerShell-kärna. |
    | **Lagring** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Namn på lagringskonto måste vara mellan 3 och 24 tecken långa och kan endast innehålla siffror och gemener. Du kan också använda ett befintligt konto.
    | **Application Insights** | Default | Skapar en Application Insights-resurs med samma *appnamn* i närmaste region som stöds. Genom att expandera den här inställningen kan du ändra namnet på den **nya resursen** eller välja en annan **plats** i en [Azure-geografiregion](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill lagra dina data. |

1. När inställningarna har validerats väljer du **Skapa**.

1. Välj **ikonen Meddelande** i det övre högra hörnet av portalen och vänta på meddelandet "Distribution lyckades".

1. Välj **Gå till resurs** att visa den nya funktionsappen. Du kan också välja **Fäst på instrumentpanelen**. Om du fäster kan du återgå till den här funktionsappresursen från instrumentpanelen.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Skapa en hybridanslutning för funktionsappen

Hybridanslutningar konfigureras från nätverksavsnittet i funktionsappen:

1. Välj fliken **Plattformsfunktioner** i funktionsappen och välj sedan **Nätverk**. 
   ![Appöversikt för plattformsnätverk](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. Välj **Konfigurera slutpunkterna för hybridanslutningar**.
   ![Nätverk](./media/functions-hybrid-powershell/select-network-feature.png)  
1. Välj **Lägg till hybridanslutning**.
   ![Hybridanslutning](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Ange information om hybridanslutningen som visas direkt efter följande skärmbild. Du kan välja att göra inställningen **slutpunktsvärd** för att matcha värdnamnet för den lokala servern så att det blir lättare att komma ihåg servern senare när du kör fjärrkommandon. Porten matchar standardporten för fjärrhanteringstjänst för Windows som definierades på servern tidigare.
  ![Lägg till hybridanslutning](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Hybridanslutningsnamn**: ContosoHybridOnPremisesServer
    
    **Slutpunktsvärd**: finance1
    
    **Slutpunktsport:** 5986
    
    **Servicebus namnområde:** Skapa nytt
    
    **Plats**: Välj en tillgänglig plats
    
    **Namn**: contosopowershellhybrid

5. Välj **OK** om du vill skapa hybridanslutningen.

## <a name="download-and-install-the-hybrid-connection"></a>Hämta och installera hybridanslutningen

1. Välj **Hämta anslutningshanteraren** om du vill spara MSI-filen lokalt på datorn.
![Hämta installationsprogrammet](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Kopiera MSI-filen från den lokala datorn till den lokala servern.
1. Kör installationsprogrammet för Hybrid-anslutningshanteraren för att installera tjänsten på den lokala servern.
![Installera hybridanslutning](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. Öppna hybridanslutningen från portalen och kopiera sedan gatewayanslutningssträngen till Urklipp.
![Kopiera hybridanslutningssträng](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Öppna användargränssnittet för Hybrid-anslutningshanteraren på den lokala servern.
![Öppna användargränssnittet för hybridanslutning](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Välj knappen **Ange manuellt** och klistra in anslutningssträngen från Urklipp.
![Klistra in anslutning](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Starta om Hybrid Connection Manager från PowerShell om det inte visas som anslutet.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Skapa en appinställning för lösenordet för ett administratörskonto

1. Välj fliken **Plattformsfunktioner** i funktionsappen.
1. Under **Allmänna inställningar**väljer du **Konfiguration**.
![Välj plattformskonfiguration](./media/functions-hybrid-powershell/select-configuration.png)  
1. Expandera **Ny programinställning** för att skapa en ny inställning för lösenordet.
1. Namnge inställningen _ContosoUserPassword_och ange lösenordet.
1. Välj **OK** och spara sedan för att lagra lösenordet i funktionsprogrammet.
![Lägga till appinställning för lösenord](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Skapa en funktion http-utlösare för att testa

1. Skapa en ny HTTP-utlösarfunktion från funktionsappen.
![Skapa ny HTTP-utlösare](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Ersätt PowerShell-koden från mallen med följande kod:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

3. Välj **Spara** och **kör** för att testa funktionen.
![Testa funktionsappen](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>Hantera andra system lokalt

Du kan använda den anslutna lokala servern för att ansluta till andra servrar och hanteringssystem i den lokala miljön. På så sätt kan du hantera dina datacenteråtgärder från Azure med hjälp av dina PowerShell-funktioner. Följande skript registrerar en PowerShell-konfigurationssession som körs under de angivna autentiseringsuppgifterna. Dessa autentiseringsuppgifter måste vara för en administratör på fjärrservrarna. Du kan sedan använda den här konfigurationen för att komma åt andra slutpunkter på den lokala servern eller datacentret.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

Ersätt följande variabler i det här skriptet med tillämpliga värden från din miljö:
* $HybridEndpoint
* $RemoteServer

I de två föregående scenarierna kan du ansluta och hantera lokala miljöer med hjälp av PowerShell i Azure-funktioner och hybridanslutningar. Vi rekommenderar att du lär dig mer om [hybridanslutningar](../app-service/app-service-hybrid-connections.md) och [PowerShell i funktioner](./functions-reference-powershell.md).

Du kan också använda [virtuella Azure-nätverk](./functions-create-vnet.md) för att ansluta till din lokala miljö via Azure Functions.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Läs mer om hur du arbetar med PowerShell-funktioner](functions-reference-powershell.md)
