---
title: "Övervaka VPN-gatewayer Azure Nätverksbevakaren felsökning | Microsoft Docs"
description: "Den här artikeln beskriver hur diagnostisera lokal anslutning med Azure Automation och Nätverksbevakaren"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 935431783b08919049c5c24b56285647bc7b35ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Övervaka VPN-gatewayer Nätverksbevakaren felsökning

Får du djupa insikter på nätverkets prestanda är viktigt att tillhandahålla tillförlitlig tjänster till kunder. Det är därför viktigt att snabbt identifiera nätverksförhållanden avbrott och vidta åtgärder för att minimera avbrott villkoret. Azure Automation kan du implementera och köra en aktivitet i ett programmässiga sätt via runbooks. Med hjälp av Azure Automation skapas ett perfekt recept för att utföra kontinuerlig och proaktiv övervakning och avisering.

## <a name="scenario"></a>Scenario

Scenariot i följande bild är ett program som flera nivåer med lokal anslutning upprättas med hjälp av en VPN-Gateway och -tunnel. Säkerställa VPN-Gateway är igång och körs är avgörande för prestanda för program.

En runbook skapas med ett skript för att söka efter anslutningsstatusen för VPN-tunnel, med hjälp av Resource felsökning API för att söka efter status för användaranslutning tunnel. Om statusen inte är felfri, skickas en e-utlösare för administratörer.

![Exempel på ett scenario][scenario]

Det här scenariot kommer:

- Skapa en runbook anropar den `Start-AzureRmNetworkWatcherResourceTroubleshooting` för att felsöka anslutningsstatus
- Länka ett schema till runbook

## <a name="before-you-begin"></a>Innan du börjar

Innan du startar det här scenariot måste du ha följande krav:

- Ett Azure automation-konto i Azure. Kontrollera att automation-konto har de senaste modulerna och har även AzureRM.Network-modulen. Modulen AzureRM.Network är tillgängliga i modulgalleriet om du behöver lägga till den i ditt automation-konto.
- Du måste ha en uppsättning autentiseringsuppgifter konfigurera i Azure Automation. Mer information finns i [Azure Automation-säkerhet](../automation/automation-security-overview.md)
- En giltig SMTP-server (Office 365, din lokala e-post eller en annan) och autentiseringsuppgifter som definierats i Azure Automation
- En konfigurerade virtuella nätverksgateway i Azure.
- Ett befintligt lagringskonto med en befintlig behållare för lagring i loggarna.

> [!NOTE]
> Den infrastruktur som beskrivs i föregående bild är som illustration och är inte skapade i steg i den här artikeln.

### <a name="create-the-runbook"></a>Skapa en runbook

Det första steget att konfigurera exemplet är att skapa runbook. Det här exemplet används en Kör som-konto. Läs om Kör som-konton [autentisera Runbooks med Azure kör som-konto](../automation/automation-sec-configure-azure-runas-account.md)

### <a name="step-1"></a>Steg 1

Gå till Azure Automation i den [Azure-portalen](https://portal.azure.com) och på **Runbooks**

![Översikt över Automation-konto][1]

### <a name="step-2"></a>Steg 2

Klicka på **lägga till en runbook** att starta processen för runbook.

![runbooks blad][2]

### <a name="step-3"></a>Steg 3

Under **Snabbregistrering**, klickar du på **skapa en ny runbook** att skapa en runbook.

![Lägg till en runbook-bladet][3]

### <a name="step-4"></a>Steg 4

I det här steget kan vi ge runbook ett namn, i exempel kallas **Get-VPNGatewayStatus**. Det är viktigt att ange ett beskrivande namn för runbook och rekommenderade ett namn som följer standard PowerShell namngivning standarder. Runbook-typen för det här exemplet är **PowerShell**, de andra alternativ är grafisk PowerShell-arbetsflöde och grafisk PowerShell-arbetsflöde.

![runbook-bladet][4]

### <a name="step-5"></a>Steg 5

Följande kodexempel innehåller all kod som behövs för exemplet i det här steget som skapats i runbook. Objekten i kod som innehåller \<värdet\> måste ersättas med värden från din prenumeration.

Använd följande kod som Klicka **spara**

```PowerShell
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
Add-AzureRmAccount `
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

När du har sparat runbook måste ett schema länkas till den att automatisera start av runbook. Starta processen, klicka på **schema**.

![Steg 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Länka ett schema till runbook

Du måste skapa ett nytt schema. Klicka på **länka ett schema till din runbook**.

![Steg 7][7]

### <a name="step-1"></a>Steg 1

På den **schema** bladet, klickar du på **skapa ett nytt schema**

![Steg 8][8]

### <a name="step-2"></a>Steg 2

På den **nytt schema** bladet fill schema-information. De värden som kan anges finns i följande lista:

- **Namnet** -ett eget namn för schemat.
- **Beskrivning** -en beskrivning av schemat.
- **Startar** -värdet är en kombination av datum, tid och tidszon som utgör schema-utlösare.
- **Återkommande** – det här värdet fastställer scheman upprepning.  Giltiga värden är **när** eller **återkommande**.
- **Upprepas var** -intervall för schemat i timmar, dagar, veckor eller månader.
- **Ställa in ett utgångsdatum** -värdet fastställer om schemat ska upphöra att gälla eller inte. Kan anges till **Ja** eller **nr**. Ett giltigt datum och tid är ska tillhandahållas om du väljer Ja.

> [!NOTE]
> Om du behöver ha en runbook körs oftare än en gång i timmen måste flera scheman skapas med olika intervall (det vill säga 15, 30, 45 minuter efter timmen)

![Steg 9][9]

### <a name="step-3"></a>Steg 3

Klicka på Spara för att spara schemat till runbook.

![Steg 10][10]

## <a name="next-steps"></a>Nästa steg

Nu när du har en förståelse för hur du integrerar Nätverksbevakaren felsökning med Azure Automation lär du dig hur du utlöser paket insamlingar på VM-aviseringar genom att besöka [skapar en avisering utlösta paketinsamling med Azure Nätverksbevakaren](network-watcher-alert-triggered-packet-capture.md).

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
