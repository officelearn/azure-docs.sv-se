---
title: Azure Automation State Configuration kontinuerlig distribution med Chocolatey
description: Beskriver DevOps kontinuerlig distribution med azure automation-tillståndskonfiguration med Chocolatey-pakethanteraren. Innehåller ett exempel med fullständig JSON Resource Manager-mall och PowerShell-källa.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 706ab128af4379a56223ff65fb12f29d37b524f7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383265"
---
# <a name="provide-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Tillhandahålla kontinuerlig distribution till virtuella datorer med hjälp av Automation State Configuration och Chocolatey

I en DevOps-värld finns det många verktyg för att hjälpa till med olika punkter i den kontinuerliga integrationspipelinen. Azure Automation [State Configuration](automation-dsc-overview.md) är ett välkommet nytt tillägg till de alternativ som DevOps-team kan använda. 

Azure Automation är en hanterad tjänst i Microsoft Azure som gör att du kan automatisera olika uppgifter med runbooks, noder och delade resurser, till exempel autentiseringsuppgifter, scheman och globala variabler. Azure Automation State Configuration utökar den här automatiseringsfunktionen till att omfatta DSC-verktyg (PowerShell Desired State Configuration). Här är en bra [översikt](automation-dsc-overview.md).

Den här artikeln visar hur du konfigurerar CD (Continuous Deployment) för en Windows-dator. Du kan enkelt utöka tekniken till att omfatta så många Windows-datorer som behövs i rollen, till exempel en webbplats, och gå därifrån till ytterligare roller.

![Kontinuerlig distribution för virtuella IaaS-datorer](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="at-a-high-level"></a>På en hög nivå

Det är en hel del på gång här, men lyckligtvis kan det delas upp i två huvudprocesser:

- Skriva kod och testa den och sedan skapa och publicera installationspaket för större och delversioner av systemet.
- Skapa och hantera virtuella datorer som installerar och kör koden i paketen.  

När båda dessa kärnprocesser är på plats är det enkelt att automatiskt uppdatera paketet på dina virtuella datorer när nya versioner skapas och distribueras.

## <a name="component-overview"></a>Översikt över komponenter

Pakethanterare som [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) är välkända i Linux-världen, men inte så mycket i Windows-världen.
[Chocolatey](https://chocolatey.org/) är en sådan sak, och Scott Hanselman [blogg](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) i ämnet är en bra introduktion. I ett nötskal låter Chocolatey dig använda kommandoraden för att installera paket från en central databas på ett Windows-operativsystem. Du kan skapa och hantera din egen databas, och Chocolatey kan installera paket från valfritt antal databaser som du anger.

[PowerShell DSC](/powershell/scripting/dsc/overview/overview) är ett PowerShell-verktyg som låter dig deklarera den konfiguration som du vill ha för en dator. Om du till exempel vill att Chocolatey ska installeras, IIS installeras, port 80 öppnas och version 1.0.0 av din webbplats ska installeras implementerar DSC Local Configuration Manager (LCM) konfigurationen. En DSC-pull-server innehåller en databas med konfigurationer för dina datorer. LCM på varje dator checkar in regelbundet för att se om dess konfiguration matchar den lagrade konfigurationen. Det kan antingen rapportera status eller försöka få maskinen tillbaka i linje med den lagrade konfigurationen. Du kan redigera den lagrade konfigurationen på pull-servern så att en dator eller uppsättning datorer anpassas till den ändrade konfigurationen.

En DSC-resurs är en kodmodul som har specifika funktioner, till exempel hantering av nätverk, Active Directory eller SQL Server. Chocolatey DSC Resource vet hur man kommer åt en NuGet Server (bland annat), ladda ner paket, installera paket och så vidare. Det finns många andra DSC-resurser i [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title). Du installerar dessa moduler på din Azure Automation State Configuration pull-server för användning av dina konfigurationer.

Resource Manager-mallar är ett deklarativt sätt att generera infrastrukturen, till exempel nätverk, undernät, nätverkssäkerhet och routning, belastningsutjämnare, nätverkskort, virtuella datorer och så vidare. Här är en [artikel](../azure-resource-manager/management/deployment-models.md) som jämför Resource Manager-distributionsmodellen (deklarativ) med Azure Service Management (ASM eller klassisk) distributionsmodell (absolut). Den här artikeln innehåller en diskussion om de viktigaste resursleverantörerna: beräkning, lagring och nätverk.

En viktig funktion i en Resource Manager-mall är dess förmåga att installera ett VM-tillägg i den virtuella datorn när den är etablerad. Ett VM-tillägg har specifika funktioner, till exempel att köra ett anpassat skript, installera antivirusprogram och köra ett DSC-konfigurationsskript. Det finns många andra typer av VM-tillägg.

## <a name="quick-trip-around-the-diagram"></a>Snabb resa runt diagrammet

Börja högst upp, du skriver din kod, bygga den, testa den och sedan skapa ett installationspaket. Chocolatey kan hantera olika typer av installationspaket, såsom MSI, MSU, ZIP. Och du har den fulla kraften i PowerShell att göra själva installationen om Chocolatey inhemska funktioner inte är upp till det. Placera paketet på något ställe som kan nås – en paketarkiv. Det här användningsexemplet använder en gemensam mapp i ett Azure blob storage-konto, men det kan vara var som helst. Chocolatey fungerar inbyggt med NuGet-servrar och några andra för hantering av paketmetadata. [I](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) den här artikeln beskrivs alternativen. I användningsexemplet används NuGet. En Nuspec är metadata om dina paket. Nuspec-informationen sammanställs till en NuPkg och lagras på en NuGet-server. När konfigurationen begär ett paket efter namn och refererar till en NuGet-server tar Chocolatey DSC-resursen på den virtuella datorn tag i paketet och installerar det. Du kan också begära en viss version av ett paket.

Längst ned till vänster i bilden finns en Azure Resource Manager-mall. I det här användningsexemplet registrerar vm-tillägget den virtuella datorn med pull-servern för Azure Automation State Configuration som en nod. Konfigurationen lagras i pull-servern två gånger: en gång som oformaterad text och en gång kompileras som en MOF-fil. I Azure-portalen representerar MOF en nodkonfiguration, i motsats till en enkel konfiguration. Det är artefakten som är associerad med en nod så att noden känner till dess konfiguration. Information nedan visar hur du tilldelar nodkonfigurationen till noden.

Skapa Nuspec, sammanställa den och lagra den i en NuGet-server är en liten sak. Och du hanterar redan virtuella datorer. 

För att ta nästa steg till kontinuerlig distribution måste du konfigurera pull-servern en gång, registrera dina noder med den en gång och skapa och lagra den ursprungliga konfigurationen på servern. När paket uppgraderas och distribueras till databasen behöver du bara uppdatera konfigurations- och nodkonfigurationen på pull-servern efter behov.

Om du inte börjar med en Resource Manager-mall är det bra. Det finns PowerShell-kommandon som hjälper dig att registrera dina virtuella datorer med pull-servern. Mer information finns i [Onboarding-datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Om användningsexemplet

Användningsexemplet i den här artikeln börjar med en virtuell dator från en allmän Windows Server 2012 R2-avbildning från Azure-galleriet. Du kan börja från alla lagrade avbildningar och sedan justera därifrån med DSC-konfigurationen.
Att ändra konfiguration som bakas in i en avbildning är dock mycket svårare än att dynamiskt uppdatera konfigurationen med DSC.

Du behöver inte använda en Resource Manager-mall och vm-tillägget för att använda den här tekniken med dina virtuella datorer. Och dina virtuella datorer behöver inte vara på Azure för att vara under CD-hantering. Allt som behövs är att Chocolatey installeras och LCM konfigureras på den virtuella datorn så att den vet var pull-servern är.

När du uppdaterar ett paket på en virtuell dator som är i produktion måste du ta den virtuella datorn ur rotation medan uppdateringen är installerad. Hur du gör detta varierar kraftigt. Med en virtuell dator bakom en Azure Load Balancer kan du till exempel lägga till en anpassad avsökning. När du uppdaterar den virtuella datorn, har avsökningen slutpunkten returnera en 400. Den tweak nödvändigt att orsaka denna förändring kan vara inne i din konfiguration, som kan tweak att växla tillbaka till att returnera en 200 när uppdateringen är klar.

Fullständig källa för det här användningsexemplet finns i [det här Visual Studio-projektet](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) på GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Steg 1: Konfigurera pull-servern och Automation-kontot

På en autentiserat (`Connect-AzAccount`) PowerShell-kommandorad: (kan ta några minuter medan pull-servern är inställd)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Du kan placera ditt Automation-konto i någon av följande regioner (kallas även platser): Östra USA 2, Södra centrala USA, US Gov Virginia, Västeuropa, Sydostasien, Östra Japan, Centrala Indien och Australien Sydost, Kanada Central, Norra Europa.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Steg 2: Gör vm-tilläggsjusteringar till Resource Manager-mallen

Information om VM-registrering (med powershell DSC-VM-tillägget) som finns i den här [Azure Quickstart-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Det här steget registrerar den nya virtuella datorn med pull-servern i listan över tillståndskonfigurationsnoder. En del av den här registreringen anger den nodkonfiguration som ska tillämpas på noden. Den här nodkonfigurationen behöver inte finnas ännu i pull-servern, så det är bra att steg 4 är där detta görs för första gången. Men här i steg 2 måste du ha bestämt namnet på noden och namnet på konfigurationen. I det här användningsexemplet är noden 'isvbox' och konfigurationen är 'ISVBoxConfig'. Så nodkonfigurationsnamnet (som ska anges i DeploymentTemplate.json) är ISVBoxConfig.isvbox.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Steg 3: Lägg till nödvändiga DSC-resurser till pull-servern

PowerShell-galleriet är instrumenterat för att installera DSC-resurser i ditt Azure Automation-konto.
Navigera till den resurs du vill ha och klicka på knappen "Distribuera till Azure Automation".

![Exempel på PowerShell-galleri](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

En annan teknik som nyligen lagts till i Azure-portalen kan du hämta nya moduler eller uppdatera befintliga moduler. Klicka dig igenom automationskontoresursen, panelen Tillgångar och slutligen panelen Moduler. I ikonen Bläddra i galleri kan du se listan över moduler i galleriet, öka detaljnivån i information och slutligen importera till ditt Automation-konto. Detta är ett bra sätt att hålla dina moduler uppdaterade från tid till annan. Och importfunktionen kontrollerar beroenden med andra moduler för att säkerställa att inget blir osynkroniserat.

Eller så är det den manuella metoden. Den här metoden används bara en gång per resurs, om du inte vill uppgradera den senare. Mer information om hur du redigerar PowerShell-integrationsmoduler finns i [Skapa integrationsmoduler för Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

>[!NOTE]
>Mappstrukturen för en PowerShell-integrationsmodul för en Windows-dator skiljer sig lite från den mappstruktur som förväntas av Azure Automation. 

1. Installera [Windows Management Framework v5](https://aka.ms/wmf5latest) (behövs inte för Windows 10).

2. Installera integrationsmodulen.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Kopiera modulmappen från **c:\Program\WindowsPowerShell\Modules\MODULE-NAME** till en tillfällig mapp.

4. Ta bort exempel och dokumentation från huvudmappen.

5. Zip huvudmappen och namnge ZIP-filen med namnet på mappen.

6. Placera ZIP-filen på en HTTP-plats som kan nås, till exempel blob-lagring i ett Azure Storage-konto.

7. Kör följande kommando.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

I exemplet medföljande implementeringar dessa steg för cChoco och xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Steg 4: Lägg till nodkonfigurationen i pull-servern

Det finns inget speciellt med första gången du importerar din konfiguration till pull-servern och kompilerar. Alla senare importer eller kompileringar av samma konfiguration ser exakt likadana ut. Varje gång du uppdaterar ditt paket och behöver skicka ut det till produktion gör du det här steget efter att konfigurationsfilen är korrekt – inklusive den nya versionen av paketet. Här är konfigurationsfilen **ISVBoxConfig.ps1:**

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

Här är **new-ConfigurationScript.ps1** skriptet (ändras för att använda Az-modulen):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Dessa steg resulterar i att en ny nodkonfiguration med namnet **ISVBoxConfig.isvbox** placeras på pull-servern. Nodkonfigurationsnamnet är `configurationName.nodeName`byggt som .

## <a name="step-5-create-and-maintain-package-metadata"></a>Steg 5: Skapa och underhålla paketmetadata

För varje paket som du lägger in i paketarkivet behöver du en nuspec som beskriver det.
Det nuspec måste kompileras och lagras i din NuGet-server. Denna process beskrivs [här](https://docs.nuget.org/create/creating-and-publishing-a-package). Du kan använda MyGet.org som NuGet-server. De säljer denna tjänst, men har en förrätt SKU som är gratis. På NuGet.org hittar du instruktioner om hur du installerar din egen NuGet-server för dina privata paket.

## <a name="step-6-tie-it-all-together"></a>Steg 6: Knyt ihop allt

Varje gång en version passerar QA och godkänns för distribution skapas paketet och nuspec och nupkg uppdateras och distribueras till NuGet-servern. Konfigurationen (steg 4 ovan) måste också uppdateras för att komma överens med det nya versionsnumret. Det måste sedan skickas till pull-servern och kompileras.

Från och med då är det upp till de virtuella datorerna som är beroende av den konfigurationen att hämta uppdateringen och installera den. Var och en av dessa uppdateringar är enkel - bara en rad eller två av PowerShell. För Azure DevOps är några av dem inkapslade i bygguppgifter som kan kedjas ihop i en version. Den här [artikeln](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) innehåller mer information. Den här [GitHub-repoen](https://github.com/Microsoft/vso-agent-tasks) information om tillgängliga bygguppgifter.

## <a name="related-articles"></a>Relaterade artiklar
* [Översikt över Azure Automation DSC](automation-dsc-overview.md)
* [Azure Automation DSC-cmdlets](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Introduktionsdatorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Azure Automation State Configuration](automation-dsc-overview.md).
- Information om hur du kommer igång finns [i Komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till målnoder finns [i Kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md).
- En PowerShell-cmdlet-referens finns i [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Prisinformation finns i [prissättningen för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Information om hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i [Kontinuerlig distribution med Azure Automation State Configuration och Chocolatey](automation-dsc-cd-chocolatey.md).
