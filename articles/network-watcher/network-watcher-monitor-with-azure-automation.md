---
title: Felsöka och övervaka VPN-gateways – Azure Automation
titleSuffix: Azure Network Watcher
description: I den här artikeln beskrivs hur du diagnostiserar lokal anslutning med Azure Automation och Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 74c9f44ff5fbbbb50bba1594d371633fd49857eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845034"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Övervaka VPN-gateways med felsökning av Network Watcher

Att få djupa insikter om nätverkets prestanda är avgörande för att tillhandahålla tillförlitliga tjänster till kunder. Det är därför viktigt att snabbt identifiera nätverksavbrottsförhållanden och vidta korrigerande åtgärder för att minska avbrottstillståndet. Med Azure Automation kan du implementera och köra en uppgift på ett programmatiskt sätt via runbooks. Med Hjälp av Azure Automation skapas ett perfekt recept för att utföra kontinuerlig och proaktiv nätverksövervakning och aviseringar.

## <a name="scenario"></a>Scenario

Scenariot i följande avbildning är ett flerskiktat program, med lokal anslutning upprättad med hjälp av en VPN Gateway och tunnel. Att se till att VPN Gateway är igång är avgörande för programmens prestanda.

En runbook skapas med ett skript för att söka efter anslutningsstatus för VPN-tunneln, med hjälp av API:et för resursfelsökning för att söka efter anslutningstunnelstatus. Om statusen inte är felfri skickas en e-postutlösare till administratörer.

![Exempel på ett scenario][scenario]

Detta scenario kommer att:

- Skapa en runbook `Start-AzureRmNetworkWatcherResourceTroubleshooting` som anropar cmdlet för att felsöka anslutningsstatus
- Länka ett schema till runbooken

## <a name="before-you-begin"></a>Innan du börjar

Innan du startar det här scenariot måste du ha följande förutsättningar:

- Ett Azure automation-konto i Azure. Kontrollera att automationskontot har de senaste modulerna och har även Modulen AzureRM.Network. AzureRM.Network-modulen är tillgänglig i modulgalleriet om du behöver lägga till den i ditt automationskonto.
- Du måste ha en uppsättning autentiseringsuppgifter konfigurera i Azure Automation. Läs mer på [Azure Automation-säkerhet](../automation/automation-security-overview.md)
- En giltig SMTP-server (Office 365, din lokala e-post eller annan) och autentiseringsuppgifter som definierats i Azure Automation
- En konfigurerad virtual network gateway i Azure.
- Ett befintligt lagringskonto med en befintlig behållare för att lagra loggarna i.

> [!NOTE]
> Infrastrukturen som visas i föregående bild är för illustrationsändamål och skapas inte med stegen i den här artikeln.

### <a name="create-the-runbook"></a>Skapa runbooken

Det första steget för att konfigurera exemplet är att skapa runbooken. I det här exemplet används ett körningskonto. Om du vill veta mer om körningskonton besöker du [Autentisera runbooks med Azure Run As-konto](../automation/automation-create-runas-account.md)

### <a name="step-1"></a>Steg 1

Navigera till Azure Automation i [Azure-portalen](https://portal.azure.com) och klicka på **Runbooks**

![Översikt över automationskonto][1]

### <a name="step-2"></a>Steg 2

Klicka på **Lägg till en runbook** om du vill starta processen för att skapa runbooken.

![runbooksblad][2]

### <a name="step-3"></a>Steg 3

Under **Snabbt skapa**klickar du på Skapa en ny **runbook** för att skapa runbooken.

![lägga till ett runbook-blad][3]

### <a name="step-4"></a>Steg 4

I det här steget ger vi runbook ett namn, i exemplet kallas det **Get-VPNGatewayStatus**. Det är viktigt att ge runbooken ett beskrivande namn och rekommenderar att du ger den ett namn som följer standardstandarder för PowerShell-namngivning. Runbook-typen för det här exemplet är **PowerShell**, de andra alternativen är grafiskt, PowerShell-arbetsflöde och Grafiskt PowerShell-arbetsflöde.

![runbook blad][4]

### <a name="step-5"></a>Steg 5

I det här steget skapas runbooken, i följande kodexempel finns all kod som behövs för exemplet. Artiklarna i koden som \<\> innehåller värde måste ersättas med värdena från din prenumeration.

Använd följande kod som klickar på **Spara**

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

När runbooken har sparats måste ett schema länkas till den för att kunna automatisera början av runbooken. Starta processen genom att klicka på **Schemalägg**.

![Steg 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Länka ett schema till runbooken

Ett nytt schema måste skapas. Klicka på **Länka ett schema till din runbook**.

![Steg 7][7]

### <a name="step-1"></a>Steg 1

Klicka på Skapa **ett nytt schema** på bladet **Schema**

![Steg 8][8]

### <a name="step-2"></a>Steg 2

På bladet **Nytt schema** fylls schemainformationen. De värden som kan anges finns i följande lista:

- **Namn** - Det egna namnet på schemat.
- **Beskrivning** - En beskrivning av schemat.
- **Startar** - Det här värdet är en kombination av datum, tid och tidszon som utgör den tid schemat utlöser.
- **Återkommande** - Det här värdet bestämmer upprepningen av scheman.  Giltiga värden är **En gång** eller **Återkommande**.
- **Upprepa varje** - Upprepningsintervallet för schemat i timmar, dagar, veckor eller månader.
- **Ange förfallodatum** - Värdet avgör om schemat ska upphöra att gälla eller inte. Kan ställas in på **Ja** eller **Nej**. Ett giltigt datum och en giltig tid ska anges om ja väljs.

> [!NOTE]
> Om du behöver ha en runbook köra oftare än varje timme, måste flera scheman skapas med olika intervall (det vill säga 15, 30, 45 minuter efter timmen)

![Steg 9][9]

### <a name="step-3"></a>Steg 3

Klicka på Spara om du vill spara schemat i runbooken.

![Steg 10][10]

## <a name="next-steps"></a>Nästa steg

Nu när du har en förståelse för hur du integrerar felsökning av Network Watcher med Azure Automation kan du läsa om hur du utlöser paketinsamlingar på VM-aviseringar genom att besöka [Skapa en avisering som utlöst paketfångst med Azure Network Watcher](network-watcher-alert-triggered-packet-capture.md).

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
