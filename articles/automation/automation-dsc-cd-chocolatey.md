---
title: Azure Automation State Konfiguration kontinuerlig distribution med Chocolatey
description: DevOps kontinuerlig distribution med Azure Automation State Configuration, DSC och Chocolatey package manager.  Exempel med fullständig JSON Resource Manager-mall och PowerShell-källa.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 4445f6e9b72380b66f3282d50871b4283f7fc7fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966746"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Användningsexempel: Kontinuerlig distribution till virtuella datorer med hjälp av konfiguration av automationstillstånd och Chocolatey

I en DevOps-värld finns det många verktyg för att hjälpa till med olika punkter i pipelinen för kontinuerlig integrering. Azure Automation State Configuration är ett välkommet nytt tillägg till de alternativ som DevOps-team kan använda. Den här artikeln visar hur du konfigurerar CD (Continuous Deployment) för en Windows-dator. Du kan enkelt utöka tekniken till att omfatta så många Windows-datorer som behövs i rollen (till exempel en webbplats) och därifrån även till ytterligare roller.

![Kontinuerlig distribution för virtuella IaaS-datorer](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>På en hög nivå

Det är en hel del på gång här, men lyckligtvis kan det delas in i två huvudprocesser:

- Skriva kod och testa den och sedan skapa och publicera installationspaket för större och delversioner av systemet.
- Skapa och hantera virtuella datorer som installerar och kör koden i paketen.  

När båda dessa kärnprocesser är på plats är det ett kort steg för att automatiskt uppdatera paketet som körs på en viss virtuell dator när nya versioner skapas och distribueras.

## <a name="component-overview"></a>Översikt över komponenter

Pakethanterare som [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) är ganska välkända i Linux-världen, men inte så mycket i Windows-världen.
[Chocolatey](https://chocolatey.org/) är en sådan sak, och Scott Hanselman [blogg](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) i ämnet är en stor intro. I ett nötskal, Chocolatey kan du installera paket från en central lagringsplats av paket i ett Windows-system med hjälp av kommandoraden. Du kan skapa och hantera din egen databas, och Chocolatey kan installera paket från valfritt antal databaser som du anger.

DSC[(Overview) (önskad](/powershell/scripting/dsc/overview/overview)tillståndskonfiguration) är ett PowerShell-verktyg som gör att du kan deklarera den konfiguration som du vill ha för en dator. Till exempel kan du säga, "Jag vill Chocolatey installerat, jag vill IIS installerat, jag vill port 80 öppnas, jag vill version 1.0.0 av min hemsida installerad." DSC Local Configuration Manager (LCM) implementerar den konfigurationen. En DSC Pull Server har en databas med konfigurationer för dina datorer. LCM på varje dator checkar in regelbundet för att se om dess konfiguration matchar den lagrade konfigurationen. Det kan antingen rapportera status eller försöka få maskinen tillbaka i linje med den lagrade konfigurationen. Du kan redigera den lagrade konfigurationen på pull-servern så att en dator eller uppsättning datorer anpassas till den ändrade konfigurationen.

Azure Automation är en hanterad tjänst i Microsoft Azure som låter dig automatisera olika uppgifter med runbooks, noder, autentiseringsuppgifter, resurser och resurser som scheman och globala variabler.
Azure Automation State Configuration utökar den här automatiseringsfunktionen till att omfatta PowerShell DSC-verktyg. Här är en bra [översikt](automation-dsc-overview.md).

En DSC-resurs är en kodmodul som har specifika funktioner, till exempel hantering av nätverk, Active Directory eller SQL Server. Chocolatey DSC Resource vet hur man kommer åt en NuGet Server (bland annat), ladda ner paket, installera paket och så vidare. Det finns många andra DSC-resurser i [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).
Dessa moduler är installerade i din Azure Automation State Configuration Pull Server (av dig) så att de kan användas av dina konfigurationer.

Resource Manager-mallar är ett deklarativt sätt att generera din infrastruktur – saker som nätverk, undernät, nätverkssäkerhet och routning, belastningsutjämnare, nätverkskort, virtuella datorer och så vidare. Här är en [artikel](../azure-resource-manager/management/deployment-models.md) som jämför Resource Manager-distributionsmodellen (deklarativ) med Azure Service Management (ASM eller klassisk) distributionsmodell (imperativ) och diskuterar de viktigaste resursleverantörerna, beräknings-, lagrings- och nätverket.

En viktig funktion i en Resource Manager-mall är dess förmåga att installera ett VM-tillägg i den virtuella datorn när den är etablerad. Ett VM-tillägg har specifika funktioner som att köra ett anpassat skript, installera antivirusprogram eller köra ett DSC-konfigurationsskript. Det finns många andra typer av VM-tillägg.

## <a name="quick-trip-around-the-diagram"></a>Snabb resa runt diagrammet

Från början skriver du din kod, bygger och testar och skapar sedan ett installationspaket.
Chocolatey kan hantera olika typer av installationspaket, såsom MSI, MSU, ZIP. Och du har den fulla kraften i PowerShell att göra själva installationen om Chocolateys infödda funktioner är inte riktigt upp till det. Placera paketet på något ställe som kan nås – en paketarkiv. Det här användningsexemplet använder en gemensam mapp i ett Azure blob storage-konto, men det kan vara var som helst. Chocolatey fungerar inbyggt med NuGet-servrar och några andra för hantering av paketmetadata. [I](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) den här artikeln beskrivs alternativen. I det här användningsexemplet används NuGet. En Nuspec är metadata om dina paket. Nuspec's är "kompilerade" i NuPkg's och lagras i en NuGet-server. När konfigurationen begär ett paket efter namn och refererar till en NuGet-server tar Chocolatey DSC-resursen (nu på den virtuella datorn) tag i paketet och installerar det åt dig. Du kan också begära en viss version av ett paket.

I den nedre vänstra delen av bilden finns en Azure Resource Manager-mall. I det här användningsexemplet registrerar VM-tillägget den virtuella datorn med Azure Automation State Configuration Pull Server (det vill säga en pull-server) som en nod. Konfigurationen lagras i pull-servern.
Egentligen är det lagras två gånger: en gång som klartext och en gång sammanställts som en MOF-fil (för dem som vet om sådana saker.) I portalen är MOF en "nodkonfiguration" (i motsats till helt enkelt "konfiguration"). Det är artefakten som är associerad med en nod så att noden känner till dess konfiguration. Information nedan visar hur du tilldelar nodkonfigurationen till noden.

Förmodligen du redan gör lite på toppen, eller det mesta. Skapa nuspec, sammanställa och lagra den i en NuGet server är en liten sak. Och du hanterar redan virtuella datorer. För att ta nästa steg till kontinuerlig distribution måste du konfigurera pull-servern (en gång), registrera dina noder med den (en gång) och skapa och lagra konfigurationen där (inledningsvis). Sedan när paket uppgraderas och distribueras till databasen, uppdatera konfigurations- och nodkonfigurationen i pull-servern (upprepa efter behov).

Om du inte börjar med en Resource Manager-mall är det också OK. Det finns PowerShell-cmdlets som hjälper dig att registrera dina virtuella datorer med pull-servern och alla de andra. Mer information finns i den här artikeln: [Onboarding-datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md).

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Steg 1: Ställa in pull-server- och automationskontot

På en autentiserat (`Connect-AzureRmAccount`) PowerShell-kommandorad: (kan ta några minuter medan pull-servern är inställd)

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Du kan placera ditt automationskonto i någon av följande regioner (aka plats): Östra USA 2, Södra centrala USA, US Gov Virginia, Västeuropa, Sydostasien, Japan East, Centrala Indien och Australien Sydost, Kanada Central, Norra Europa.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>Steg 2: Vm-tillägg tweaks till Resource Manager mall

Information om VM-registrering (med powershell DSC-VM-tillägget) som finns i den här [Azure Quickstart-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Det här steget registrerar den nya virtuella datorn med pull-servern i listan över tillståndskonfigurationsnoder. En del av den här registreringen anger den nodkonfiguration som ska tillämpas på noden. Den här nodkonfigurationen behöver inte finnas ännu i pull-servern, så det är OK att steg 4 är där detta görs för första gången. Men här i steg 2 måste du ha bestämt namnet på noden och namnet på konfigurationen. I det här användningsexemplet är noden 'isvbox' och konfigurationen är 'ISVBoxConfig'. Så nodkonfigurationsnamnet (som ska anges i DeploymentTemplate.json) är ISVBoxConfig.isvbox.

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Steg 3: Lägga till nödvändiga DSC-resurser till pull-servern

PowerShell-galleriet är instrumenterat för att installera DSC-resurser i ditt Azure Automation-konto.
Navigera till den resurs du vill ha och klicka på knappen "Distribuera till Azure Automation".

![Exempel på PowerShell-galleri](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

En annan teknik som nyligen lagts till i Azure Portal kan du dra in nya moduler eller uppdatera befintliga moduler. Klicka dig igenom resursen Automation-konto, panelen Tillgångar och slutligen panelen Moduler. I ikonen Bläddra i galleri kan du se listan över moduler i galleriet, öka detaljnivån i information och slutligen importera till ditt Automation-konto. Detta är ett bra sätt att hålla dina moduler uppdaterade från tid till annan. Och importfunktionen kontrollerar beroenden med andra moduler för att säkerställa att inget blir osynkroniserat.

Eller så är det den manuella metoden. Mappstrukturen för en PowerShell-integrationsmodul för en Windows-dator skiljer sig lite från den mappstruktur som förväntas av Azure Automation.
Detta kräver lite tweaking från din sida. Men det är inte svårt, och det görs bara en gång per resurs (om du inte vill uppgradera den i framtiden.) Mer information om hur du redigerar PowerShell-integrationsmoduler finns i den här artikeln: [Redigera integrationsmoduler för Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- Installera modulen som du behöver på din arbetsstation enligt följande:
  - Installera [Windows Management Framework, v5](https://aka.ms/wmf5latest) (behövs inte för Windows 10)
  - `Install-Module –Name MODULE-NAME`< – tar tag i modulen från PowerShell-galleriet
- Kopiera modulmappen från `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` till en temp-mapp
- Ta bort exempel och dokumentation från huvudmappen
- Zip huvudmappen, namnge ZIP-filen exakt samma som mappen 
- Placera ZIP-filen på en HTTP-plats som kan nås, till exempel blob-lagring i ett Azure Storage-konto.
- Kör den här PowerShell:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

I exemplet medföljanden utförs dessa steg för cChoco och xNetworking. Se [Anteckningar för](#notes) specialhantering för cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Steg 4: Lägga till nodkonfigurationen i pull-servern

Det finns inget speciellt med första gången du importerar din konfiguration till pull-servern och kompilerar. Alla efterföljande import/kompileatorer av samma konfiguration ser exakt likadana ut. Varje gång du uppdaterar ditt paket och behöver skicka ut det till produktion gör du det här steget efter att konfigurationsfilen är korrekt – inklusive den nya versionen av paketet. Här är konfigurationsfilen och PowerShell:

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

Ny konfiguration.ps1:

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

Dessa steg resulterar i en ny nod konfiguration med namnet "ISVBoxConfig.isvbox" placeras på pull-servern. Nodkonfigurationsnamnet är byggt som "configurationName.nodeName".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Steg 5: Skapa och underhålla paketmetadata

För varje paket som du lägger in i paketarkivet behöver du en nuspec som beskriver det.
Det nuspec måste kompileras och lagras i din NuGet-server. Denna process beskrivs [här](https://docs.nuget.org/create/creating-and-publishing-a-package). Du kan använda MyGet.org som NuGet-server. De säljer denna tjänst, men har en förrätt SKU som är gratis. På NuGet.org hittar du instruktioner om hur du installerar din egen NuGet-server för dina privata paket.

## <a name="step-6-tying-it-all-together"></a>Steg 6: Knyta ihop allt

Varje gång en version passerar QA och godkänns för distribution skapas paketet, nuspec och nupkg uppdateras och distribueras till NuGet-servern. Dessutom måste konfigurationen (steg 4 ovan) uppdateras för att komma överens med det nya versionsnumret. Den måste skickas till pull-servern och kompileras.
Från och med då är det upp till de virtuella datorerna som är beroende av den konfigurationen att hämta uppdateringen och installera den. Var och en av dessa uppdateringar är enkla - bara en rad eller två av PowerShell. När det gäller Azure DevOps är några av dem inkapslade i bygguppgifter som kan kedjas ihop i en version. Den här [artikeln](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) innehåller mer information. Den här [GitHub-repoen](https://github.com/Microsoft/vso-agent-tasks) information om de olika tillgängliga bygguppgifterna.

## <a name="notes"></a>Anteckningar

Det här användningsexemplet börjar med en virtuell dator från en allmän Windows Server 2012 R2-avbildning från Azure-galleriet. Du kan börja från alla lagrade avbildningar och sedan justera därifrån med DSC-konfigurationen.
Att ändra konfiguration som bakas in i en avbildning är dock mycket svårare än att dynamiskt uppdatera konfigurationen med DSC.

Du behöver inte använda en Resource Manager-mall och vm-tillägget för att använda den här tekniken med dina virtuella datorer. Och dina virtuella datorer behöver inte vara på Azure för att vara under CD-hantering. Allt som behövs är att Chocolatey installeras och LCM konfigureras på den virtuella datorn så att den vet var pull-servern är.

När du uppdaterar ett paket på en virtuell dator som är i produktion måste du naturligtvis ta den virtuella datorn ur rotation medan uppdateringen är installerad. Hur du gör detta varierar kraftigt. Med en virtuell dator bakom en Azure Load Balancer kan du till exempel lägga till en anpassad avsökning. När du uppdaterar den virtuella datorn, har avsökningen slutpunkten returnera en 400. Den tweak nödvändigt att orsaka denna förändring kan vara inne i din konfiguration, som kan tweak att växla tillbaka till att returnera en 200 när uppdateringen är klar.

Fullständig källa för det här användningsexemplet finns i [det här Visual Studio-projektet](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) på GitHub.

## <a name="related-articles"></a>Relaterade artiklar
* [Översikt över Azure Automation DSC](automation-dsc-overview.md)
* [Azure Automation DSC-cmdlets](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Introduktionsdatorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Nästa steg

- En översikt finns i Konfiguration av [Azure Automation State](automation-dsc-overview.md)
- Information om hur du kommer igång finns [i Komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md)
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till målnoder finns [i Kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md)
- Cmdlet-referens för PowerShell finns i [cmdlets för Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation)
- Prisinformation finns i [prissättningen för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Information om hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i [Kontinuerlig distribution med Azure Automation State Configuration och Chocolatey](automation-dsc-cd-chocolatey.md)
