---
title: Skala sessioner värdar Azure Automation – Azure
description: Hur du automatiskt skalar värdar för virtuella Windows-fjärrskrivbordssessioner med Azure Automation.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f94852a99f0bc430ac193b9951de607cdd7fa933
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362551"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Skala sessionsbaserade värdar med hjälp av Azure Automation

>[!IMPORTANT]
>Det här innehållet gäller för hösten 2019-versionen som inte stöder Azure Resource Manager virtuella Windows Desktop-objekt.

Du kan minska din totala distributions kostnad för virtuella Windows-datorer genom att skala dina virtuella datorer (VM). Det innebär att du stänger av och avallokerar sessions värden för virtuella datorer vid låg belastnings tider, sedan aktiverar dem igen och omallokerar dem under hög belastnings tid.

I den här artikeln får du lära dig om skalnings verktyget som skapats med Azure Automation och Azure Logic Apps som automatiskt skalar virtuella datorer i Windows Virtual Desktop-miljön. Om du vill lära dig hur du använder skalnings verktyget kan du gå vidare till [krav](#prerequisites).

## <a name="report-issues"></a>Rapportera problem

Problem rapporter för skalnings verktyget hanteras för närvarande på GitHub i stället för Microsoft Support. Om du stöter på problem med skalnings verktyget kan du rapportera dem Bu att öppna ett GitHub-ärende med etiketten "4a-WVD-Scaling-logicapps" på [sidan RDS GitHub](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps).

## <a name="how-the-scaling-tool-works"></a>Hur skalnings verktyget fungerar

Skalnings verktyget ger till gång till ett kostnads fritt automatiserings alternativ för kunder som vill optimera sina VM-kostnader för en värd.

Du kan använda skalnings verktyget för att:

- Schemalägg VM: ar för start och stopp baserat på kontors tid och låg belastning.
- Skala ut virtuella datorer baserat på antalet sessioner per CPU-kärna.
- Skala i virtuella datorer under låg belastnings tider och lämna det lägsta antalet virtuella dator värdar som körs.

Skalnings verktyget använder en kombination av Azure Automation PowerShell-Runbooks, Webhooks och Azure Logic Apps för att fungera. När verktyget körs anropar Azure Logic Apps en webhook för att starta Azure Automation Runbook. Runbooken skapar sedan ett jobb.

Under den högsta användnings tiden kontrollerar jobbet det aktuella antalet sessioner och VM-kapaciteten för den aktuella värddatorn som kör sessionen för varje adresspool. Den här informationen används för att beräkna om de virtuella datorerna i sessionen kan stödja befintliga sessioner baserat på den *SessionThresholdPerCPU* -parameter som definierats för **createazurelogicapp.ps1** -filen. Om de virtuella datorerna i sessionen inte har stöd för befintliga sessioner startar jobbet ytterligare virtuella dator värdar i den aktuella adresspoolen.

>[!NOTE]
>*SessionThresholdPerCPU* begränsar inte antalet sessioner på den virtuella datorn. Den här parametern bestämmer bara när nya virtuella datorer måste startas för att belastningsutjämna anslutningarna. Om du vill begränsa antalet sessioner måste du följa anvisningarna [set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) för att konfigurera *MaxSessionLimit* -parametern enligt detta.

Under den högsta användnings tiden avgör jobbet vilken virtuell dator i sessionen som ska stängas av baserat på parametern *MinimumNumberOfRDSH* . Jobbet ställer in de virtuella datorerna i den virtuella datorn i tömnings läge för att förhindra att nya sessioner ansluter till värdarna. Om du ställer in parametern *LimitSecondsToForceLogOffUser* på ett positivt värde som inte är noll meddelar jobbet alla användare som är inloggade att spara sitt arbete, väntar på den konfigurerade tiden och tvingar sedan användarna att logga ut. När alla användarsessioner på VM-sessionen har loggats ut stängs jobbet av den virtuella datorn.

Om du ställer in parametern *LimitSecondsToForceLogOffUser* på noll, kommer jobbet att tillåta konfigurations inställningen för sessionen i angivna grup principer för att hantera signering av användarsessioner. Om du vill se dessa grup principer går du till **dator konfiguration**  >  **principer**  >  **administrativa mallar**  >  **Windows-komponenter**  >  **Terminal Services**  >  **Terminal Server**  >  **tids gränser för sessioner**. Om det finns aktiva sessioner på en virtuell dator för en virtuell dator kommer jobbet att lämna den virtuella dator som körs på sessionen. Om det inte finns några aktiva sessioner stängs jobbet för den virtuella datorns sessions värd.

Jobbet körs regelbundet baserat på ett angivet upprepnings intervall. Du kan ändra det här intervallet baserat på storleken på din Windows Virtual Desktop-miljö, men kom ihåg att starta och stänga av virtuella datorer kan ta lite tid, så kom ihåg att ta hänsyn till fördröjningen. Vi rekommenderar att du ställer in upprepnings intervallet på var 15: e minut.

Verktyget har dock också följande begränsningar:

- Den här lösningen gäller endast för virtuella datorer i pooler för sessioner.
- Den här lösningen hanterar virtuella datorer i valfri region, men kan endast användas i samma prenumeration som ditt Azure Automation konto och Azure Logic Apps.

>[!NOTE]
>Skalnings verktyget styr belastnings Utjämnings läget för den värddator som skalan. Den anger den till bredd-första belastnings utjämning för både högsta och låg belastnings tid.

## <a name="prerequisites"></a>Krav

Innan du börjar konfigurera skalnings verktyget ser du till att du har följande klart:

- En [Windows-klient för virtuella skriv bord och värd bassäng](create-host-pools-arm-template.md)
- VM-poolen för sessioner som kon figurer ATS och registrerats med Windows Virtual Desktop-tjänsten
- En användare med [deltagar åtkomst](../../role-based-access-control/role-assignments-portal.md) i Azure-prenumerationen

Datorn som du använder för att distribuera verktyget måste ha:

- Windows PowerShell 5,1 eller senare
- Microsoft AZ PowerShell-modulen

Om du har allt klart är det dags att sätta igång.

## <a name="create-an-azure-automation-account"></a>Skapa ett Azure Automation-konto

Först behöver du ett Azure Automation-konto för att köra PowerShell-runbooken. Så här konfigurerar du ditt konto:

1. Öppna Windows PowerShell som administratör.
2. Kör följande cmdlet för att logga in på ditt Azure-konto.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >Ditt konto måste ha deltagar behörighet för den Azure-prenumeration som du vill distribuera skalnings verktyget på.

3. Kör följande cmdlet för att ladda ned skriptet för att skapa Azure Automation-kontot:

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Kör följande cmdlet för att köra skriptet och skapa Azure Automation-kontot:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. Cmdletens utdata kommer att innehålla en webhook-URI. Se till att spara en post i URI: n eftersom du kommer att använda den som en parameter när du konfigurerar körnings schema för Azure Logic Apps.

6. När du har konfigurerat ditt Azure Automation-konto loggar du in på din Azure-prenumeration och kontrollerar att ditt Azure Automation-konto och att den relevanta runbooken har visats i den angivna resurs gruppen, som du ser i följande bild:

> [!div class="mx-imgBorder"]
> ![En bild av sidan för Azure-översikten visar det nyligen skapade Automation-kontot och runbooken.](../media/automation-account.png)

  Markera namnet på din Runbook för att kontrol lera om din webhook är där det ska vara. Gå sedan till avsnittet resurser för Runbook och välj **Webhooks**.

## <a name="create-an-azure-automation-run-as-account"></a>Skapa ett Kör som-konto för Azure Automation

Nu när du har ett Azure Automation konto måste du också skapa ett Azure Automation kör som-konto för att få åtkomst till dina Azure-resurser.

Ett [Azure Automation kör som-konto](../../automation/manage-runas-account.md) tillhandahåller autentisering för att hantera resurser i Azure med Azure-cmdletar. När du skapar ett Kör som-konto skapar det en ny tjänst huvud användare i Azure Active Directory och tilldelar rollen deltagare till tjänstens huvud namns användare på prenumerations nivån, kör som-kontot i Azure är ett bra sätt att autentisera säkert med certifikat och ett huvud namn för tjänsten utan att behöva lagra ett användar namn och lösen ord i ett Credential-objekt. Om du vill veta mer om kör som-autentisering, se [begränsa behörigheter för kör som-konto](../../automation/manage-runas-account.md#limit-run-as-account-permissions).

Alla användare som är medlemmar i rollen prenumerations administratörer och administratören av prenumerationen kan skapa ett Kör som-konto genom att följa anvisningarna i nästa avsnitt.

Skapa ett Kör som-konto på ditt Azure-konto:

1. Välj **Alla tjänster** i Azure-portalen. I listan över resurser anger och väljer du **Automation-konton**.

2. På sidan **Automation-konton** väljer du namnet på ditt Automation-konto.

3. I fönstret till vänster i fönstret väljer du **Kör som-konton** under avsnittet konto inställningar.

4. Välj **Kör som-konto i Azure**. När fönstret **Lägg till Azure kör som-konto** visas granskar du översiktlig information och väljer sedan **skapa** för att starta processen för att skapa kontot.

5. Vänta några minuter för Azure för att skapa kör som-kontot. Du kan spåra hur du skapar en förloppet i menyn under meddelanden.

6. När processen har slutförts skapas en till gång med namnet AzureRunAsConnection i det angivna Automation-kontot. Anslutnings till gången innehåller program-ID, klient-ID, prenumerations-ID och tumavtryck för certifikatet. Kom ihåg program-ID: t eftersom du kommer att använda det senare.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Skapa en roll tilldelning i Windows Virtual Desktop

Därefter måste du skapa en roll tilldelning så att AzureRunAsConnection kan samverka med det virtuella Windows-skrivbordet. Se till att använda PowerShell för att logga in med ett konto som har behörighet att skapa roll tilldelningar.

Börja med att hämta och importera [Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som ska användas i PowerShell-sessionen om du inte redan gjort det. Kör följande PowerShell-cmdlets för att ansluta till det virtuella Windows-skrivbordet och Visa dina klienter.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

När du hittar klienten med de värdar som du vill skala, följer du instruktionerna i [skapa ett Azure Automation konto](#create-an-azure-automation-account) och använder klient namnet som du fick från föregående cmdlet i följande cmdlet för att skapa roll tilldelningen:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Skapa Azure Logic-appen och körnings schemat

Slutligen måste du skapa Azure Logic-appen och konfigurera ett körnings schema för det nya skalnings verktyget.

1.  Öppna Windows PowerShell som administratör

2.  Kör följande cmdlet för att logga in på ditt Azure-konto.

     ```powershell
     Login-AzAccount
     ```

3. Kör följande cmdlet för att ladda ned createazurelogicapp.ps1 skript filen på den lokala datorn.

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazurelogicapp.ps1"
     ```

4. Kör följande cmdlet för att logga in på Windows Virtual Desktop med ett konto som har behörighet som RDS-ägare eller RDS-deltagare.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Kör följande PowerShell-skript för att skapa Azure Logic-appen och körnings schemat.

     ```powershell
     $aadTenantId = (Get-AzContext).Tenant.Id

     $azureSubscription = Get-AzSubscription | Out-GridView -PassThru -Title "Select your Azure Subscription"
     Select-AzSubscription -Subscription $azureSubscription.Id
     $subscriptionId = $azureSubscription.Id

     $resourceGroup = Get-AzResourceGroup | Out-GridView -PassThru -Title "Select the resource group for the new Azure Logic App"
     $resourceGroupName = $resourceGroup.ResourceGroupName
     $location = $resourceGroup.Location

     $wvdTenant = Get-RdsTenant | Out-GridView -PassThru -Title "Select your WVD tenant"
     $tenantName = $wvdTenant.TenantName

     $wvdHostpool = Get-RdsHostPool -TenantName $wvdTenant.TenantName | Out-GridView -PassThru -Title "Select the host pool you'd like to scale"
     $hostPoolName = $wvdHostpool.HostPoolName

     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"
     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $automationAccount = Get-AzAutomationAccount -ResourceGroupName $resourceGroup.ResourceGroupName | Out-GridView -PassThru
     $automationAccountName = $automationAccount.AutomationAccountName
     $automationAccountConnection = Get-AzAutomationConnection -ResourceGroupName $resourceGroup.ResourceGroupName -AutomationAccountName $automationAccount.AutomationAccountName | Out-GridView -PassThru -Title "Select the Azure RunAs connection asset"
     $connectionAssetName = $automationAccountConnection.Name

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"
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

     När du har kört skriptet ska den logiska appen visas i en resurs grupp, som du ser i följande bild.

     > [!div class="mx-imgBorder"]
     > ![En bild av översikts sidan för ett exempel på en Azure Logic-app.](../media/logic-app.png)

Om du vill göra ändringar i körnings schemat, till exempel ändra intervallet eller tids zonen för upprepning, går du till autoskalning Scheduler och väljer **Redigera** för att gå till Logic Apps designer.

> [!div class="mx-imgBorder"]
> ![En bild av Logic Apps designer. De upprepnings-och webhook-menyer som låter användaren redigera upprepnings tider och webhook-filen är öppen.](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Hantera skalnings verktyget

Nu när du har skapat skalnings verktyget kan du komma åt dess utdata. I det här avsnittet beskrivs några funktioner som du kan ha nytta av.

### <a name="view-job-status"></a>Visa jobbstatus

Du kan visa en sammanfattnings status för alla Runbook-jobb eller Visa en mer djupgående status för ett särskilt Runbook-jobb i Azure Portal.

Till höger om det valda Automation-kontot under "jobb statistik" kan du Visa en lista över sammanfattningar av alla Runbook-jobb. Om du öppnar sidan **jobb** till vänster i fönstret visas aktuella jobb status, start tider och slut för ande tider.

> [!div class="mx-imgBorder"]
> ![En skärm bild av sidan jobb status.](../media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Visa utdata för loggar och skalnings verktyg

Du kan visa loggarna för skalnings-och skalnings åtgärder genom att öppna din Runbook och välja namnet på jobbet.

Navigera till runbooken (standard namnet är WVDAutoScaleRunbook) i resurs gruppen som är värd för Azure Automation konto och välj **Översikt**. På sidan Översikt väljer du ett jobb under senaste jobb för att Visa skalnings verktygets utdata, som du ser i följande bild.

> [!div class="mx-imgBorder"]
> ![En bild av utdatafönstret för skalnings verktyget.](../media/tool-output.png)

