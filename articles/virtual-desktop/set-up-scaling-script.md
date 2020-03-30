---
title: Skala sessionsvärdar Azure Automation – Azure
description: Så här skalar du windows virtuella skrivbordssessionsvärdar automatiskt med Azure Automation.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3a853dc32f8716f3f2ba32896a7a4a239efcc5bd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349871"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Skala sessionsvärdar med Azure Automation

Du kan minska din totala distributionskostnad för Windows Virtual Desktop genom att skala dina virtuella datorer. Detta innebär att stänga av och frigöra virtuella datorer för sessionsvärd under lågbelastningsanvändningstimmar, sedan slå på dem igen och omfördela dem under rusningstid.

I den här artikeln får du lära dig mer om skalningsverktyget som skapats med Azure Automation och Azure Logic Apps och som automatiskt skalar sessionvärden virtuella datorer i din Windows Virtual Desktop-miljö. Om du vill veta hur du använder skalningsverktyget går du vidare till [Förutsättningar](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>Så här fungerar skalningsverktyget

Skalningsverktyget ger ett billigt automatiseringsalternativ för kunder som vill optimera sina kostnader för värdbaserade virtuella datorer.

Du kan använda skalningsverktyget för att:
 
- Schemalägg virtuella datorer så att de startar och stoppar baserat på hög- och lågtrafiktid.
- Skala ut virtuella datorer baserat på antalet sessioner per CPU-kärna.
- Skala i virtuella datorer under lågtrafiktimmar, vilket gör att det minsta antalet virtuella sessionsvärdar körs.

Skalningsverktyget använder en kombination av Azure Automation PowerShell-runbooks, webhooks och Azure Logic Apps för att fungera. När verktyget körs anropar Azure Logic Apps en webhook för att starta Azure Automation-runbooken. Runbook skapar sedan ett jobb.

Under maximal användningstid kontrollerar jobbet det aktuella antalet sessioner och den virtuella datorns kapacitet för den aktuella sessionens värd för varje värdpool. Den här informationen används för att beräkna om de virtuella datorerna som körs kan stödja befintliga sessioner baserat på parametern *SessionThresholdPerCPU* som definierats för filen **createazurelogicapp.ps1.** Om startvärdarna för sessionen inte kan stödja befintliga sessioner startar jobbet ytterligare virtuella datorer för sessionsvärdar i värdpoolen.

>[!NOTE]
>*SessionThresholdPerCPU* begränsar inte antalet sessioner på den virtuella datorn. Den här parametern avgör bara när nya virtuella datorer behöver startas för att belastningsutjämna anslutningarna. Om du vill begränsa antalet sessioner måste du följa instruktionerna [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) för att konfigurera parametern *MaxSessionLimit* därefter.

Under lågbelastningstiden avgör jobbet vilka virtuella datorer för sessionsvärdar som ska stängas av baserat på parametern *MinimumNumberOfRDSH.* Jobbet ställer in virtuella datorer för sessionsvärdar till tömningsläge för att förhindra att nya sessioner ansluter till värdarna. Om du ställer in parametern *LimitSecondsToForceLogOffUser* på ett positivt värde som inte är noll, meddelar jobbet alla som för närvarande är inloggade användare att spara sitt arbete, väntar på den konfigurerade tiden och tvingar sedan användarna att logga ut. När alla användarsessioner på sessionsvärden VM har loggats ut stängs jobbet av den virtuella datorn.

Om du ställer in parametern *LimitSecondsToForceLogOffUser* till noll, tillåter jobbet att sessionskonfigurationsinställningen i angivna gruppprinciper hanterar signering av användarsessioner. Om du vill se dessa gruppprinciper går du till**Administrativa mallar för** >  **datorkonfigurationsprinciper** > **Policies** > **i Windows Components** > **Terminal Services** > **Terminal Server** > **Session Time Limits**. Om det finns några aktiva sessioner på en vm-värd för en session lämnar jobbet sessionsvärden VM körs. Om det inte finns några aktiva sessioner stängs jobbet av i den virtuella datorn för sessionsvärd.

Jobbet körs regelbundet baserat på ett inställt återkommande intervall. Du kan ändra det här intervallet baserat på storleken på windows virtual desktop-miljön, men kom ihåg att det kan ta lite tid att starta och stänga av virtuella datorer, så kom ihåg att ta hänsyn till fördröjningen. Vi rekommenderar att du ställer in upprepningsintervallet till var 15:e minut.

Verktyget har dock också följande begränsningar:

- Den här lösningen gäller endast poolade virtuella datorer för sessionsvärdar.
- Den här lösningen hanterar virtuella datorer i alla regioner, men kan bara användas i samma prenumeration som ditt Azure Automation-konto och Azure Logic Apps.

>[!NOTE]
>Skalningsverktyget styr belastningsutjämningsläget för den värdpool som den skalar. Den ställer in den på bredd-första belastningsutjämning för både topp- och lågtrafik.

## <a name="prerequisites"></a>Krav

Innan du börjar konfigurera skalningsverktyget ska du se till att du har följande saker klara:

- En [Windows Virtual Desktop-klient- och värdpool](create-host-pools-arm-template.md)
- Virtuella datorer för sessionsvärdpool som konfigurerats och registrerats med tjänsten Windows Virtual Desktop
- En användare med [deltagaråtkomst](../role-based-access-control/role-assignments-portal.md) på Azure-prenumeration

Maskinen du använder för att distribuera verktyget måste ha: 

- Windows PowerShell 5.1 eller senare
- Microsoft Az PowerShell-modulen

Om du har allt klart, så låt oss komma igång.

## <a name="create-an-azure-automation-account"></a>Skapa ett Azure Automation-konto

Först behöver du ett Azure Automation-konto för att köra PowerShell-runbooken. Så här konfigurerar du ditt konto:

1. Öppna Windows PowerShell som administratör.
2. Kör följande cmdlet för att logga in på ditt Azure-konto.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >Ditt konto måste ha deltagarrättigheter för Den Azure-prenumeration som du vill distribuera skalningsverktyget på.

3. Kör följande cmdlet för att hämta skriptet för att skapa Azure Automation-kontot:

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Kör följande cmdlet för att köra skriptet och skapa Azure Automation-kontot:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. Cmdletens utdata kommer att innehålla en webhook URI. Se till att föra ett register över URI eftersom du ska använda den som en parameter när du ställer in körningsschemat för Azure Logic-apparna.

6. När du har konfigurerat ditt Azure Automation-konto loggar du in på din Azure-prenumeration och kontrollerar att ditt Azure Automation-konto och den relevanta runbooken har dykt upp i din angivna resursgrupp, som visas i följande avbildning:

   ![En bild av översiktssidan för Azure som visar det nyligen skapade automatiseringskontot och runbooken.](media/automation-account.png)

  Om du vill kontrollera om din webhook är där den ska vara väljer du namnet på din runbook. Gå sedan till avsnittet Resurser i runbooken och välj **Webhooks**.

## <a name="create-an-azure-automation-run-as-account"></a>Skapa ett Azure Automation Run As-konto

Nu när du har ett Azure Automation-konto måste du också skapa ett Azure Automation Run As-konto för att komma åt dina Azure-resurser.

Ett [Azure Automation Run As-konto](../automation/manage-runas-account.md) ger autentisering för hantering av resurser i Azure med Azure-cmdlets. När du skapar ett Run As-konto skapas en ny tjänsthuvudanvändare i Azure Active Directory och tilldelas rollen Deltagare till tjänstens huvudanvändare på prenumerationsnivå, azure run as-kontot är ett bra sätt att autentisera säkert med certifikat och ett huvudnamn för tjänsten utan att behöva lagra ett användarnamn och lösenord i ett autentiseringsobjekt. Mer information om Kör som autentisering finns i [Begränsa kör som kontobehörigheter](../automation/manage-runas-account.md#limiting-run-as-account-permissions).

Alla användare som är medlemmar i rollen Prenumerationsadministratörer och coadministrator för prenumerationen kan skapa ett Run As-konto genom att följa nästa avsnitts instruktioner.

Så här skapar du ett Run As-konto i ditt Azure-konto:

1. Välj **Alla tjänster** i Azure-portalen. Ange och välj **Automation-konton**i listan över resurser .

2. På sidan **Automation-konton** väljer du namnet på ditt Automation-konto.

3. Välj **Kör som konton** under avsnittet Kontoinställningar i fönstrets vänstra sida.

4. Välj **Azure-körning som konto**. När fönstret **Lägg till Azure Kör som konto** visas granskar du översiktsinformationen och väljer sedan **Skapa** för att starta processen för att skapa kontot.

5. Vänta några minuter innan Azure skapar kontot Kör som. Du kan spåra förloppet för att skapa i menyn under Meddelanden.

6. När processen är klar skapas en tillgång med namnet AzureRunAsConnection i det angivna Automation-kontot. Anslutningstillgången innehåller program-ID, klient-ID, prenumerations-ID och certifikattumavtryck. Kom ihåg program-ID: et eftersom du använder det senare.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Skapa en rolltilldelning i Windows Virtual Desktop

Därefter måste du skapa en rolltilldelning så att AzureRunAsConnection kan interagera med Windows Virtual Desktop. Se till att använda PowerShell för att logga in med ett konto som har behörighet att skapa rolltilldelningar.

Hämta och importera först [Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som du kan använda i PowerShell-sessionen om du inte redan har gjort det. Kör följande PowerShell-cmdletar för att ansluta till Windows Virtual Desktop och visa dina klienter.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

När du hittar klienten med de värdpooler som du vill skala följer du instruktionerna i [Skapa ett Azure Automation-konto](#create-an-azure-automation-account) och använder klientnamnet som du fick från föregående cmdlet i följande cmdlet för att skapa rolltilldelningen:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Skapa Azure Logic App och körningsschema

Slutligen måste du skapa Azure Logic App och ställa in ett körningsschema för ditt nya skalningsverktyg.

1.  Öppna Windows PowerShell som administratör

2.  Kör följande cmdlet för att logga in på ditt Azure-konto.

     ```powershell
     Login-AzAccount
     ```

3. Kör följande cmdlet för att hämta skriptfilen createazurelogicapp.ps1 på din lokala dator.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. Kör följande cmdlet för att logga in på Windows Virtual Desktop med ett konto som har rds-ägare eller RDS Contributor-behörigheter.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Kör följande PowerShell-skript för att skapa Azure Logic-appen och körningsschemat.

     ```powershell
     $resourceGroupName = Read-Host -Prompt "Enter the name of the resource group for the new Azure Logic App"
     
     $aadTenantId = Read-Host -Prompt "Enter your Azure AD tenant ID"

     $subscriptionId = Read-Host -Prompt "Enter your Azure Subscription ID"

     $tenantName = Read-Host -Prompt "Enter the name of your WVD tenant"

     $hostPoolName = Read-Host -Prompt "Enter the name of the host pool you'd like to scale"

     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"

     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"

     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"

     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"

     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"

     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"

     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"

     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"

     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $location = Read-Host -Prompt "Enter the name of the Azure region where you will be creating the logic app"

     $connectionAssetName = Read-Host -Prompt "Enter the name of the Azure RunAs connection asset"

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"

     $automationAccountName = Read-Host -Prompt "Enter the name of the Azure Automation Account"

     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"

     .\createazurelogicapp.ps1 -ResourceGroupName $resourceGroupName `
       -AADTenantID $aadTenantId `
       -SubscriptionID $subscriptionId `
       -TenantName $tenantName `
       -HostPoolName $hostPoolName `
       -RecurrenceInterval $recurrenceInterval `
       -BeginPeakTime $beginPeakTime `
       -EndPeakTime $endPeakTime `
       -TimeDifference $timeDifference `
       -SessionThresholdPerCPU $sessionThresholdPerCPU `
       -MinimumNumberOfRDSH $minimumNumberOfRdsh `
       -LimitSecondsToForceLogOffUser $limitSecondsToForceLogOffUser `
       -LogOffMessageTitle $logOffMessageTitle `
       -LogOffMessageBody $logOffMessageBody `
       -Location $location `
       -ConnectionAssetName $connectionAssetName `
       -WebHookURI $webHookURI `
       -AutomationAccountName $automationAccountName `
       -MaintenanceTagName $maintenanceTagName
     ```

     När du har kört skriptet ska Logic App visas i en resursgrupp, vilket visas i följande bild.

     ![En bild av översiktssidan för ett exempel på Azure Logic App.](media/logic-app.png)

Om du vill göra ändringar i körningsschemat, till exempel ändra upprepningsintervallet eller tidszonen, går du till schemaläggaren för automatisk skalning och väljer **Redigera** för att gå till Logic Apps Designer.

![En bild av Logic Apps Designer. Menyerna Återkommande och Webhook som gör att användaren kan redigera upprepningstider och webhook-filen är öppna.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Hantera skalningsverktyget

Nu när du har skapat skalningsverktyget kan du komma åt dess utdata. I det här avsnittet beskrivs några funktioner som kan vara till hjälp.

### <a name="view-job-status"></a>Visa jobbstatus

Du kan visa en sammanfattad status för alla runbook-jobb eller visa en mer djupgående status för ett visst runbook-jobb i Azure-portalen.

Till höger om ditt valda Automation-konto, under Jobbstatistik, kan du visa en lista med sammanfattningar av alla runbook-jobb. Om du öppnar sidan **Jobb** till vänster i fönstret visas aktuella jobbstatusar, starttider och slutförandetider.

![En skärmbild av jobbstatussidan.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Visa utdata för loggar och skalningsverktyg

Du kan visa loggarna för skalning och skalning genom att öppna runbooken och välja namnet på jobbet.

Navigera till runbooken (standardnamnet är WVDAutoScaleRunbook) i resursgruppen som är värd för Azure Automation-kontot och välj **Översikt**. På översiktssidan väljer du ett jobb under Senaste jobb om du vill visa utdata för skalningsverktyg, vilket visas i följande bild.

![En bild av utdatafönstret för skalningsverktyget.](media/tool-output.png)

## <a name="report-issues"></a>Rapportera problem

Om du stöter på problem med skalningsverktyget kan du rapportera dem på [rds GitHub-sidan](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps).
