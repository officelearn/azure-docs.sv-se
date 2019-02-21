---
title: Azure Automation State Configuration kontinuerlig distribution med Chocolatey
description: DevOps kontinuerlig distribution med tillståndskonfigurationen för Azure Automation DSC och Chocolatey-Pakethanteraren.  Exempel med fullständig JSON Resource Manager-mall och PowerShell-källa.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3eb68c4394afeb4719d92fb56d3ae9028d8566c9
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56456120"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Användningsexempel: Kontinuerlig distribution till virtuella datorer med Automation-Tillståndskonfiguration och Chocolatey

Det finns många verktyg som hjälper dig med olika punkter i pipeline för kontinuerlig integrering i en DevOps-värld. Azure Automation State Configuration är en Välkommen till de alternativ som DevOps-team kan använda. Den här artikeln visar inställningen upp kontinuerlig distribution (CD) för en Windows-dator. Du kan enkelt utöka tekniken för att inkludera så många Windows-datorer efter behov i rollen (till exempel en webbplats), och därifrån att ytterligare roller samt.

![Kontinuerlig distribution för virtuella IaaS-datorer](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>På hög nivå

Det finns en hel som händer här, men som tur är det kan delas upp i två huvudsakliga processer:

- Skriva kod och testa det, skapar och publicerar installationspaket för större och den lägre versionen av systemet.
- Skapa och hantera virtuella datorer som ska installera och köra koden i paketen.  

När båda dessa kärnprocesser är på plats är det ett kort steg för att automatiskt uppdatera paketet körs på en viss virtuell dator som nya versioner skapas och distribueras.

## <a name="component-overview"></a>Komponent-översikt

Paketera chefer som [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) är ganska välkända i Linux-världen, men inte så mycket Windows över hela världen.
[Chocolatey](https://chocolatey.org/) är sådana en sak och Scott Hanselman [blogg](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) på avsnittet är en bra introduktion. Kortfattat, kan Chocolatey du installera paket från en central lagringsplats med paket i ett Windows-system från kommandoraden. Du kan skapa och hantera din egen lagringsplats och Chocolatey kan installera paket från valfritt antal databaser som du har angett.

Desired State Configuration (DSC) ([översikt](/powershell/dsc/overview)) är ett PowerShell-verktyg som hjälper dig att deklarera den konfiguration som du vill använda för en dator. Anta exempelvis att du kan, ”jag vill Chocolatey installerad, jag vill att IIS är installerat, jag vill porten 80 är öppen, jag vill ha version 1.0.0 av min webbplats installeras”. DSC lokala Configuration Manager (LCM) implementerar konfigurationen. En DSC-Hämtningsserver innehåller en databas som konfigurationer för dina datorer. LCM på varje dator ansluter regelbundet till om dess konfiguration matchar lagrade konfigurationen. Det kan rapportera status, eller så kan du försöka att se datorn till justering till den lagrade konfigurationen. Du kan redigera den lagrade konfigurationen på hämtningsservern framkalla en dator eller en uppsättning datorer att komma i kombination med den ändrade konfigurationen.

Azure Automation är en hanterad tjänst i Microsoft Azure som gör det möjligt att automatisera olika uppgifter med hjälp av runbooks, noder, autentiseringsuppgifter, resurser och tillgångar, till exempel scheman och globala variabler.
Azure Automation State Configuration utökar den här automation-funktionen för att inkludera PowerShell DSC-verktyg. Här är en bra [översikt](automation-dsc-overview.md).

En DSC-resurs är en modul av kod som har specifika funktioner, t.ex hantering av nätverk, Active Directory, eller SQL Server. Chocolatey DSC-resursen vet hur man åtkomst till en NuGet-Server (bland annat), ladda ned paket, installera paket och så vidare. Det finns många andra DSC-resurser i den [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).
Dessa moduler är installerade i Azure Automation tillstånd hämta konfigurationsservern (som du) så att de kan användas av dina konfigurationer.

Resource Manager-mallar tillhandahåller en deklarativ metod för att generera din infrastruktur – till exempel nätverk, undernät, nätverkssäkerhet och routning, läsa in belastningsutjämnare, nätverkskort, virtuella datorer och så vidare. Här är en [artikeln](../azure-resource-manager/resource-manager-deployment-model.md) som jämför den Resource Manager-distributionsmodellen (deklarativt) med Azure Service Management (ASM eller klassisk)-distributionsmodellen (imperativt) och beskriver resursprovidrar core, databearbetning, lagring och nätverk.

En viktig egenskap i en Resource Manager-mallen är möjligheten att installera ett VM-tillägg på den virtuella datorn eftersom den har etablerats. Ett VM-tillägg har specifika funktioner, till exempel använda ett anpassat skript, installerar ett antivirusprogram eller ett DSC-konfigurationsskript. Det finns många andra typer av VM-tillägg.

## <a name="quick-trip-around-the-diagram"></a>Snabb resa runt i diagram

Från toppen du skriver koden, bygg och testa sedan skapa ett installationspaket.
Chocolatey kan hantera olika typer av installationspaket, till exempel MSI, MSU, ZIP. Och du har det mesta av PowerShell för att göra den faktiska installationen om Chocolateys inbyggda funktioner som inte är helt upp till den. Placera paketet i okänd nås – en paketdatabas. Det här exemplet för användning används en offentlig mapp i en Azure blob storage-konto, men det kan finnas var som helst. Chocolatey fungerar internt med NuGet-servrar och några andra för hantering av paketmetadata. [Den här artikeln](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) beskrivs alternativ. Det här exemplet användning använder NuGet. En Nuspec är metadata om dina paket. Nuspec ”kompileras” i Nupkgs och lagras i en NuGet-server. När din konfiguration av begäranden i ett paket med namnet och refererar till en NuGet-server, hämtar paketet Chocolatey DSC-resurs (nu på den virtuella datorn) och installerar den åt dig. Du kan också begära en viss version av ett paket.

I det nedre vänstra hörnet i bilden har en Azure Resource Manager-mall. I det här exemplet för användning registrerar VM-tillägget den virtuella datorn med Azure Automation tillstånd Pull konfigurationsservern (det vill säga en pull-server) som en nod. Konfigurationen har sparats i pull-servern.
Faktiskt, lagras den två gånger: en gång för som oformaterad text och när kompilerad som en MOF-fil (för de vet om sådant.) I portalen är MOF en ”nodkonfiguration” (i stället för bara ”configuration”). Det är den artefakt som är associerat med en nod, så att noden får information om dess konfiguration. Informationen nedan visar hur du tilldelar nodkonfigurationen till noden.

Antas vara gör du redan bitars högst upp eller de flesta av den. Skapar nuspec, kompilera och lagrar den i en NuGet-server är en liten sak. Och du hanterar redan virtuella datorer. Att ta nästa steg kontinuerlig distribution kräver att konfigurera hämtningsservern (en gång), registrera dina noder med det (en gång), och skapar och lagrar konfigurationen där (först). Uppdatera sedan som paket uppgraderas och distribueras till databasen, konfiguration och nodkonfiguration i pull-servern (Upprepa efter behov).

Om du inte börjar med en Resource Manager-mall, är det också OK. Det finns PowerShell-cmdlets som är utformade för att registrera dina virtuella datorer med pull-servern och alla resten. Mer information finns i den här artikeln: [Konfigurera datorer för hantering av Azure Automation-Tillståndskonfiguration](automation-dsc-onboarding.md).

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Steg 1: Konfigurera kontot för pull-servern och automation

När en autentiserad (`Connect-AzureRmAccount`) PowerShell-kommandoraden: (kan ta några minuter medan hämtningsservern har ställts in)

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Du kan placera ditt automation-konto i någon av följande regioner (även kallat plats): Östra USA 2, södra centrala USA, Virginia (USA-förvaltad region), Västeuropa, Sydostasien, östra Japan, östra, centrala Indien och Australien, sydöstra Australien, Kanada, centrala, Europa, norra.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>Steg 2: VM-tillägget justeringar för Resource Manager-mallen

Informationen för VM-registrering (med tillägget PowerShell DSC-VM) i det här [Azure-Snabbstartsmall](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Det här steget registrerar en ny virtuell dator med pull-servern i listan över noder för konfiguration av tillstånd. En del av denna registrering är anger nodkonfigurationen som ska tillämpas på noden. Den här noden konfigurationen behöver inte finns ännu i pull-server så att det är OK steg 4 är där detta görs för första gången. Men här i steg 2 behöver du har valt namnet på noden och namnet på konfigurationen. Noden är ”isvbox” i det här exemplet användning och konfigurationen är 'ISVBoxConfig'. Nodkonfigurationsnamn (om du vill ange i DeploymentTemplate.json) är ”ISVBoxConfig.isvbox”.

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Steg 3: Lägga till DSC-resurser som krävs på pull-servern

PowerShell-galleriet är utrustade för att installera DSC-resurser i Azure Automation-konto.
Navigera till den resurs du vill ha och klicka på knappen ”distribuera till Azure Automation”.

![Exempel på PowerShell-galleriet](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

En annan metod som nyligen har lagts till i Azure Portal kan du hämta nya moduler eller uppdatera befintliga modeller. Klicka dig igenom resursen i Automation-konto, sida vid sida med tillgångar och slutligen moduler panelen. Sök i galleri-ikonen kan du se listan över moduler i galleriet, granska nedåt i detaljerna och slutligen importera till ditt Automation-konto. Det här är ett bra sätt att hålla dina moduler uppdaterade från tid till annan. Och funktionen kontrollerar beroenden med andra moduler så att du vet ingenting synkroniserad.

Eller så finns den manuella metoden. Mappstrukturen i en PowerShell-modul för integrering för en Windows-dator skiljer sig lite från mappstrukturen som förväntas av Azure Automation.
Detta kräver lite justera från din sida. Men det är inte svårt och det görs bara en gång per resurs (såvida du inte vill uppgradera den i framtiden.) Mer information om redigering PowerShell integreringsmoduler finns i den här artikeln: [Redigering integreringsmoduler för Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- Installera modulen som du behöver på din arbetsstation, enligt följande:
  - Installera [Windows Management Framework, v5](https://aka.ms/wmf5latest) (behövs inte för Windows 10)
  - `Install-Module –Name MODULE-NAME`    < – hämtar modulen från PowerShell-galleriet
- Kopiera modulmappen från `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` till en tillfällig mapp
- Ta bort exempel och dokumentation från den huvudsakliga mappen
- ZIP-mappen huvudsakliga naming ZIP-filen exakt samma sätt som mappen 
- Placera ZIP-filen i en HTTP-plats som kan nås, till exempel blob storage i ett Azure Storage-konto.
- Kör det här PowerShell:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

Det inkluderade exemplet utför de här stegen för cChoco och xNetworking. Se den [anteckningar](#notes) för särskild hantering för cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Steg 4: Lägga till nodkonfigurationen på pull-servern

Det finns inget särskilt den första gången du importera konfigurationen till den pull-servern och kompilera. Alla efterföljande import/kompilerar med samma konfiguration är exakt lika. Varje gång du uppdaterar ditt paket och behöva skicka ut den till produktion göra du det här steget när du har säkerställt konfigurationsfilen är rätt – inklusive den nya versionen av paketet. Här är den konfigurationsfil och i PowerShell:

ISVBoxConfig.ps1:

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

New-ConfigurationScript.ps1:

```powershell
Import-AzureRmAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

De här stegen resulterar i en ny nodkonfiguration med namnet ”ISVBoxConfig.isvbox” som placerats på hämtningsservern. Namnet på noden configuration har utformats som ”configurationName.nodeName”.

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Steg 5: Skapa och underhålla paketmetadata

För varje paket som du placerar i paketdatabasen, behöver du en nuspec som beskriver den.
Den nuspec måste kompileras och lagras i NuGet-server. Den här processen beskrivs [här](https://docs.nuget.org/create/creating-and-publishing-a-package). Du kan använda MyGet.org som en NuGet-server. De sälja den här tjänsten, men har en starter SKU som är kostnadsfri. Du hittar anvisningar om hur du installerar en egen NuGet-server för dina privata paket på NuGet.org.

## <a name="step-6-tying-it-all-together"></a>Steg 6: Knyta ihop det allt på samma plats

Varje gång en version skickar QA och har godkänts för distribution, skapas paketet nuspec och nupkg uppdateras och distribueras till NuGet-servern. Dessutom måste du uppdatera konfigurationen (steg 4 ovan) för att komma överens med det nya versionsnumret. Det måste skickas till den pull-servern och kompileras.
Från den punkten på är det upp till de virtuella datorer som är beroende av att konfigurationen för att hämta uppdateringen och installera den. Var och en av de här uppdateringarna är enkla – bara en eller två rader av PowerShell. När det gäller Azure DevOps, är några av dem inkapslade i build-uppgifter som kan sammanlänkas i en version. Detta [artikeln](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) innehåller mer information. Detta [GitHub-lagringsplatsen](https://github.com/Microsoft/vso-agent-tasks) beskriver de olika tillgängliga build-uppgifterna.

## <a name="notes"></a>Anteckningar

Det här exemplet för användning som börjar med en virtuell dator från en allmän Windows Server 2012 R2-avbildning från Azure-galleriet. Du kan starta från alla lagrad bild och sedan justera därifrån med DSC-konfiguration.
Ändra konfiguration som är inbyggd i en bild är dock mycket svårare än att dynamiskt uppdaterar konfigurationen med DSC.

Du behöver använda Resource Manager-mall och VM-tillägget för att använda den här tekniken med dina virtuella datorer. Och dina virtuella datorer behöver inte finnas på Azure för att vara under CD-hantering. Allt som behövs är att Chocolatey installeras och LCM som konfigurerats på den virtuella datorn så att den vet var hämtningsservern finns.

Naturligtvis när du uppdaterar ett paket på en virtuell dator som är i produktion, måste du koppla den virtuella datorn bort från roteringen medan uppdateringen är installerad. Så här gör du varierar mycket. Till exempel med en virtuell dator bakom en belastningsutjämnare för Azure, du kan lägga till en anpassad avsökning. Vid uppdatering av den virtuella datorn, har du avsökningen slutpunkten returnerar en 400. Justering krävs för att göra den här ändringen kan ingå i din konfiguration kan justera om du vill växla tillbaka till returnerar 200 när uppdateringen är klar.

Fullständig källan för det här exemplet användningen är i [Visual Studio-projekt](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) på GitHub.

## <a name="related-articles"></a>Relaterade artiklar
* [Översikt över Azure Automation DSC](automation-dsc-overview.md)
* [Azure Automation DSC-cmdletar](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Konfigurera datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [tillståndskonfigurationen för Azure Automation](automation-dsc-overview.md)
- Kom igång genom att se [komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md)
- Läs om hur du kompilera DSC-konfigurationer så att du kan tilldela dem till målnoder i [kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md)
- PowerShell-cmdlet-referens, se [tillståndskonfigurationen för Azure Automation-cmdletar](/powershell/module/azurerm.automation/#automation)
- Information om priser finns i [priser för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Om du vill se ett exempel på hur du använder Azure Automation-Tillståndskonfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med hjälp av Azure Automation Tillståndskonfiguration och Chocolatey](automation-dsc-cd-chocolatey.md)
