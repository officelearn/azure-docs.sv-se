---
title: Felsöka med Azure Automation-Runbooks
description: Lär dig hur du felsöker problem med Azure Automation-runbooks
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: bb340b8439927f191bc4a22f385d85d4e21b1cdb
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064069"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Felsöka med runbooks

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>När du arbetar med Azure Automation runbook-autentiseringsfel

### <a name="sign-in-failed"></a>Scenario: Logga in på Azure-konto misslyckades

#### <a name="issue"></a>Problem

Du får följande felmeddelande när du arbetar med den `Add-AzureAccount` eller `Connect-AzureRmAccount` cmdlets.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Orsak

Det här felet uppstår om namn på autentiseringsuppgift tillgångsinformation inte är giltig eller om användarnamnet och lösenordet som du använde för att konfigurera Automation-autentiseringsuppgiftstillgång inte är giltiga.

#### <a name="resolution"></a>Lösning

För att avgöra vad som är fel gör du följande:  

1. Kontrollera att du inte har några specialtecken, inklusive den **@** tecken i Automation namn på autentiseringsuppgift tillgång som du använder för att ansluta till Azure.  
2. Kontrollera att du kan använda användarnamn och lösenord som lagras i Azure Automation-autentiseringsuppgifter i din lokala PowerShell ISE-redigeraren. Du kan göra detta genom att köra följande cmdlets i PowerShell ISE:  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management   
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Det innebär att du inte har konfigurerat dina Azure Active Directory-autentiseringsuppgifter korrekt om autentiseringen misslyckas lokalt. Referera till [autentiserar till Azure med Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) blogginlägget att hämta Azure Active Directory-kontot som har ställts in korrekt.  

### <a name="unable-to-find-subscription"></a>Scenario: Det gick inte att hitta Azure-prenumeration

#### <a name="issue"></a>Problem

Du får följande felmeddelande när du arbetar med den `Select-AzureSubscription` eller `Select-AzureRmSubscription` cmdlets.:

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Fel

Det här felet uppstår om prenumerationsnamn inte är giltig eller om Azure Active Directory-användare som försöker hämta prenumerationsinformation om inte är konfigurerad som en administratör för prenumerationen.

#### <a name="resolution"></a>Lösning

För att avgöra om du har autentiserats korrekt till Azure och har åtkomst till den prenumeration som du försöker markera gör du följande:  

1. Se till att du kör den **Add-AzureAccount** innan du kör den **Välj AzureSubscription** cmdlet.  
2. Om du fortfarande ser det här felmeddelandet ändra din kod genom att lägga till den **Get-AzureSubscription** följande cmdlet i **Add-AzureAccount** cmdlet och sedan köra koden. Nu ska du kontrollera om utdata från Get-AzureSubscription innehåller information om din prenumeration.  

   * Om du inte ser någon prenumerationsinformation i utdata, innebär detta att prenumerationen inte är initierats ännu.  
   * Om du ser prenumerationsinformation i utdata, kontrollerar du att du använder rätt namn eller ID med den **Välj AzureSubscription** cmdlet.

### <a name="auth-failed-mfa"></a>Scenario: Till Azure-autentiseringen misslyckades eftersom multifaktorautentisering har aktiverats

#### <a name="issue"></a>Problem

Du får det felmeddelande vid autentisering till Azure med Azure användarnamn och lösenord:

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Orsak

Om du har multifaktorautentisering på ditt Azure-konto kan du inte använda en Azure Active Directory-användare för att autentisera till Azure. Du måste i stället använda ett certifikat eller en tjänstens huvudnamn för att autentisera till Azure.

#### <a name="resolution"></a>Lösning

För att använda ett certifikat med cmdlet: ar för distribution av Azure klassiska modellen måste referera till [skapa och lägga till ett certifikat för att hantera Azure-tjänster.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Om du vill använda ett huvudnamn för tjänsten med Azure Resource Manager-cmdlets, referera till [att skapa tjänstens huvudnamn med hjälp av Azure portal](../../azure-resource-manager/resource-group-create-service-principal-portal.md) och [autentiserar ett huvudnamn för tjänsten med Azure Resource Manager.](../../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="common-errors-when-working-with-runbooks"></a>Vanliga fel när du arbetar med runbooks

### <a name="job-attempted-3-times"></a>Scenario: Jobbstart runbook gjordes tre gånger, men det gick inte att starta varje gång

#### <a name="issue"></a>Problem

Din runbook misslyckas med felmeddelandet:

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Orsak

Det här felet kan orsakas av följande skäl:

1. Gränsen för minne. Det finns dokumenterade begränsningar på hur mycket minne som allokerats till en Sandbox [Automation tjänstbegränsningarna](../../azure-subscription-service-limits.md#automation-limits) så att ett jobb kan misslyckas om den använder mer än 400 MB minne den.

2. Inkompatibel modul. Detta kan inträffa om modulen beroenden inte är rätt och om inte din runbook vanligtvis returnerar ”kommandot gick inte att hitta” eller ”det går inte att binda parametern” visas.

#### <a name="resolution"></a>Lösning

Något av följande lösningar problemet på:

* Föreslagna metoder för att fungera inom gränsen för minne är att dela arbetsbelastningen mellan flera runbooks, inte bearbeta så mycket data i minnet, inte att skriva onödiga utdata från dina runbooks, eller överväga hur många kontrollpunkter som du skriver i PowerShell-arbetsflöde runbooks.  

* Uppdatera din Azure moduler genom att följa stegen [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md).  

### <a name="fails-deserialized-object"></a>Scenario: Runbook misslyckas på grund av avserialiserat objekt

#### <a name="issue"></a>Problem

Din runbook misslyckas med felmeddelandet:

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Orsak

Om din runbook är ett PowerShell-arbetsflöde, lagrar komplexa objekt i ett avserialiserat format för att spara runbook-tillstånd om arbetsflödet pausas.

#### <a name="resolution"></a>Lösning

Något av följande tre lösningar åtgärda problemet:

1. Om du skicka komplexa objekt från en cmdlet till en annan, omsluta dessa cmdlets i ett InlineScript.
2. Skicka namn eller värde som du behöver från till komplexa objekt i stället för att skicka hela objektet.
3. Använda en PowerShell-runbook i stället för en PowerShell-arbetsflödesrunbook.

### <a name="quota-exceeded"></a>Scenario: Runbook-jobbet misslyckades eftersom den tilldelade kvoten överskreds

#### <a name="issue"></a>Problem

Runbook-jobb misslyckas med felmeddelandet:

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Orsak

Det här felet uppstår när jobbkörningen överskrider 500 minut ledigt kvoten för ditt konto. Den här kvoten gäller för alla typer av jobb körning av uppgifter som testar ett jobb, starta ett jobb från portalen, köra ett jobb med hjälp av webhooks och schemalägga ett jobb ska köras med hjälp av Azure portal eller i ditt datacenter. Mer information om priser för Automation finns [Automation priser](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Lösning

Om du vill använda mer än 500 minuter bearbetning per månad måste du ändra prenumerationen från kostnadsfria nivån till den grundläggande nivån. Du kan uppgradera till den grundläggande nivån genom att utföra följande steg:  

1. Logga in till din Azure-prenumeration  
2. Välj det Automation-konto som du vill uppgradera  
3. Klicka på **inställningar** > **priser**.
4. Klicka på **aktivera** på sidan längst ned att uppgradera ditt konto så att den **grundläggande** nivå.

### <a name="cmdlet-not-recognized"></a>Scenario: Cmdlet som inte känns igen när du kör en runbook

#### <a name="issue"></a>Problem

Runbook-jobb misslyckas med felmeddelandet:

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Orsak

Det här felet beror på när PowerShell-motorn inte kan hitta den cmdlet som du använder i din runbook. Detta kan bero på att den modul som innehåller cmdlet saknas från kontot, det finns en konflikt med ett namn på runbook, eller cmdleten finns också i en annan modul och Automation kan inte matcha namnet.

#### <a name="resolution"></a>Lösning

Något av följande lösningar problemet på:  

* Kontrollera att du har angett rätt namn.  
* Kontrollera att cmdleten finns i ditt Automation-konto och att det inte finns några konflikter. Om du vill kontrollera om cmdleten finns, öppnar du en runbook i redigeringsläge och Sök efter den cmdlet som du vill söka efter i biblioteket eller kör `Get-Command <CommandName>`. När du har validerat som cmdleten är tillgänglig för kontot, och att det inte finns några namnet står i konflikt med andra cmdlets eller runbooks, lägga till på arbetsytan och se till att du använder en giltig parameter som angetts i din runbook.  
* Om du har en namnkonflikt och cmdleten finns i två olika moduler, kan du lösa detta genom att använda det fullständiga namnet för cmdleten. Du kan till exempel använda **ModuleName\CmdletName**.  
* Om du kör den runbook lokalt i en hybrid worker-gruppen, se till att följande cmdlet /-modulen är installerad på datorn som är värd för worker-hybriden.

### <a name="evicted-from-checkpoint"></a>Scenario: En tidskrävande runbook misslyckas konsekvent med undantaget: ”jobbet kan inte fortsätta eftersom det upprepade gånger har avlägsnats från samma kontrollpunkten”

#### <a name="issue"></a>Problem

Det här beteendet är inbyggt på grund av ”fördelning” övervakning av processer i Azure Automation, som automatiskt inaktiverar en runbook om den körs längre än tre timmar. Returneras följande felmeddelande ger dock inte ”härnäst” alternativ.

#### <a name="cause"></a>Orsak

En runbook kan avbrytas för ett antal orsaker. Pausar hända oftast på grund av fel. Till exempel ett undantagsfel i en runbook, ett nätverksfel eller en krasch på Runbook Worker köra runbook orsak runbook att pausa och starta från den senaste kontrollpunkten när återupptas.

#### <a name="resolution"></a>Lösning

Dokumenterade lösning för att undvika det här problemet är att använda kontrollpunkter i ett arbetsflöde. Mer information finns i [Learning PowerShell-arbetsflöden](../automation-powershell-workflow.md#checkpoints). En mer utförlig förklaring av ”fördelning” och kontrollpunkt kan hittas i den här bloggen artikeln [med kontrollpunkter i Runbooks](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).

## <a name="common-errors-when-importing-modules"></a>Vanliga fel när du importerar moduler

### <a name="module-fails-to-import"></a>Scenario: Modulen inte att importera eller cmdlets kan inte utföras efter import

#### <a name="issue"></a>Problem

En modul som inte går att importera eller importeras korrekt, men inga cmdletar extraheras.

#### <a name="cause"></a>Orsak

Några vanliga orsaker som en modul inte kan importera har till Azure Automation är:

* Strukturen matchar inte strukturen Automation måste den vara i.
* Modulen är beroende av en annan modul som inte har distribuerats till ditt Automation-konto.
* Modulen saknar dess beroenden i mappen.
* Den `New-AzureRmAutomationModule` cmdlet som används för att ladda upp modulen, och du inte har gett fullständig lagringssökvägen eller har inte att läsa in modulen med hjälp av en offentligt tillgänglig URL.

#### <a name="resolution"></a>Lösning

Något av följande lösningar problemet på:

* Kontrollera att modulen följer följande format: ModuleName.Zip **->** ModuleName eller versionsnummer **->** (ModuleName.psm1, ModuleName.psd1)
* Öppna filen .psd1 och se om modulen har några beroenden. Om du överför dessa moduler till Automation-kontot.
* Kontrollera att alla refererade DLL-filer finns i mappen modulen.

## <a name="next-steps"></a>Nästa steg

Om du inte fick ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Du kan arkivera en incident i Azure-supporten om du behöver mer hjälp. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.