---
title: Övervaka VPN-gatewayer med Azure Network Watcher troubleshooting | Microsoft Docs
description: Den här artikeln beskrivs hur diagnostisera lokal anslutning med Azure Automation och Network Watcher
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: c26aef777df3ef5c7df6575b8d939a329740a97e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64719788"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Övervaka VPN gateway med Network Watcher troubleshooting

Få djupgående insikter på nätverkets prestanda är viktigt att tillhandahålla tillförlitliga tjänster till kunder. Det är därför viktigt att snabbt identifiera nätverksförhållanden för avbrott och vidta åtgärder för att minimera avbrott i villkoret. Azure Automation kan du implementera och kör en uppgift i ett programmässiga sätt via runbooks. Med Azure Automation skapar ett perfekt recept för att utföra kontinuerlig och proaktiv övervakning och avisering.

## <a name="scenario"></a>Scenario

Scenariot i följande bild är ett program med flera nivåer, med plats-anslutning upprättas med hjälp av en VPN-Gateway och -tunnel. Att se till att VPN-Gateway är igång och körs är viktigt att prestanda för program.

En runbook skapas med ett skript för att söka efter anslutningsstatusen för VPN-tunneln med resursen felsökning av API för att söka efter tunnel anslutningsstatus. Om statusen inte är felfri, skickas ett e-postutlösare till administratörer.

![Exempel på ett scenario][scenario]

Det här scenariot kommer att:

- Skapa en runbook anropar den `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet för att felsöka anslutningsstatus
- Länka ett schema till runbook

## <a name="before-you-begin"></a>Innan du börjar

Innan du startar det här scenariot måste du ha följande förutsättningar:

- Ett Azure automation-konto i Azure. Kontrollera att automation-kontot har de senaste modulerna och har även AzureRM.Network-modulen. AzureRM.Network modulen finns i modulgalleriet om du vill lägga till den i ditt automation-konto.
- Du måste ha en uppsättning autentiseringsuppgifter som konfigurerar i Azure Automation. Läs mer på [säkerheten i Azure Automation](../automation/automation-security-overview.md)
- En giltig SMTP-server (Office 365, din lokala e-post eller en annan) och autentiseringsuppgifter som definierats i Azure Automation
- En konfigurerad virtuell nätverksgateway i Azure.
- Ett befintligt lagringskonto med en befintlig behållare för att lagra loggarna i.

> [!NOTE]
> Den infrastruktur som beskrivs i den föregående bilden är för tydlighetens skull och har skapats inte med de steg som ingår i den här artikeln.

### <a name="create-the-runbook"></a>Skapa runbook

Det första steget för att konfigurera exemplet är att skapa runbook. Det här exemplet används en Kör som-konto. Mer information om Kör som-konton, besök [autentisera Runbooks med Kör som-konto](../automation/automation-create-runas-account.md)

### <a name="step-1"></a>Steg 1

Gå till Azure Automation i den [Azure-portalen](https://portal.azure.com) och klicka på **Runbooks**

![Översikt över Automation-konto][1]

### <a name="step-2"></a>Steg 2

Klicka på **Lägg till en runbook** att starta processen att skapa runbook.

![runbooks bladet][2]

### <a name="step-3"></a>Steg 3

Under **Snabbregistrering**, klickar du på **skapa en ny runbook** att skapa en runbook.

![Lägg till en runbook][3]

### <a name="step-4"></a>Steg 4

I det här steget ska vi ge runbook ett namn, i det här exemplet kallas **Get-VPNGatewayStatus**. Det är viktigt att ge ett beskrivande namn för runbooken och rekommenderade ger den ett namn som följer standard namngivningskonvention för PowerShell. Runbook-typen för det här exemplet är **PowerShell**, de andra alternativen är grafisk, PowerShell-arbetsflöde, och grafiskt PowerShell-arbetsflöde.

![runbook-bladet][4]

### <a name="step-5"></a>Steg 5

I följande kodexempel innehåller all kod som behövs för det här exemplet i det här steget som skapats i runbook. Objekten i koden som innehåller \<värdet\> måste de ersättas med värden från din prenumeration.

Använd följande kod som Klicka **spara**

```powershell
# Set these variables to the proper values for your environment
$o365AutomationCredential = "<Office 365 account>"
$fromEmail = "<from email address>"
$toEmail = "<to email address>"
$smtpServer = "<smtp.office365.com>"
$smtpPort = 587
$runAsConnectionName = "<AzureRunAsConnection>"
$subscriptionId = "<subscription id>"
$region = "<Azure region>"
$vpnConnectionName = "<vpn connection name>"
$vpnConnectionResourceGroup = "<resource group name>"
$storageAccountName = "<storage account name>"
$storageAccountResourceGroup = "<resource group name>"
$storageAccountContainer = "<container name>"

# Get credentials for Office 365 account
$cred = Get-AutomationPSCredential -Name $o365AutomationCredential

# Get the connection "AzureRunAsConnection "
$servicePrincipalConnection=Get-AutomationConnection -Name $runAsConnectionName

"Logging in to Azure..."
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $region }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName -ResourceGroupName $vpnConnectionResourceGroup
$sa = Get-AzureRmStorageAccount -Name $storageAccountName -ResourceGroupName $storageAccountResourceGroup 
$storagePath = "$($sa.PrimaryEndpoints.Blob)$($storageAccountContainer)"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath $storagePath

if($result.code -ne "Healthy")
    {
        $body = "Connection for $($connection.name) is: $($result.code) `n$($result.results[0].summary) `nView the logs at $($storagePath) to learn more."
        Write-Output $body
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To $toEmail `
        -Subject $subject `
        -Body $body `
        -UseSsl `
        -Port $smtpPort `
        -SmtpServer $smtpServer `
        -From $fromEmail `
        -BodyAsHtml `
        -Credential $cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>Steg 6

När runbook sparas kan måste ett schema kopplas till den för att automatisera början av runbooken. Starta processen genom att klicka på **schema**.

![Steg 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Länka ett schema till runbook

Du måste skapa ett nytt schema. Klicka på **länka ett schema till din runbook**.

![Steg 7][7]

### <a name="step-1"></a>Steg 1

På den **schema** bladet klickar du på **skapa ett nytt schema**

![Steg 8][8]

### <a name="step-2"></a>Steg 2

På den **nytt schema** bladet fylla i schemainformationen om. De värden som kan anges finns i följande lista:

- **Namn på** -det egna namnet på schemat.
- **Beskrivning av** – en beskrivning av schemat.
- **Startar** – det här värdet är en kombination av datum, tid och tidszon som utgör schemautlösare.
- **Upprepning** – det här värdet fastställer scheman för upprepning.  Giltiga värden är **när** eller **återkommande**.
- **Utför varje** -intervall för schemat i timmar, dagar, veckor eller månader.
- **Ställa in ett utgångsdatum** -värdet som avgör om schemat ska upphöra att gälla eller inte. Kan anges till **Ja** eller **nr**. Ett giltigt datum och tid är ska tillhandahållas om Ja väljs.

> [!NOTE]
> Om du behöver ha en runbook som körs oftare än en gång i timmen måste flera scheman skapas med olika intervall (det vill säga 15, 30, 45 minuter efter timmen)

![Steg 9][9]

### <a name="step-3"></a>Steg 3

Klicka på Spara för att spara schemat till runbook.

![Steg 10][10]

## <a name="next-steps"></a>Nästa steg

Nu när du har en förståelse för hur du integrerar Network Watcher felsökning med Azure Automation, lär du dig hur du utlöser infångade paket på VM-aviseringar genom att besöka [skapar en avisering utlösta infångade med Azure Network Watcher](network-watcher-alert-triggered-packet-capture.md).

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png
