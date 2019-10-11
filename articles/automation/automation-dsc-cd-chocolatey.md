---
title: Azure Automation kontinuerlig distribution av tillstånds konfiguration med choklad
description: DevOps kontinuerlig distribution med Azure Automation tillstånds konfiguration, DSC och choklad paket hanterare.  Exempel med fullständig JSON Resource Manager-mall och PowerShell-källa.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f4512b79873d7f770b32a452a02c53bc5575bdac
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243594"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Användnings exempel: kontinuerlig distribution till Virtual Machines med automatiserings tillstånds konfiguration och choklad

I en DevOps-värld finns det många verktyg för att hjälpa till med olika punkter i den kontinuerliga integrerings pipelinen. Azure Automation tillstånds konfiguration är ett välkomst tillägg till de alternativ som DevOps team kan använda. Den här artikeln beskriver hur du konfigurerar kontinuerlig distribution (CD) för en Windows-dator. Du kan enkelt utöka tekniken för att inkludera så många Windows-datorer som behövs i rollen (t. ex. en webbplats, till exempel) och även från där till ytterligare roller.

![Kontinuerlig distribution för virtuella IaaS-datorer](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>På hög nivå

Det är mycket en stund att fortsätta här, men lyckligt vis kan den delas upp i två huvud processer:

- Skriva kod och testa den och sedan skapa och publicera installations paket för högre och lägre versioner av systemet.
- Skapa och hantera virtuella datorer som ska installera och köra koden i paketen.  

När båda dessa kärn processer är på plats är det ett kort steg för att automatiskt uppdatera det paket som körs på en viss virtuell dator när nya versioner skapas och distribueras.

## <a name="component-overview"></a>Komponent översikt

Paket hanterare som [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) är välkända i Linux-världen, men inte så mycket i Windows-världen.
[Choklad](https://chocolatey.org/) är en sådan sak och Scott Hanselmans [blogg](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) i avsnittet är en bra introduktion. Med en kortfattat så Jenkins kan du installera paket från en central lagrings plats för paket i ett Windows-system med hjälp av kommando raden. Du kan skapa och hantera din egen lagrings plats och choklad kan installera paket från valfritt antal databaser som du anger.

Önskad tillstånds konfiguration (DSC) ([Översikt](/powershell/scripting/dsc/overview/overview)) är ett PowerShell-verktyg som du kan använda för att deklarera den konfiguration som du vill använda för en dator. Du kan till exempel säga att "Jag vill ha choklad installerat, jag vill att IIS ska installeras, jag vill att port 80 öppnas, jag vill att version 1.0.0 av min webbplats ska installeras." Den lokala DSC-Configuration Manager (LCM) implementerar den konfigurationen. En DSC-pull-server innehåller en databas med konfigurationer för dina datorer. LCM på varje dator kontrollerar regelbundet för att se om konfigurationen matchar den lagrade konfigurationen. Den kan antingen rapportera status eller försöka återställa datorn till en justering med den lagrade konfigurationen. Du kan redigera den lagrade konfigurationen på hämtnings servern för att få en dator eller en uppsättning datorer att komma i justering med den ändrade konfigurationen.

Azure Automation är en hanterad tjänst i Microsoft Azure som gör att du kan automatisera olika uppgifter med hjälp av Runbooks, noder, autentiseringsuppgifter, resurser och till gångar som scheman och globala variabler.
Azure Automation tillstånds konfiguration utökar den här Automation-funktionen för att inkludera PowerShell DSC-verktyg. Här är en bra [Översikt](automation-dsc-overview.md).

En DSC-resurs är en modul med kod som har vissa funktioner, till exempel hantering av nätverk, Active Directory eller SQL Server. Den choklad DSC-resursen vet hur man kommer åt en NuGet-Server (bland annat), laddar ned paket, installerar paket och så vidare. Det finns många andra DSC-resurser i [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).
Dessa moduler installeras i din Azure Automation hämtnings Server för tillstånds konfiguration (av dig) så att de kan användas av dina konfigurationer.

Resource Manager-mallar ger ett deklarativ sätt att skapa infrastrukturen – saker som nätverk, undernät, nätverks säkerhet och routning, belastningsutjämnare, nätverkskort, virtuella datorer och så vidare. Här är en [artikel](../azure-resource-manager/resource-manager-deployment-model.md) som jämför Resource Manager-distributions modellen (deklarativ) med distributions modellen för Azure Service Management (ASM eller klassisk) (tvingande) och diskuterar huvud resurs leverantörer, beräkning, lagring och nätverk.

En viktig funktion i en Resource Manager-mall är möjligheten att installera ett VM-tillägg i den virtuella datorn när det har tillhandahållits. Ett VM-tillägg har vissa funktioner som att köra ett anpassat skript, installera antivirus program eller köra ett konfigurations skript för DSC. Det finns många andra typer av VM-tillägg.

## <a name="quick-trip-around-the-diagram"></a>Snabb resa runt diagrammet

Börja överst, Skriv din kod, skapa och testa och skapa sedan ett installations paket.
Choklad kan hantera olika typer av installations paket, till exempel MSI, MSU, ZIP. Och du har full kraften hos PowerShell för att utföra den faktiska installationen om de inbyggda funktionerna i Chokladet inte är upp till den. Placera paketet på en plats som kan uppnås – en paket lagrings plats. I det här användnings exemplet används en offentlig mapp i ett Azure Blob Storage-konto, men det kan vara var som helst. Choklad fungerar internt med NuGet-servrar och några andra för hantering av paketets metadata. I [den här artikeln](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) beskrivs alternativen. I det här användnings exemplet används NuGet. En nuspec är metadata om dina paket. Nuspec är "kompilerade" i NuPkg och lagras i en NuGet-Server. När din konfiguration begär ett paket efter namn och refererar till en NuGet-Server, hämtar den choklad DSC-resursen (nu på den virtuella datorn) paketet och installerar det åt dig. Du kan också begära en speciell version av ett paket.

I den nedre vänstra delen av bilden finns en Azure Resource Manager mall. I det här användnings exemplet registrerar VM-tillägget den virtuella datorn med hämtnings servern för Azure Automation tillstånds konfiguration (det vill säga en pull-server) som en nod. Konfigurationen lagras i hämtnings servern.
Faktiskt lagras den två gånger: en gång som vanlig text och kompilerad som en MOF-fil (för dem som vet om sådana saker). I portalen är MOF en "Node Configuration" (till skillnad från enbart "konfiguration"). Det är den artefakt som är associerad med en nod så att noden känner till konfigurationen. Information nedan visar hur du tilldelar nodens konfiguration till noden.

Det förmodas att du redan är den högsta delen eller det mesta. Att skapa nuspec, kompilera och lagra den i en NuGet-Server är en liten sak. Och du redan hanterar virtuella datorer. Om du tar nästa steg i kontinuerlig distribution måste du konfigurera hämtnings servern (en gång), registrera dina noder med den (en gång) och skapa och lagra konfigurationen där (från början). När paket uppgraderas och distribueras till lagrings platsen uppdaterar du konfigurationen och nodens konfiguration i hämtnings servern (Upprepa vid behov).

Om du inte börjar med en Resource Manager-mall är det också OK. Det finns PowerShell-cmdletar utformade för att hjälpa dig att registrera dina virtuella datorer med hämtnings servern och resten av resten. Mer information finns i den här artikeln: [onboarding Machines for Management by Azure Automation State Configuration](automation-dsc-onboarding.md).

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Steg 1: Konfigurera pull-servern och automation-kontot

På en autentiserad (`Connect-AzureRmAccount`) PowerShell-kommando rad: (kan ta några minuter medan hämtnings servern har kon figurer ATS)

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Du kan använda ditt Automation-konto i någon av följande regioner (aka location): östra USA 2, södra centrala USA, US Gov, Virginia, Västeuropa, Sydostasien, Östra Japan, centrala Indien och Australien, sydöstra, centrala Kanada, Nord Europa.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>Steg 2: VM-tillägget anpassas till Resource Manager-mallen

Information om VM-registrering (med PowerShell DSC VM-tillägg) som tillhandahålls i den här [Azure snabb starts mal len](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Det här steget registrerar din nya virtuella dator med hämtnings servern i listan över noder för tillstånds konfiguration. En del av den här registreringen anger den Node-konfiguration som ska tillämpas på noden. Den här noden behöver inte finnas ännu i hämtnings servern, så det är OK som steg 4 är där detta görs för första gången. Men här i steg 2 måste du ha valt namnet på noden och namnet på konfigurationen. I det här användnings exemplet är noden "isvbox" och konfigurationen är "ISVBoxConfig". Konfigurations namnet för noden (som ska anges i DeploymentTemplate. JSON) är "ISVBoxConfig. isvbox".

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Steg 3: lägga till nödvändiga DSC-resurser till hämtnings servern

PowerShell-galleriet instrumenteras för att installera DSC-resurser i ditt Azure Automation-konto.
Navigera till den resurs du vill använda och klicka på knappen "distribuera till Azure Automation".

![PowerShell-galleriet exempel](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

En annan teknik som nyligen har lagts till i Azure-portalen gör att du kan hämta nya moduler eller uppdatera befintliga moduler. Klicka via kontot för Automation-kontot, panelen till gångar och slutligen panelen moduler. Med ikonen Bläddra i galleriet kan du se en lista över moduler i galleriet, öka detalj nivån till information och slutligen importera till ditt Automation-konto. Detta är ett bra sätt att hålla dina moduler uppdaterade från tid till gång. Dessutom kontrollerar import funktionen beroenden med andra moduler för att se till att ingen blir osynkroniserade.

Eller så finns det en manuell metod. Mappstrukturen för en PowerShell-integrering för en Windows-dator skiljer sig lite från mappstrukturen som förväntas av Azure Automation.
Detta kräver lite lite justeringar av din del. Men det är inte svårt, och det sker bara en gång per resurs (om du inte vill uppgradera den i framtiden). Mer information om hur du redigerar PowerShell-integrations moduler finns i den här artikeln: [Redigera integrations moduler för Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- Installera den modul du behöver på din arbets Station enligt följande:
  - Installera [Windows Management Framework, V5](https://aka.ms/wmf5latest) (behövs inte för Windows 10)
  - `Install-Module –Name MODULE-NAME` < – hämtar modulen från PowerShell-galleriet
- Kopiera mappen module från `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` till en tillfällig mapp
- Ta bort exempel och dokumentation från huvudmappen
- Zippa huvudmappen, namnge ZIP-filen exakt på samma sätt som mappen 
- Sätt ZIP-filen i en nåbar HTTP-plats, till exempel Blob Storage i ett Azure Storage konto.
- Kör denna PowerShell:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

Det inkluderade exemplet utför de här stegen för cChoco och xNetworking. Se [anteckningar](#notes) för särskild hantering av cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Steg 4: lägga till nodens konfiguration på hämtnings servern

Det finns inget särskilt om första gången du importerar konfigurationen till pull-servern och kompilerar. All efterföljande import/kompilering av samma konfiguration ser exakt likadant ut. Varje gång du uppdaterar ditt paket och behöver skicka ut det till produktion kan du göra det här steget när du har säkerställt att konfigurations filen är korrekt, inklusive den nya versionen av paketet. Här är konfigurations filen och PowerShell:

ISVBoxConfig. ps1:

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

New-ConfigurationScript. ps1:

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

De här stegen leder till att en ny Node-konfiguration med namnet "ISVBoxConfig. isvbox" placeras på hämtnings servern. Konfigurations namnet för noden har skapats som "configurationName. nodnamn".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Steg 5: skapa och underhålla paketets metadata

För varje paket som du infogar i paket lagrings platsen behöver du en nuspec som beskriver det.
Den nuspec måste kompileras och lagras i din NuGet-Server. Den här processen beskrivs [här](https://docs.nuget.org/create/creating-and-publishing-a-package). Du kan använda MyGet.org som en NuGet-Server. De säljer tjänsten, men har en start-SKU som är kostnads fri. På NuGet.org hittar du instruktioner om hur du installerar din egen NuGet-Server för dina privata paket.

## <a name="step-6-tying-it-all-together"></a>Steg 6: knyta samman allt

Varje gången en version passerar frågor och svar har godkänts för distribution skapas paketet, nuspec och nupkg uppdateras och distribueras till NuGet-servern. Dessutom måste konfigurationen (steg 4 ovan) uppdateras för att godkännas med det nya versions numret. Det måste skickas till pull-servern och kompileras.
Från den tidpunkten är det upp till de virtuella datorer som är beroende av den konfigurationen för att hämta uppdateringen och installera den. Var och en av dessa uppdateringar är enkla – bara en rad eller två av PowerShell. När det gäller Azure-DevOps är en del av dem inkapslade i bygg aktiviteter som kan sammanställas tillsammans i en version. Den här [artikeln](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) innehåller mer information. Den här [GitHub-lagrings platsen](https://github.com/Microsoft/vso-agent-tasks) innehåller information om de olika tillgängliga build-uppgifterna.

## <a name="notes"></a>Anteckningar

Det här användnings exemplet börjar med en virtuell dator från en generisk Windows Server 2012 R2-avbildning från Azure-galleriet. Du kan starta från en lagrad avbildning och sedan ändra från DSC-konfigurationen.
Att ändra konfigurationen för bakade till en avbildning är dock mycket svårare än att dynamiskt uppdatera konfigurationen med DSC.

Du behöver inte använda en Resource Manager-mall och VM-tillägget för att använda den här tekniken med dina virtuella datorer. Och de virtuella datorerna behöver inte vara på Azure för att vara under CD-hantering. Allt som krävs är att choklad installeras och att LCM har kon figurer ATS på den virtuella datorn så att den vet var hämtnings servern är.

När du uppdaterar ett paket på en virtuell dator som är i produktion måste du naturligtvis ta den virtuella datorn ur bruk medan uppdateringen installeras. Hur du gör detta varierar mycket. Med en virtuell dator bakom en Azure Load Balancer kan du till exempel lägga till en anpassad avsökning. När du uppdaterar den virtuella datorn måste avsöknings slut punkten returnera en 400. Den justeringar som krävs för att göra den här ändringen kan finnas i konfigurationen, så att du kan ändra tillbaka till att returnera en 200 när uppdateringen är klar.

Fullständig källa för det här användnings exemplet är i [det här Visual Studio-projektet](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) på GitHub.

## <a name="related-articles"></a>Relaterade artiklar
* [Översikt över Azure Automation DSC](automation-dsc-overview.md)
* [Azure Automation DSC-cmdletar](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Onboarding Machines for Management by Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Azure Automation tillstånds konfiguration](automation-dsc-overview.md)
- För att komma igång, se [komma igång med konfiguration av Azure Automation tillstånd](automation-dsc-getting-started.md)
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till mål noder finns i [kompilera konfigurationer i Azure Automation tillstånds konfiguration](automation-dsc-compile.md)
- Referens för PowerShell-cmdlet finns i [Azure Automation cmdlets för tillstånds konfiguration](/powershell/module/azurerm.automation/#automation)
- För pris information, se [priser för Azure Automation tillstånds konfiguration](https://azure.microsoft.com/pricing/details/automation/)
- Om du vill se ett exempel på hur du använder Azure Automation tillstånds konfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med Azure Automation tillstånds konfiguration och choklad](automation-dsc-cd-chocolatey.md)
