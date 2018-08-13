---
title: Felsöka fel med Azure Automation-Runbooks
description: Lär dig att felsöka problem med Azure Automation-runbooks
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 07/13/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 53b35fbdc469639b1fdc09293e05247bcc5d8c31
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714493"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Felsöka fel med runbooks

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Autentiseringsfel när du arbetar med Azure Automation-runbooks

### <a name="sign-in-failed"></a>Scenario: Logga in på Azure-konto misslyckades

#### <a name="issue"></a>Problem

Du får följande felmeddelande när du arbetar med den `Add-AzureAccount` eller `Connect-AzureRmAccount` cmdletar.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Orsak

Det här felet uppstår om Tillgångsnamn autentiseringsuppgifter inte är giltigt eller om användarnamnet och lösenordet som användes för att konfigurera Automation-autentiseringsuppgiftstillgång inte är giltiga.

#### <a name="resolution"></a>Lösning

För att avgöra vad som är fel, gör du följande:  

1. Se till att det inte finns några specialtecken, inklusive den ** @ ** tecknet i Automation namn på autentiseringsuppgift tillgång som du använder för att ansluta till Azure.  
2. Kontrollera att du kan använda användarnamn och lösenord som lagras i Azure Automation-autentiseringsuppgift i din lokala PowerShell ISE-redigerare. Du kan göra detta genom att köra följande cmdlets i PowerShell ISE:  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Om autentiseringen misslyckas lokalt, innebär det att du inte har konfigurerat dina autentiseringsuppgifter för Azure Active Directory korrekt. Referera till [autentisering i Azure med Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) blogginlägg för att skaffa Azure Active Directory-konto som har ställts in korrekt.  

4. Om det verkar vara ett tillfälligt fel, försök att lägga till logik för omprövning i en autentisering vana att göra autentisering mer robust.

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

### <a name="unable-to-find-subscription"></a>Scenario: Det gick inte att hitta Azure-prenumeration

#### <a name="issue"></a>Problem

Du får följande felmeddelande när du arbetar med den `Select-AzureSubscription` eller `Select-AzureRmSubscription` cmdlets.:

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Fel

Det här felet uppstår om prenumerationens namn inte är giltigt eller om den Azure Active Directory-användare som försöker hämta information om prenumerationen inte har konfigurerats som en administratör för prenumerationen.

#### <a name="resolution"></a>Lösning

För att avgöra om du har autentiserat korrekt till Azure och har åtkomst till den prenumeration som du försöker markera, gör du följande:  

1. Se till att du kör den **Add-AzureAccount** innan du kör den **Select-AzureSubscription** cmdlet.  
2. Om du fortfarande ser det här felmeddelandet, ändra din kod genom att lägga till den **Get-AzureSubscription** följande cmdlet i **Add-AzureAccount** cmdlet och sedan köra koden. Kontrollera nu om utdata från Get-AzureSubscription innehåller information om din prenumeration.  

   * Om du inte ser någon prenumerationsinformation i utdata, innebär det att prenumerationen inte är initierats ännu.  
   * Om du ser prenumerationsinformation i utdata, kontrollerar du att du använder rätt prenumerationsnamn eller ID med den **Select-AzureSubscription** cmdlet.

### <a name="auth-failed-mfa"></a>Scenario: Till Azure-autentiseringen misslyckades eftersom multifaktorautentisering har aktiverats

#### <a name="issue"></a>Problem

Du får följande felmeddelande när du autentiserar till Azure med ditt Azure användarnamn och lösenord:

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Orsak

Om du har multifaktorautentisering på din Azure-konto kan använda du inte en Azure Active Directory-användare för att autentisera till Azure. Du måste i stället använda ett certifikat eller ett huvudnamn för tjänsten för att autentisera till Azure.

#### <a name="resolution"></a>Lösning

Om du vill använda ett certifikat med cmdlet: ar för klassiska Azure-modellen måste referera till [skapa och lägga till ett certifikat för att hantera Azure-tjänster.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Om du vill använda ett huvudnamn för tjänsten med Azure Resource Manager-cmdletar, som avser [skapar tjänstens huvudnamn med hjälp av Azure portal](../../azure-resource-manager/resource-group-create-service-principal-portal.md) och [autentisera tjänstens huvudnamn med Azure Resource Manager.](../../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="common-errors-when-working-with-runbooks"></a>Vanliga fel när du arbetar med runbooks

### <a name="task-was-cancelled"></a>Scenario: Runbook misslyckas med fel: en uppgift avbröts

#### <a name="issue"></a>Problem

Din runbook misslyckas med ett fel som liknar följande exempel:

```
Exception: A task was canceled.
```

#### <a name="cause"></a>Orsak

Det här felet kan orsakas av med inaktuella Azure-moduler.

#### <a name="resolution"></a>Lösning

Det här felet kan lösas genom att uppdatera din Azure-moduler till den senaste versionen.

I ditt Automation-konto klickar du på **moduler**, och klicka på **uppdatera Azure-moduler**. Uppdateringen tar ungefär 15 minuter, en gång fullständig kör den runbook som misslyckades kontrollerades.

### <a name="not-recognized-as-cmdlet"></a>Scenario: Runbook misslyckas på grund av en cmdlet som saknas

#### <a name="issue"></a>Problem

Din runbook misslyckas med ett fel som liknar följande exempel:

```
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Orsak

Det här felet kan orsakas av följande orsaker:

1. Modulen som innehåller cmdlet: en har inte importerats till automation-konto
2. Modulen containg cmdleten har importerats men är inaktuell

#### <a name="resolution"></a>Lösning

Det här felet kan lösas genom att fylla i en av följande uppgifter:

Om modulen är en Azure-modulen, se [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md) att lära dig hur du uppdaterar dina moduler i ditt automation-konto.

Om det är en separat modul, se till att modulen i importerade i ditt Automation-konto.

### <a name="job-attempted-3-times"></a>Scenario: Runbook-jobbstart gjordes tre gånger, men det gick inte att starta varje gång

#### <a name="issue"></a>Problem

Din runbook misslyckas med felet:

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Orsak

Det här felet kan orsakas av följande orsaker:

1. Minnesgräns. Det finns dokumenterade begränsningar för hur mycket minne som allokerats till en Sandbox [Automation tjänstbegränsningar](../../azure-subscription-service-limits.md#automation-limits) så att ett jobb kan misslyckas den om den använder mer än 400 MB minne.

2. Modulen inkompatibel. Detta kan inträffa om modulberoenden inte är rätt och om inte din runbook vanligtvis returnerar ett ”kommando inte hittas” eller ”det går inte att binda parametern” meddelande.

#### <a name="resolution"></a>Lösning

Någon av följande lösningar problemet på:

* Föreslagna metoder för att fungera inom minnesgränsen är att dela upp arbetsbelastningen mellan flera runbooks, inte bearbeta så mycket data i minnet, inte att skriva onödiga utdata från runbooks, eller överväga hur många kontrollpunkter som du skriver till PowerShell-arbetsflöde runbooks.  

* Uppdatera din Azure-moduler genom att följa stegen [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md).  

### <a name="fails-deserialized-object"></a>Scenario: Runbook misslyckas på grund av avserialiserat objekt

#### <a name="issue"></a>Problem

Din runbook misslyckas med felet:

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Orsak

Om din runbook är ett PowerShell-arbetsflöde, lagrar komplexa objekt i ett avserialiserat format för att bevara din runbook tillstånd om arbetsflödet pausas.

#### <a name="resolution"></a>Lösning

Något av följande tre lösningar åtgärda problemet:

1. Om du skicka komplexa objekt från en cmdlet till en annan omsluta dessa cmdletar i en InlineScript.
2. Skicka namn eller värde som du behöver från det komplexa objektet istället för att skicka hela objektet.
3. Använda en PowerShell-runbook i stället för en PowerShell Workflow-runbook.

### <a name="quota-exceeded"></a>Scenario: Runbook-jobbet misslyckades eftersom den tilldelade kvoten har överskridits

#### <a name="issue"></a>Problem

Runbook-jobb misslyckas med felet:

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Orsak

Det här felet uppstår när jobbkörningen överskrider den lediga kvoten 500 minuters för ditt konto. Den här kvoten gäller för alla typer av körningen jobbuppgifter som testar ett jobb, startar ett jobb från portalen, köra ett jobb med hjälp av webhooks och schemalägga ett jobb ska köras med hjälp av Azure portal eller i ditt datacenter. Läs mer om priser för Automation i [Automation-prissättning](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Lösning

Om du vill använda mer än 500 minuter bearbetningen per månad, måste du ändra din prenumeration från kostnadsfritt nivån till Basic-nivån. Du kan uppgradera till Basic-nivån genom att utföra följande steg:  

1. Logga in till din Azure-prenumeration  
2. Välj det Automation-konto som du vill uppgradera  
3. Klicka på **inställningar** > **priser**.
4. Klicka på **aktivera** på sidan nedifrån och uppgradera ditt konto till den **grundläggande** nivå.

### <a name="cmdlet-not-recognized"></a>Scenario: Cmdleten inte känns igen när du kör en runbook

#### <a name="issue"></a>Problem

Runbook-jobb misslyckas med felet:

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Orsak

Det här felet beror på när PowerShell-motorn inte kan hitta den cmdlet som du använder i din runbook. Detta kan bero på att modulen som innehåller cmdleten saknas i kontot, det finns en namnkonflikt med en runbook-namn eller cmdlet: en finns också i en annan modul och Automation kan inte matcha namnet.

#### <a name="resolution"></a>Lösning

Någon av följande lösningar problemet på:  

* Kontrollera att du har angett korrekt cmdlet-namn.  
* Kontrollera att cmdleten finns i ditt Automation-konto och att det inte finns några konflikter. Om du vill kontrollera om cmdlet: en är tillgänglig, öppnar du en runbook i redigeringsläge och Sök efter den cmdlet som du vill söka efter i biblioteket eller kör `Get-Command <CommandName>`. När du har godkänt som cmdlet: en är tillgänglig för kontot och det finns inga namnet står i konflikt med andra cmdletar eller runbooks kan lägga till den på arbetsytan och se till att du använder en giltig parameter som angetts i din runbook.  
* Om du har en namnkonflikt och cmdlet: en är tillgänglig i två olika moduler, kan du lösa detta genom att använda det fullständigt kvalificerade namnet för cmdleten. Du kan till exempel använda **ModuleName\CmdletName**.  
* Om du kör runbook lokala platser i en hybrid worker-grupp och sedan kontrollera att är modulen/cmdleten installerad på den dator som är värd för hybrid worker.

### <a name="evicted-from-checkpoint"></a>Scenario: En tidskrävande runbook misslyckas konsekvent med undantaget: ”jobbet kan inte fortsätta att köras eftersom det upprepade gånger har avlägsnats från samma kontrollpunkten”

#### <a name="issue"></a>Problem

Det här beteendet är avsiktligt på grund av ”rättmätiga del” övervakning av processer i Azure Automation, som automatiskt inaktiverar en runbook om den körs längre än tre timmar. Returneras följande felmeddelande ger dock inte ”vad är nästa steg” alternativ.

#### <a name="cause"></a>Orsak

En runbook kan stängas av för ett antal orsaker. Pausar inträffar oftast på grund av fel. Till exempel ett undantagsfel i en runbook, ett nätverksfel eller en krasch på den Runbook Worker som kör runbook orsak runbook att pausa och starta från den senaste kontrollpunkten när återupptas.

#### <a name="resolution"></a>Lösning

Den dokumenterade lösningen för att undvika det här problemet är att använda kontrollpunkter i ett arbetsflöde. Mer information, referera till [Learning PowerShell-arbetsflöden](../automation-powershell-workflow.md#checkpoints). En mer omfattande beskrivning av ”rättmätiga del” och kontrollpunkt kan hittas i den här bloggen artikeln [med hjälp av kontrollpunkter i Runbooks](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).

### <a name="long-running-runbook"></a>Scenario: En tidskrävande runbook kan inte slutföras

#### <a name="issue"></a>Problem

Det här beteendet är avsiktligt i Azure sandboxar på grund av ”rättmätiga del” övervakning av processer i Azure Automation, som automatiskt inaktiverar en runbook om den körs längre än tre timmar.

#### <a name="cause"></a>Orsak

Runbook kördes över 3 timme gränsen som tillåts av rättmätiga del i en Azure-Sandbox

#### <a name="resolution"></a>Lösning

Den rekommenderade lösningen är att köra runbook på en [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybrid Worker-arbeten begränsas inte av den [rättmätiga del](../automation-runbook-execution.md#fair-share) 3 timme runbook gränsen som Azure sandbox-miljöer.

## <a name="common-errors-when-importing-modules"></a>Vanliga fel när du importerar moduler

### <a name="module-fails-to-import"></a>Scenario: Module inte kan importera eller cmdlet: ar kan inte utföras när du har importerat

#### <a name="issue"></a>Problem

En modul kan inte importera eller importeras ordentligt, men inga cmdletar extraheras.

#### <a name="cause"></a>Orsak

Några vanliga orsaker som en modul inte kan importera till Azure Automation är:

* Strukturen matchar inte strukturen som Automation måste det finnas i.
* Modulen är beroende av en annan modul som inte har distribuerats till ditt Automation-konto.
* Modulen saknar dess beroenden i mappen.
* Den `New-AzureRmAutomationModule` cmdlet som används för att ladda upp modulen, och du inte har gett den fullständiga lagringssökväg eller har inte att läsa in modulen med hjälp av en offentligt tillgänglig URL.

#### <a name="resolution"></a>Lösning

Någon av följande lösningar problemet på:

* Kontrollera att modulen följer följande format: ModuleName.Zip ** -> ** ModuleName eller versionsnummer ** -> ** (ModuleName.psm1, ModuleName.psd1)
* Öppna filen .psd1 och om modulen har några beroenden. I annat fall kan du ladda upp dessa moduler till Automation-kontot.
* Se till att alla refererade DLL-filer finns i modulmappen.

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan öppna du en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.