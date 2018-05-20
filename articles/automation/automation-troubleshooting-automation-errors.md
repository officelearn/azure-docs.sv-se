---
title: Felsökning av vanliga problem med Azure Automation | Microsoft Docs
description: Den här artikeln innehåller information för att felsöka och lösa vanliga problem i Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
tags: top-support-issue
keywords: fel när Automation, felsökning, problemet
ms.openlocfilehash: 990422cb686c84501127c3fcf233f96aec5bc065
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshooting-common-issues-in-azure-automation"></a>Felsökning av vanliga problem i Azure Automation 
Du får hjälp med att felsöka vanliga fel som du kan uppleva i Azure Automation och ger förslag på lösningar för att lösa problemen.

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>När du arbetar med Azure Automation runbook-autentiseringsfel
### <a name="scenario-sign-in-to-azure-account-failed"></a>Scenario: Logga in på Azure-konto misslyckades
**Fel:** felmeddelandet ”Unknown_user_type: Okänd typ” när du arbetar med cmdlets Add-AzureAccount eller Anslut-AzureRmAccount.

**Orsaken till felet:** det här felet uppstår om namn på autentiseringsuppgift tillgångsinformation inte är giltig eller om användarnamnet och lösenordet som du använde för att konfigurera Automation-autentiseringsuppgiftstillgång inte är giltiga.

**Felsökningstips:** för att avgöra vad som är fel gör du följande:  

1. Kontrollera att du inte har några specialtecken, inklusive den **@** tecken i Automation namn på autentiseringsuppgift tillgång som du använder för att ansluta till Azure.  
2. Kontrollera att du kan använda användarnamn och lösenord som lagras i Azure Automation-autentiseringsuppgifter i din lokala PowerShell ISE-redigeraren. Du kan göra detta genom att köra följande cmdlets i PowerShell ISE:  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Connect-AzureRmAccount –Credential $Cred
3. Det innebär att du inte har konfigurerat dina Azure Active Directory-autentiseringsuppgifter korrekt om autentiseringen misslyckas lokalt. Referera till [autentiserar till Azure med Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) blogginlägget att hämta Azure Active Directory-kontot som har ställts in korrekt.  

### <a name="scenario-unable-to-find-the-azure-subscription"></a>Scenario: Det gick inte att hitta Azure-prenumeration
**Fel:** felmeddelandet ”prenumeration med namnet ``<subscription name>`` går inte att hitta” när du arbetar med cmdlets väljer AzureSubscription eller Select-AzureRmSubscription.

**Orsaken till felet:** det här felet uppstår om prenumerationsnamn inte är giltig eller om Azure Active Directory-användare som försöker hämta prenumerationsinformation om inte är konfigurerad som en administratör för prenumerationen.

**Felsökningstips:** för att avgöra om du har autentiserats korrekt till Azure och har åtkomst till den prenumeration som du försöker markera gör du följande:  

1. Se till att du kör den **Add-AzureAccount** innan du kör den **Välj AzureSubscription** cmdlet.  
2. Om du fortfarande ser det här felmeddelandet ändra din kod genom att lägga till den **Get-AzureSubscription** följande cmdlet i **Add-AzureAccount** cmdlet och sedan köra koden. Nu ska du kontrollera om utdata från Get-AzureSubscription innehåller information om din prenumeration.  

   * Om du inte ser någon prenumerationsinformation i utdata, innebär detta att prenumerationen inte är initierats ännu.  
   * Om du ser prenumerationsinformation i utdata, kontrollerar du att du använder rätt namn eller ID med den **Välj AzureSubscription** cmdlet.   

### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>Scenario: Till Azure-autentiseringen misslyckades eftersom multifaktorautentisering har aktiverats
**Fel:** felmeddelandet ”Lägg till-AzureAccount: AADSTS50079: registrering av stark autentisering (bevis upp) krävs” när du autentiserar till Azure med Azure användarnamn och lösenord.

**Orsaken till felet:** om du har multifaktorautentisering på ditt Azure-konto kan du inte använda en Azure Active Directory-användare för att autentisera till Azure. Du måste i stället använda ett certifikat eller en tjänstens huvudnamn för att autentisera till Azure.

**Felsökningstips:** för att använda ett certifikat med cmdlet: ar för distribution av Azure klassiska modellen måste referera till [skapa och lägga till ett certifikat för att hantera Azure-tjänster.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Om du vill använda ett huvudnamn för tjänsten med Azure Resource Manager-cmdlets, referera till [att skapa tjänstens huvudnamn med hjälp av Azure portal](../azure-resource-manager/resource-group-create-service-principal-portal.md) och [autentiserar ett huvudnamn för tjänsten med Azure Resource Manager.](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="common-errors-when-working-with-runbooks"></a>Vanliga fel när du arbetar med runbooks
### <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-it-failed-to-start-each-time"></a>Scenario: Jobbstart runbook gjordes tre gånger, men det gick inte att starta varje gång
**Fel:** din runbook misslyckas med felmeddelandet ”” jobbet har försökt tre gånger, men misslyckades ”.

**Orsaken till felet:** det här felet kan orsakas av följande skäl:  

1. Gränsen för minne. Det finns dokumenterade begränsningar på hur mycket minne som allokerats till en Sandbox [Automation tjänstbegränsningarna](../azure-subscription-service-limits.md#automation-limits) så att ett jobb kan misslyckas om den använder mer än 400 MB minne den. 

2. Inkompatibel modul. Detta kan inträffa om modulen beroenden inte är rätt och om inte din runbook vanligtvis returnerar ”kommandot gick inte att hitta” eller ”det går inte att binda parametern” visas. 

**Felsökningstips:** någon av följande lösningar på problemet:

* Föreslagna metoder för att fungera inom gränsen för minne är att dela arbetsbelastningen mellan flera runbooks, inte bearbeta så mycket data i minnet, inte att skriva onödiga utdata från dina runbooks, eller överväga hur många kontrollpunkter som du skriver i PowerShell-arbetsflöde runbooks.  

* Uppdatera din Azure moduler genom att följa stegen [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).  


### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>Scenario: Runbook misslyckas på grund av avserialiserat objekt
**Fel:** din runbook misslyckas med felmeddelandet ”Det går inte att binda parametern ``<ParameterName>``. Det går inte att konvertera den ``<ParameterType>`` värde av typen Deserialized ``<ParameterType>`` till typen ``<ParameterType>``”.

**Orsaken till felet:** om din runbook är ett PowerShell-arbetsflöde, lagrar komplexa objekt i ett avserialiserat format för att spara runbook-tillstånd om arbetsflödet pausas.

**Felsökningstips:** något av följande tre lösningar åtgärda problemet:

1. Om du skicka komplexa objekt från en cmdlet till en annan, omsluta dessa cmdlets i ett InlineScript.
2. Skicka namn eller värde som du behöver från till komplexa objekt i stället för att skicka hela objektet.
3. Använda en PowerShell-runbook i stället för en PowerShell-arbetsflödesrunbook.

### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>Scenario: Runbook-jobbet misslyckades eftersom den tilldelade kvoten överskreds
**Fel:** runbook-jobb misslyckas med felmeddelandet ”kvoten för den månatliga total jobbkörningstid har nåtts för den här prenumerationen”.

**Orsaken till felet:** detta fel uppstår när jobbkörningen överskrider 500 minut ledigt kvoten för ditt konto. Den här kvoten gäller för alla typer av jobb körning av uppgifter som testar ett jobb, starta ett jobb från portalen, köra ett jobb med hjälp av webhooks och schemalägga ett jobb ska köras med hjälp av Azure portal eller i ditt datacenter. Mer information om priser för Automation finns [Automation priser](https://azure.microsoft.com/pricing/details/automation/).

**Felsökningstips:** om du vill använda mer än 500 minuter bearbetning per månad måste du ändra prenumerationen från kostnadsfria nivån till den grundläggande nivån. Du kan uppgradera till den grundläggande nivån genom att utföra följande steg:  

1. Logga in till din Azure-prenumeration  
2. Välj det Automation-konto som du vill uppgradera  
3. Klicka på **inställningar** > **priser**.
4. Klicka på **aktivera** på sidan längst ned att uppgradera ditt konto så att den **grundläggande** nivå.

### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>Scenario: Cmdlet som inte känns igen när du kör en runbook
**Fel:** runbook-jobb misslyckas med felmeddelandet ”``<cmdlet name>``: termen ``<cmdlet name>`` identifieras inte som namnet på en cmdlet, funktion, skriptfilen eller körbart program”.

**Orsaken till felet:** felet orsakas när PowerShell-motorn inte kan hitta den cmdlet som du använder i din runbook. Detta kan bero på att den modul som innehåller cmdlet saknas från kontot, det finns en konflikt med ett namn på runbook, eller cmdleten finns också i en annan modul och Automation kan inte matcha namnet.

**Felsökningstips:** någon av följande lösningar på problemet:  

* Kontrollera att du har angett rätt namn.  
* Kontrollera att cmdleten finns i ditt Automation-konto och att det inte finns några konflikter. Om du vill kontrollera om cmdleten finns, öppnar du en runbook i redigeringsläge och Sök efter den cmdlet som du vill söka efter i biblioteket eller kör **Get-Command ``<CommandName>``** . När du har validerat som cmdleten är tillgänglig för kontot, och att det inte finns några namnet står i konflikt med andra cmdlets eller runbooks, lägga till på arbetsytan och se till att du använder en giltig parameter som angetts i din runbook.  
* Om du har en namnkonflikt och cmdleten finns i två olika moduler, kan du lösa detta genom att använda det fullständiga namnet för cmdleten. Du kan till exempel använda **ModuleName\CmdletName**.  
* Om du kör den runbook lokalt i en hybrid worker-gruppen, se till att följande cmdlet /-modulen är installerad på datorn som är värd för worker-hybriden.

### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>Scenario: En tidskrävande runbook misslyckas konsekvent med undantaget: ”jobbet kan inte fortsätta eftersom det upprepade gånger har avlägsnats från samma kontrollpunkten”.
**Orsaken till felet:** detta beteende är inbyggt på grund av ”fördelning” övervakning av processer i Azure Automation, som automatiskt inaktiverar en runbook om den körs längre än tre timmar. Returneras följande felmeddelande ger dock inte ”härnäst” alternativ. En runbook kan avbrytas för ett antal orsaker. Pausar hända oftast på grund av fel. Till exempel ett undantagsfel i en runbook, ett nätverksfel eller en krasch på Runbook Worker köra runbook orsak runbook att pausa och starta från den senaste kontrollpunkten när återupptas.

**Felsökningstips:** dokumenterade lösning för att undvika det här problemet är att använda kontrollpunkter i ett arbetsflöde. Mer information finns i [Learning PowerShell-arbetsflöden](automation-powershell-workflow.md#checkpoints). En mer utförlig förklaring av ”fördelning” och kontrollpunkt kan hittas i den här bloggen artikeln [med kontrollpunkter i Runbooks](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).

## <a name="common-errors-when-importing-modules"></a>Vanliga fel när du importerar moduler
### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>Scenario: Modulen inte att importera eller cmdlets kan inte utföras efter import
**Fel:** en modul som inte går att importera eller importeras korrekt, men inga cmdletar extraheras.

**Orsaken till felet:** är några vanliga orsaker som en modul inte kan importera har till Azure Automation:

* Strukturen matchar inte strukturen Automation måste den vara i.
* Modulen är beroende av en annan modul som inte har distribuerats till ditt Automation-konto.
* Modulen saknar dess beroenden i mappen.
* Den **ny AzureRmAutomationModule** cmdlet som används för att ladda upp modulen, och du inte har gett fullständig lagringssökvägen eller har inte att läsa in modulen med hjälp av en offentligt tillgänglig URL.

**Felsökningstips:** någon av följande lösningar på problemet:

* Kontrollera att modulen följer följande format: ModuleName.Zip **->** ModuleName eller versionsnummer **->** (ModuleName.psm1, ModuleName.psd1)
* Öppna filen .psd1 och se om modulen har några beroenden. Om du överför dessa moduler till Automation-kontot.
* Kontrollera att alla refererade DLL-filer finns i mappen modulen.

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Vanliga fel när du arbetar med önskad tillstånd Configuration DSC)
### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>Scenario: Noden är statusen misslyckades med felet ”kunde inte hittas”
**Fel:** noden har en rapport med **misslyckades** status och med felet ”försöket att hämta åtgärden från servern https://``<url>``//accounts/ ``<account-id>`` /Nodes(AgentId=``<agent-id>``) / GetDscAction misslyckades på grund av en giltig konfiguration ``<guid>`` kan inte hittas ”.

**Orsaken till felet:** det här felet uppstår vanligen när noden har tilldelats en Konfigurationsnamnet (till exempel ABC) i stället för ett namn på konfigurationsnod (till exempel ABC. Webbserver).

**Felsökningstips:**

* Kontrollera att du tilldelar noden med ”nodkonfigurationsnamnet” och inte ”Konfigurationsnamnet”.
* Du kan tilldela en konfiguration av noden till en nod med hjälp av Azure-portalen eller med en PowerShell-cmdlet.

  * För att tilldela en konfiguration av noden till en nod med hjälp av Azure portal, öppna den **DSC-noder** och sedan markera en nod och klickar på på **tilldela nodkonfiguration** knappen.  
  * Använd om du vill tilldela en nod med hjälp av PowerShell-cmdlet för konfiguration av noden **Set AzureRmAutomationDscNode** cmdlet

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>Scenario: Inga nodkonfigurationer (MOF-filer) skapas när en konfiguration kompileras
**Fel:** DSC-Kompileringsjobb pausar med fel: ”kompilering slutfördes, men ingen nod configuration.mofs genererades”.

**Orsaken till felet:** när följande uttryck i **nod** nyckelord i DSC-konfigurationen utvärderas till `$null`, och inga nodkonfigurationer produceras.

**Felsökningstips:** någon av följande lösningar på problemet:

* Se till att uttrycket bredvid den **nod** nyckelord i konfigurationsdefinition utvärderas inte till $null.
* Om du skickar ConfigurationData vid kompilering av konfigurationen, kontrollera att du skickar de förväntade värdena konfigurationen kräver från [ConfigurationData](automation-dsc-compile.md#configurationdata).

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a>Scenario: Rapporten DSC-nod blir fastnat ”pågående”-tillstånd
**Fel:** DSC-agenten matar ut ”ingen instans hittades med den angivna egenskapsvärden”.

**Orsaken till felet:** du har uppgraderat WMF-version och ha skadad WMI.

**Felsökningstips:** att åtgärda problemet Följ instruktionerna i den [DSC kända problem och begränsningar](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) artikel.

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a>Scenario: Det går inte att använda en autentiseringsuppgift i en DSC-konfiguration
**Fel:** DSC-Kompileringsjobb pausades med fel ”: System.InvalidOperationException fel vid bearbetning av egenskapen Credential om du av typen ``<some resource name>``: konvertera och lagra ett krypterat lösenord som klartext tillåts endast om PSDscAllowPlainTextPassword har angetts till true ”.

**Orsaken till felet:** du har använt en autentiseringsuppgift i en konfiguration, men inte anger rätt **ConfigurationData** ange **PSDscAllowPlainTextPassword** till true för varje nod konfiguration.

**Felsökningstips:**

* Se till att skicka in rätt **ConfigurationData** ange **PSDscAllowPlainTextPassword** till true för varje nodkonfiguration som nämns i konfigurationen. Mer information finns i [tillgångar i Azure Automation DSC](automation-dsc-compile.md#assets).

## <a name="common-errors-when-onboarding-solutions"></a>Vanliga fel när onboarding-lösningar

När onboarding-lösningar som kan uppstå fel. Följande är en lista över vanliga fel som du kan köra över.

### <a name="computergroupqueryformaterror"></a>ComputerGroupQueryFormatError

**Orsaken till felet:**

Felet innebär att sparad sökning datorn grupp frågan som används för att rikta lösningen inte var korrekt formaterad. Frågan kan ha ändrats eller det kan ha ändrats av systemet.

**Felsökningstips:**

Du kan ta bort frågan för den här lösningen och reonboard lösning som återskapar frågan. Frågan kan hittas i din arbetsyta under **sparade sökningar**. Namnet på frågan är **MicrosoftDefaultComputerGroup**, och kategorin i frågan är namnet på den lösning som är associerade med den här frågan. Om flera lösningar är aktiverade på **MicrosoftDefaultComputerGroup** visas flera gånger under **sparade sökningar**.

### <a name="policyviolation"></a>PolicyViolation

**Orsaken till felet:**

Felet innebär att distributionen misslyckades på grund av överträdelse av en eller flera principer.

**Felsökningstips:**

För att distribuera lösningen, måste du överväga att ändra den angivna principen. Eftersom många olika typer av principer som kan definieras beror de ändringar som krävs på den princip som har överskridits. Till exempel om en princip har definierats i en resursgrupp som nekas behörighet att ändra innehållet i vissa typer av resurser i resursgruppen, kan du till exempel göra något av följande:

*   Ta bort principen helt och hållet.
* Försök att publicera till en annan resursgrupp.
* Ändra principen av, till exempel:
   * Nytt riktad principen till en en viss resurs (till exempel ett visst Automation-konto).
   * Ändra uppsättningen av resurser som principen har konfigurerats för att neka.

Kontrollera meddelanden i det övre högra hörnet i Azure-portalen eller navigera till den resursgrupp som innehåller ditt automation-konto och välj **distributioner** under **inställningar** att visa den misslyckade distribution. Läs mer om Azure princip besök: [översikt över Azure princip](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

Om du har följt de föregående felsökning och det går inte att hitta svaret kan du granska den ytterligare stöd för följande alternativ:

* Få hjälp från Azure-experter. Skicka din förfrågan till den [MSDN Azure eller Stack Overflow-forum](https://azure.microsoft.com/support/forums/).
* En Azure-supportincident-filen. Gå till den [Azure stöder site](https://azure.microsoft.com/support/options/) och på **få support** under **Technical och fakturering support**.
* Skicka en begäran om skriptet på [Script Center](https://azure.microsoft.com/documentation/scripts/) om du söker efter en Azure Automation runbook-lösning eller en modul för integrering.
* Skicka feedback eller funktionen begäranden för Azure Automation på [User Voice](https://feedback.azure.com/forums/34192--general-feedback).
