---
title: Felsöka Azure Automation-runbookfel
description: Lär dig hur du felsöker och löser problem som kan uppstå med Azure Automation-runbooks.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 5ed25821f606b98bacf2acf3c2c389a8437406fa
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770905"
---
# <a name="troubleshoot-runbook-errors"></a>Felsöka runbook-fel

 I den här artikeln beskrivs de olika runbookfel som kan uppstå och hur du löser dem.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="diagnosing-runbook-issues"></a>Diagnostisera runbook-problem

När du får fel under körning av runbook i Azure Automation kan du använda följande steg för att diagnostisera problemen.

1. **Kontrollera att runbook-skriptet körs på den lokala datorn.** 

    Se [PowerShell-dokument eller](/powershell/scripting/overview) [Python-dokument](https://docs.python.org/3/) för språkreferens- och utbildningsmoduler. Om du kör skriptet lokalt kan du identifiera och lösa vanliga fel, till exempel:

      * Moduler som saknas
      * Syntaxfel
      * Logiska fel

2. **Undersök [runbook-felströmmar](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output).**

    Titta på dessa strömmar för specifika meddelanden och jämför dem med de fel som dokumenteras i den här artikeln.

3. **Kontrollera att dina noder och Automation-arbetsyta har de moduler som krävs.** 

    Om runbooken importerar några moduler kontrollerar du att de är tillgängliga för ditt Automation-konto med hjälp av stegen i [Importmoduler](../shared-resources/modules.md#importing-modules). Uppdatera dina moduler till den senaste versionen genom att följa instruktionerna på [Uppdatera Azure-moduler i Azure Automation](..//automation-update-azure-modules.md). Mer felsökningsinformation finns i [Felsöka moduler](shared-resources.md#modules).

4. **Gör om din runbook är avstängd eller oväntat misslyckas.**

    * [Kontrollera jobbstatus definierar](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) runbook status och några möjliga orsaker.
    * [Lägg till ytterligare utdata](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) i runbooken för att identifiera vad som händer innan runbooken pausas.
    * [Hantera eventuella undantag](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) som genereras av ditt jobb.

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scenario: Kör Login-AzureRMAccount för att logga in

### <a name="issue"></a>Problem

Följande felmeddelande visas när du kör en runbook:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Orsak

Det här felet kan uppstå när du inte använder ett Run As-konto eller så har kontot Kör som upphört att gälla. Se [Hantera Azure Automation Run As-konton](https://docs.microsoft.com/azure/automation/manage-runas-account).

Det här felet har två huvudsakliga orsaker:

* Det finns olika versioner av AzureRM- eller Az-modulen.
* Du försöker komma åt resurser i en separat prenumeration.

### <a name="resolution"></a>Lösning

Om det här felet visas när du har uppdaterat en AzureRM- eller Az-modul bör du uppdatera alla dina moduler till samma version.

Om du försöker komma åt resurser i en annan prenumeration kan du följa stegen nedan för att konfigurera behörigheter.

1. Gå till kontot För körning av automatisering som och kopiera program-ID och tumavtryck.

    ![Kopiera ID och tumavtryck](../media/troubleshoot-runbooks/collect-app-id.png)

1. Gå till prenumerationens åtkomstkontroll där Automation-kontot INTE finns och lägg till en ny rolltilldelning.

    ![Åtkomstkontroll](../media/troubleshoot-runbooks/access-control.png)

1. Lägg till det program-ID som samlats in tidigare. Välj Deltagarbehörigheter.

    ![Lägg till rolltilldelning](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Kopiera namnet på prenumerationen.

1. Du kan nu använda följande runbook-kod för att testa behörigheterna från ditt Automation-konto till den andra prenumerationen. Ersätt `"\<CertificateThumbprint\>"` med det värde som kopierades i steg 1. Ersätt `"\<SubscriptionName\>"` med det värde som kopierades i steg 4.

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

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Scenario: Det gick inte att hitta Azure-prenumerationen

### <a name="issue"></a>Problem

Följande felmeddelande visas när du `Select-AzureSubscription` `Select-AzureRMSubscription`arbetar `Select-AzSubscription` med , eller cmdlet:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Fel

Det här felet kan uppstå om:

* Prenumerationsnamnet är ogiltigt.
* Azure Active Directory-användare som försöker hämta prenumerationsinformationen är inte konfigurerad som administratör för prenumerationen.
* Cmdleten är inte tillgänglig.

### <a name="resolution"></a>Lösning

Följ stegen nedan för att avgöra om du har autentiserats till Azure och har åtkomst till prenumerationen som du försöker välja.

1. Testa det utanför Azure Automation för att se till att skriptet fungerar fristående.
2. Kontrollera att skriptet kör cmdleten [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) innan cmdlet körs. `Select-*`
3. Lägg `Disable-AzContextAutosave –Scope Process` till i början av runbooken. Det här cmdlet-anropet säkerställer att alla autentiseringsuppgifter endast gäller för körningen av den aktuella runbooken.
4. Om det här felmeddelandet fortfarande visas ändrar du koden genom att lägga till parametern `AzContext` för `Connect-AzAccount`och kör sedan koden.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scenario: Autentisering till Azure misslyckades eftersom multifaktorautentisering är aktiverat

### <a name="issue"></a>Problem

Du får följande felmeddelande när du autentiserar till Azure med ditt Azure-användarnamn och lösenord:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Orsak

Om du har multifaktorautentisering på ditt Azure-konto kan du inte använda en Azure Active Directory-användare för att autentisera till Azure. I stället måste du använda ett certifikat eller ett tjänsthuvudnamn för att autentisera.

### <a name="resolution"></a>Lösning

Om du vill använda ett certifikat med Azure classic deployment model cmdlets läser du [Skapa och lägga till ett certifikat för att hantera Azure-tjänster](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Information om hur du använder ett tjänsthuvudnamn med Azure Resource Manager-cmdlets finns i [Skapa tjänsthuvudnamn med Azure-portalen](../../active-directory/develop/howto-create-service-principal-portal.md) och [Autentisera ett tjänsthuvudnamn med Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scenario: Du ser ett fel i jobbströmmarna om get_SerializationSettings-metoden

### <a name="issue"></a>Problem

Följande fel visas i jobbströmmarna för en runbook:

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

Det här felet orsakas av att använda både AzureRM- och Az-modul cmdlets i en runbook. Den inträffar när du importerar Az-modulen innan du importerar AzureRM-modulen.

### <a name="resolution"></a>Lösning

Az- och AzureRM-cmdlets kan inte importeras och användas i samma runbook. Mer information om Az-cmdlets i Azure Automation finns [i Hantera moduler i Azure Automation](../shared-resources/modules.md).

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>Scenario: Runbook misslyckas med felet: En aktivitet avbröts

### <a name="issue"></a>Problem

Runbooken misslyckas med ett fel som liknar följande exempel:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Orsak

Det här felet kan orsakas av att använda inaktuella Azure-moduler.

### <a name="resolution"></a>Lösning

Du kan lösa det här felet genom att uppdatera dina Azure-moduler till den senaste versionen. 

1. Klicka på **Moduler**i ditt Automation-konto och sedan **uppdatera Azure-moduler**. 
2. Uppdateringen tar ungefär 15 minuter. När den är klar kör du runbooken igen som misslyckades.

Mer information om hur du uppdaterar modulerna finns [i Uppdatera Azure-moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scenario: Runbooks misslyckas när det handlar om flera prenumerationer

### <a name="issue"></a>Problem

När du kör runbooks kan runbooken inte hantera Azure-resurser.

### <a name="cause"></a>Orsak

Runbooken använder inte rätt kontext när du kör.

### <a name="resolution"></a>Lösning

Prenumerationskontexten kan gå förlorad när en runbook anropar flera runbooks. Om du vill vara säkra på att prenumerationskontexten skickas till `Start-AzureRmAutomationRunbook` runbooks, `AzureRmContext` låt klientkörningsboken skicka kontexten till cmdleten i parametern. Använd `Disable-AzureRmContextAutosave` cmdleten `Scope` med parametern inställd på `Process` att se till att de angivna autentiseringsuppgifterna endast används för den aktuella runbooken. Mer information finns i [Arbeta med flera prenumerationer](../automation-runbook-execution.md#working-with-multiple-subscriptions).

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

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Scenario: Term som inte känns igen som namnet på en cmdlet, funktion, skript

### <a name="issue"></a>Problem

Runbooken misslyckas med ett fel som liknar följande exempel:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Orsak

Det här felet kan inträffa av följande skäl:

* Modulen som innehåller cmdleten importeras inte till Automation-kontot.
* Modulen som innehåller cmdlet importeras men är föråldrad.

### <a name="resolution"></a>Lösning

Gör en av följande aktiviteter för att lösa det här felet. 

* En Azure-modul finns i [Så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md) för att lära dig hur du uppdaterar dina moduler i ditt Automation-konto.

* För en icke-Azure-modul kontrollerar du att modulen som importeras till ditt Automation-konto.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scenario: Start av runbook-jobb gjordes tre gånger, men det gick inte att starta varje gång

### <a name="issue"></a>Problem

Runbooken misslyckas med följande fel:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Orsak

Det här felet uppstår på grund av något av följande problem:

* Minnesgräns. Ett jobb kan misslyckas om det använder mer än 400 MB minne. De dokumenterade gränserna för minne som allokerats till en sandlåda finns vid [begränsningar för tjänsten Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* Nätverksuttag. Azure sandlådor är begränsade till 1000 samtidiga nätverksuttag. Se [Begränsningar för tjänsten Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Modulen är inte kompatibel. Modulberoenden kanske inte är korrekta. I det här fallet returnerar `Command not found` `Cannot bind parameter` runbooken vanligtvis ett meddelande eller ett meddelande.

* Ingen autentisering med Active Directory för sandlåda. Din runbook försökte anropa en körbar eller underprocess som körs i en Azure-sandlåda. Det går inte att konfigurera runbooks för att autentisera med Azure AD med hjälp av Azure Active Directory Authentication Library (ADAL).

* För mycket undantagsdata. Runbooken försökte skriva för mycket undantagsdata till utdataströmmen.

### <a name="resolution"></a>Lösning

* Minnesgräns, nätverksuttag. Föreslagna sätt att arbeta inom minnesgränserna är att dela upp arbetsbelastningen mellan flera runbooks, bearbeta mindre data i minnet, undvika att skriva onödiga utdata från dina runbooks och överväga hur många kontrollpunkter som skrivs in i dina PowerShell-arbetsflödeskörningsböcker. Använd den tydliga metoden, till exempel `$myVar.clear`, `[GC]::Collect` för att rensa ut variabler och använda för att köra skräpinsamling omedelbart. Dessa åtgärder minskar minnesavtrycket för din runbook under körning.

* Modulen är inte kompatibel. Uppdatera dina Azure-moduler genom att följa stegen i [Så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md).

* Ingen autentisering med Active Directory för sandlåda. När du autentiserar till Azure AD med en runbook kontrollerar du att Azure AD-modulen är tillgänglig i ditt Automation-konto. Var noga med att ge kontot Kör som de behörigheter som krävs för att utföra de uppgifter som runbooken automatiserar.

  Om runbooken inte kan anropa en körbar eller underprocess som körs i en Azure-sandlåda använder du runbooken på en [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybridarbetare begränsas inte av de minnes- och nätverksgränser som Azure-sandlådor har.

* För mycket undantagsdata. Det finns en 1 MB gräns för jobbutdataströmmen. Kontrollera att runbooken omsluter anrop till en `try` `catch` körbar eller underprocess med och blockerar. Om åtgärderna genererar ett undantag ska du låta koden skriva meddelandet från undantaget till en automatiseringsvariabel. Den här tekniken förhindrar att meddelandet skrivs in i jobbutdataströmmen.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scenario: Inloggning till Azure-konto misslyckades

### <a name="issue"></a>Problem

Du får något av följande fel `Connect-AzAccount` när du arbetar med cmdlet:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Orsak

Dessa fel uppstår om referenstillgångsnamnet inte är giltigt. De kan också uppstå om användarnamnet och lösenordet som du använde för att konfigurera tillgången för Automation-autentiseringsuppgifter inte är giltiga.

### <a name="resolution"></a>Lösning

Ta så här för att ta reda på vad som är fel:

1. Se till att du inte har några specialtecken. Dessa tecken `\@` innehåller tecknet i det resursnamn för Automation-autentiseringsuppgifter som du använder för att ansluta till Azure.
2. Kontrollera om du kan använda användarnamnet och lösenordet som lagras i Azure Automation-autentiseringsuppgifterna i din lokala PowerShell ISE-redigerare. Kör följande cmdlets i PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

3. Om autentiseringen misslyckas lokalt har du inte konfigurerat dina Azure Active Directory-autentiseringsuppgifter korrekt. Se [Autentisering till Azure med hjälp av Azure Active Directory-blogginlägg](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) för att få Azure Active Directory-kontot korrekt konfigurerat.

4. Om felet verkar vara tillfälligt kan du prova att lägga till logik för återförsök i autentiseringsrutinen för att göra autentiserar mer robust.

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

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scenario: Objektreferensen har inte angetts för en förekomst av ett objekt

### <a name="issue"></a>Problem

Följande felmeddelande visas när du anropar en `Wait` underordnad runbook med parametern och utdataströmmen innehåller ett objekt:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Orsak

`Start-AzAutomationRunbook`Hanterar inte utdataströmmen korrekt om strömmen innehåller objekt.

### <a name="resolution"></a>Lösning

Vi rekommenderar att du implementerar en avsökningslogik och använder cmdleten [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) för att hämta utdata. Ett exempel på den här logiken definieras nedan.

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

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Scenario: Runbook misslyckas på grund av deserialiserade objekt

### <a name="issue"></a>Problem

Runbook misslyckas med felet:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Orsak

Om runbooken är ett PowerShell-arbetsflöde lagras komplexa objekt i ett avserialiserat format för att spara runbook-tillståndet om arbetsflödet pausas.

### <a name="resolution"></a>Lösning

Använd någon av följande lösningar för att åtgärda problemet.

* Om du rör komplexa objekt från en cmdlet till en annan, `InlineScript` linda dessa cmdlets i en aktivitet.
* Skicka namnet eller värdet som du behöver från det komplexa objektet i stället för att skicka hela objektet.
* Använd en PowerShell-runbook i stället för en PowerShell-arbetsflödeskörningsbok.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Scenario: Körningsjobbet misslyckas eftersom allokerad kvot har överskridits

### <a name="issue"></a>Problem

Körningsjobbet misslyckas med felet:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Orsak

Det här felet uppstår när jobbkörningen överskrider den 500 minuter långa kostnadsfria kvoten för ditt konto. Den här kvoten gäller för alla typer av jobbkörningsuppgifter. Vissa av dessa uppgifter testar ett jobb, startar ett jobb från portalen, kör ett jobb med hjälp av webhooks eller schemalägger ett jobb för att köra med antingen Azure-portalen eller ditt datacenter. Mer information om priser för Automation finns i Priser för [automatisering](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Lösning

Om du vill använda mer än 500 minuters bearbetning per månad ändrar du prenumerationen från den kostnadsfria nivån till basic-nivån.

1. Logga in på din Azure-prenumeration.
2. Välj det Automation-konto som ska uppgraderas.
3. Klicka på **Inställningar**och sedan **pris.**
4. Klicka på **Aktivera** längst ned på sidan om du vill uppgradera kontot till basic-nivån.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scenario: Cmdlet känns inte igen när du kör en runbook

### <a name="issue"></a>Problem

Körningsjobbet misslyckas med felet:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Orsak

Det här felet uppstår när PowerShell-motorn inte hittar den cmdlet du använder i runbooken. Det är möjligt att modulen som innehåller cmdlet saknas från kontot, det finns en namnkonflikt med ett runbook-namn eller att cmdleten också finns i en annan modul och Automation kan inte matcha namnet.

### <a name="resolution"></a>Lösning

Använd någon av följande lösningar för att åtgärda problemet.

* Kontrollera att du har angett cmdletnamnet korrekt.
* Kontrollera att cmdlet finns i ditt Automation-konto och att det inte finns några konflikter. Om du vill kontrollera om cmdleten finns öppnar du en runbook i redigeringsläge och `Get-Command <CommandName>`söker efter den cmdlet som du vill söka efter i biblioteket eller kör . När du har validerat att cmdlet är tillgängligt för kontot och att det inte finns några namnkonflikter med andra cmdletar eller runbooks lägger du till cmdlet på arbetsytan och kontrollerar att du använder en giltig parameteruppsättning i runbooken.
* Om du har en namnkonflikt och cmdleten är tillgänglig i två olika moduler löser du problemet med det fullständigt kvalificerade namnet för cmdleten. Du kan till exempel använda `ModuleName\CmdletName`.
* Om du kör runbooken lokalt i en hybridarbetsgrupp kontrollerar du att modulen och cmdleten är installerade på datorn som är värd för hybridarbetaren.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scenario: En tidskrävande runbook misslyckas med att slutföra

### <a name="issue"></a>Problem

Runbooken visas i stoppat tillstånd efter att ha körts i 3 timmar. Det här felet kan också visas:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Det här beteendet är avsiktligt i Azure-sandlådor på grund av [rättvis resursövervakning](../automation-runbook-execution.md#fair-share) av processer inom Azure Automation. Om en process körs längre än tre timmar stoppar beskärda delar automatiskt en runbook. Statusen för en runbook som går förbi tidsgränsen för rättvis resurs skiljer sig åt med runbook-typ. PowerShell- och Python-runbooks är inställda på en stoppad status. PowerShell Workflow runbooks är inställda på Misslyckades.

### <a name="cause"></a>Orsak

Runbooken körde över 3-timmarsgränsen som tillåts av en rättvis resurs i en Azure-sandlåda.

### <a name="resolution"></a>Lösning

En rekommenderad lösning är att köra runbook på en [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybridarbetare begränsas inte av den 3-timmars befästdelningskörningsgräns som Azure sandlådor har. Runbooks som körs på Hybrid Runbook Workers bör utvecklas för att stödja omstartsbeteenden om det finns oväntade lokala infrastrukturproblem.

En annan lösning är att optimera runbook genom att skapa [underordnade runbooks](../automation-child-runbooks.md). Om runbooken loopar igenom samma funktion på flera resurser, till exempel i en databasåtgärd i flera databaser, kan du flytta funktionen till en underordnad runbook. Varje underordnad runbook körs parallellt i en separat process. Detta minskar den totala tiden för den överordnade runbooken att slutföra.

De PowerShell-cmdlets som aktiverar scenariot för den underordnade runbooken är:

* [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0). Med den här cmdleten kan du starta en runbook och skicka parametrar till runbooken.

* [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0). Om det finns åtgärder som måste utföras när den underordnade runbooken har slutförts, kan du med den här cmdleten kontrollera jobbstatusen för varje underordnad.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Scenario: Status: 400 Felaktig begäran när du ringer en webhook

### <a name="issue"></a>Problem

NÃ¤r du fÃ¶rsÃ¶ks fÃ¶r att anropa en webhook fÃ¶r en Azure Automation-runbook visas fÃ¶10fel:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Orsak

Den webhook som du försöker ringa är antingen inaktiverad eller har upphört att gälla.

### <a name="resolution"></a>Lösning

Om webhooken är inaktiverad kan du återaktivera webhooken via Azure-portalen. Om webhooken har upphört att gälla måste du ta bort och sedan återskapa den. Du kan bara [förnya en webhook](../automation-webhooks.md#renew-webhook) om den inte redan har upphört att gälla.

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scenario: 429: Begäran är för närvarande för stor ...

### <a name="issue"></a>Problem

Följande felmeddelande visas när cmdlet körs: `Get-AzAutomationJobOutput`

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Orsak

Det här felet kan uppstå när du hämtar jobbutdata från en runbook som har många [utförliga strömmar](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Lösning

Gör något av följande för att lösa det här felet.

* Redigera runbooken och minska antalet jobbströmmar som den avger.

* Minska antalet strömmar som ska hämtas när cmdlet körs. För att göra detta kan du `Stream` ange värdet för parametern för [cmdlet Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) för att bara hämta utdataströmmar. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Scenario: PowerShell-jobbet misslyckas med fel: Det går inte att anropa metoden

### <a name="issue"></a>Problem

Följande felmeddelande visas när du startar ett PowerShell-jobb i en runbook som körs i Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Orsak

Det här felet kan tyda på att runbooks som körs i en Azure-sandlåda inte kan köras i [fullständigt språkläge](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Lösning

Det finns två sätt att lösa det här felet.

* I stället för att använda [Start-Job](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7)använder du [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) för att starta runbooken.
* Prova att köra runbooken på en Hybrid Runbook Worker.

Mer information om det här beteendet och andra beteenden i Azure Automation-runbooks finns i [Runbook-beteende](../automation-runbook-execution.md#runbook-behavior).

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Scenario: Linux Hybrid Runbook Worker får en uppmaning om ett lösenord när du signerar en runbook

### <a name="issue"></a>Problem

Om `sudo` du kör kommandot för en Linux Hybrid Runbook Worker hämtas en oväntad fråga om ett lösenord.

### <a name="cause"></a>Orsak

**Nxautomationuser-kontot** för Log Analytics-agenten för Linux är inte korrekt konfigurerat i **sudoers-filen.** Hybrid Runbook Worker behöver lämplig konfiguration av kontobehörigheter och andra data så att den kan signera runbooks på Linux Runbook Worker.

### <a name="resolution"></a>Lösning

* Kontrollera att Hybrid Runbook Worker har GnuPG (GPG) körbar på maskinen.

* Verifiera konfigurationen av **nxautomationuser-kontot** i **sudoers-filen.** Se [Köra runbooks på en hybridkörningsarbetare](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Scenario: Cmdlet misslyckas i PnP PowerShell runbook på Azure Automation

### <a name="issue"></a>Problem

När en runbook skriver ett PnP PowerShell-genererat objekt till Azure Automation-utdata direkt, kan cmdlet-utdata inte strömma tillbaka till Automation.

### <a name="cause"></a>Orsak

Det här problemet uppstår oftast när Azure Automation bearbetar runbooks som anropar PnP PowerShell-cmdlets, `add-pnplistitem`till exempel, utan att fånga returobjekten.

### <a name="resolution"></a>Lösning

Redigera skripten för att tilldela eventuella returvärden till variabler så att cmdlets inte försöker skriva hela objekt till standardutdata. Ett skript kan omdirigera utdataströmmen till en cmdlet enligt nedan.

```azurecli
  $null = add-pnplistitem
```

Om skriptet tolkar cmdlet-utdata måste skriptet lagra utdata i en variabel och ändra variabeln i stället för att bara strömma utdata.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-invalid-status-code-forbidden-when-using-key-vault-inside-a-runbook"></a>Scenario: Ogiltig statuskod "Förbjudet" när du använder Key Vault i en runbook

### <a name="issue"></a>Problem

När du försöker komma åt Key Vault via en Azure Automation-runbook får du följande felmeddelande:

```error
Operation returned an invalid status code 'Forbidden' 
```

### <a name="cause"></a>Orsak

Möjliga orsaker till det här problemet:

* Använder inte ett Run As-konto.
* Otillräckliga behörigheter.

### <a name="resolution"></a>Lösning

#### <a name="not-using-run-as-account"></a>Använder inte konto Run As

Följ stegen i [steg 5 – Lägg till autentisering för att hantera Azure-resurser](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) för att säkerställa att du använder ett Run As-konto för att komma åt Key Vault. 

#### <a name="insufficient-permissions"></a>Otillräckliga behörigheter

Följ stegen vid [Lägg till behörigheter i Key Vault](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) för att säkerställa att ditt Run As-konto har tillräcklig behörighet för att komma åt Key Vault. 

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>Mitt problem visas inte ovan

I avsnitten nedan visas andra vanliga fel och dokumentation som innehåller stöd som hjälper dig att lösa problemet.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Hybrid Runbook Worker-jobb kör inte jobb eller svarar inte

Om du kör jobb på en Hybrid Runbook Worker i stället för i Azure Automation kan du behöva [felsöka hybridarbetaren själv](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook misslyckas med ingen behörighet eller någon variation

Kör som konton kanske inte har samma behörigheter mot Azure-resurser som ditt nuvarande konto. Kontrollera att ditt Run As-konto har [behörighet att komma åt alla resurser](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) som används i skriptet.

### <a name="issues-passing-parameters-into-webhooks"></a>Problem som skickar parametrar till webhooks

Mer information om hur du skickar parametrar till webhooks finns i [Starta en runbook från en webhook](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="issues-using-az-modules"></a>Problem med Az-moduler

Det går inte att använda Az-moduler och AzureRM-moduler i samma Automation-konto. Se [Az-moduler i runbooks](https://docs.microsoft.com/azure/automation/az-modules) för mer information.

### <a name="inconsistent-behavior-in-runbooks"></a>Inkonsekvent beteende i runbooks

Följ vägledningen i [Runbook-körningen](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) för att undvika problem med samtidiga jobb, resurser som skapas flera gånger eller annan tidskänslig logik i runbooks.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook misslyckas med felet Ingen behörighet, Förbjuden (403) eller någon variant

Kör som konton kanske inte har samma behörigheter mot Azure-resurser som ditt nuvarande konto. Kontrollera att ditt Run As-konto har [behörighet att komma åt alla resurser](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) som används i skriptet.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbooks fungerade men stoppades plötsligt

* Kontrollera att kontot Kör som inte har upphört att gälla. Se [förnyelse av certifieringen](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Om du använder en [webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) för att starta en runbook kontrollerar du att webhook inte har upphört att gälla.

### <a name="passing-parameters-into-webhooks"></a>Skicka parametrar till webhooks

Mer information om hur du skickar parametrar till webhooks finns i [Starta en runbook från en webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="using-az-modules"></a>Använda Az-moduler

Det går inte att använda Az-moduler och AzureRM-moduler i samma Automation-konto. Se [Az-moduler i runbooks](https://docs.microsoft.com/azure/automation/az-modules).

### <a name="using-self-signed-certificates"></a>Använda självsignerade certifikat

Information om hur du använder självsignerade certifikat finns i [Skapa ett nytt certifikat](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Åtkomst nekad när du använder Azure sandbox för en runbook

Azure-sandlådan förhindrar åtkomst till alla COM-servrar utanför processen. Ett program eller en runbook i begränsat läge kan till exempel inte anropa till WMI (Windows Management Instrumentation) eller till Windows Installer -tjänsten (msiserver.exe). Mer information om hur du använder sandlådan finns [i Körning av Runbook i Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="recommended-documents"></a>Rekommenderade dokument

* [Starta en runbook i Azure Automation](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Runbook-körning i Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Forum](https://azure.microsoft.com/support/forums/).
* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du lämna in en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.
