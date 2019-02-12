---
title: Skapa en Azure Automation-Integreringsmodul
description: Självstudie som steg för steg beskriver hur du skapar, testar och använder integreringsmoduler i Azure Automation.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9122cf5cc908d578d8b781c6fdc49d7b04b0ab58
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990350"
---
# <a name="azure-automation-integration-modules"></a>Azure Automation-integreringsmoduler

PowerShell är den grundläggande tekniken bakom Azure Automation. Eftersom Azure Automation bygger på PowerShell är PowerShell-moduler fundamentala för att kunna expandera Azure Automation. I den här artikeln får lära du dig ärendets natur Azure Automation använder PowerShell-moduler, kallas integreringsmoduler. Du får också lära dig rekommenderade metoder för att skapa egna PowerShell-moduler för att kontrollera att de fungerar som integreringsmoduler i Azure Automation. 

## <a name="what-is-a-powershell-module"></a>Vad är en PowerShell-modul?

En PowerShell-modul är en grupp med PowerShell-cmdlets som **Get-Date** eller **Copy-Item**, som kan användas från:

* PowerShell-konsolen
* skript
* Arbetsflöden
* runbooks
* DSC-resurser

Alla funktioner i PowerShell är tillgängliga via cmdlets och DSC-resurser. Varje cmdlet och DSC-resurs backas upp av en PowerShell-modul, många av dessa medföljer själva PowerShell. Till exempel den **Get-Date** cmdleten är en del av den `Microsoft.PowerShell.Utility` PowerShell-modulen och **Copy-Item** cmdleten är en del av den `Microsoft.PowerShell.Management` PowerShell-modulen och Package DSC-resurs är en del av den PowerShell-modulen PSDesiredStateConfiguration. Båda dessa moduler medföljer PowerShell. Många PowerShell-moduler som levereras inte som en del av PowerShell. Dessa moduler distribueras med första eller från tredje part, produkter eller, t.ex. PowerShell-galleriet. Modulerna är användbara eftersom de gör avancerade uppgifter enklare genom kapslade funktioner.  Du kan lära dig mer om [PowerShell-moduler på MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx). 

## <a name="what-is-an-azure-automation-integration-module"></a>Vad är en Azure Automation-integreringsmodul?

En Integreringsmodul är inte detsamma som en PowerShell-modul. Det är bara en PowerShell-modul som kan innehålla ytterligare en fil – en metadatafil som anger en Azure Automation-anslutningstyp som ska användas med modulens cmdlets i runbooks. PowerShell-moduler kan importeras till Azure Automation så att deras cmdlets tillgängliga för användning i runbooks och deras DSC-resurser tillgängliga för användning i DSC-konfigurationer. I bakgrunden, Azure Automation lagrar dessa moduler och när runbook-jobben och DSC-kompileringsjobben in dem i Azure Automation-sandboxar där köra runbooks och DSC-konfigurationer kompileras. Alla DSC-resurser i moduler placeras också automatiskt på Automation DSC-hämtningsservern. De kan hämtas av datorer när de använder DSC-konfigurationer.  

Vi levererar ett antal Azure PowerShell-moduler direkt i Azure Automation. Men du kan importera PowerShell-moduler för det system, tjänst eller verktyg som du vill integrera med.

> [!NOTE]
> Vissa moduler levereras som **globala modulerna** i Automation-tjänsten. De här globala modulerna är tillgängliga för dig när du skapar ett automation-konto och vi uppdaterar dem ibland, varpå de automatiskt till ditt automation-konto. Om du inte vill att de ska uppdateras automatiskt kan du importera alltid samma modul själv och som har företräde framför den globala modulversionen av den modul som vi levererar i tjänsten.

Formatet som du importerar ett integreringsmodulpaket i är en komprimerad fil med samma namn som modulen och filnamnstillägget .zip. Det innehåller Windows PowerShell-modulen och eventuella stödfiler, inklusive en manifestfil (.psd1) om modulen har en.

Om modulen ska innehålla en Azure Automation-anslutningstyp måste den också innehålla en fil med namnet `<ModuleName>-Automation.json` som anger egenskaperna för anslutningstypen. Det här är en json-fil som placeras i modulmappen för komprimerade ZIP-fil. Den här filen innehåller fälten för en **anslutning** som krävs för att ansluta till systemet eller tjänsten som modulen representerar. Den här konfigurationen identisk att en anslutningstyp skapas i Azure Automation. Genom att använda den här filen kan du ange fältnamn, typer och huruvida fälten ska vara krypterade och/eller valfria för modulens anslutningstyp. I följande exempel är en mall i json-format:

```json
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
   "TypeName":  "System.String"
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

Om du har distribuerat Service Management Automation och skapat integreringsmodulpaket för dina automation-runbooks, den här mallen bör se bekant ut till dig.

## <a name="authoring-best-practices"></a>Metodtips för redigering

Även om PowerShell-moduler finns, finns det fortfarande ett antal saker som vi rekommenderar att du överväger när du redigerar en PowerShell-modul för användning i Azure Automation. Några rekommendationer är användbara för att dina moduler ska fungerar bra i PowerShell-arbetsflöde.

1. Lägg till en sammanfattning, beskrivning och hjälp-URI för varje cmdlet i modulen. I PowerShell kan du definiera viss hjälpinformation för cmdlets så att användaren kan få hjälp med att använda dem genom att köra cmdleten **Get-Help**. Här är till exempel hur du kan definiera en sammanfattning och hjälp-URI för en PowerShell-modul som skrivits i en .psm1-fil. 
   
    ```powershell
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='https://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,
   
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,
   
       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )
   
    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken
   
    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
   
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred
   
    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```

   Tillhandahåller den här informationen visas den med den **Get-Help** cmdlet i PowerShell-konsolen. Den här informationen visas också i Azure Automation.  Till exempel när aktiviteter infogas i samband med runbook-redigering. Klicka på ”Visa detaljerad hjälp” öppnas hjälp-URI: N i en annan flik i webbläsaren som du använder för att få åtkomst till Azure Automation.

   ![Hjälp med integreringsmoduler](media/automation-integration-modules/automation-integration-module-activitydesc.png)

2. Om modulen körs mot ett fjärrsystem:

   1. Den bör innehålla en metadatafil för integreringsmodulen som definierar informationen som behövs för att ansluta till fjärrsystemet, eller anslutningstypen.
   2. Varje cmdlet i modulen ska kunna använda ett anslutningsobjekt (en instans av anslutningstypen) som en parameter.  

    Cmdlets i modulen blir enklare att använda i Azure Automation om ett objekt kan skickas med fälten för anslutningstypen som en parameter till cmdleten. På så sätt kan användarna mappa parametrar för anslutningstillgången till cmdletens motsvarande parametrar varje gång de anropar en cmdlet.

    Baserat på runbook-exemplet ovan används en Twilio-anslutningstillgång kallad CorpTwilio för att komma åt Twilio och returnera alla telefonnummer i kontot.  Observera hur den mappar fälten för anslutningen till parametrarna för cmdleten?

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    Ett enklare och bättre sätt att använda det här beteendet är skicka anslutningsobjektet direkt till cmdleten:

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```

    Du kan aktivera beteenden som i föregående exempel för dina cmdlets genom att tillåta att de accepterar ett anslutningsobjekt direkt som en parameter i stället för bara Anslutningsfält för parametrar. Vanligtvis vill du en parameteruppsättning för var och en, så att en användare som inte använder Azure Automation kan anropa dina cmdlets utan att skapa en hash-tabell som fungerar som anslutningsobjektet. Parameteruppsättningen **SpecifyConnectionFields**, används för att skicka egenskaperna för anslutningsfältet en i taget. Med **UseConnectionObject** kan du skicka anslutningen rakt igenom. Som du ser kan du skicka den på endera sätt med cmdleten Send-TwilioSMS i [Twilio PowerShell-modulen](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8):

    ```powershell
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='https://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,
   
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,
   
         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection
   
       )
    }
    ```

3. Definiera utdatatypen för alla cmdlets i modulen. Genom att definiera en utdatatyp för en cmdlet kan IntelliSense hjälpa dig under designfasen med att fastställa utdataegenskaperna för cmdleten, för användning under redigeringar. Det är särskilt användbart under runbook grafisk redigering av Automation, där goda kunskaper om designfasen är nyckeln till en enkel användarupplevelse med modulen.

   ![Utdatatyp för grafiska runbooks](media/automation-integration-modules/runbook-graphical-module-output-type.png)

   Detta liknar funktionen ”type-ahead” för en cmdlet-utdata i PowerShell ISE utan att behöva köra den.

   ![POSH IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)

4. Cmdlets i modulen bör inte acceptera komplexa objekttyper för parametrar. Till skillnad från PowerShell lagrar PowerShell Workflow komplexa typer i avserialiserat format. Primitiva typer Håll primitiver, men komplexa typer konverteras till deras avserialiserade versioner, som i grunden är egenskapsuppsättningar. Om du till exempel använde cmdleten **Get-process** i en runbook (eller ett PowerShell-arbetsflöde för den delen) returneras ett objekt av typen [Deserialized.System.Diagnostic.Process], inte den förväntade typen [System.Diagnostic.Process]. Den här typen har samma egenskaper som den icke-avserialiserade typen, men inga av metoderna. Och om du försöker skicka det här värdet som en parameter till en cmdlet, där cmdleten förväntar sig ett [System.Diagnostic.Process]-värde för den här parametern får du följande felmeddelande: *Det går inte att bearbeta argumenttransformation på parametern 'process'. Fel: ”Det går inte att konvertera värdet” System.Diagnostics.Process (CcmExec) ”av typen” Deserialized.System.Diagnostics.Process ”till typen” System.Diagnostics.Process ”.*   Det beror på att det finns ett typmatchningsfel mellan den förväntade typen [System.Diagnostic.Process] och den angivna typen [Deserialized.System.Diagnostic.Process]. Kringgå det här problemet är att se till att modulens cmdlets inte accepterar komplexa typer för parametrar. Det här är fel sätt att göra det på.

    ```powershell
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ```

    Det korrekta sättet är att ta en primitiv hämtas som används internt av cmdlet: en för att hämta det komplexa objektet och använda den. Eftersom cmdlets körs i PowerShell-kontexten, inte i PowerShell Workflow, blir $process rätt [System.Diagnostic.Process]-typ i cmdleten.  

    ```powershell
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```

   Anslutningstillgångar i runbooks är hash-tabeller, som är en komplex typ, och ändå verkar dessa hash för att kunna skickas till cmdlets för deras-Connection-parameter, med utan typkonverteringsundantag. Rent tekniskt kan vissa PowerShell-typer konverteras korrekt från deras serialiserade form till deras avserialiserade form och kan därför skickas till cmdlets för parametrar som accepterar den icke-avserialiserade typen. Hashtable är ett exempel. Det är möjligt för modulförfattarens definierade typer kan implementeras på ett sätt som de kan deserialiseras korrekt, men det finns vissa kompromisser att tänka på. Typen måste ha en standardkonstruktor och en PSTypeConverter, och alla dess egenskaper måste vara offentliga. Men för redan definierade typer som modulägaren inte äger, det finns inget sätt att ”åtgärda” dem, därför rekommendationen att undvika komplexa typer för parametrar helt och hållet. Redigeringstips tips: Om dina cmdlets måste en komplex parametertyp, är det en lösningen i PowerShell-arbetsflöden, att innesluta cmdleten som genererar den komplexa typen och cmdleten som använder den komplexa typen i samma InlineScript-aktivitet. Eftersom InlineScript kör sitt innehåll som PowerShell i stället för som ett PowerShell-arbetsflöde kommer cmdleten som genererar den komplexa typen att generera rätt typ, inte den avserialiserade komplexa typen.

5. Gör alla cmdlets i modulen tillståndslösa. PowerShell Workflow kör alla cmdlets som anropas i arbetsflödet i en annan session. Det innebär att cmdlets som är beroende av sessionstillstånd som skapas / ändras av andra cmdlets i samma modul inte fungerar i PowerShell Workflow-runbooks.  Här är ett exempel på vad du inte ska göra.
   
    ```powershell
    $globalNum = 0
    function Set-GlobalNum {
       param(
           [int] $num
       )
   
       $globalNum = $num
    }
    function Get-GlobalNumTimesTwo {
       $output = $globalNum * 2
   
       $output
    }
    ```

6. Modulen bör finnas i ett Xcopy-aktiverat paket. Azure Automation-moduler är distributd till begränsat Automation-läge när runbooks behöver köra. Modulerna måste fungera oberoende av värden som de körs på. Du bör kunna Zip upp och flytta ett paket för modulen och låta den fungera som vanligt när de importeras till en annan värd PowerShell-miljö. För att det ska ske, bör inte modulen beror på några filer utanför modulmappen. Den här mappen är den mapp som dekomprimeras vid modulen importeras till Azure Automation. Modulen bör också inte beroende av unika registerinställningar på en värd som dessa inställningar anges när en produkt installeras. Om denna bästa praxis inte följs användas modulen inte i Azure Automation.  

7. Om du refererar till [Az för Azure Powershell-moduler](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) i din modul, se till att du inte också refererar till `AzureRM`. Den `Az` modulen kan inte användas tillsammans med den `AzureRM` moduler. `Az` har stöd för runbooks men inte har importerat som standard. Vill veta mer om den `Az` moduler och saker att ta hänsyn till finns i [Az modulen stöd i Azure Automation](az-modules.md).

## <a name="next-steps"></a>Nästa steg

* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
* Mer information om hur du skapar PowerShell-moduler finns i [Skriva en Windows PowerShell-modul](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)
