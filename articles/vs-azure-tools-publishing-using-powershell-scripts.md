---
title: Använda Windows PowerShell-skript för att publicera i utvecklings- och testmiljöer | Microsoft Docs
description: Lär dig hur du använder Windows PowerShell-skript från Visual Studio för att publicera i utvecklings- och testmiljöer.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 5fff1301-5469-4d97-be88-c85c30f837c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: 872ca1dcd48c953180227580d805838c94ea232d
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115552"
---
# <a name="using-windows-powershell-scripts-to-publish-to-dev-and-test-environments"></a>Använda Windows PowerShell-skript för att publicera i utvecklings- och testmiljöer

När du skapar ett webbprogram i Visual Studio kan skapa du ett Windows PowerShell-skript som du senare kan använda för att automatisera publiceringen av din webbplats till Azure som en Webbapp i Azure App Service eller en virtuell dator. Du kan redigera och utöka Windows PowerShell-skriptet i Visual Studio-redigeraren efter dina behov, eller integrera skriptet med befintliga bygg, testa och publicera skript.

Med dessa skript kan etablera du anpassade versioner (även kallat utvecklings- och testmiljöer) för din webbplats för tillfälliga användning. Du kan till exempel konfigurera en viss version av din webbplats på en Azure virtuell dator eller på mellanlagringsplatsen på en webbplats för att köra ett test-paket, återskapa en bugg, och testa en felkorrigering, utvärderingsversion föreslagna ändringen eller konfigurera en anpassad miljö för en demonstration eller presentation. När du har skapat ett skript som publicerar projektet kan du återskapa identiska miljöer genom att köra skriptet efter behov, eller kör skriptet med din egen version av ditt webbprogram för att skapa en anpassad miljö för att testa.

## <a name="prerequisites"></a>Förutsättningar

* Azure SDK 2.3 eller senare. Se [Visual Studio-nedladdningar](http://go.microsoft.com/fwlink/?LinkID=624384). (Du behöver inte Azure SDK för att generera skript för webbprojekt. Den här funktionen används för webbprojekt, inte web-roller i molntjänster.)
* Azure PowerShell 0.7.4 eller senare. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
* [Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175) eller senare.

## <a name="additional-tools"></a>Ytterligare verktyg

Det finns fler verktyg och resurser för att arbeta med PowerShell i Visual Studio för Azure-utveckling. Se [PowerShell Tools för Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012).

## <a name="generating-the-publish-scripts"></a>Genererar skripten publicera

Du kan generera publicera skripten för en virtuell dator som är värd för din webbplats när du skapar ett nytt projekt genom att följa [instruktionerna](virtual-machines/windows/classic/web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Du kan också [generera publicera skript för web apps i Azure App Service](app-service/scripts/app-service-powershell-deploy-github.md).

## <a name="scripts-that-visual-studio-generates"></a>Skript som Visual Studio genererar

Visual Studio genererar en lösning på servernivå mapp med namnet **PublishScripts** som innehåller två Windows PowerShell-filer, ett publicera-skript för den virtuella datorn eller webbplats och en modul som innehåller funktioner som du kan använda i den skript. Visual Studio genererar även en fil i JSON-format som anger information om projektet som du distribuerar.

### <a name="windows-powershell-publish-script"></a>Windows PowerShell publicera skript

Publicera skriptet innehåller specifika publicera stegen för att distribuera till en webbplats eller virtuell dator. Visual Studio har syntaxfärgning för utveckling av Windows PowerShell. Hjälp för funktionerna är tillgänglig och du kan fritt redigera funktionerna i skriptet till dina föränderliga behov.

### <a name="windows-powershell-module"></a>Windows PowerShell-modulen

Windows PowerShell-modulen som Visual Studio genererar innehåller funktioner som publicera skriptet använder. Dessa Azure PowerShell-funktioner är inte avsedda att ändras. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

### <a name="json-configuration-file"></a>JSON-konfigurationsfil
JSON-fil skapas i den **konfigurationer** mapp och innehåller konfigurationsdata som anger exakt vilka resurser som ska distribueras till Azure. Namnet på den fil som Visual Studio genererar är projekt-namn-WAWS-dev.json om du har skapat en webbplats eller projekt namnet-VM-dev.json om du har skapat en virtuell dator. Här är ett exempel på en JSON-konfigurationsfil som genereras när du skapar en webbplats. De flesta av värdena är självförklarande. Namnet på webbplatsen skapas av Azure, så det inte kanske matchar ditt projektnamn.

```json
{
    "environmentSettings": {
        "webSite": {
            "name": "WebApplication26632",
            "location": "West US"
        },
        "databases": [{
            "connectionStringName": "DefaultConnection",
            "databaseName": "WebApplication26632_db",
            "serverName": "YourDatabaseServerName",
            "user": "sqluser2",
            "password": "",
            "edition": "",
            "size": "",
            "collation": "",
            "location": "West US"
        }]
    }
}
```

När du skapar en virtuell dator, liknar JSON-konfigurationsfilen följande. En molnbaserad tjänst skapas som en behållare för den virtuella datorn. Den virtuella datorn innehåller vanliga slutpunkter för webbåtkomst via HTTP och HTTPS, samt slutpunkter för webbdistribution som låter dig publicera till webbplatsen från din lokala datorer, fjärrskrivbord och Windows PowerShell.

```json
{
    "environmentSettings": {
        "cloudService": {
            "name": "myusernamevm1",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myusernamevm1",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201403.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [{
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [{
            "connectionStringName": "",
            "databaseName": "",
            "serverName": "",
            "user": "",
            "password": ""
        }],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Du kan redigera JSON-konfigurationen om du vill ändra vad som händer när du kör skripten publicera. Den `cloudService` och `virtualMachine` avsnitt krävs, men du kan ta bort den `databases` avsnittet om du inte behöver den. De egenskaper som är tomma i standardkonfigurationsfilen som Visual Studio genererar är valfritt. Dessa egenskaper som har värden i standardkonfigurationsfilen krävs.

Om du har en webbplats som har flera distributionsmiljöer (kallas fack) i stället för en enda produktionsplatsen i Azure kan du inkludera fack-name Namnet på webbplatsen i JSON-konfigurationsfil. Exempel: Om du har en webbplats som heter **webbplats** och en plats för den som heter **testa** URI: N är `mysite-test.cloudapp.net`, men namnet på rätt ska användas i konfigurationsfilen är mysite(test). Du kan bara utföra det här om webbplatsen och fack finns redan i din prenumeration. Om de inte finns skapar du en webbplats genom att köra skriptet utan att ange facket kan sedan skapa platsen i den [Azure-portalen](https://portal.azure.com/), och därefter köra skriptet med det ändrade webbplatsnamnet. Mer information om distributionsplatser för web apps finns i [konfigurera mellanlagringsmiljöer för webbappar i Azure App Service](app-service/web-sites-staged-publishing.md).

## <a name="how-to-run-the-publish-scripts"></a>Hur du kör skripten publicera

Om du aldrig har kört ett Windows PowerShell-skript innan, måste du först ställa in körningsprincipen att aktivera skript körs. Principen är en säkerhetsfunktion för att förhindra att användare kör Windows PowerShell-skript om de är sårbara för skadlig kod eller virus som rör körning av skript.

### <a name="run-the-script"></a>Kör skript

1. Skapa Web Deploy-paket för ditt projekt. Ett webbdistributionspaket är en komprimerad fil (ZIP-fil) som innehåller filer som du vill kopiera till din webbplats eller virtuell dator. Du kan skapa Web Deploy-paket i Visual Studio för webbprogram i alla.

![Skapa webb-distribuera paket](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

Mer information finns i [så här: skapa ett Webbdistributionspaket i Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx). Du kan även automatisera skapandet av din webbdistributionspaket, enligt beskrivningen i [anpassa och utöka publicera scripts[(#customizing-and-extending-publish-scripts)]

1. I **Solution Explorer**, öppna snabbmenyn för skriptet och välj sedan **öppna med PowerShell ISE**.
2. Om du kör Windows PowerShell-skript på den här datorn för första gången, öppna Kommandotolken med administratörsbehörighet och skriver du följande kommando:

    ```powershell
    Set-ExecutionPolicy RemoteSigned
    ```

3. Logga in på Azure med hjälp av följande kommando.

    ```powershell
    Add-AzureAccount
    ```

    När du uppmanas, anger du ditt användarnamn och lösenord.

    Observera att den här metoden för att tillhandahålla autentiseringsuppgifter för Azure inte fungerar när du automatiserar skriptet. I stället bör du använda den `.publishsettings` filen för att ange autentiseringsuppgifter. En gång kan du använda kommandot **Get-AzurePublishSettingsFile** att hämta filen från Azure och därefter använda **Import AzurePublishSettingsFile** att importera filen. Mer detaljerade anvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

4. (Valfritt) Om du vill skapa Azure-resurser som den virtuella datorn, databas och en webbplats utan att publicera ditt webbprogram, använda den **publicera WebApplication.ps1** med den **-konfigurationen**argumentet inställt på JSON-konfigurationsfil. Den här kommandoraden använder JSON-konfigurationsfil för att avgöra vilka resurser för att skapa. Eftersom den använder standardinställningarna för andra argument på kommandoraden, skapar resurserna, men publicera inte ditt webbprogram. – Utförliga alternativet får du mer information om vad som händer.

    ```powershell
    Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json
    ```

5. Använd den **publicera WebApplication.ps1** kommandot som visas i något av följande exempel för att anropa skriptet och publicera ditt webbprogram. Om du vill åsidosätta standardinställningarna för någon av de övriga argument, till exempel prenumerationens namn, publicera paketnamn, autentiseringsuppgifter för virtuell dator eller server Databasautentiseringsuppgifter kan ange du dessa parametrar. Använd den **– utförlig** alternativet om du vill se mer information om förloppet för publiceringsprocessen.

    ```powershell
    Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
    –SubscriptionName Contoso `
    -WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
    -DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
    -Verbose
    ```

    Om du skapar en virtuell dator, kommandot ser ut som följande. Det här exemplet visar också hur du anger autentiseringsuppgifterna för flera databaser. För de virtuella datorerna som dessa skript skapar, är SSL-certifikatet inte från en betrodd rotcertifikatutfärdare. Därför måste du använda den **– AllowUntrusted** alternativet.

    ```powershell
    Publish-WebApplication.ps1 `
    -Configuration C:\Path\ADVM-VM-test.json `
    -SubscriptionName Contoso `
    -WebDeployPackage C:\Path\ADVM.zip `
    -AllowUntrusted `
    -VMPassword @{name = "vmUserName"; password = "YourPasswordHere"} `
    -DatabaseServerPassword @{Name="server1";Password="adminPassword1"}, @{Name="server2";Password="adminPassword2"} `
    -Verbose
    ```

    Skriptet kan skapa databaser, men det skapar inte databasservrar. Om du vill skapa en databasserver kan du använda den **New AzureSqlDatabaseServer** funktionen i Azure-modulen.

## <a name="customizing-and-extending-the-publish-scripts"></a>Anpassa och utöka publicera-skript
Du kan anpassa publicera skript och JSON-konfigurationsfil. Funktioner i Windows PowerShell-modulen **AzureWebAppPublishModule.psm1** är inte avsedda att ändras. Om du bara vill ange en annan databas eller ändra några av egenskaperna för den virtuella datorn kan du redigera JSON-konfigurationsfil. Om du vill utöka funktionerna i skript för att automatisera att skapa och testa ditt projekt kan du implementera funktionen platshållare i **publicera WebApplication.ps1**.

Om du vill automatisera och bygga projektet, lägger du till kod som anropar MSBuild till `New-WebDeployPackage` som visas i det här kodexemplet. Sökvägen till MSBuild-kommandot är olika beroende på vilken version av Visual Studio som du har installerat. Du kan använda funktionen för att få rätt sökväg, **Get-MSBuildCmd**, vilket visas i det här exemplet.

### <a name="to-automate-building-your-project"></a>Att automatisera och bygga projektet
1. Lägg till den `$ProjectFile` parametern i det globala param-avsnittet.

    ```powershell
    [Parameter(Mandatory = $false)]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [String]
    $ProjectFile,
    ```

2. Kopiera funktionen `Get-MSBuildCmd` i skriptet.

    ```powershell
    function Get-MSBuildCmd
    {
            process
    {

                $path =  Get-ChildItem "HKLM:\SOFTWARE\Microsoft\MSBuild\ToolsVersions\" |
                                    Sort-Object {[double]$_.PSChildName} -Descending |
                                    Select-Object -First 1 |
                                    Get-ItemProperty -Name MSBuildToolsPath |
                                    Select -ExpandProperty MSBuildToolsPath

                $path = (Join-Path -Path $path -ChildPath 'msbuild.exe')

            return Get-Item $path
        }
    }
    ```

3. Ersätt `New-WebDeployPackage` med följande kod och Ersätt platshållarna rad konstruera `$msbuildCmd`. Den här koden är för Visual Studio 2017. Om du använder Visual Studio 2015, ändra den **VisualStudioVersion** egenskap `14.0` (`12.0` för Visual Studio 2013).

    ```powershell
    function New-WebDeployPackage
    {
        #Write a function to build and package your web application
    ```

    Använd MsBuild.exe för att skapa ditt webbprogram. Hjälp finns i MSBuild Kommandoradsreferens på: [http://go.microsoft.com/fwlink/?LinkId=391339](http://go.microsoft.com/fwlink/?LinkId=391339)

    ```powershell
    Write-VerboseWithTime 'Build-WebDeployPackage: Start'

    $msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=15.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory

    Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
    ```

### <a name="start-execution-of-the-build-command"></a>Starta körning av build-kommandot

```powershell
$job = Start-Process cmd.exe -ArgumentList('/C "' + $msbuildCmd + '"') -WindowStyle Normal -Wait -PassThru

if ($job.ExitCode -ne 0) {
    throw('MsBuild exited with an error. ExitCode:' + $job.ExitCode)
}

#Obtain the project name
$projectName = (Get-Item $ProjectFile).BaseName

#Construct the path to web deploy zip package
$DeployPackageDir =  '.\MSBuildOutputPath\_PublishedWebsites\{0}_Package\{0}.zip' -f $projectName

#Get the full path for the web deploy zip package. This is required for MSDeploy to work
$WebDeployPackage = Resolve-Path –LiteralPath $DeployPackageDir

Write-VerboseWithTime 'Build-WebDeployPackage: End'

return $WebDeployPackage
}
```

1. Anropa den `New-WebDeployPackage` funktionen innan den här raden: `$Config = Read-ConfigFile $Configuration` för web apps eller `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` för virtuella datorer.

    ```powershell
    if($ProjectFile)
    {
    $WebDeployPackage = New-WebDeployPackage
    }
    ```

2. Anropa anpassade skriptet från kommandoraden med hjälp av skicka den `$Project` argument, som i följande exempel:

    ```powershell
    .\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
    -ProjectFile ..\WebApplication5\WebApplication5.csproj `
    -VMPassword @{Name="VMUser";Password="Test.123"} `
    -AllowUntrusted `
    -Verbose
    ```

    Lägg till kod för att automatisera testning av ditt program, `Test-WebApplication`. Se till att ta bort kommentarerna raderna i **publicera WebApplication.ps1** där dessa funktioner anropas. Om du inte anger en implementering kan du manuellt skapa projektet med Visual Studio och kör sedan skriptet publicera för att publicera på Azure.

## <a name="publishing-function-summary"></a>Sammanfattning av Publishing funktioner
Om du vill få hjälp med funktioner som du kan använda Kommandotolken för Windows PowerShell, använder du kommandot `Get-Help function-name`. Hjälpen innehåller parametern hjälp och exempel. Samma hjälptexten finns också i skriptet källfilerna **AzureWebAppPublishModule.psm1** och **publicera WebApplication.ps1**. Skript och hjälp är lokaliserade på ditt språk för Visual Studio.

**AzureWebAppPublishModule**

| Funktionsnamn | Beskrivning |
| --- | --- |
| Add-AzureSQLDatabase |Skapar en ny Azure SQL-databas. |
| Add-AzureSQLDatabases |Skapar Azure SQL-databaser från värdena i JSON-konfigurationsfil som Visual Studio genererar. |
| Add-AzureVM |Skapar en Azure virtuell dator och returnerar URL: en för den distribuerade virtuella datorn. Funktionen ställer in kraven och anropar sedan den **New-AzureVM** funktionen (Azure-modulen) för att skapa en ny virtuell dator. |
| Add-AzureVMEndpoints |Lägger till nya inkommande slutpunkter till en virtuell dator och returnerar den virtuella datorn med den nya slutpunkten. |
| Add-AzureVMStorage |Skapar ett nytt Azure storage-konto i den aktuella prenumerationen. Namnet på kontot börjar med ”devtest” följt av en unik alfanumerisk sträng. Funktionen returnerar namnet på det nya lagringskontot. Ange en plats eller en tillhörighetsgrupp för det nya lagringskontot. |
| Add-AzureWebsite |Skapar en webbplats med angivet namn och plats. Den här funktionen anropar den **New-AzureWebsite** funktionen i Azure-modulen. Om prenumerationen inte redan innehåller en webbplats med det angivna namnet kan den här funktionen skapar webbplatsen och returnerar ett objekt för webbplatsen. Annars returneras `$null`. |
| Backup-prenumeration |Sparar den aktuella Azure-prenumerationen i den `$Script:originalSubscription` variabel i skriptet omfång. Den här funktionen sparar den aktuella Azure-prenumerationen (som erhålls av `Get-AzureSubscription -Current`) och dess storage-konto och prenumeration som ändras av det här skriptet (lagras i variabeln `$UserSpecifiedSubscription`) och dess storage-konto i skriptet omfång. Genom att spara värdena, kan du använda en funktion som `Restore-Subscription`, för att återställa det ursprungliga aktuella prenumeration och storage-kontot till aktuell status om aktuell status har ändrats. |
| Find-AzureVM |Hämtar den angivna Azure-datorn. |
| Formatet DevTestMessageWithTime |Annat datum och tid till ett meddelande. Den här funktionen är avsedd för meddelanden som skrivits till fel och utförlig dataströmmar. |
| Get-AzureSQLDatabaseConnectionString |Monterar en anslutningssträng för att ansluta till en Azure SQL database. |
| Get-AzureVMStorage |Returnerar namnet på det första lagringskontot med namnet har formatet ”devtest *” (skiftlägesokänsligt) i den angivna platsen eller tillhörighetsgruppen. Om den ”devtest*” lagringskontot matchar inte platsen eller tillhörighetsgruppen, funktionen ignorerar den. Ange en plats eller en tillhörighetsgrupp. |
| Get-MSDeployCmd |Returnerar ett kommando för att köra verktyget MsDeploy.exe. |
| New-AzureVMEnvironment |Söker efter eller skapar en virtuell dator i den prenumeration som matchar värden i JSON-konfigurationsfil. |
| Publish-WebPackage |Publicera paketet använder MsDeploy.exe och en webbplats. ZIP-filen för att distribuera resurser till en webbplats. Den här funktionen genererar inte några utdata. Om anropet till MSDeploy.exe misslyckas, genereras ett undantag. För att få mer detaljerad kan använda den **-utförlig** alternativet. |
| Publish-WebPackageToVM |Verifierar parametervärdena och anropar sedan den **Publish-WebPackage** funktion. |
| Read-ConfigFile |Verifierar JSON-konfigurationsfil och returnerar en hashtabell med valda värden. |
| Restore-Subscription |Återställer den aktuella prenumerationen till den ursprungliga prenumerationen. |
| Test-AzureModule |Returnerar `$true` om den installera Azure modulversionen är 0.7.4 eller senare. Returnerar `$false` om modulen har inte installerats eller är en tidigare version. Den här funktionen har inga parametrar. |
| Test-AzureModuleVersion |Returnerar `$true` om versionen av Azure-modulen är 0.7.4 eller senare. Returnerar `$false` om modulen har inte installerats eller är en tidigare version. Den här funktionen har inga parametrar. |
| Test-HttpsUrl |Konverterar den angivna URL-Adressen till ett System.Uri-objekt. Returnerar `$True` om URL: en är absolut och dess schema är https. Returnerar `$false` om URL: en är relativ, dess schema inte är HTTPS eller Indatasträngen kan inte konverteras till en URL. |
| Test-Member |Returnerar `$true` om en egenskap eller metod är medlem i objektet. I annat fall returnerar `$false`. |
| Skriv ErrorWithTime |Skriver ett felmeddelande som föregås av den aktuella tiden. Den här funktionen anropar den **Format DevTestMessageWithTime** funktion till åtkomstgruppen för tid innan du skriver meddelandet till en dataström med fel. |
| Skriv HostWithTime |Skriver ett meddelande till värdprogrammet (**Write-Host**) föregås av den aktuella tiden. Effekten av att skriva till värdprogrammet varierar. De flesta program som är värdar för Windows PowerShell Skriv meddelandena till standardutdata. |
| Skriv VerboseWithTime |Skriver ett utförligt meddelande föregås av den aktuella tiden. Eftersom den anropar **Write-Verbose**, visas bara när skriptet körs med den **utförlig** parametern eller när den **VerbosePreference** inställningar har angetts till  **Fortsätta**. |

**Publicera WebApplication**

| Funktionsnamn | Beskrivning |
| --- | --- |
| New-AzureWebApplicationEnvironment |Skapar Azure-resurser, till exempel en webbplats eller virtuell dator. |
| Ny WebDeployPackage |Den här funktionen är inte implementerad. Du kan lägga till kommandon i den här funktionen att skapa projektet. |
| Publish-AzureWebApplication |Publicerar ett webbprogram till Azure. |
| Publicera WebApplication |Skapar och distribuerar Web Apps, virtuella datorer, SQL-databaser och lagringskonton för ett webbprojekt i Visual Studio. |
| Test-WebApplication |Den här funktionen är inte implementerad. Du kan lägga till kommandon i den här funktionen för att testa ditt program. |

## <a name="next-steps"></a>Nästa steg
Mer information om PowerShell-skript genom att läsa [med Windows PowerShell-skript](https://technet.microsoft.com/library/bb978526.aspx) och finns i andra Azure PowerShell-skript på den [Script Center](https://azure.microsoft.com/documentation/scripts/).
