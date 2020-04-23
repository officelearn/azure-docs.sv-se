---
title: Felsöka Azure Automation Runbook-fel
description: Lär dig hur du felsöker och löser problem som kan uppstå i Azure Automation runbooks.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 73f79145f63e0d8afee7596f1f8231a054ef1c2e
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097701"
---
# <a name="troubleshoot-runbook-errors"></a>Felsöka Runbook-fel

 I den här artikeln beskrivs de olika Runbook-felen som kan uppstå och hur du kan lösa dem.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="diagnosing-runbook-issues"></a>Diagnostisera Runbook-problem

När du får fel meddelanden under Runbook-körningen i Azure Automation kan du använda följande steg för att diagnostisera problemen.

1. **Se till att Runbook-skriptet körs korrekt på den lokala datorn.** 

    Se PowerShell- [dokumenten](/powershell/scripting/overview) eller [python-dokumenten](https://docs.python.org/3/) för språk referens-och inlärnings moduler. Att köra skriptet lokalt kan identifiera och lösa vanliga fel, till exempel:

      * Moduler som saknas
      * Syntaxfel
      * Logic-fel

2. **Undersök [fel strömmar](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)för Runbook.**

    Titta på dessa strömmar för vissa meddelanden och jämför dem med de fel som dokumenteras i den här artikeln.

3. **Se till att noderna och automation-arbetsytan har de moduler som krävs.** 

    Om din Runbook importerar några moduler kontrollerar du att de är tillgängliga för ditt Automation-konto med hjälp av stegen i [importera moduler](../shared-resources/modules.md#importing-modules). Uppdatera dina moduler till den senaste versionen genom att följa anvisningarna i [Uppdatera Azure-moduler i Azure Automation](..//automation-update-azure-modules.md). Mer felsöknings information finns i [Felsöka moduler](shared-resources.md#modules).

4. **Gör om din Runbook är inaktive rad eller inte fungerar som den ska.**

    * [Kontrol lera jobb status](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) definierar Runbook-status och vissa möjliga orsaker.
    * [Lägg till ytterligare utdata](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) till runbooken för att identifiera vad som händer innan runbooken pausas.
    * [Hantera eventuella undantag](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) som genereras av ditt jobb.

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scenario: kör login-AzureRMAccount för att logga in

### <a name="issue"></a>Problem

Du får följande fel meddelande när du kör en Runbook:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Orsak

Det här felet kan inträffa om du inte använder ett Kör som-konto eller om kör som-kontot har upphört att gälla. Se [hantera Azure Automation kör som-konton](https://docs.microsoft.com/azure/automation/manage-runas-account).

Det här felet har två primära orsaker:

* Det finns olika versioner av AzureRM-eller AZ-modulen.
* Du försöker komma åt resurser i en separat prenumeration.

### <a name="resolution"></a>Lösning

Om du får det här felet när du har uppdaterat en AzureRM-eller AZ-modul bör du uppdatera alla moduler till samma version.

Om du försöker komma åt resurser i en annan prenumeration kan du följa stegen nedan för att konfigurera behörigheter.

1. Gå till kör som-kontot för Automation och kopiera program-ID och tumavtryck.

    ![Kopiera ID och tumavtryck](../media/troubleshoot-runbooks/collect-app-id.png)

1. Gå till prenumerationens Access Control där Automation-kontot inte finns och Lägg till en ny roll tilldelning.

    ![Åtkomstkontroll](../media/troubleshoot-runbooks/access-control.png)

1. Lägg till det program-ID som samlats in tidigare. Välj deltagar behörighet.

    ![Lägg till rolltilldelning](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Kopiera namnet på prenumerationen.

1. Du kan nu använda följande Runbook-kod för att testa behörigheterna från ditt Automation-konto till den andra prenumerationen. Ersätt `"\<CertificateThumbprint\>"` med värdet som kopieras i steg 1. Ersätt `"\<SubscriptionName\>"` med värdet som kopieras i steg 4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Scenario: det går inte att hitta Azure-prenumerationen

### <a name="issue"></a>Problem

Du får följande fel meddelande när du arbetar med `Select-AzureSubscription`cmdleten `Select-AzureRMSubscription`, `Select-AzSubscription` eller:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Fel

Det här felet kan inträffa om:

* Prenumerations namnet är inte giltigt.
* Den Azure Active Directory användare som försöker hämta prenumerations informationen har inte kon figurer ATS som administratör för prenumerationen.
* Cmdleten är inte tillgänglig.

### <a name="resolution"></a>Lösning

Följ stegen nedan för att avgöra om du har autentiserats för Azure och har åtkomst till den prenumeration som du försöker välja.

1. Kontrol lera att skriptet fungerar fristående genom att testa det utanför Azure Automation.
2. Kontrol lera att skriptet kör cmdleten [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) innan du `Select-*` kör cmdleten.
3. Lägg `Disable-AzContextAutosave –Scope Process` till i början av din Runbook. Detta cmdlet-anrop garanterar att alla autentiseringsuppgifter endast gäller för körningen av den aktuella runbooken.
4. Om det här fel meddelandet fortfarande visas ändrar du koden genom att lägga till `AzContext` parametern för `Connect-AzAccount`och sedan köra koden.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scenario: autentisering till Azure misslyckades eftersom Multi-Factor Authentication har Aktiver ATS

### <a name="issue"></a>Problem

Du får följande fel när du autentiserar till Azure med ditt användar namn och lösen ord för Azure:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Orsak

Om du har Multi-Factor Authentication på ditt Azure-konto kan du inte använda en Azure Active Directory användare för att autentisera till Azure. I stället måste du använda ett certifikat eller ett huvud namn för tjänsten för att autentisera.

### <a name="resolution"></a>Lösning

Om du vill använda ett certifikat med de klassiska cmdletarna för distributions modellen i Azure, se [skapa och lägga till ett certifikat för att hantera Azure-tjänster](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Information om hur du använder ett huvud namn för tjänsten med Azure Resource Manager cmdlets finns i [skapa tjänstens huvud namn med Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) och [autentisera ett huvud namn för tjänsten med Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scenario: du ser ett fel i jobb strömmar om metoden get_SerializationSettings

### <a name="issue"></a>Problem

Följande fel visas i jobb strömmar för en Runbook:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AZAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Orsak

Det här felet orsakas av både AzureRM-och AZ-modulens cmdlets i en Runbook. Det inträffar när du importerar AZ-modulen innan du importerar AzureRM-modulen.

### <a name="resolution"></a>Lösning

AZ-och AzureRM-cmdletar kan inte importeras och används i samma Runbook. Mer information om AZ-cmdlets i Azure Automation finns i [Hantera moduler i Azure Automation](../shared-resources/modules.md).

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>Scenario: runbooken Miss lyckas med felet: en aktivitet avbröts

### <a name="issue"></a>Problem

Din Runbook Miss lyckas med ett fel som liknar följande exempel:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Orsak

Det här felet kan orsakas av att inaktuella Azure-moduler används.

### <a name="resolution"></a>Lösning

Du kan lösa det här felet genom att uppdatera Azure-moduler till den senaste versionen. 

1. I ditt Automation-konto klickar du på **moduler**och **uppdaterar sedan Azure-moduler**. 
2. Uppdateringen tar ungefär 15 minuter. Kör den Runbook som misslyckades när den är klar.

Mer information om hur du uppdaterar dina moduler finns [i Uppdatera Azure-moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scenario: Runbooks fungerar inte vid hantering av flera prenumerationer

### <a name="issue"></a>Problem

När Runbooks körs kan inte Runbook Hantera Azure-resurser.

### <a name="cause"></a>Orsak

Runbooken använder inte rätt kontext vid körning.

### <a name="resolution"></a>Lösning

Prenumerations kontexten kan gå förlorad när en Runbook anropar flera Runbooks. För att säkerställa att prenumerations kontexten skickas till runbooks, måste klientens Runbook skicka kontexten till `Start-AzureRmAutomationRunbook` cmdlet: en i `AzureRmContext` parametern. Använd `Disable-AzureRmContextAutosave` cmdleten med `Scope` parametern inställd på `Process` för att säkerställa att de angivna autentiseringsuppgifterna endast används för den aktuella runbooken. Mer information finns i [arbeta med flera prenumerationer](../automation-runbook-execution.md#working-with-multiple-subscriptions).

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Scenario: termen känns inte igen som namnet på en cmdlet, funktion, skript

### <a name="issue"></a>Problem

Din Runbook Miss lyckas med ett fel som liknar följande exempel:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Orsak

Det här felet kan inträffa av följande orsaker:

* Modulen som innehåller cmdleten importeras inte till Automation-kontot.
* Modulen som innehåller cmdleten importeras men är inaktuell.

### <a name="resolution"></a>Lösning

Utför en av följande åtgärder för att lösa det här felet. 

* En Azure-modul finns i [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md) för att lära dig hur du uppdaterar dina moduler i ditt Automation-konto.

* För en icke-Azure-modul kontrollerar du att modulen har importer ATS till ditt Automation-konto.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scenario: ett försök gjordes att starta Runbook-jobbet tre gånger, men det gick inte att starta varje gång

### <a name="issue"></a>Problem

Din Runbook Miss lyckas med följande fel:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Orsak

Felet beror på ett av följande problem:

* Minnes gräns. Ett jobb kan Miss förorsakat om det använder mer än 400 MB minne. De dokumenterade gränserna för minne som allokerats till en sandbox finns i [begränsningar för Automation-tjänster](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* Nätverks platser. Azure-sand lådor är begränsade till 1000 samtidiga nätverks platser. Se [begränsningar för Automation-tjänster](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Modulen är inkompatibel. Beroenden för modulen kan vara felaktiga. I det här fallet returnerar din Runbook vanligt vis `Command not found` ett `Cannot bind parameter` eller meddelande.

* Ingen autentisering med Active Directory för sandbox. Din Runbook försökte anropa en körbar fil eller under process som körs i en Azure-sandbox. Det finns inte stöd för att konfigurera Runbooks för autentisering med Azure AD med hjälp av Azure Active Directory Authentication Library (ADAL).

* För mycket undantags data. Din Runbook försökte skriva för mycket undantags data till utdataströmmen.

### <a name="resolution"></a>Lösning

* Minnes gräns, nätverks platser. Föreslagna sätt att arbeta inom minnes gränserna är att dela arbets belastningen mellan flera Runbooks, bearbeta mindre data i minnet, undvika att skriva onödiga utdata från dina runbooks och fundera över hur många kontroll punkter som skrivs in i PowerShell Workflow-Runbooks. Använd Clear-metoden, till exempel `$myVar.clear`, för att rensa variabler och använda `[GC]::Collect` för att köra skräp insamling direkt. De här åtgärderna minskar din Runbooks minnes storlek under körning.

* Modulen är inkompatibel. Uppdatera dina Azure-moduler genom att följa stegen i [så här uppdaterar du Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md).

* Ingen autentisering med Active Directory för sandbox. När du autentiserar till Azure AD med en Runbook kontrollerar du att Azure AD-modulen är tillgänglig i ditt Automation-konto. Se till att ge kör som-kontot de behörigheter som krävs för att utföra de uppgifter som Runbook automatiserar.

  Om din Runbook inte kan anropa en körbar fil eller under process som körs i en Azure-sandbox, använder du runbooken på en [hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybrid Worker begränsas inte av de minnes-och nätverks gränser som Azure-sandbox har.

* För mycket undantags data. Det finns en gräns på jobbets utgående data ström. Se till att din Runbook innesluter anrop till en körbar fil eller under process `try` med `catch` hjälp av och-block. Om åtgärderna genererar ett undantag ska koden skriva meddelandet från undantaget till en Automation-variabel. Den här tekniken förhindrar att meddelandet skrivs in i utdataströmmen för jobb.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scenario: det gick inte att logga in på Azure-kontot

### <a name="issue"></a>Problem

Du får ett av följande fel när du arbetar med `Connect-AzAccount` cmdleten:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Orsak

Felen uppstår om inloggnings namnet är ogiltigt. De kan också inträffa om det användar namn och lösen ord som du använde för att konfigurera inloggnings uppgifterna för Automation inte är giltiga.

### <a name="resolution"></a>Lösning

Ta reda på vad som är fel genom att följa stegen nedan:

1. Kontrol lera att du inte har några specialtecken. De här tecknen innehåller `\@` det tecken i inloggnings namnet för Automation-autentiseringsuppgifter som du använder för att ansluta till Azure.
2. Kontrol lera om du kan använda det användar namn och lösen ord som lagras i Azure Automation autentiseringsuppgiften i din lokala PowerShell ISE-redigerare. Kör följande cmdlets i PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

3. Om autentiseringen Miss lyckas lokalt har du inte konfigurerat dina Azure Active Directory autentiseringsuppgifter korrekt. Läs igenom [autentiseringen till Azure med hjälp av Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) blogg inlägget för att få Azure Active Directorys konto rätt konfigurerat.

4. Om felet verkar vara tillfälligt kan du försöka lägga till logik för återförsök för autentisering för att göra autentiseringen stabilare.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Scenario: felaktig objekt referens vid anrop till Add-AzAccount

### <a name="issue"></a>Problem

Du får det här felet när du `Add-AzAccount`arbetar med, vilket är ett alias `Connect-AzAccount` för cmdleten:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Orsak

Det här felet kan inträffa om runbooken inte gör rätt steg innan du anropar `Add-AzAccount` för att lägga till Automation-kontot. Ett exempel på en av de nödvändiga stegen är att logga in med ett Kör som-konto. Information om vilka åtgärder som ska användas i din Runbook finns i [Runbook-körning i Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scenario: objekt referensen har inte angetts till en instans av ett objekt

### <a name="issue"></a>Problem

Du får följande fel meddelande när du anropar en underordnad `Wait` Runbook med parametern och utdataströmmen innehåller ett objekt:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Orsak

`Start-AzAutomationRunbook`hanterar inte utdataströmmen korrekt om data strömmen innehåller objekt.

### <a name="resolution"></a>Lösning

Vi rekommenderar att du implementerar en avsöknings logik och använder cmdleten [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) för att hämta utdata. Ett exempel på den här logiken definieras nedan.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$jobResults | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Scenario: Runbook Miss lyckas på grund av ett avserialiserat objekt

### <a name="issue"></a>Problem

Din Runbook Miss lyckas med felet:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Orsak

Om din Runbook är ett PowerShell-arbetsflöde lagrar den komplexa objekt i ett avserialiserat format för att bevara ditt Runbook-tillstånd om arbets flödet har pausats.

### <a name="resolution"></a>Lösning

Åtgärda problemet med någon av följande lösningar.

* Om du flyttar komplexa objekt från en cmdlet till en annan kan du omsluta dessa cmdletar i en `InlineScript` aktivitet.
* Skicka det namn eller värde som du behöver från det komplexa objektet i stället för att skicka hela objektet.
* Använd en PowerShell-Runbook i stället för en PowerShell Workflow-Runbook.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Scenario: Runbook-jobbet fungerar inte eftersom allokerad kvot har överskridits

### <a name="issue"></a>Problem

Ditt Runbook-jobb Miss lyckas med felet:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Orsak

Felet uppstår när jobb körningen överskrider den kostnads fria kvoten på 500 minuter för ditt konto. Den här kvoten gäller för alla typer av jobb körnings aktiviteter. Några av dessa uppgifter testar ett jobb, startar ett jobb från portalen, kör ett jobb genom att använda Webhooks eller schemalägga ett jobb som ska köras med hjälp av antingen Azure Portal eller ditt data Center. Läs mer om prissättning för Automation i avsnittet om [automatiserings priser](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Lösning

Om du vill använda mer än 500 minuters bearbetning per månad ändrar du din prenumeration från den kostnads fria nivån till Basic-nivån.

1. Logga in på din Azure-prenumeration.
2. Välj det Automation-konto som ska uppgraderas.
3. Klicka på **Inställningar**och sedan på **priser**.
4. Klicka på **Aktivera** på sidan längst ned för att uppgradera ditt konto till Basic-nivån.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scenario: cmdleten känns inte igen när en Runbook kördes

### <a name="issue"></a>Problem

Ditt Runbook-jobb Miss lyckas med felet:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Orsak

Det här felet beror på att PowerShell-motorn inte kan hitta den cmdlet som du använder i din Runbook. Det är möjligt att modulen som innehåller cmdleten saknas från kontot. det finns en namn konflikt med ett Runbook-namn, eller så finns cmdleten även i en annan modul och automation kan inte matcha namnet.

### <a name="resolution"></a>Lösning

Åtgärda problemet med någon av följande lösningar.

* Kontrol lera att du har angett rätt cmdlet-namn.
* Se till att cmdleten finns i ditt Automation-konto och att det inte finns några konflikter. Du kan kontrol lera om cmdleten finns genom att öppna en Runbook i redigerings läge och söka efter den cmdlet som du vill söka efter i biblioteket eller `Get-Command <CommandName>`köra. När du har verifierat att cmdleten är tillgänglig för kontot och att det inte finns något namn i konflikt med andra cmdletar eller Runbooks, lägger du till cmdleten på arbets ytan och kontrollerar att du använder en giltig parameter uppsättning i din Runbook.
* Om du har en namn konflikt och cmdleten är tillgänglig i två olika moduler, löser du problemet genom att använda det fullständigt kvalificerade namnet för cmdleten. Du kan till exempel använda `ModuleName\CmdletName`.
* Om du kör en Runbook lokalt i en hybrid Worker-grupp kontrollerar du att modulen och cmdleten är installerade på den dator som är värd för Hybrid Worker.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scenario: en tids krävande Runbook slutförs inte

### <a name="issue"></a>Problem

Din Runbook visas i stoppat tillstånd efter att ha körts i 3 timmar. Du kan också få det här felet:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Det här beteendet är avsiktligt i Azure-sand lådor på grund av [rättvis resurs](../automation-runbook-execution.md#fair-share) övervakning av processer inom Azure Automation. Om en process körs längre än tre timmar stoppar den verkliga delningen en Runbook automatiskt. Status för en Runbook som går förbi den verkliga delnings tiden skiljer sig åt efter Runbook-typ. PowerShell-och python-Runbooks anges till stoppad status. PowerShell Workflow-Runbooks är inställda på misslyckade.

### <a name="cause"></a>Orsak

Runbooken kördes över den gräns på 3 timmar som tillåts av rättvis resurs i en Azure-sandbox.

### <a name="resolution"></a>Lösning

En rekommenderad lösning är att köra runbooken på en [hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybrid Worker begränsas inte av den 3 timmars verkliga resurs-Runbook-gränsen som Azure-sandbox har. Runbooks som körs på Hybrid Runbook Worker bör utvecklas för att stödja omstarts beteenden om det uppstår oväntade problem med lokal infrastruktur.

En annan lösning är att optimera runbooken genom att skapa [underordnade Runbooks](../automation-child-runbooks.md). Om din Runbook upprepas genom samma funktion på flera resurser, till exempel i en databas åtgärd på flera databaser, kan du flytta funktionen till en underordnad Runbook. Varje underordnad runbook körs parallellt i en separat process. Det här beteendet minskar den totala tiden som den överordnade runbooken slutförs.

PowerShell-cmdletar som aktiverar det underordnade Runbook-scenariot är:

* [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0). Med den här cmdleten kan du starta en runbook och skicka parametrar till runbooken.

* [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0). Om det finns åtgärder som måste utföras efter att den underordnade Runbook-flödet har slutförts kan du kontrol lera jobb status för varje underordnad i den här cmdleten.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Scenario: status: 400 Felaktig begäran vid anrop till en webhook

### <a name="issue"></a>Problem

När du försöker anropa en webhook för en Azure Automation Runbook får du följande fel meddelande:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Orsak

Webhooken som du försöker anropa är antingen inaktive rad eller har upphört att gälla.

### <a name="resolution"></a>Lösning

Om webhooken är inaktive rad kan du återaktivera webhooken via Azure Portal. Om webhooken har upphört att gälla måste du ta bort och återskapa den. Du kan bara [förnya en webhook](../automation-webhooks.md#renew-webhook) om den inte redan har gått ut.

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scenario: 429: den begärda frekvensen är för stor...

### <a name="issue"></a>Problem

Du får följande fel meddelande när du kör `Get-AzAutomationJobOutput` cmdleten:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Orsak

Felet kan uppstå när jobbets utdata hämtas från en Runbook som har många [utförliga data strömmar](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Lösning

Gör något av följande för att lösa det här felet.

* Redigera runbooken och minska antalet jobb strömmar som den avger.

* Minska antalet strömmar som ska hämtas när cmdleten körs. Om du vill göra detta kan du ange värdet för `Stream` parametern för [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) -cmdlet: en så att endast utgående strömmar hämtas. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Scenario: PowerShell-jobbet Miss lyckas med felet: det går inte att anropa metoden

### <a name="issue"></a>Problem

Du får följande fel meddelande när du startar ett PowerShell-jobb i en Runbook som körs i Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Orsak

Det här felet kan tyda på att Runbooks som körs i en Azure-sandbox inte kan köras i det [fullständiga språk läget](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Lösning

Det finns två sätt att lösa det här felet.

* I stället för att använda [Start-Job](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7)använder du [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) för att starta runbooken.
* Försök att köra runbooken på en Hybrid Runbook Worker.

Mer information om det här beteendet och andra beteenden för Azure Automation runbooks finns i [Runbook-beteende](../automation-runbook-execution.md#runbook-behavior).

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Scenario: Linux Hybrid Runbook Worker tar emot ett lösen ord när en Runbook signeras

### <a name="issue"></a>Problem

Om du `sudo` kör kommandot för ett Linux-hybrid Runbook Worker hämtas en oväntad prompt för ett lösen ord.

### <a name="cause"></a>Orsak

**Nxautomationuser** -kontot för Log Analytics agent för Linux har inte kon figurer ATS korrekt i **sudoers** -filen. Hybrid Runbook Worker behöver lämplig konfiguration av konto behörigheter och andra data så att de kan signera Runbooks på Linux Runbook Worker.

### <a name="resolution"></a>Lösning

* Kontrol lera att den Hybrid Runbook Worker har den körbara filen GnuPG (GPG) på datorn.

* Verifiera konfigurationen av **nxautomationuser** -kontot i **sudoers** -filen. Se [köra Runbooks på en hybrid Runbook Worker](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Scenario: cmdleten fungerar inte i PnP PowerShell-Runbook på Azure Automation

### <a name="issue"></a>Problem

När en Runbook skriver ett PnP-genererat objekt till Azure Automation utdata direkt, kan inte cmdlet-utdata strömma tillbaka till Automation.

### <a name="cause"></a>Orsak

Det här problemet uppstår vanligt vis när Azure Automation bearbetar Runbooks som anropar PnP PowerShell-cmdlets, `add-pnplistitem`till exempel, utan att fånga in retur objekt.

### <a name="resolution"></a>Lösning

Redigera skripten för att tilldela eventuella retur värden till variabler så att-cmdletarna inte försöker skriva hela objekt till standardutdata. Ett skript kan omdirigera utdataströmmen till en cmdlet enligt vad som visas nedan.

```azurecli
  $null = add-pnplistitem
```

Om skriptet parsar utdata för cmdleten måste skriptet lagra utdata i en variabel och manipulera variabeln i stället för att helt enkelt strömma utdata.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-invalid-status-code-forbidden-when-using-key-vault-inside-a-runbook"></a>Scenario: ogiltig status kod "förbjuden" när Key Vault i en Runbook används

### <a name="issue"></a>Problem

När du försöker komma åt Key Vault via en Azure Automation Runbook får du följande fel meddelande:

```error
Operation returned an invalid status code 'Forbidden' 
```

### <a name="cause"></a>Orsak

Möjliga orsaker till det här problemet:

* Använder inget kör som-konto.
* Otillräcklig behörighet.

### <a name="resolution"></a>Lösning

#### <a name="not-using-run-as-account"></a>Kör som-konto används inte

Följ stegen i [steg 5 – Lägg till autentisering för att hantera Azure-resurser](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) för att se till att du använder ett Kör som-konto för att få åtkomst till Key Vault. 

#### <a name="insufficient-permissions"></a>Otillräcklig behörighet

Följ stegen i [Lägg till behörigheter för Key Vault](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) för att säkerställa att ditt kör som-konto har tillräcklig behörighet för att komma åt Key Vault. 

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>Mitt problem visas inte ovan

I avsnitten nedan visas andra vanliga fel som ger stöd för att hjälpa dig att lösa problemet.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Hybrid Runbook Worker-jobb kör inte jobb eller svarar inte

Om du kör jobb på en Hybrid Runbook Worker i stället för i Azure Automation kan du behöva [Felsöka själva hybrid Worker](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook Miss lyckas utan behörighet eller viss variation

Kör som-konton kanske inte har samma behörigheter för Azure-resurser som ditt aktuella konto. Se till att ditt kör som-konto har [åtkomst behörighet till alla resurser](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) som används i skriptet.

### <a name="issues-passing-parameters-into-webhooks"></a>Problem med att skicka parametrar till Webhooks

Hjälp med att skicka parametrar till Webhooks finns i [starta en Runbook från en webhook](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="issues-using-az-modules"></a>Problem med AZ-moduler

Det går inte att använda AZ-moduler och AzureRM-moduler i samma Automation-konto. Mer information finns i [AZ-moduler i Runbooks](https://docs.microsoft.com/azure/automation/az-modules) .

### <a name="inconsistent-behavior-in-runbooks"></a>Inkonsekvent beteende i runbooks

Följ anvisningarna i [Runbook-körningen](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) för att undvika problem med samtidiga jobb, resurser som skapas flera gånger eller annan tids känslig logik i Runbooks.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook Miss lyckas med fel meddelandet ingen behörighet, förbjuden (403) eller viss variation

Kör som-konton kanske inte har samma behörigheter för Azure-resurser som ditt aktuella konto. Se till att ditt kör som-konto har [åtkomst behörighet till alla resurser](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) som används i skriptet.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbooks fungerade men stoppades plötsligt

* Se till att kör som-kontot inte har upphört att gälla. Se [certifierings förnyelse](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Om du använder en [webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) för att starta en Runbook kontrollerar du att webhook inte har upphört att gälla.

### <a name="passing-parameters-into-webhooks"></a>Skicka parametrar till webhooks

Hjälp med att skicka parametrar till Webhooks finns i [starta en Runbook från en webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="using-az-modules"></a>Använda Az-moduler

Det går inte att använda AZ-moduler och AzureRM-moduler i samma Automation-konto. Se [AZ-moduler i Runbooks](https://docs.microsoft.com/azure/automation/az-modules).

### <a name="using-self-signed-certificates"></a>Använda självsignerade certifikat

Information om hur du använder självsignerade certifikat finns i [skapa ett nytt certifikat](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Åtkomst nekad när Azure-sandbox används för en Runbook

Azure sandbox förhindrar åtkomst till alla out-of-process COM-servrar. Ett begränsat program eller en Runbook kan till exempel inte anropa till Windows Management Instrumentation (WMI) eller till tjänsten Windows Installer (MSIServer. exe). Mer information om hur du använder sandbox finns i [Runbook-körning i Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="recommended-documents"></a>Rekommenderade dokument

* [Starta en Runbook i Azure Automation](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Runbook-körning i Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
