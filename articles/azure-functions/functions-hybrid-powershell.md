---
title: Hantera lokala lokala resurser med hjälp av PowerShell-funktioner
description: Lär dig hur du konfigurerar Hybridanslutningar i Azure Relay för att ansluta en PowerShell Function-app till lokala resurser, som sedan kan användas för att fjärrhantera den lokala resursen.
author: eamono
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 36fc4c873dccfe9fa814bddccd829ed04207f095
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226941"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Hantera hybrid miljöer med PowerShell i Azure Functions och App Service Hybridanslutningar

Funktionen Azure App Service Hybridanslutningar ger åtkomst till resurser i andra nätverk. Du kan lära dig mer om den här funktionen i [hybridanslutningar](../app-service/app-service-hybrid-connections.md) -dokumentationen. Den här artikeln beskriver hur du använder den här funktionen för att köra PowerShell-funktioner som riktar sig mot en lokal server. Den här servern kan sedan användas för att hantera alla resurser i den lokala miljön från en Azure PowerShell-funktion.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Konfigurera en lokal server för PowerShell-fjärrkommunikation

Följande skript aktiverar PowerShell-fjärrkommunikation och skapar en ny brand Väggs regel och en WinRM https-lyssnare. I test syfte används ett självsignerat certifikat. I en produktions miljö rekommenderar vi att du använder ett signerat certifikat.

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

## <a name="create-a-powershell-function-app-in-the-portal"></a>Skapa en PowerShell Function-app i portalen

App Service Hybridanslutningar-funktionen är bara tillgänglig i de grundläggande, standard-och isolerade pris planerna. När du skapar Function-appen med PowerShell skapar eller väljer du någon av dessa planer.

1. I [Azure Portal](https://portal.azure.com)väljer du **+ skapa en resurs** i menyn till vänster och väljer sedan **Function app**.

1. För **värd plan**väljer du **App Service plan**och väljer sedan **App Service plan/plats**.

1. Välj **Skapa ny**, ange ett **App Service plan** namn, Välj en **plats** i en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som du har åtkomst till och välj sedan **pris nivå**.

1. Välj S1 standard-plan och välj sedan **Använd**.

1. Välj **OK** för att skapa planen och konfigurera sedan de återstående **Funktionsapp** inställningarna som anges i tabellen omedelbart efter följande skärm bild:

    ![PowerShell Core Function-app](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z`, `0-9` och `-`.  | 
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. |
    | **Resursgrupp** |  myResourceGroup | Namnet på den nya resursgrupp där du vill skapa funktionsappen. Du kan också använda det föreslagna värdet. |
    | **OS** | Önskat OS | Välj Windows. |
    | **Körningsstack** | Önskat språk | Välj PowerShell Core. |
    | **Storage** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Lagrings konto namn måste innehålla mellan 3 och 24 tecken och får bara innehålla siffror och gemena bokstäver. Du kan också använda ett befintligt konto.
    | **Application Insights** | Standardvärde | Skapar en Application Insights resurs av samma *app-namn* i den närmaste region som stöds. Genom att utöka den här inställningen kan du ändra det **nya resurs namnet** eller välja en annan **plats** i en region i [Azure](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill lagra dina data. |

1. När inställningarna har verifierats väljer du **skapa**.

1. Välj **meddelande** ikonen i det övre högra hörnet i portalen och vänta på meddelandet "distributionen lyckades".

1. Välj **Gå till resurs** att visa den nya funktionsappen. Du kan också välja **Fäst vid instrument panelen**. Genom att fästa blir det enklare att återgå till den här funktions program resursen från instrument panelen.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Skapa en hybrid anslutning för Function-appen

Hybrid anslutningar konfigureras från avsnittet nätverk i Function-appen:

1. Välj fliken **plattforms funktioner** i Function-appen och välj sedan **nätverk**. 
   Översikt över ![app för plattforms nätverk](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. Välj **Konfigurera dina hybrid anslutningar slut punkter**.
   ![Nätverk](./media/functions-hybrid-powershell/select-network-feature.png)  
1. Välj **Lägg till hybrid anslutning**.
   ![hybrid anslutning](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Ange information om hybrid anslutningen som visas direkt efter följande skärm bild. Du kan välja att göra **slut punkts värd** inställningen matcha värd namnet för den lokala servern för att göra det lättare att komma ihåg servern senare när du kör fjärrkommandon. Porten matchar standard porten för Windows Remote Management-tjänsten som definierades på servern tidigare.
  ![lägga till hybrid anslutning](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Hybrid anslutnings namn**: ContosoHybridOnPremisesServer
    
    **Slut punkts värd**: finance1
    
    **Slut punkts port**: 5986
    
    **Service Bus-namnrymd**: skapa ny
    
    **Plats**: Välj en tillgänglig plats
    
    **Namn**: contosopowershellhybrid

5. Välj **OK** för att skapa hybrid anslutningen.

## <a name="download-and-install-the-hybrid-connection"></a>Ladda ned och installera hybrid anslutningen

1. Välj **Hämta anslutnings hanteraren** för att spara. msi-filen lokalt på datorn.
![Hämta installations programmet](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Kopiera MSI-filen från den lokala datorn till den lokala servern.
1. Kör installations programmet för Hybridanslutningshanteraren för att installera tjänsten på den lokala servern.
![installera hybrid anslutning](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. Öppna hybrid anslutningen från portalen och kopiera sedan Gateway-anslutningssträngen till Urklipp.
![kopiera hybrid anslutnings sträng](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Öppna Hybridanslutningshanteraren gränssnittet på den lokala servern.
![öppna hybrid anslutnings gränssnittet](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Välj knappen **ange manuellt** och klistra in anslutnings strängen från Urklipp.
![klistra in anslutnings](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Starta om Hybridanslutningshanteraren från PowerShell om den inte visas som ansluten.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Skapa en app-inställning för lösen ordet för ett administratörs konto

1. Välj fliken **plattforms funktioner** i Function-appen.
1. Under **allmänna inställningar**väljer du **konfiguration**.
![välja plattforms konfiguration](./media/functions-hybrid-powershell/select-configuration.png)  
1. Expandera den **nya program inställningen** för att skapa en ny inställning för lösen ordet.
1. Ge inställningen namnet _ContosoUserPassword_och ange lösen ordet.
1. Välj **OK** och spara för att lagra lösen ordet i Function-programmet.
![Lägg till app-inställning för lösen ord](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Skapa en funktion för http-utlösare att testa

1. Skapa en ny HTTP-utlösnings funktion från Function-appen.
![skapa en ny HTTP-utlösare](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
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

3. Välj **Spara** och **Kör** för att testa funktionen.
![testa appens funktion](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>Hantera andra system lokalt

Du kan använda den anslutna lokala servern för att ansluta till andra servrar och hanterings system i den lokala miljön. På så sätt kan du hantera data Center åtgärder från Azure med hjälp av PowerShell-funktionerna. Följande skript registrerar en PowerShell-klientsession som körs under de angivna autentiseringsuppgifterna. Autentiseringsuppgifterna måste vara för en administratör på fjärrservrarna. Du kan sedan använda den här konfigurationen för att få åtkomst till andra slut punkter på den lokala servern eller data centret.

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

Ersätt följande variabler i skriptet med tillämpliga värden från din miljö:
* $HybridEndpoint
* $RemoteServer

I de två föregående scenarierna kan du ansluta och hantera dina lokala miljöer med hjälp av PowerShell i Azure Functions och Hybridanslutningar. Vi rekommenderar att du lär dig mer om [hybridanslutningar](../app-service/app-service-hybrid-connections.md) och [PowerShell i functions](./functions-reference-powershell.md).

Du kan också använda virtuella Azure- [nätverk](./functions-create-vnet.md) för att ansluta till din lokala miljö via Azure Functions.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Lär dig mer om att arbeta med PowerShell-funktioner](functions-reference-powershell.md)
