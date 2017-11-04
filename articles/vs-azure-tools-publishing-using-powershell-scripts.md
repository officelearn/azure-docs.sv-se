---
title: "Med Windows PowerShell-skript för att publicera till utvecklings- och testmiljöer | Microsoft Docs"
description: "Lär dig hur du använder Windows PowerShell-skript från Visual Studio för att publicera till utveckling och testa miljöer."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 5fff1301-5469-4d97-be88-c85c30f837c1
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: 92753860ec820172e46f483831eb0c1cf1acb038
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="using-windows-powershell-scripts-to-publish-to-dev-and-test-environments"></a>Använda Windows PowerShell-skript för att publicera i utvecklings- och testmiljöer
När du skapar ett webbprogram i Visual Studio kan skapa du ett Windows PowerShell-skript som du kan använda för att automatisera publicering av din webbplats till Azure som en Webbapp i Azure App Service eller en virtuell dator. Du kan redigera och utöka Windows PowerShell-skript i Visual Studio-redigeraren som passar dina krav eller integrera skriptet med befintliga bygga, testa och publicera skript.

Med dessa skript kan etablera du anpassade versioner (även kallat utvecklings- och testmiljöer) för din plats för temporär användning. Du kan till exempel konfigurera en viss version av din webbplats på en virtuell Azure-dator eller på mellanlagringsplatsen på en webbplats som kör ett test-paket, återskapa ett programfel, och testa en felkorrigering, utvärderingsversion föreslagna ändringen eller konfigurera en anpassad miljö för demonstration eller presentation. När du har skapat ett skript som publicerar ditt projekt kan du återskapa identiska miljöer genom att köra skriptet igen vid behov eller köra skriptet med din egen version av ditt webbprogram för att skapa en anpassad miljö för att testa.

## <a name="what-you-need"></a>Vad du behöver
* Azure SDK 2.3 eller senare. Se [Visual Studio-hämtningar](http://go.microsoft.com/fwlink/?LinkID=624384) för mer information.

Du behöver inte Azure SDK för att generera skript för webbprojekt. Den här funktionen är för webbprojekt, inte webbroller i molntjänster.

* Azure PowerShell 0.7.4 eller senare. Se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) för mer information.
* [Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175) eller senare.

## <a name="additional-tools"></a>Ytterligare verktyg
Det finns ytterligare verktyg och resurser för att arbeta med PowerShell i Visual Studio för Azure-utveckling. Se [PowerShell-verktyg för Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012).

## <a name="generating-the-publish-scripts"></a>Generera publicera skript
Du kan generera publicera skript för en virtuell dator som är värd för din webbplats när du skapar ett nytt projekt genom att följa [instruktionerna](virtual-machines/windows/classic/web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Du kan också [generera publicera skript för web apps i Azure App Service](app-service/app-service-web-get-started-dotnet.md).

## <a name="scripts-that-visual-studio-generates"></a>Skript som skapas av Visual Studio
Visual Studio skapar en lösning på objektnivå mapp med namnet **PublishScripts** som innehåller två Windows PowerShell-filer, ett publicera-skript för den virtuella datorn eller webbplats och en modul som innehåller funktioner som du kan använda i den skript. Visual Studio skapar också en fil i JSON-format som anger information om projektet som du distribuerar.

### <a name="windows-powershell-publish-script"></a>Windows PowerShell publicera skript
Publicera skriptet innehåller specifika publicera steg för att distribuera till en webbplats eller virtuell dator. Visual Studio tillhandahåller syntax färgläggning för Windows PowerShell-utveckling. Hjälp för funktionerna som är tillgänglig och du fritt kan redigera funktionerna i skriptet till dina föränderliga behov.

### <a name="windows-powershell-module"></a>Windows PowerShell-modulen
Windows PowerShell-modulen som Visual Studio genererar innehåller funktioner som använder skriptet publicera. Dessa är Azure PowerShell-funktioner och är inte avsedda att ändras. Se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) för mer information.

### <a name="json-configuration-file"></a>JSON-konfigurationsfil
JSON-filen skapas i den **konfigurationer** mapp och innehåller konfigurationsdata som anger exakt vilka resurser du vill distribuera till Azure. Namnet på den fil som Visual Studio genererar är projekt-namn-WAWS-dev.json om du har skapat en webbplats eller projekt namnet-VM-dev.json om du har skapat en virtuell dator. Här är ett exempel på en JSON-konfigurationsfil som genereras när du skapar en webbplats. De flesta av värdena är självförklarande. Namnet på webbplatsen genereras av Azure, så den inte kan matcha ditt projektnamn.

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
När du skapar en virtuell dator liknar JSON-konfigurationsfil följande. Observera att en tjänst i molnet har skapats som en behållare för den virtuella datorn. Den virtuella datorn innehåller normalt slutpunkter för webbåtkomst via HTTP och HTTPS, samt slutpunkter för webbdistribution där du kan publicera på webbplatsen från din lokala dator, fjärrskrivbord och Windows PowerShell.

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

Du kan redigera JSON-konfiguration för att ändra vad som händer när du kör skripten publicera. Den `cloudService` och `virtualMachine` avsnitt krävs, men du kan ta bort den `databases` avsnittet om du inte behöver den. De egenskaper som är tomma i standardkonfigurationsfilen som genereras av Visual Studio är valfritt. de som har värden i standardkonfigurationsfilen krävs.

Om du har en webbplats som har flera distributionsmiljöer (kallas även fack) i stället för en enda produktionsplatsen i Azure kan du inkludera platsnamnet i namnet på webbplatsen i JSON-konfigurationsfil. Om du har en webbplats som heter exempelvis **webbplats** och en plats för den namngivna **testa** URI är test.cloudapp.net webbplats, men det korrekt namnet i konfigurationsfilen är mysite(test). Du kan bara göra detta om webbplatsen och fack finns redan i din prenumeration. Om de inte finns skapar du webbplatsen genom att köra skriptet utan att ange facket kan sedan skapa platsen i den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885), och därefter köra skriptet med det ändrade webbplatsnamnet. Mer information om distributionsplatser för webbprogram finns [skapa mellanlagringsmiljöer för web apps i Azure App Service](app-service/web-sites-staged-publishing.md).

## <a name="how-to-run-the-publish-scripts"></a>Hur publicera skript ska köras
Om du aldrig har kört Windows PowerShell-skript före, måste du först ställa in körningsprincipen att skript körs. Detta är en säkerhetsfunktion för att förhindra att användare kör Windows PowerShell-skript om de är sårbara för skadlig kod eller virus som rör skript körs.

### <a name="run-the-script"></a>Kör skriptet
1. Skapa Web Deploy-paket för projektet. Ett Web Deploy-paket är en komprimerad fil (ZIP-fil) som innehåller filer som du vill kopiera till din webbplats eller virtuell dator. Du kan skapa Web Deploy-paket i Visual Studio för alla webbprogram.

![Skapa webb distribuera paket](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

Mer information finns i [så här: skapa ett Webbdistributionspaket i Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx). Du kan också automatisera skapandet av Web Deploy-paket, enligt beskrivningen i avsnittet **anpassa och utöka publicera skript** senare i det här avsnittet.

1. I **Solution Explorer**, öppna snabbmenyn för skriptet och välj sedan **öppna med PowerShell ISE**.
2. Om det här är första gången du kör Windows PowerShell-skript på den här datorn, öppna Kommandotolken med administratörsbehörighet och Skriv följande kommando:

    ```powershell
    Set-ExecutionPolicy RemoteSigned
    ```

3. Logga in på Azure med hjälp av följande kommando.

    ```powershell
    Add-AzureAccount
    ```

    När du uppmanas, ange ditt användarnamn och lösenord.

    Observera att den här metoden för att tillhandahålla autentiseringsuppgifter för Azure inte fungerar när du automatiserar skriptet. I stället bör du använda .publishsettings-fil för att ange autentiseringsuppgifter. En gång kan du använda kommandot **Get-AzurePublishSettingsFile** att hämta filen från Azure och därefter använda **importera AzurePublishSettingsFile** att importera filen. Detaljerade instruktioner finns [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

4. (Valfritt) Om du vill skapa Azure-resurser som den virtuella datorn, databas och webbplats utan att publicera ditt webbprogram, använder den **publicera WebApplication.ps1** kommandot med de **-konfiguration**argumentet inställt på JSON-konfigurationsfil. Den här kommandoraden använder JSON-konfigurationsfil för att avgöra vilka resurser du vill skapa. Eftersom den använder standardinställningarna för andra kommandoradsargument skapar resurser, men publicera inte ditt webbprogram. – Utförlig alternativet får du mer information om vad som händer.

    ```powershell
    Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json
    ```

5. Använd den **publicera WebApplication.ps1** som visas i följande exempel anropar skriptet och publicera ditt webbprogram. Om du behöver åsidosätter standardinställningarna för alla andra argument, till exempel prenumerationsnamn, publicera paketnamn, autentiseringsuppgifter för virtuell dator eller server Databasautentiseringsuppgifter kan du ange dessa parametrar. Använd den **– utförlig** alternativet om du vill se mer information om förloppet publiceringsprocessen.

    ```powershell
    Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
    –SubscriptionName Contoso `
    -WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
    -DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
    -Verbose
    ```

    Om du skapar en virtuell dator, ser kommandot ut på följande. Det här exemplet visar även hur ange autentiseringsuppgifter för flera databaser. För virtuella datorer som dessa skript skapar är SSL-certifikatet inte från en betrodd rotcertifikatutfärdare. Därför måste du använda den **– AllowUntrusted** alternativet.

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

    Skriptet kan skapa databaser, men det skapar inte databasservrar. Om du vill skapa en databasserver kan du använda den **ny AzureSqlDatabaseServer** funktion i Azure-modulen.

## <a name="customizing-and-extending-the-publish-scripts"></a>Anpassa och utöka publicera skript
Du kan anpassa publicera skript och JSON-konfigurationsfil. Funktioner i Windows PowerShell-modulen **AzureWebAppPublishModule.psm1** är inte avsedda att ändras. Redigera JSON-konfigurationsfil om du bara vill ange en annan databas eller ändra några egenskaper för den virtuella datorn. Om du vill utöka funktionerna i skript för att automatisera bygger och testar ditt projekt kan du implementera funktionen stub-rutiner i **publicera WebApplication.ps1**.

För att automatisera skapande av projektet, lägger du till kod som anropar MSBuild till `New-WebDeployPackage` som visas i det här kodexemplet. Sökvägen till MSBuild-kommandot är olika beroende på vilken version av Visual Studio som du har installerat. Du kan använda funktionen för att få rätt sökväg, **Get-MSBuildCmd**som visas i det här exemplet.

### <a name="to-automate-building-your-project"></a>Automatisera skapa projektet
1. Lägg till den `$ProjectFile` parametern i avsnittet globala param.

    ```powershell
    [Parameter(Mandatory = $false)]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [String]
    $ProjectFile,
    ```

2. Kopiera funktionen `Get-MSBuildCmd` till skriptfilen.

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

3. Ersätt `New-WebDeployPackage` med följande kod och Ersätt platshållarna rad konstruerar `$msbuildCmd`. Den här koden är för Visual Studio 2015. Om du använder Visual Studio 2013, ändrar du den **VisualStudioVersion** egenskapen nedan till `12.0`.

    ```powershell
    function New-WebDeployPackage
    {
        #Write a function to build and package your web application
    ```

    Använd MsBuild.exe för att skapa webbprogrammet. Mer information finns i MSBuild Kommandoradsreferens på: [http://go.microsoft.com/fwlink/?LinkId=391339](http://go.microsoft.com/fwlink/?LinkId=391339)

    ```powershell
    Write-VerboseWithTime 'Build-WebDeployPackage: Start'

    $msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=14.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory

    Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
    ```

### <a name="start-execution-of-the-build-command"></a>Starta körning av kommandot build

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

1. Anropa den `New-WebDeployPackage` funktionen innan den här raden: `$Config = Read-ConfigFile $Configuration` för webbappar eller `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` för virtuella datorer.

    ```powershell
    if($ProjectFile)
    {
    $WebDeployPackage = New-WebDeployPackage
    }
    ```

2. Anropa anpassade skriptet från kommandoraden med hjälp av överföring av `$Project` argument, som i följande exempel-kommandoraden.

    ```powershell
    .\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
    -ProjectFile ..\WebApplication5\WebApplication5.csproj `
    -VMPassword @{Name="VMUser";Password="Test.123"} `
    -AllowUntrusted `
    -Verbose
    ```

    Lägg till kod för att automatisera testning av ditt program, `Test-WebApplication`. Se till att ta bort kommentarerna raderna i **publicera WebApplication.ps1** där dessa funktioner kallas. Om du inte anger en implementering, kan du manuellt skapa ditt projekt i Visual Studio och kör sedan publicera-skript för att publicera till Azure.

## <a name="publishing-function-summary"></a>Sammanfattning av Publishing funktioner
Om du vill få hjälp med funktioner som du kan använda Kommandotolken för Windows PowerShell, använder du kommandot `Get-Help function-name`. Hjälpen innehåller parametern hjälp och exempel. Samma hjälptexten finns också i källfiler skriptet **AzureWebAppPublishModule.psm1** och **publicera WebApplication.ps1**. Skript och hjälp lokaliserade i Visual Studio-språk.

**AzureWebAppPublishModule**

| Funktionsnamn | Beskrivning |
| --- | --- |
| Lägg till AzureSQLDatabase |Skapar en ny Azure SQL-databas. |
| Lägg till AzureSQLDatabases |Skapar Azure SQL-databaser från värden i JSON-konfigurationsfil som genereras av Visual Studio. |
| Lägg till AzureVM |Skapar en virtuell dator i Azure och returnerar URL för den distribuerade virtuella datorn. Funktionen ställer in kraven och anropar sedan den **ny AzureVM** funktionen (Azure-modulen) för att skapa en ny virtuell dator. |
| Lägg till AzureVMEndpoints |Lägger till nya inkommande slutpunkter till en virtuell dator och återställer den virtuella datorn med ny slutpunkt. |
| Lägg till AzureVMStorage |Skapar ett nytt Azure storage-konto i den aktuella prenumerationen. Namnet på kontot som börjar med ”devtest” följt av en unik alfanumerisk sträng. Funktionen returnerar namnet på det nya lagringskontot. Du måste ange en plats eller en tillhörighetsgrupp för det nya lagringskontot. |
| Lägg till AzureWebsite |Skapar en webbplats med angivet namn och plats. Anropar den här funktionen i **ny AzureWebsite** funktion i Azure-modulen. Om prenumerationen inte redan innehåller en webbplats med det angivna namnet kan den här funktionen skapar webbplatsen och returnerar ett objekt för webbplatsen. Annars returneras `$null`. |
| Backup-prenumeration |Sparar den aktuella Azure-prenumerationen i den `$Script:originalSubscription` variabeln i skriptet omfång. Den här funktionen sparar den aktuella Azure-prenumerationen (som erhålls av `Get-AzureSubscription -Current`) och dess storage-konto och prenumeration som har ändrats med det här skriptet (lagras i variabeln `$UserSpecifiedSubscription`) och dess storage-kontot i skriptet omfång. Genom att spara värdena kan du använda en funktion som `Restore-Subscription`, för att återställa det ursprungliga aktuella prenumeration och storage-kontot till aktuell status om aktuell status har ändrats. |
| Hitta AzureVM |Hämtar den angivna virtuella Azure-datorn. |
| Formatet DevTestMessageWithTime |Annat datum och tid till ett meddelande. Den här funktionen är avsedd för meddelanden som skrivs till fel och utförlig dataströmmar. |
| Get-AzureSQLDatabaseConnectionString |Monterar en anslutningssträng för att ansluta till en Azure SQL database. |
| Get-AzureVMStorage |Returnerar namnet på det första storage-kontot med namnmönster ”devtest*” (skiftlägesokänsligt) i den angivna platsen eller tillhörighetsgruppen. Om den ”devtest*” lagringskontot matchar inte platsen eller tillhörighetsgruppen, funktionen ignoreras. Du måste ange en plats eller en tillhörighetsgrupp. |
| Get-MSDeployCmd |Returnerar ett kommando för att köra verktyget MsDeploy.exe. |
| Ny AzureVMEnvironment |Söker efter eller skapar en virtuell dator i den prenumeration som matchar värden i JSON-konfigurationsfil. |
| Publicera WebPackage |Publicera paketet använder MsDeploy.exe och en webbplats. ZIP-filen för att distribuera resurser till en webbplats. Den här funktionen genererar inte inga utdata. Om anropet till MSDeploy.exe misslyckas, genereras ett undantag. Om du vill ha mer detaljerad information, Använd den **-Verbose** alternativet. |
| Publicera WebPackageToVM |Verifierar parametervärden och anropar sedan den **publicera WebPackage** funktion. |
| Läs ConfigFile |Validerar JSON-konfigurationsfil och returnerar en hash-tabell för valda värden. |
| Restore-prenumeration |Återställer den aktuella prenumerationen till den ursprungliga prenumerationen. |
| Testa AzureModule |Returnerar `$true` versionen om den installera Azure-modulen är 0.7.4 eller senare. Returnerar `$false` om modulen är inte installerat eller är en tidigare version. Den här funktionen har inga parametrar. |
| Testa AzureModuleVersion |Returnerar `$true` om versionen av Azure-modulen är 0.7.4 eller senare. Returnerar `$false` om modulen är inte installerat eller är en tidigare version. Den här funktionen har inga parametrar. |
| Testa HttpsUrl |Konverterar den angivna URL-Adressen till ett System.Uri-objekt. Returnerar `$True` om URL: en är absolut och dess schemat är https. Returnerar `$false` om URL: en är relativ, dess schema inte är HTTPS eller den inmatade strängen kan inte konverteras till en URL. |
| Test-medlem |Returnerar `$true` om en egenskap eller metod är medlem i objektet. Annars returnerar `$false`. |
| Skriv ErrorWithTime |Skriver ett felmeddelande som föregås av den aktuella tiden. Anropar den här funktionen i **Format DevTestMessageWithTime** funktionen lägga tiden innan skrivning meddelandet till fel dataströmmen till. |
| Skriv HostWithTime |Skriver ett meddelande till värdprogrammet (**Write-Host**) med den aktuella tiden prefixet. Effekten av att skriva till värdprogrammet varierar. De flesta program som är värdar för Windows PowerShell skriva meddelanden till standardutdata. |
| Skriv VerboseWithTime |Skriver ett utförligt meddelande föregås av den aktuella tiden. Eftersom det anropar **Write-Verbose**, ett meddelande visas endast när skriptet körs med den **utförlig** parametern eller när den **VerbosePreference** inställningsgrupp  **Fortsätta**. |

**Publicera WebApplication**

| Funktionsnamn | Beskrivning |
| --- | --- |
| Ny AzureWebApplicationEnvironment |Skapar Azure-resurser, till exempel en webbplats eller virtuell dator. |
| Ny WebDeployPackage |Den här funktionen är inte implementerad. Du kan lägga till kommandon i den här funktionen för att skapa projektet. |
| Publicera AzureWebApplication |Publicerar ett program till Azure. |
| Publicera WebApplication |Skapar och distribuerar Web Apps, virtuella datorer, SQL-databaser och storage-konton för ett webbprojekt i Visual Studio. |
| Test-WebApplication |Den här funktionen är inte implementerad. Du kan lägga till kommandon i den här funktionen för att testa ditt program. |

## <a name="next-steps"></a>Nästa steg
Mer information om PowerShell-skript genom att läsa [med Windows PowerShell-skript](https://technet.microsoft.com/library/bb978526.aspx) och se andra Azure PowerShell-skript på den [Script Center](https://azure.microsoft.com/documentation/scripts/).
