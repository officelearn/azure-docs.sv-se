---
title: Felsöka fel med Azure Automation runbooks
description: Lär dig hur du felsöker problem med Azure Automation runbooks
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 759422ea8c327ae67278354217dac4c60b32f7a9
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850324"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Felsöka fel med Runbooks

I den här artikeln beskrivs vanliga problem med Runbooks och hur du löser dem.

## <a name="steps-to-troubleshoot-runbooks"></a>Steg för att felsöka Runbooks

När du har problem med att köra Runbooks i Azure Automation kan du använda följande steg för att diagnostisera problemet.

1. **Se till att Runbook-skriptet körs på den lokala datorn:**  Se PowerShell- [dokumenten](/powershell/scripting/overview) eller [python](https://docs.python.org/3/) -dokumenten för språk referens-och inlärnings moduler.

   Att köra skriptet lokalt kan identifiera och lösa vanliga fel, till exempel:

   - **Moduler som saknas**
   - **Syntaxfel**
   - **Logic-fel**

2. **Se till att noderna och automation-arbetsytan har de moduler som krävs:** Om din Runbook importerar några moduler, se till att de är tillgängliga i ditt Automation-konto med hjälp av stegen som anges i [importera moduler](../shared-resources/modules.md#import-modules). Mer information finns i [Felsöka moduler](shared-resources.md#modules).

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Autentiseringsfel vid arbete med Azure Automation runbooks

### <a name="login-azurerm"></a>Situationen Kör login-AzureRMAccount för inloggning

#### <a name="issue"></a>Problem

Du får följande fel meddelande när du kör en Runbook:

```error
Run Login-AzureRMAccount to login.
```

#### <a name="cause"></a>Orsak

Det här felet har två primära orsaker:

* Olika versioner av AzureRM-moduler.
* Du försöker komma åt resurser i en separat prenumeration.

#### <a name="resolution"></a>Lösning

Om du får det här felet när du har uppdaterat en AzureRM-modul bör du uppdatera alla dina AzureRM-moduler till samma version.

Om du försöker komma åt resurser i en annan prenumeration kan du följa stegen nedan för att konfigurera behörigheter.

1. Gå till Automation-kontots kör som-konto och kopiera program-ID och tumavtryck.
  ![Kopiera program-ID och tumavtryck](../media/troubleshoot-runbooks/collect-app-id.png)
1. Gå till prenumerationens Access Control där Automation-kontot inte finns och Lägg till en ny roll tilldelning.
  ![Åtkomstkontroll](../media/troubleshoot-runbooks/access-control.png)
1. Lägg till det program-ID som du samlade in i föregående steg. Välj deltagar behörighet.
   ![Lägg till roll tilldelning](../media/troubleshoot-runbooks/add-role-assignment.png)
1. Kopiera prenumerations namnet för nästa steg.
1. Du kan nu använda följande Runbook-kod för att testa behörigheterna från ditt Automation-konto till den andra prenumerationen.

    Ersätt "\<CertificateThumbprint\>" med värdet som du kopierade i steg #1 och värdet "\<SubscriptionName\>" som du kopierade i steg #4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzureRmSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzureRmSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzureRmContext $subscription
        Write-Output $subscription.Name
    }
    ```

### <a name="sign-in-failed"></a>Situationen Det gick inte att logga in på Azure-kontot

#### <a name="issue"></a>Problem

Du får följande fel meddelande när du arbetar med `Add-AzureAccount` cmdletarna eller `Connect-AzureRmAccount` :

```error
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Orsak

Det här felet uppstår om inloggnings namnet för autentiseringsuppgiften inte är giltigt. Det här felet kan också inträffa om det användar namn och lösen ord som du använde för att konfigurera inloggnings uppgifterna för Automation inte är giltiga.

#### <a name="resolution"></a>Lösning

Ta reda på vad som är fel genom att följa stegen nedan:

1. Kontrol lera att du inte har några specialtecken. De här tecknen innehåller **\@** det tecken i inloggnings namnet för Automation-autentiseringsuppgifter som du använder för att ansluta till Azure.
2. Kontrol lera att du kan använda det användar namn och lösen ord som lagras i Azure Automation autentiseringsuppgiften i din lokala PowerShell ISE-redigerare. Du kan kontrol lera att användar namnet och lösen ordet är korrekta genom att köra följande cmdlets i PowerShell ISE:

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Om autentiseringen Miss lyckas lokalt innebär det att du inte har konfigurerat dina Azure Active Directory autentiseringsuppgifter korrekt. Läs om hur du [autentiserar till Azure med hjälp av Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) blogg inlägg för att få Azure Active Directorys konto rätt konfigurerat.

4. Om det ser ut som ett tillfälligt fel kan du försöka lägga till logik för återförsök för autentisering för att göra autentiseringen stabilare.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
   $LogonAttempt++
   # Logging in to Azure...
   $connectionResult = Connect-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $servicePrincipalConnection.TenantId `
      -ApplicationId $servicePrincipalConnection.ApplicationId `
      -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

   Start-Sleep -Seconds 30
   }
   ```

### <a name="unable-to-find-subscription"></a>Situationen Det gick inte att hitta Azure-prenumerationen

#### <a name="issue"></a>Problem

Du får följande fel meddelande när du arbetar med `Select-AzureSubscription` cmdletarna eller `Select-AzureRmSubscription` :

```error
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Fel

Det här felet kan inträffa om:

* Prenumerations namnet är inte giltigt

* Den Azure Active Directory användare som försöker hämta prenumerations informationen har inte kon figurer ATS som administratör för prenumerationen.

#### <a name="resolution"></a>Lösning

Vidta följande steg för att avgöra om du har autentiserat till Azure och har åtkomst till den prenumeration som du försöker välja:

1. Testa skriptet utanför Azure Automation för att se till att det fungerar fristående.
2. Kontrol lera att du kör `Add-AzureAccount` cmdleten innan du `Select-AzureSubscription` kör cmdleten.
3. Lägg `Disable-AzureRmContextAutosave –Scope Process` till i början av din Runbook. Den här cmdleten säkerställer att alla autentiseringsuppgifter endast gäller för körningen av den aktuella runbooken.
4. Om det här fel meddelandet fortfarande visas ändrar du koden genom att lägga till parametern **AzureRmContext** efter `Add-AzureAccount` cmdleten och sedan köra koden.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>Situationen Autentisering till Azure misslyckades eftersom Multi-Factor Authentication har Aktiver ATS

#### <a name="issue"></a>Problem

Du får följande fel när du autentiserar till Azure med ditt användar namn och lösen ord för Azure:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Orsak

Om du har Multi-Factor Authentication på ditt Azure-konto kan du inte använda en Azure Active Directory användare för att autentisera till Azure. I stället måste du använda ett certifikat eller ett huvud namn för tjänsten för att autentisera till Azure.

#### <a name="resolution"></a>Lösning

Om du vill använda ett certifikat med de klassiska cmdletarna för distributions modellen i Azure, se [skapa och lägga till ett certifikat för att hantera Azure-tjänster.](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Om du vill använda ett huvud namn för tjänsten med Azure Resource Manager-cmdletar, se [skapa tjänstens huvud namn med Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) och [autentisera ett huvud namn för tjänsten med Azure Resource Manager.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="common-errors-when-working-with-runbooks"></a>Vanliga fel när du arbetar med Runbooks

### <a name="child-runbook-object"></a>Underordnad Runbook returnerar fel när utdataströmmen innehåller objekt i stället för enkla data typer

#### <a name="issue"></a>Problem

Du får följande fel meddelande när du anropar en underordnad `-Wait` Runbook med växeln och utdataströmmen innehåller ett objekt:

```error
Object reference not set to an instance of an object
```

#### <a name="cause"></a>Orsak

Det finns ett känt problem där [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) inte hanterar utdataströmmen korrekt om den innehåller objekt.

#### <a name="resolution"></a>Lösning

För att lösa detta rekommenderas att du i stället implementerar en avsöknings logik och använder [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) -cmdlet: en för att hämta utdata. Ett exempel på den här logiken definieras i följande exempel.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

### <a name="get-serializationsettings"></a>Situationen Du ser ett fel i jobb strömmar om metoden get_SerializationSettings

#### <a name="issue"></a>Problem

Du ser i fel i dina jobb strömmar för en Runbook med följande meddelande:

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Orsak

Det här felet orsakas av både AzureRM-och AZ-cmdletar i en Runbook. Det inträffar när du importerar `Az` innan du `AzureRM`importerar.

#### <a name="resolution"></a>Lösning

AZ-och AzureRM-cmdletar kan inte importeras och används i samma Runbook, för att lära dig mer om AZ-stöd i Azure Automation finns [stöd för AZ-modulen i Azure Automation](../az-modules.md).

### <a name="task-was-cancelled"></a>Situationen Runbooken Miss lyckas med felet: En uppgift avbröts

#### <a name="issue"></a>Problem

Din Runbook Miss lyckas med ett fel som liknar följande exempel:

```error
Exception: A task was canceled.
```

#### <a name="cause"></a>Orsak

Det här felet kan orsakas av att inaktuella Azure-moduler används.

#### <a name="resolution"></a>Lösning

Det här felet kan lösas genom att uppdatera dina Azure-moduler till den senaste versionen.

I ditt Automation-konto klickar du på **moduler**och sedan på **Uppdatera Azure-moduler**. Uppdateringen tar ungefär 15 minuter efter att du har slutfört körningen av den Runbook som misslyckades. Mer information om hur du uppdaterar dina moduler finns [i Uppdatera Azure-moduler i Azure Automation](../automation-update-azure-modules.md).

### <a name="runbook-auth-failure"></a>Situationen Runbooks fungerar inte vid hantering av flera prenumerationer

#### <a name="issue"></a>Problem

När Runbooks körs med `Start-AzureRmAutomationRunbook`, kan inte Runbook Hantera Azure-resurser.

#### <a name="cause"></a>Orsak

Runbooken använder inte rätt kontext vid körning.

#### <a name="resolution"></a>Lösning

När du arbetar med flera prenumerationer kan prenumerations kontexten försvinna när du anropar Runbooks. För att säkerställa att prenumerations kontexten skickas till Runbooks lägger du till `AzureRmContext` parametern i cmdleten och skickar kontexten till den. Vi rekommenderar också att du använder `Disable-AzureRmContextAutosave` -cmdleten med **process** omfånget för att säkerställa att de autentiseringsuppgifter du använder bara används för den aktuella runbooken.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

### <a name="not-recognized-as-cmdlet"></a>Situationen Runbooken Miss lyckas på grund av en cmdlet som saknas

#### <a name="issue"></a>Problem

Din Runbook Miss lyckas med ett fel som liknar följande exempel:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Orsak

Detta fel kan inträffa baserat på någon av följande orsaker:

* Modulen som innehåller cmdleten har inte importer ATS till Automation-kontot
* Modulen som innehåller cmdleten importeras men är inaktuell

#### <a name="resolution"></a>Lösning

Det här felet kan lösas genom att utföra en av följande uppgifter:

Om modulen är en Azure-modul läser [du så här uppdaterar du Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md) för att lära dig hur du uppdaterar dina moduler i ditt Automation-konto.

Om det är en separat modul kontrollerar du att modulen har importer ATS till ditt Automation-konto.

### <a name="job-attempted-3-times"></a>Situationen Ett försök gjordes att starta Runbook-jobbet tre gånger, men det kunde inte startas varje gång

#### <a name="issue"></a>Problem

Din Runbook Miss lyckas med felet:

```error
The job was tried three times but it failed
```

#### <a name="cause"></a>Orsak

Det här felet uppstår på grund av något av följande problem:

* Minnes gräns. Dokumenterade gränser för hur mycket minne som allokeras till en sandbox finns i [begränsningar för Automation-tjänster](../../azure-subscription-service-limits.md#automation-limits). Ett jobb kan Miss lyckas om det använder mer än 400 MB minne.

* Nätverks platser. Azure-sand lådor är begränsade till 1000 samtidiga nätverks platser enligt beskrivningen i [begränsningar för Automation-tjänster](../../azure-subscription-service-limits.md#automation-limits).

* Modulen är inkompatibel. Det här felet kan inträffa om beroenden i en modul inte är korrekt och om de inte är det returnerar runbooken normalt meddelandet "kommandot hittades inte" eller "det går inte att binda parameter".

* Din Runbook försökte anropa en körbar fil eller under process i en Runbook som körs i en Azure-sandbox. Det här scenariot stöds inte i sand boxar i Azure.

* Din Runbook försökte skriva för mycket undantags data till utdataströmmen.

#### <a name="resolution"></a>Lösning

Någon av följande lösningar löser problemet:

* Föreslagna metoder för att arbeta inom minnes gränsen är att dela upp arbets belastningen mellan flera Runbooks, inte att bearbeta lika mycket data i minnet, inte skriva onödiga utdata från dina runbooks eller fundera över hur många kontroll punkter du skriver till ditt PowerShell-arbetsflöde Runbooks. Du kan använda metoden clear, till exempel `$myVar.clear()` för att rensa variabeln och använda `[GC]::Collect()` för att köra skräp insamling direkt. De här åtgärderna minskar din Runbooks minnes storlek under körning.

* Uppdatera dina Azure-moduler genom att följa stegen så här [uppdaterar du Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md).

* En annan lösning är att köra runbooken på en [hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybrid Worker begränsas inte av de minnes-och nätverks gränser som Azure-sand lådorna är.

* Om du behöver anropa en process (till exempel. exe eller under process. Call) i en Runbook måste du köra runbooken på en [hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

* Det finns en gräns på jobbets utgående data ström. Se till att du omger anrop till en körbar fil eller under process i ett try/catch-block. Om de genererar ett undantag skriver du meddelandet från det undantaget till en Automation-variabel. Detta gör att det inte kan skrivas till utdataström för jobb.

### <a name="fails-deserialized-object"></a>Situationen Runbook fungerar inte på grund av avserialiserat objekt

#### <a name="issue"></a>Problem

Din Runbook Miss lyckas med felet:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Orsak

Om din Runbook är ett PowerShell-arbetsflöde lagrar den komplexa objekt i ett avserialiserat format för att bevara ditt Runbook-tillstånd om arbets flödet har pausats.

#### <a name="resolution"></a>Lösning

Följande tre lösningar löser problemet:

* Om du flyttar komplexa objekt från en cmdlet till en annan omsluter du dessa cmdletar i en InlineScript.
* Skicka det namn eller värde som du behöver från det komplexa objektet i stället för att skicka hela objektet.
* Använd en PowerShell-Runbook i stället för en PowerShell Workflow-Runbook.

### <a name="runbook-fails"></a>Situationen Min Runbook fungerar inte, men fungerar när den körs lokalt

#### <a name="issue"></a>Problem

Skriptet fungerar inte när det körs som en Runbook, men fungerar när det körs lokalt.

#### <a name="cause"></a>Orsak

Skriptet kan Miss lyckas när den körs som en Runbook av någon av följande orsaker:

* Autentiseringsproblem
* Obligatoriska moduler importeras inte eller är inaktuella.
* Skriptet kan uppmanas att ange användar interaktion.
* Vissa moduler gör antaganden om bibliotek som finns på Windows-datorer. De här biblioteken kanske inte finns i ett begränsat läge.
* Vissa moduler är beroende av en .NET-version som skiljer sig från den som är tillgänglig i sand boxen.

#### <a name="resolution"></a>Lösning

Följande lösningar kan åtgärda det här problemet:

* Verifiera att du autentiseras korrekt [på Azure](../manage-runas-account.md).
* Se till att dina [Azure-moduler importeras och är uppdaterade](../automation-update-azure-modules.md).
* Kontrol lera att ingen av dina cmdletar efterfrågar information. Det här beteendet stöds inte i Runbooks.
* Kontrol lera om något som ingår i modulen har ett beroende av något som inte ingår i modulen.
* Azure-sandbox använder .NET Framework 4.7.2, om en modul använder en senare version fungerar den inte. I det här fallet bör du använda en [hybrid Runbook Worker](../automation-hybrid-runbook-worker.md)

Om ingen av dessa lösningar löser din problemReview innehåller [jobb loggar](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för detaljerad information i till varför din Runbook kan ha misslyckats.

### <a name="quota-exceeded"></a>Situationen Runbook-jobbet misslyckades eftersom den tilldelade kvoten överskreds

#### <a name="issue"></a>Problem

Ditt Runbook-jobb Miss lyckas med felet:

```error
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Orsak

Felet uppstår när jobb körningen överskrider den kostnads fria kvoten på 500 minuter för ditt konto. Den här kvoten gäller för alla typer av jobb körnings aktiviteter. Vissa av dessa uppgifter kan vara att testa ett jobb, starta ett jobb från portalen, köra ett jobb genom att använda Webhooks eller schemalägga ett jobb som ska köras med hjälp av antingen Azure Portal eller i ditt data Center. Läs mer om prissättning för Automation i avsnittet om [automatiserings priser](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Lösning

Om du vill använda mer än 500 minuters bearbetning per månad måste du ändra din prenumeration från den kostnads fria nivån till Basic-nivån. Du kan uppgradera till nivån Basic genom att utföra följande steg:

1. Logga in till din Azure-prenumeration
2. Välj det Automation-konto som du vill uppgradera
3. Klicka på **Inställningar** > **prissättning**.
4. Klicka på **Aktivera** på sidan längst ned för att uppgradera ditt konto till **Basic** -nivån.

### <a name="cmdlet-not-recognized"></a>Situationen Cmdleten känns inte igen när en Runbook kördes

#### <a name="issue"></a>Problem

Ditt Runbook-jobb Miss lyckas med felet:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Orsak

Det här felet beror på att PowerShell-motorn inte kan hitta den cmdlet som du använder i din Runbook. Det här felet kan bero på att modulen som innehåller cmdleten saknas från kontot. det finns en namn konflikt med ett Runbook-namn, eller så finns cmdleten även i en annan modul och automation kan inte matcha namnet.

#### <a name="resolution"></a>Lösning

Någon av följande lösningar löser problemet:

* Kontrol lera att du har angett rätt cmdlet-namn.
* Kontrol lera att cmdleten finns i ditt Automation-konto och att det inte finns några konflikter. Du kan kontrol lera om cmdleten finns genom att öppna en Runbook i redigerings läge och söka efter den cmdlet som du vill söka efter i biblioteket `Get-Command <CommandName>`eller köra. När du har verifierat att cmdleten är tillgänglig för kontot och att det inte finns något namn i konflikt med andra cmdletar eller Runbooks, lägger du till den på arbets ytan och kontrollerar att du använder en giltig parameter uppsättning i din Runbook.
* Om du har en namn konflikt och cmdleten är tillgänglig i två olika moduler kan du lösa det här problemet genom att använda det fullständigt kvalificerade namnet för cmdleten. Du kan till exempel använda **ModuleName\CmdletName**.
* Om du kör en Runbook lokalt i en hybrid Worker-grupp kontrollerar du att modulen och cmdleten är installerad på den dator som är värd för Hybrid Worker.

### <a name="long-running-runbook"></a>Situationen En tids krävande Runbook slutförs inte

#### <a name="issue"></a>Problem

Din Runbook visas i **stoppat** tillstånd efter att ha körts i 3 timmar. Du kan också få felet:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Det här beteendet är avsiktligt i Azure-sand lådor på grund av "god resurs" övervakning av processer inom Azure Automation. Om den körs längre än tre timmar stoppar en egen resurs automatiskt en Runbook. Status för en Runbook som infaller efter den verkliga delnings tids gränsen skiljer sig åt efter Runbook-typ. PowerShell-och python-Runbooks anges till **stoppad** status. PowerShell Workflow-Runbooks är inställdapå misslyckade.

#### <a name="cause"></a>Orsak

Runbooken kördes över den 3 timmars gräns som tillåts av rättvis resurs i en Azure-sandbox.

#### <a name="resolution"></a>Lösning

En rekommenderad lösning är att köra runbooken på en [hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

Hybrid Worker begränsas inte av den [verkliga resursen](../automation-runbook-execution.md#fair-share) 3 timmes Runbook-begränsningen som Azure-Sandbox är. Runbooks som körs i hybrid Runbook Worker bör utvecklas för att stödja omstarts beteenden om det uppstår oväntade problem med lokal infrastruktur.

Ett annat alternativ är att optimera runbooken genom att skapa [underordnade Runbooks](../automation-child-runbooks.md). Om din Runbook upprepas genom samma funktion på flera resurser, till exempel en databas åtgärd på flera databaser, kan du flytta den funktionen till en underordnad Runbook. Var och en av dessa underordnade Runbooks körs parallellt i separata processer. Det här beteendet minskar den totala tiden som den överordnade runbooken slutförs.

PowerShell-cmdletar som aktiverar det underordnade Runbook-scenariot är:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) – med denna cmdlet kan du starta en Runbook och skicka parametrar till Runbook

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) – om det finns åtgärder som måste utföras efter att den underordnade Runbook-flödet har slutförts kan du kontrol lera jobb status för varje underordnad i denna cmdlet.

### <a name="expired webhook"></a>Situationen Status: 400 Felaktig begäran vid anrop till en webhook

#### <a name="issue"></a>Problem

När du försöker anropa en webhook för en Azure Automation Runbook får du följande fel meddelande:

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>Orsak

Webhooken som du försöker anropa är antingen inaktive rad eller har upphört att gälla.

#### <a name="resolution"></a>Lösning

Om webhooken är inaktive rad kan du återaktivera webhooken via Azure Portal. När en webhook har upphört att gälla måste webhooken tas bort och återskapas. Du kan bara [förnya en webhook](../automation-webhooks.md#renew-webhook) om den inte redan har gått ut.

### <a name="429"></a>Situationen 429: Begärandehastigheten är för närvarande för hög. Försök igen

#### <a name="issue"></a>Problem

Du får följande fel meddelande när du kör `Get-AzureRmAutomationJobOutput` cmdleten:

```error
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>Orsak

Det här felet kan inträffa när jobbets utdata hämtas från en Runbook som har många [utförliga data strömmar](../automation-runbook-output-and-messages.md#verbose-stream).

#### <a name="resolution"></a>Lösning

Det finns två sätt att lösa det här felet:

* Redigera runbooken och minska antalet jobb strömmar som den avger.
* Minska antalet strömmar som ska hämtas när cmdleten körs. Om du vill följa det här beteendet kan `-Stream Output` du ange parametern `Get-AzureRmAutomationJobOutput` till cmdlet: en för att bara hämta utgående strömmar. 

### <a name="cannot-invoke-method"></a>Situationen PowerShell-jobb Miss lyckas med felet: Det går inte att anropa metoden

#### <a name="issue"></a>Problem

Du får följande fel meddelande när du startar ett PowerShell-jobb i en Runbook som körs i Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

#### <a name="cause"></a>Orsak

Det här felet kan inträffa när du startar ett PowerShell-jobb i en Runbook som körs i Azure. Detta kan inträffa på grund av att Runbooks kördes i en Azure-sandbox kanske inte körs i det [fullständiga språk läget](/powershell/module/microsoft.powershell.core/about/about_language_modes)).

#### <a name="resolution"></a>Lösning

Det finns två sätt att lösa det här felet:

* `Start-Job` Använd`Start-AzureRmAutomationRunbook` för att starta en Runbook i stället för att använda.
* Om din Runbook har det här fel meddelandet kan du köra det på en Hybrid Runbook Worker

Mer information om det här beteendet och andra beteenden för Azure Automation runbooks finns i [Runbook-beteende](../automation-runbook-execution.md#runbook-behavior).

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
