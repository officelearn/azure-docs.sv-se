---
title: Azure Automation DSC kontinuerlig distribution med Chocolatey
description: "DevOps kontinuerlig distribution med Azure Automation DSC och Chocolatey Pakethanteraren.  Exempel med fullständig JSON ARM-mallen och PowerShell-källa."
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: 8c1427bd40a6fd75a755c4709d88a4b8e4c55571
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-dsc-and-chocolatey"></a>Exempel på användning: Kontinuerlig distribution till virtuella datorer med hjälp av Automation DSC och Chocolatey
I en DevOps-värld finns många verktyg för att hjälpa till med olika punkter i pipeline för kontinuerlig Integration.  Azure Automation önskat tillståndskonfigurationen (DSC) är en Välkommen nya tillägg till de alternativ som DevOps team kan använda.  Den här artikeln visar inställningen in kontinuerlig distribution (CD) för en Windows-dator.  Du kan enkelt utöka tekniken för att inkludera så många Windows-datorer som behövs i roll (till exempel en webbplats), och därifrån samt ytterligare roller.

![Kontinuerlig distribution för IaaS-VM](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>På en hög nivå
Det finns lite som pågår, men som tur är det kan delas i två huvudsakliga processer: 

* Skriva kod och testa det, skapar och publicerar installationspaket för högre och lägre versioner av systemet. 
* Skapa och hantera virtuella datorer som ska installera och köra koden i paket.  

När båda dessa kärnprocesser är på plats är det ett kort steg för att automatiskt uppdatera paket som körs på en viss virtuell dator som nya versioner skapas och distribueras.

## <a name="component-overview"></a>Översikt över komponenten
Paketet chefer som [lgh get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) är ganska välkända i Linux-världen, men inte så mycket i Windows-världen.  [Chocolatey](https://chocolatey.org/) sådana sak och Scott Hanselman [blogg](http://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) på avsnittet är en bra introduktion.  I kort sagt kan kan Chocolatey du installera paket från en central databas av paket i en Windows-dator med hjälp av kommandoraden.  Du kan skapa och hantera egna databasen och Chocolatey kan installera paket från valfritt antal databaser som du har angett.

Desired Configuration tillstånd (DSC) ([översikt](https://technet.microsoft.com/library/dn249912.aspx)) är ett PowerShell-verktyg som hjälper dig att deklarera den konfiguration som du vill använda för en dator.  Anta exempelvis att du kan, ”jag vill Chocolatey installerad, jag vill att IIS har installerats, jag vill öppna port 80, jag vill 1.0.0 på webbplatsen installerade versionen”.  DSC lokala Configuration Manager (MGM) implementerar denna konfiguration. En DSC Pull-Server innehåller en databas av konfigurationer för dina datorer. MGM på varje dator kontrollerar regelbundet om dess konfiguration matchar den lagrade konfigurationen. Det kan rapportera status eller försök att göra datorn justering till den lagrade konfigurationen. Du kan redigera den lagrade konfigurationen på pull-servern att starta en dator eller en uppsättning datorer att komma i justering med ändrade konfigurationen.

Azure Automation är en hanterad tjänst i Microsoft Azure som gör det möjligt att automatisera olika uppgifter med hjälp av runbooks, noder, autentiseringsuppgifter, resurser och resurser, t.ex scheman och globala variabler. Azure Automation DSC utökar funktionen automation för att inkludera PowerShell DSC-verktyg.  Här är en bra [översikt](automation-dsc-overview.md).

En DSC-resurs är en modul av kod som har specifika funktioner, t.ex hantering av nätverk, Active Directory eller SQL Server.  Resursen Chocolatey DSC vet hur man åtkomst till en NuGet-Server (bland annat), ladda ned paket, installera paket och så vidare.  Det finns många andra DSC-resurser i den [PowerShell-galleriet](http://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).  Dessa moduler är installerade i Azure Automation DSC Pull-servern (av du) så att de kan användas av dina konfigurationer.

Resource Manager-mallar tillhandahåller en deklarativ metod för att skapa din infrastruktur – till exempel nätverk, undernät, nätverkssäkerhet och routning, läsa in belastningsutjämning, nätverkskort, virtuella datorer och så vidare.  Här är en [artikel](../azure-resource-manager/resource-manager-deployment-model.md) som jämför den Resource Manager-distributionsmodellen (deklarativ) med Azure Service Management (ASM eller klassisk) distribution modellen (tvingande) och beskriver core resursproviders, beräkning, lagring och nätverk.

En nyckelfunktion i Resource Manager-mall är möjligheten att installera en VM-tillägget i den virtuella datorn eftersom den har etablerats.  En VM-tillägget har specifika funktioner, till exempel använda ett anpassat skript, installera antivirusprogram eller ett DSC-konfigurationsskript.  Det finns många andra typer av VM-tillägg.

## <a name="quick-trip-around-the-diagram"></a>Snabb kommunikation runt diagrammet
Början uppifrån, du skriva koden, skapa, testa och sedan skapa ett installationspaket.  Chocolatey kan hantera olika typer av installationspaket, till exempel MSI MSU, ZIP.  Och du har alla fördelar med PowerShell göra verklig installation om Chocolateys inbyggda funktioner som inte är helt upp till den.  Placera paketet i okänd nås – en paket-databas.  Det här exemplet användning använder en offentlig mapp i ett Azure blob storage-konto, men det kan finnas var som helst.  Chocolatey fungerar internt med NuGet-servrar och några andra för hantering av paketmetadata.  [Den här artikeln](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) beskrivs alternativen.  Det här exemplet användning använder NuGet.  En Nuspec är metadata om dina paket.  Nuspec ”kompileras” till Nupkgs och lagras i en NuGet-server.  När konfigurationen av begäranden i ett paket med namnet och refererar till en NuGet-server, hämtar paketet Chocolatey DSC-resursen (nu på den virtuella datorn) och installerar du.  Du kan också begära en viss version av ett paket.

I det nedre vänstra hörnet av bilden finns en mall för Azure Resource Manager (ARM).  I det här exemplet användning registrerar VM-tillägget den virtuella datorn med Azure Automation DSC Pull-servern (det vill säga en pull-server) som en nod.  Konfigurationen har sparats i pull-server.  Faktiskt, lagras två gånger: en gång i klartext och när kompilerats som en MOF-fil (för de vet om sådant.)  I portalen är MOF en ”nodkonfiguration” (i stället för bara ”configuration”).  Det är den artefakt som är associerad med en nod, så noden vet dess konfiguration.  Informationen nedan visar hur du tilldelar nodkonfigurationen till noden.

Du utför förmodligen redan bitars längst upp eller de flesta av den.  Skapa nuspec, kompilering och lagra det i en NuGet-server är en liten sak.  Och du hanterar redan virtuella datorer.  Ta nästa steg att kontinuerlig distribution kräver ställer in pull-server (en gång), registrerar noderna med den (en gång), och skapa och lagra det (först).  Uppdatera sedan som paket uppgraderas och distribueras till databasen, konfiguration och konfiguration av noden i pull-servern (Upprepa efter behov).

Om du inte behöver börja med en ARM-mall, är det också OK.  Det finns PowerShell-cmdlets som utformats för att hjälpa dig att registrera dina virtuella datorer med pull-server och alla övriga. Mer information finns i den här artikeln: [Onboarding datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Steg 1: Konfigurera kontot för pull-servern och automatisering
Vid en autentiserad (Add-AzureRmAccount) PowerShell-kommandorad: (kan ta några minuter innan den pull-servern har konfigurerats)

    New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
    New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT 

Du kan placera ditt automation-konto till någon av följande regioner (aka plats): östra USA 2, södra centrala USA, oss Gov Virginia, Västeuropa, Sydostasien, östra, centrala Indien och Australien-sydost, Kanada Central, Norra Europa.

## <a name="step-2-vm-extension-tweaks-to-the-arm-template"></a>Steg 2: VM-tillägget justeringar för ARM-mallen
Informationen för VM-registrering (med PowerShell DSC VM-tillägget) i det här [Azure Quickstart mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).  Det här steget registrerar den nya virtuella datorn med den pull-servern i listan över DSC-noder.  En del av denna registrering är anger nodkonfiguration ska tillämpas på noden.  Konfiguration av den här noden är inte finns än i pull-servern så att det är OK steg 4 är där detta görs för första gången.  Men här i steg 2 behöver du har valt namnet på noden och namnet på konfigurationen.  Noden är 'isvbox' i detta exempel användning och konfigurationen är 'ISVBoxConfig'.  Nodkonfigurationsnamnet (måste anges i DeploymentTemplate.json) är därför 'ISVBoxConfig.isvbox'.  

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Steg 3: Lägga till nödvändiga DSC-resurser på pull-servern
PowerShell-galleriet instrumenterats om du vill installera DSC-resurser i Azure Automation-konto.  Navigera till den resurs du vill använda och klicka på knappen ”distribuera till Azure Automation”.

![PowerShell-galleriet exempel](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

En annan metod som nyligen har lagts till i Azure-portalen kan du dra in nya moduler eller uppdatera befintliga moduler. Klicka på resursen Automation-konto, panelen tillgångar och slutligen panelen moduler.  Ikonen Bläddra galleriet kan du se en lista med moduler i galleriet, detaljnivån information och slutligen importera till ditt Automation-konto. Detta är ett bra sätt att hålla dina moduler uppdaterade då. Och importfunktionen kontrollerar beroenden med andra moduler så att du vet ingenting synkroniserat.

Eller så har den manuella metoden.  Mappstruktur för en PowerShell-modul för integrering för en Windows-dator skiljer sig något från mappstrukturen som förväntas av Azure Automation.  Detta kräver lite modifiera från din sida.  Men det är inte svårt och det görs bara en gång per resurs (om du vill uppgradera den i framtiden.)  Mer information om redigering av PowerShell integreringsmoduler finns i den här artikeln: [redigering integreringsmoduler för Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

* Installera modulen som du behöver på din arbetsstation enligt följande:
  * Installera [Windows Management Framework, v5](http://aka.ms/wmf5latest) (behövs inte för Windows 10)
  * `Install-Module –Name MODULE-NAME`    < – hämtar modulen från PowerShell-galleriet 
* Kopiera mappen modul från `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` till en tillfällig mapp 
* Ta bort exempel och dokumentation från mappen huvudsakliga 
* ZIP-mappen huvudsakliga naming ZIP-filen exakt samma sätt som mappen 
* Placera ZIP-filen i en HTTP-plats som kan nås, till exempel blob-lagring i Azure Storage-konto.
* Kör följande PowerShell:
  
      New-AzureRmAutomationModule `
          -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
          -Name MODULE-NAME –ContentLink "https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip"

Inkluderade exempel utför de här stegen för cChoco och xNetworking. Finns det [anteckningar](#notes) för särskild hantering för cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Steg 4: Lägga till nodkonfigurationen på pull-servern
Det finns inget särskilt om första gången du importera konfigurationen till den pull-server och kompilera.  Alla efterföljande import/kompilerar med samma konfiguration är exakt lika.  Varje gång du uppdaterar paketet och behöver skicka ut den till produktion göra du det här steget när du har säkerställt konfigurationsfilen är korrekt – inklusive den nya versionen av paketet.  Här är konfigurationsfilen och PowerShell:

ISVBoxConfig.ps1:

    Configuration ISVBoxConfig 
    { 
        Import-DscResource -ModuleName cChoco 
        Import-DscResource -ModuleName xNetworking

        Node "isvbox" {   

            cChocoInstaller installChoco 
            { 
                InstallDir = "C:\choco" 
            }

            WindowsFeature installIIS 
            { 
                Ensure="Present" 
                Name="Web-Server" 
            }

            xFirewall WebFirewallRule 
            { 
                Direction = "Inbound" 
                Name = "Web-Server-TCP-In" 
                DisplayName = "Web Server (TCP-In)" 
                Description = "IIS allow incoming web site traffic." 
                DisplayGroup = "IIS Incoming Traffic" 
                State = "Enabled" 
                Access = "Allow" 
                Protocol = "TCP" 
                LocalPort = "80" 
                Ensure = "Present" 
            }

            cChocoPackageInstaller trivialWeb 
            {            
                Name = "trivialweb" 
                Version = "1.0.0" 
                Source = “MY-NUGET-V2-SERVER-ADDRESS” 
                DependsOn = "[cChocoInstaller]installChoco", 
                "[WindowsFeature]installIIS" 
            } 
        }    
    }

New-ConfigurationScript.ps1:

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

Dessa steg resultera i en ny konfiguration av noden med namnet ”ISVBoxConfig.isvbox” på den pull-servern.  Namnet på konfigurationsnod bygger som ”configurationName.nodeName”.

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Steg 5: Skapa och underhålla paketmetadata
För varje paket du föra in paketdatabasen, behöver du en nuspec som beskriver den.  Den nuspec måste kompileras och lagras i NuGet-server. Den här processen beskrivs [här](http://docs.nuget.org/create/creating-and-publishing-a-package).  Du kan använda MyGet.org som en NuGet-server.  De sälja den här tjänsten, men har en starter SKU som är ledigt.  Vid NuGet.org hittar du anvisningar om hur du installerar en egen NuGet-server för dina privata paket.

## <a name="step-6-tying-it-all-together"></a>Steg 6: Binda alla ihop
Varje gång en version skickar QA och har godkänts för distribution, skapas paketet nuspec och nupkg uppdateras och distribueras till NuGet-servern.  Dessutom måste du uppdatera konfigurationen (steg 4 ovan) för att komma överens med det nya versionsnumret.  Det måste skickas till den pull-servern och kompileras.  Från den punkten på är det upp till de virtuella datorerna som är beroende av att konfigurationen för att hämta uppdateringen och installera den.  Var och en av de här uppdateringarna är enkla - bara en eller två rader i PowerShell.  Några av dem är inkapslade i build-uppgifter som kan sammankopplas i en version för Visual Studio Team Services.  Detta [artikel](https://www.visualstudio.com/en-us/docs/alm-devops-feature-index#continuous-delivery) innehåller mer information.  Detta [GitHub-repo](https://github.com/Microsoft/vso-agent-tasks) beskrivs de olika tillgängliga build-uppgifterna.

## <a name="notes"></a>Anteckningar
Det här exemplet för användning som börjar med en virtuell dator från en allmän Windows Server 2012 R2-avbildning från Azure-galleriet.  Du kan starta från en lagrad bild och sedan justera därifrån med DSC-konfigurationen.  Ändra konfiguration som är inbyggd i en bild är dock mycket svårare än dynamiskt uppdaterar konfigurationen med DSC.

Du behöver inte använda en ARM-mall och tillägg för virtuell dator för att använda den här metoden med dina virtuella datorer.  Och dina virtuella datorer behöver inte vara på Azure ska vara CD: N hanteras.  Allt som behövs är att Chocolatey installeras MGM som konfigurerats på den virtuella datorn så att den vet där pull-server är.  

Naturligtvis när du uppdaterar ett paket på en virtuell dator som är i produktion, måste du ta den virtuella datorn utanför rotation när uppdateringen har installerats.  Hur du gör detta varierar mycket.  Till exempel med en virtuell dator bakom en belastningsutjämnare i Azure, du kan lägga till en anpassad avsökning.  När du uppdaterar den virtuella datorn ha avsökningen slutpunkten returnera en 400.  Justera krävs för att göra den här ändringen kan ingå i din konfiguration kan justera om du vill växla tillbaka till returnera en 200 när uppdateringen är klar.

Fullständig källan för det här exemplet för användning finns i [Visual Studio-projekt](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) på GitHub.

## <a name="related-articles"></a>Relaterade artiklar
* [Azure Automation DSC-översikt](automation-dsc-overview.md)
* [Azure Automation DSC-cmdlets](https://msdn.microsoft.com/library/mt244122.aspx)
* [Onboarding-datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md)

