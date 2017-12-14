---
title: Skapa en Azure Automation-integreringsmodul | Microsoft Docs
description: "Självstudie som steg för steg beskriver hur du skapar, testar och använder integreringsmoduler i Azure Automation."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: 
ms.assetid: 27798efb-08b9-45d9-9b41-5ad91a3df41e
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/13/2017
ms.author: magoedte
ms.openlocfilehash: 589043f45a87595c9356cd8143beca23a4f83517
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="azure-automation-integration-modules"></a>Azure Automation-integreringsmoduler
PowerShell är den grundläggande tekniken bakom Azure Automation. Eftersom Azure Automation bygger på PowerShell är PowerShell-moduler fundamentala för att kunna expandera Azure Automation. I den här artikeln beskriver vi steg för steg hur Azure Automation använder PowerShell-moduler, även kallade ”integreringsmoduler”. Artikeln innehåller också metodtips som hjälper dig att skapa egna PowerShell-moduler som fungerar som integreringsmoduler i Azure Automation. 

## <a name="what-is-a-powershell-module"></a>Vad är en PowerShell-modul?
En PowerShell-modul är en grupp med PowerShell-cmdlets som **Get-Date** eller **Copy-Item**, som kan användas från PowerShell-konsolen, skript, arbetsflöden, runbooks och PowerShell DSC-resurser som WindowsFeature eller File, som kan användas från PowerShell DSC-konfigurationer. Alla funktioner i PowerShell är tillgängliga via cmdlets och DSC-resurser, och varje cmdlet/DSC-resurs backas upp av en PowerShell-modul. Många av dessa medföljer själva PowerShell. Exempelvis är cmdleten **Get-Date** en del av PowerShell-modulen Microsoft.PowerShell.Utility, cmdleten **Copy-Item** en del av PowerShell-modulen Microsoft.PowerShell.Management och Package DSC-resursen en del av PowerShell-modulen PSDesiredStateConfiguration. Båda dessa moduler medföljer PowerShell. Men många PowerShell-moduler levereras inte som en del av PowerShell och distribueras i stället med tillverkarens produkter eller med tredjepartsprodukter som System Center 2012 Configuration Manager eller av den stora PowerShell-communityn, t.ex. via PowerShell-galleriet.  Modulerna är användbara eftersom de gör avancerade uppgifter enklare genom kapslade funktioner.  Du kan lära dig mer om [PowerShell-moduler på MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx). 

## <a name="what-is-an-azure-automation-integration-module"></a>Vad är en Azure Automation-integreringsmodul?
En integreringsmodul skiljer sig inte så mycket från en PowerShell-modul. Det är bara en PowerShell-modul som kan innehålla ytterligare en fil – en metadatafil som anger en Azure Automation-anslutningstyp som ska användas med modulens cmdletar i runbooks. Oavsett om det gäller valfria filer eller inte kan dessa PowerShell-moduler importeras till Azure Automation så att deras cmdlets kan användas i runbooks och deras DSC-resurser i DSC-konfigurationer. Azure Automation lagrar dessa moduler i bakgrunden och när runbook-jobben och DSC-kompileringsjobben körs läser tjänsten in dem i begränsade Azure Automation-lägen där runbooks körs och DSC-konfigurationer kompileras.  DSC-resurser i moduler placeras också automatiskt på Automation DSC-hämtningsservern så att de kan hämtas av datorer som försöker använda DSC-konfigurationer.  

Vi levererar ett antal färdiga Azure PowerShell-moduler med Azure Automation som du kan använda för att snabbt komma igång med automatiseringen av Azure-hanteringen, men du kan importera PowerShell-moduler för det system, den tjänst eller det verktyg som du vill integrera med. 

> [!NOTE]
> Vissa moduler levereras som ”globala moduler” i Automation-tjänsten. De här globala modulerna är tillgängliga när du skapar ett Automation-konto och vi uppdaterar dem ibland, varpå de skickas ut automatiskt till ditt Automation-konto. Om du inte vill att de ska uppdateras automatiskt kan du alltid importera samma modul själv och den modulen äger företräde framför den globala modulversionen av den modul som vi leverererar automatiskt i tjänsten. 

Formatet som du importerar ett integreringsmodulpaket i är en komprimerad fil med samma namn som modulen och filnamnstillägget .zip. Det innehåller Windows PowerShell-modulen och eventuella stödfiler, inklusive en manifestfil (.psd1) om modulen har en.

Om modulen ska innehålla en Azure Automation-anslutningstyp måste den också innehålla en fil med namnet `<ModuleName>-Automation.json` som anger egenskaperna för anslutningstypen. Det här är en JSON-fil som placeras i modulmappen för den komprimerade ZIP-filen och som innehåller fälten för en ”anslutning” som krävs för att ansluta till systemet eller tjänsten som modulen representerar. Detta resulterar i att en anslutningstyp skapas i Azure Automation. Genom att använda den här filen kan du ange fältnamn, typer och huruvida fälten ska vara krypterade och/eller valfria för modulens anslutningstyp. Följande är en mall i JSON-format:

```
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

Om du har distribuerat Service Management Automation och skapat integreringsmodulpaket för dina Automation-runbooks bör du känna igen detta. 

## <a name="authoring-best-practices"></a>Metodtips för redigering
Även om integreringsmodulerna i grunden är PowerShell-moduler finns det ett antal åtgärder som vi rekommenderar att du överväger när du redigerar en PowerShell-modul så att den blir så användbar som möjligt i Azure Automation. Vissa av dessa är specifika för Azure Automation och några av dem är användbara för att dina moduler ska fungerar bra i PowerShell Workflow, oavsett om du använder Automation eller inte. 

1. Lägg till en sammanfattning, beskrivning och hjälp-URI för varje cmdlet i modulen. I PowerShell kan du definiera viss hjälpinformation för cmdlets så att användaren kan få hjälp med att använda dem genom att köra cmdleten **Get-Help**. Så här kan du till exempel definiera en sammanfattning och hjälp-URI för en PowerShell-modul som skrivits i en .psm1-fil.<br>  
   
    ```
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='http://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
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
   <br> Om du tillhandahåller den här informationen visas den här hjälpen med cmdleten **Get-Help** i PowerShell-konsolen, men hjälpfunktionen visas också i Azure Automation.  Till exempel när aktiviteter infogas i samband med runbook-redigering. Om du klickar på ”Visa detaljerad hjälp” öppnas hjälp-URI:n på en annan flik i webbläsaren som du använder för att få åtkomst till Azure Automation.<br>![Hjälp med integreringsmoduler](media/automation-integration-modules/automation-integration-module-activitydesc.png)
2. Om modulen körs mot ett fjärrsystem:

    a. Den bör innehålla en metadatafil för integreringsmodulen som definierar informationen som behövs för att ansluta till fjärrsystemet, eller anslutningstypen.  
    b. Varje cmdlet i modulen ska kunna använda ett anslutningsobjekt (en instans av anslutningstypen) som en parameter.  

    Cmdlets i modulen blir enklare att använda i Azure Automation om ett objekt kan skickas med fälten för anslutningstypen som en parameter till cmdleten. På så sätt behöver inte användarna mappa parametrar för anslutningstillgången till cmdletens motsvarande parametrar varje gång de anropar en cmdlet. Baserat på runbook-exemplet ovan används en Twilio-anslutningstillgång kallad CorpTwilio för att komma åt Twilio och returnera alla telefonnummer i kontot.  Observera hur den mappar fälten för anslutningen till parametrarna för cmdleten?<br>
   
    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    Ett enklare och bättre sätt är att skicka anslutningsobjektet direkt till cmdleten:
   
    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```
   
    Du kan aktivera beteenden som detta för dina cmdlets genom att tillåta att de accepterar ett anslutningsobjekt direkt som en parameter i stället för bara anslutningsfält för parametrar. Normalt vill du ha en parameteruppsättning för vart och ett så att en användare som inte använder Azure Automation kan anropa dina cmdlets utan att skapa en hash-tabell som fungerar som anslutningsobjektet. Parameteruppsättningen **SpecifyConnectionFields** nedan används för att skicka egenskaperna för anslutningsfältet en i taget. Med **UseConnectionObject** kan du skicka anslutningen rakt igenom. Som du ser kan du skicka den på endera sätt med cmdleten Send-TwilioSMS i [Twilio PowerShell-modulen](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8): 
   
    ```
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='http://www.twilio.com/docs/api/rest/sending-sms')]
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
   <br>
3. Definiera utdatatypen för alla cmdlets i modulen. Genom att definiera en utdatatyp för en cmdlet kan IntelliSense hjälpa dig under designfasen med att fastställa utdataegenskaperna för cmdleten, för användning under redigeringar. Det är särskilt användbart under grafisk redigering av Automation-runbooks där goda kunskaper om designfasen är en förutsättning för en enkel användarupplevelse med modulen.<br><br> ![Utdatatyp för grafiska runbooks](media/automation-integration-modules/runbook-graphical-module-output-type.png)<br> Detta påminner om funktionen ”type-ahead” (ifyllningsförslag) för en cmdlets utdata i PowerShell ISE utan att den behöver köras.<br><br> ![POSH IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)<br>
4. Cmdlets i modulen ska inte acceptera komplexa objekttyper för parametrar. Till skillnad från PowerShell lagrar PowerShell Workflow komplexa typer i avserialiserat format. Primitiva typer förblir primitiver, men komplexa typer konverteras till deras avserialiserade versioner, som i grunden är egenskapsuppsättningar. Om du till exempel använde cmdleten **Get-process** i en runbook (eller ett PowerShell-arbetsflöde för den delen) returneras ett objekt av typen [Deserialized.System.Diagnostic.Process], inte den förväntade typen [System.Diagnostic.Process]. Den här typen har samma egenskaper som den icke-avserialiserade typen, men inga av metoderna. Om du försöker överföra det här värdet som en parameter till en cmdlet, där cmdleten förväntar sig ett [System.Diagnostic.Process]-värde för den här parametern får du följande fel: *Det går inte att bearbeta argumenttransformation på parametern 'process'. Fel: Det går inte att konvertera värdet "System.Diagnostics.Process (CcmExec)" av typen "Deserialized.System.Diagnostics.Process" till typen "System.Diagnostics.Process".*   Detta beror på ett typmatchningsfel mellan den förväntade typen [System.Diagnostic.Process] och den angivna typen [Deserialized.System.Diagnostic.Process]. Du kan kringgå det här problemet genom att se till att modulens cmdlets inte accepterar komplexa typer för parametrar. Det här är fel sätt att göra det på.
   
    ```
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ``` 
    <br>
    Och det här är rätt sätt, där en primitiv hämtas som kan användas internt av cmdleten för att hämta det komplexa objektet och använda det. Eftersom cmdlets körs i PowerShell-kontexten, inte i PowerShell Workflow, blir $process rätt [System.Diagnostic.Process]-typ i cmdleten.  
   
    ```
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```
   <br>
   Anslutningstillgångar i runbooks är hash-tabeller, som är en komplex typ, och ändå verkar dessa hash-tabeller kunna användas utan problem i cmdlets för deras -Connection-parameter, utan typkonverteringsundantag. Rent tekniskt kan vissa PowerShell-typer konverteras korrekt från deras serialiserade form till deras avserialiserade form och kan därför skickas till cmdlets för parametrar som accepterar den icke-avserialiserade typen. Hashtable är ett exempel. Modulskaparens definierade typer kan implementeras på ett sätt så att de även kan deserialiseras korrekt, men det finns några nackdelar som du bör vara medveten om. Typen måste ha en standardkonstruktor och en PSTypeConverter, och alla dess egenskaper måste vara offentliga. Men för redan definierade typer som modulägaren inte äger finns det inget sätt att ”åtgärda” dem, därav rekommendationen att undvika komplexa typer för parametrar helt och hållet. Redigeringstips för runbook! Om dina cmdlets av någon anledning behöver använda en komplex parametertyp, eller om du använder någon annans modul som kräver det, kan du kringgå problemet i PowerShell Workflow-runbooks och PowerShell-arbetsflöden i lokala PowerShell genom att innesluta cmdleten som genererar den komplexa typen och cmdleten som använder den komplexa typen i samma InlineScript-aktivitet. Eftersom InlineScript kör sitt innehåll som PowerShell i stället för som ett PowerShell-arbetsflöde kommer cmdleten som genererar den komplexa typen att generera rätt typ, inte den avserialiserade komplexa typen.
5. Gör alla cmdlets i modulen tillståndslösa. PowerShell Workflow kör alla cmdlets som anropas i arbetsflödet i en annan session. Det innebär att cmdlets som är beroende av sessionstillstånd som skapas/ändras av andra cmdlets i samma modul inte fungerar i PowerShell Workflow-runbooks.  Här är ett exempel på vad du inte ska göra.
   
    ```
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
   <br>
6. Modulen bör finnas i ett Xcopy-aktiverat paket. Eftersom Azure Automation-moduler distribueras till begränsat Automation-läge när runbooks behöver köra, måste de fungera oberoende av värden som de körs på. Det innebär att du måste kunna dekomprimera modulpaketet, flytta det till en annan värd med samma eller en senare version av PowerShell och se till att det fungerar som vanligt när det importeras till den värdens PowerShell-miljö. För att det ska ske får modulen inte vara beroende av några filer utanför modulmappen (den mapp som dekomprimeras vid importen till Azure Automation) eller av unika registerinställningar på en värd, till exempel de som anges av installationsprogrammet för en produkt. Om du inte följer denna riktlinje kan modulen inte användas i Azure Automation.  

## <a name="next-steps"></a>Nästa steg

* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
* Mer information om hur du skapar PowerShell-moduler finns i [Skriva en Windows PowerShell-modul](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)

