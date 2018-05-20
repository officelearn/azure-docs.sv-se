---
title: Onboarding-datorer för hantering av Azure Automation DSC
description: Hur du ställer in datorer för hantering med Azure Automation DSC
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4493f9da0de12fbdfffdf0f4da0dd581ac3b589f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="onboarding-machines-for-management-by-azure-automation-dsc"></a>Onboarding-datorer för hantering av Azure Automation DSC

## <a name="why-manage-machines-with-azure-automation-dsc"></a>Varför hantera datorer med Azure Automation DSC?

Som [PowerShell Desired State Configuration](https://technet.microsoft.com/library/dn249912.aspx), Azure Automation Desired State Configuration är en enkel men kraftfull, configuration management-tjänst för DSC-noder (fysiska och virtuella datorer) i molnet eller lokalt Datacenter. Den möjliggör skalbarhet över tusentals datorer snabbt och enkelt från en central, säker plats. Du kan enkelt publicera datorer, tilldela dem deklarativ konfigurationer och visa rapporter som visar var datorn överensstämmelse till det tillstånd som du angav. Azure Automation DSC management lagret är att DSC vad Azure Automation management lagret är att PowerShell-skript. Med andra ord på samma sätt som Azure Automation kan du hantera PowerShell-skript, får du också hantera DSC-konfigurationer. Läs mer om fördelarna med att använda Azure Automation DSC i [översikt över Azure Automation DSC](automation-dsc-overview.md).

Azure Automation DSC kan användas för att hantera en mängd olika datorer:

* Virtuella Azure-datorer (klassisk)
* Virtuella Azure-datorer
* Amazon Web Services (AWS) virtuella datorer
* Fysisk virtuell Windows-datorer lokalt eller i ett moln än Azure/AWS
* Fysisk virtuell Linux-datorer lokalt, i Azure eller i ett moln än Azure

Dessutom om du inte är redo att hantera datorkonfigurationen från molnet kan Azure Automation DSC också användas som en endast rapport-slutpunkt. På så sätt kan du ange (push) önskad konfiguration via DSC lokala och visa omfattande rapportering på noden kompatibilitet med tillståndet i Azure Automation.

> [!NOTE]
> Hantera virtuella Azure-datorer med DSC ingår utan extra kostnad om virtuella DSC-tillägg som installeras är större än 2.70. Referera till den [ **Automation sida med priser** ](https://azure.microsoft.com/pricing/details/automation/) för mer information.


I följande avsnitt beskrivs hur du kan publicera varje typ av dator till Azure Automation DSC.

## <a name="azure-virtual-machines-classic"></a>Virtuella Azure-datorer (klassisk)

Med Azure Automation DSC kan du enkelt publicera virtuella Azure-datorer (klassisk) för konfigurationshantering med antingen Azure-portalen eller PowerShell. Under huven och en administratör behöver fjärråtkomst till den virtuella datorn, registrerar Azure VM Desired State Configuration-tillägget den virtuella datorn med Azure Automation DSC. Eftersom Azure VM Desired State Configuration-tillägg körs asynkront, steg att följa upp förloppet eller felsöka den finns i följande [ **felsöka Azure virtuella onboarding** ](#troubleshooting-azure-virtual-machine-onboarding) avsnitt.

### <a name="azure-portal"></a>Azure Portal

I den [Azure-portalen](http://portal.azure.com/), klickar du på **Bläddra** -> **virtuella datorer (klassisk)**. Välj Windows virtuell dator som du vill publicera. Klicka på den virtuella datorns instrumentpanelen bladet **alla inställningar** -> **tillägg** -> **Lägg till** -> **Azure Automation DSC** -> **skapa**. Ange den [PowerShell DSC Local Configuration Manager värden](https://msdn.microsoft.com/powershell/dsc/metaconfig4) krävs för din användningsfall registreringsnyckel ditt Automation-konto och URL: en registrering och eventuellt en nodkonfiguration att tilldela den virtuella datorn.

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

Att hitta URL: en för registrering och nyckeln för Automation-konto för att publicera datorn för att se följande [ **Secure registrering** ](#secure-registration) avsnitt:

### <a name="powershell"></a>PowerShell

```powershell
# log in to both Azure Service Management and Azure Resource Manager
Add-AzureAccount
Connect-AzureRmAccount

# fill in correct values for your VM/Automation account here
$VMName = ""
$ServiceName = ""
$AutomationAccountName = ""
$AutomationAccountResourceGroup = ""

# fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
# NOTE: DSC Node Configuration names are case sensitive in the portal.
$NodeConfigName = ""

# get Azure Automation DSC registration info
$Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
$RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

# use the DSC extension to onboard the VM for management with Azure Automation DSC
$VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

$PublicConfiguration = ConvertTo-Json -Depth 8 @{
    SasToken = ""
    ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
    ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

# update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
# See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
    Properties = @{
    RegistrationKey = @{
        UserName = 'notused'
        Password = 'PrivateSettingsRef:RegistrationKey'
    }
    RegistrationUrl = $RegistrationInfo.Endpoint
    NodeConfigurationName = $NodeConfigName
    ConfigurationMode = "ApplyAndMonitor"
    ConfigurationModeFrequencyMins = 15
    RefreshFrequencyMins = 30
    RebootNodeIfNeeded = $False
    ActionAfterReboot = "ContinueConfiguration"
    AllowModuleOverwrite = $False
    }
}

$PrivateConfiguration = ConvertTo-Json -Depth 8 @{
    Items = @{
        RegistrationKey = $RegistrationInfo.PrimaryKey
    }
}

$VM = Set-AzureVMExtension `
    -VM $vm `
    -Publisher Microsoft.Powershell `
    -ExtensionName DSC `
    -Version 2.19 `
    -PublicConfiguration $PublicConfiguration `
    -PrivateConfiguration $PrivateConfiguration `
    -ForceUpdate

$VM | Update-AzureVM
```

> [!NOTE]
> DSC-nodkonfiguration är skiftlägeskänsliga i portalen. Om den inte matchar visas noden inte under DSC-noder.

## <a name="azure-virtual-machines"></a>Virtuella Azure-datorer

Azure Automation DSC kan du enkelt publicera virtuella Azure-datorer för konfigurationshantering, med hjälp av Azure-portalen, Azure Resource Manager-mallar eller PowerShell. Under huven och en administratör behöver fjärråtkomst till den virtuella datorn, registrerar Azure VM Desired State Configuration-tillägget den virtuella datorn med Azure Automation DSC. Eftersom Azure VM Desired State Configuration-tillägg körs asynkront, steg att följa upp förloppet eller felsöka den finns i följande [ **felsöka Azure virtuella onboarding** ](#troubleshooting-azure-virtual-machine-onboarding) avsnitt.

### <a name="azure-portal"></a>Azure Portal

I den [Azure-portalen](https://portal.azure.com/), gå till Azure Automation-konto där du vill publicera virtuella datorer. Klicka på instrumentpanelen för automatisering konto, **DSC-noder** -> **+ Lägg till Azure VM**.

Välj en virtuell Azure-dator ska publiceras.

Om datorn inte har PowerShell önskad installerat tillägg på tillstånd och energiläget körs, klickar du på **Anslut**.

Under **registrering**, ange den [PowerShell DSC Local Configuration Manager värden](https://msdn.microsoft.com/powershell/dsc/metaconfig4) krävs för din användningsfall och eventuellt en nodkonfiguration att tilldela den virtuella datorn.

![](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Virtuella Azure-datorer kan distribueras och publicerats så att Azure Automation DSC via Azure Resource Manager-mallar. Se [konfigurera en virtuell dator via DSC-tillägg och Azure Automation DSC](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) för en exempelmall som onboards en befintlig virtuell dator till Azure Automation DSC. Att hitta Registreringsnyckeln och URL: en registrering vidtas som indata i den här mallen finns i följande [ **Secure registrering** ](#secure-registration) avsnitt.

### <a name="powershell"></a>PowerShell

Den [registrera AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) cmdlet kan användas för att publicera virtuella datorer i Azure-portalen via PowerShell.

## <a name="amazon-web-services-aws-virtual-machines"></a>Amazon Web Services (AWS) virtuella datorer

Du kan enkelt publicera Amazon Web Services virtuella datorer för konfigurationshantering av Azure Automation DSC med AWS DSC Toolkit. Du kan lära dig mer om toolkit [här](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Fysisk virtuell Windows-datorer lokalt eller i ett moln än Azure/AWS

Lokalt Windows-datorer och Windows-datorer i Azure-moln (till exempel Amazon Web Services) kan också vara publicerats så att Azure Automation DSC, så länge som de har utgående åtkomst till internet, via några enkla steg:

1. Kontrollera att den senaste versionen av [WMF 5](http://aka.ms/wmf5latest) installeras på datorer som du vill publicera till Azure Automation DSC.
2. Följ anvisningarna i följande avsnitt [ **genererar DSC metaconfigurations** ](#generating-dsc-metaconfigurations) att generera en mapp som innehåller nödvändiga DSC-metaconfigurations.
3. Via fjärranslutning tillämpa PowerShell DSC-metakonfigurationen för de datorer du vill publicera. **Datorn som kommandot körs från måste ha den senaste versionen av [WMF 5](http://aka.ms/wmf5latest) installerat**:

    ```powershell
    Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
    ```

4. Om du inte använder PowerShell DSC-metaconfigurations via fjärranslutning, kopiera mappen metaconfigurations från steg 2 till varje dator för att publicera. Sedan anropar **Set DscLocalConfigurationManager** lokalt på varje dator som ska publiceras.
5. Med hjälp av Azure-portalen eller cmdletar måste du kontrollera att datorerna som ska publiceras visas som DSC-noder som är registrerade i Azure Automation-konto.

## <a name="physicalvirtual-linux-machines-on-premises-in-azure-or-in-a-cloud-other-than-azure"></a>Fysisk virtuell Linux-datorer lokalt, i Azure eller i ett moln än Azure

Lokal Linux-datorer, Linux-datorer i Azure och Linux-datorer i Azure-moln kan också vara publicerats så att Azure Automation DSC, så länge som de har utgående åtkomst till internet, via några enkla steg:

1. Kontrollera att den senaste versionen av [PowerShell Desired State Configuration för Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) installeras på datorer som du vill publicera till Azure Automation DSC.
2. Om den [PowerShell DSC Local Configuration Manager standardvärden](https://msdn.microsoft.com/powershell/dsc/metaconfig4) matchar din användningsfall och du vill publicera datorer så att de **både** hämtar från och rapportera till Azure Automation DSC:

   + Använd Register.py på varje Linux-dator för att publicera till Azure Automation DSC, ska publiceras Local Configuration Manager för PowerShell DSC-standardvärden:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   + Den registreringsnyckel och URL: en registrering för Automation-kontot finns i följande [ **Secure registrering** ](#secure-registration) avsnitt.

     Om PowerShell DSC Local Configuration Manager som standard **gör** **inte** matchar din användningsfall, eller om du vill publicera datorer så att de endast rapportera till Azure Automation DSC, men inte hämtar konfigurationen eller PowerShell-moduler från den, Följ steg 3-6. Annars Fortsätt direkt till steg 6.

3. Följ anvisningarna i följande [ **genererar DSC metaconfigurations** ](#generating-dsc-metaconfigurations) avsnittet för att skapa en mapp som innehåller nödvändiga DSC-metaconfigurations.
4. Via fjärranslutning tillämpa PowerShell DSC-metakonfigurationen för de datorer du vill publicera:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String "<root password>" -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard

    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Datorn som kommandot körs från måste ha den senaste versionen av [WMF 5](http://aka.ms/wmf5latest) installerad.

1. Om du inte använda PowerShell DSC-metaconfigurations via fjärranslutning, för varje Linux-dator för att publicera, kopiera metakonfigurationen som motsvarar den datorn från mappen i steg 5 till Linux-dator. Sedan anropar `SetDscLocalConfigurationManager.py` lokalt på varje Linux-dator du vill publicera till Azure Automation DSC:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Med hjälp av Azure-portalen eller cmdletar måste du kontrollera att datorerna som ska publiceras visas som DSC-noder som är registrerade i Azure Automation-konto.

## <a name="generating-dsc-metaconfigurations"></a>Generera DSC metaconfigurations

Att publicera Allmänt någon dator till Azure Automation DSC en [DSC-metakonfigurationen](https://msdn.microsoft.com/powershell/dsc/metaconfig) kan genereras som, när tillämpas, talar om DSC-agenten på datorn för att hämta från och/eller rapportera till Azure Automation DSC. DSC-metaconfigurations för Azure Automation DSC kan genereras med hjälp av en PowerShell DSC-konfiguration eller Azure Automation PowerShell-cmdlets.

> [!NOTE]
> DSC-metaconfigurations innehåller hemligheterna behövs till publicera en dator till ett Automation-konto för hantering. Se till att skydda alla DSC-metaconfigurations som du skapar eller tar bort dem efter användning.

### <a name="using-a-dsc-configuration"></a>Med hjälp av DSC-konfiguration

1. Öppna PowerShell ISE som administratör på en dator i din lokala miljö. Datorn måste ha den senaste versionen av [WMF 5](http://aka.ms/wmf5latest) installerad.
2. Kopiera följande skript lokalt. Det här skriptet innehåller en PowerShell DSC-konfiguration för att skapa metaconfigurations och ett kommando för att skapa metakonfigurationen startar.

> [!NOTE]
> DSC-nodkonfiguration är skiftlägeskänsliga i portalen. Om den inte matchar visas noden inte under DSC-noder.

    ```powershell
    # The DSC configuration that will generate metaconfigurations
    [DscLocalConfigurationManager()]
    Configuration DscMetaConfigs
    {

        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = "ApplyAndMonitor",

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = "ContinueConfiguration",

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq "")
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
        $RefreshMode = "PUSH"
        }
        else
        {
        $RefreshMode = "PULL"
        }

        Node $ComputerName
        {

            Settings
            {
                RefreshFrequencyMins = $RefreshFrequencyMins
                RefreshMode = $RefreshMode
                ConfigurationMode = $ConfigurationMode
                AllowModuleOverwrite = $AllowModuleOverwrite
                RebootNodeIfNeeded = $RebootNodeIfNeeded
                ActionAfterReboot = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationDSC
                {
                    ServerUrl = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationDSC
                {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationDSC
            {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
    }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
    $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
    }

    # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    DscMetaConfigs @Params
    ```

3. Fyll i registreringsnyckel och URL: en för ditt Automation-konto, samt namnen på datorerna som ska publiceras. Alla andra parametrar är valfria. Den registreringsnyckel och URL: en registrering för Automation-kontot finns i följande [ **Secure registrering** ](#secure-registration) avsnitt.
4. Om du vill att datorerna rapportera DSC statusinformation till Azure Automation DSC, men inte pull-konfiguration eller PowerShell-moduler, ange den **ReportOnly** parameter till true.
5. Kör skriptet. Du bör nu ha en mapp med namnet **DscMetaConfigs** som innehåller PowerShell DSC-metaconfigurations för datorer i arbetskatalogen publicera (som administratör):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Med hjälp av Azure Automation-cmdlets

Om standardinställningarna PowerShell DSC Local Configuration Manager som matchar dina användningsfall och du vill publicera datorer så att de hämtar från såväl rapportera till Azure Automation DSC, ger Azure Automation-cmdlets en förenklad metoden för att skapa DSC metaconfigurations behövs:

1. Öppna PowerShell-konsolen eller PowerShell ISE som administratör på en dator i din lokala miljö.
2. Anslut till Azure Resource Manager med hjälp av **Connect-AzureRmAccount**
3. Ladda ned PowerShell DSC-metaconfigurations för datorer som du vill ska publiceras från Automation-kontot som du vill publicera noder:

    ```powershell
    # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
    $Params = @{

        ResourceGroupName = 'ContosoResources'; # The name of the ARM Resource Group that contains your Azure Automation Account
        AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
        ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
        OutputFolder = "$env:UserProfile\Desktop\";
    }
    # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    Get-AzureRmAutomationDscOnboardingMetaconfig @Params
    ```
    
4. Du bör nu ha en mapp med namnet ***DscMetaConfigs***, som innehåller PowerShell DSC-metaconfigurations för datorer publicera (som administratör):
    
    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Säker registrering

Datorer kan på ett säkert sätt publicera till en Azure Automation-konto via WMF 5 DSC registrering protokollet, vilket låter en DSC-nod att autentisera till en Pull-PowerShell DSC- eller Reporting server (inklusive Azure Automation DSC). Noden registrerar till servern på en **URL: en registrering**, autentiseras med hjälp av en **registreringsnyckel**. Under registreringen, DSC-nod och DSC Pull/rapportserver förhandla ett unikt certifikat för den här noden ska användas för autentisering till servern efter registreringen. Den här processen förhindrar publicerats så noder från personifiera en annan, till exempel om en nod har komprometterats och uppför medvetet. Efter registreringen nyckel för tjänstregistrering används inte för autentiseringen igen och tas bort från noden.

Du kan hämta den information som krävs för protokollet för DSC-registreringen från **nycklar** under **kontoinställningar** i Azure-portalen. Öppna bladet genom att klicka på nyckelikonen på den **Essentials** panelen för Automation-kontot.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

* URL: en för registrering är URL-fältet i bladet hantera nycklar.
* Nyckel för tjänstregistrering är den primära åtkomstnyckeln eller sekundära åtkomstnyckeln i bladet hantera nycklar. Antingen nyckeln kan användas.

För extra säkerhet kan primära och sekundära åtkomstnycklarna för ett Automation-konto kan genereras när som helst (om den **hantera nycklar** bladet) för att förhindra framtida nod registreringar med tidigare nycklar.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Felsökning av virtuell dator i Azure-onboarding

Azure Automation DSC kan du enkelt inbyggda Windows Azure-datorer för konfigurationshantering. Under huven används Azure VM Desired State Configuration-tillägget för att registrera den virtuella datorn med Azure Automation DSC. Eftersom Azure VM Desired State Configuration-tillägg körs asynkront, spåra förloppet och felsökning av körningen kan det vara viktigt.

> [!NOTE]
> En metod för onboarding av en virtuell Azure Windows-dator till Azure Automation DSC som använder Azure VM Desired State Configuration-tillägget kan ta upp till en timme att visa upp som har registrerats i Azure Automation-nod. Detta beror på att installationen av Windows Management Framework 5.0 på den virtuella datorn via Azure VM DSC-tillägg som krävs för att publicera den virtuella datorn till Azure Automation DSC.

För att felsöka eller visa status för Azure VM Desired State Configuration-tillägget, Azure-portalen går du till den virtuella datorn som publicerats så och klicka sedan på **tillägg** under **inställningar**. Klicka på **DSC** eller **DSCForLinux** beroende på ditt operativsystem. Mer information kan du klicka på **visa detaljerad statusinformation om**.

## <a name="certificate-expiration-and-reregistration"></a>Certifikatet upphör att gälla och omregistrering

När du registrerar en dator som en DSC-nod i Azure Automation DSC, finns det flera skäl till varför du kanske måste registrera om noden i framtiden:

* Varje nod förhandlar automatiskt ett unikt certifikat för autentisering som upphör att gälla efter ett år efter registrering. Protokollet PowerShell DSC-registrering kan för närvarande automatiskt förnya certifikat när de närmar sig förfallodatum, så du behöver registrera om noderna efter ett år tid. Innan du registrerar om, kontrollerar du att varje nod körs Windows Management Framework 5.0 RTM. Om en nod Autentiseringscertifikatet upphör att gälla och noden inte återregistrerade, noden kan inte kommunicera med Azure Automation och har markerats Unresponsive. Omregistreringen utförs 90 dagar eller mindre från certifikatets förfallotid eller när som helst efter tidpunkt då certifikatet upphör att gälla, leder till ett nytt certifikat som genereras och används.
* Att ändra [PowerShell DSC Local Configuration Manager värden](https://msdn.microsoft.com/powershell/dsc/metaconfig4) som angavs under registreringen av noden, till exempel ConfigurationMode. För närvarande kan värdena DSC-agenten endast ändras via omregistreringen. Det enda undantaget är nodkonfiguration tilldelad noden – här kan ändras i Azure Automation DSC direkt.

Omregistreringen kan utföras på samma sätt som du har registrerat noden till en början med hjälp av onboarding-metoderna som beskrivs i det här dokumentet. Du behöver inte att avregistrera en nod från Azure Automation DSC innan du registrerar om den.

## <a name="related-articles"></a>Relaterade artiklar

* [Översikt över Azure Automation DSC](automation-dsc-overview.md)
* [Azure Automation DSC-cmdlets](/powershell/module/azurerm.automation/#automation)
* [Priser för Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)
