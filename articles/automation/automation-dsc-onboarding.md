---
title: Konfigurera datorer för hantering av Azure Automation DSC
description: Hur du ställer in datorer för hantering med Azure Automation DSC
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.topic: conceptual
ms.date: 07/20/2018
manager: carmonm
ms.openlocfilehash: 907bb9b998012a5b907c041b3eba061036289cc1
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186993"
---
# <a name="onboarding-machines-for-management-by-azure-automation-dsc"></a>Konfigurera datorer för hantering av Azure Automation DSC

## <a name="why-manage-machines-with-azure-automation-dsc"></a>Varför hantera datorer med Azure Automation DSC?

Som [PowerShell Desired State Configuration](https://technet.microsoft.com/library/dn249912.aspx), Azure Automation Desired State Configuration är en enkel men kraftfull configuration management-tjänsten för DSC-noder (fysiska och virtuella datorer) i valfritt moln eller på plats Datacenter. Den möjliggör skalbarhet till tusentals datorer snabbt och enkelt från en central och säker plats. Du kan enkelt integrera datorer, tilldela dem deklarativa konfigurationer och visa rapporter som visar var och en dator är efterlevnad av önskat tillstånd som du har angett. Azure Automation DSC-hanteringslager är att DSC nyheter Azure Automation-hanteringslager till PowerShell-skript. Med andra ord på samma sätt som Azure Automation hjälper dig att hantera PowerShell-skript får också hantera DSC-konfigurationer. Läs mer om fördelarna med att använda Azure Automation DSC i [översikt över Azure Automation DSC](automation-dsc-overview.md).

Azure Automation DSC kan användas för att hantera olika datorer:

* Virtuella Azure-datorer (distribuerad både i den klassiska portalen och distributionsmodellen Azure Resource Manager)
* Amazon Web Services (AWS) EC2-instanser 
* Fysiska/virtuella Windows-datorer lokalt eller i ett annat moln än Azure/AWS
* Fysiska/virtuella Linux-datorer lokalt, i Azure eller i ett annat moln än Azure

Dessutom, om du inte är redo att hantera datorkonfigurationen från molnet kan Azure Automation DSC också användas som en slutpunkt för endast. På så sätt kan du ange (push) önskad konfiguration via DSC på plats och visa omfattande rapportering på noden efterlevnad av önskat tillstånd i Azure Automation.

> [!NOTE]
> Hantera virtuella Azure-datorer med DSC ingår utan extra kostnad om VM DSC-tillägget installerat är större än 2.70. Referera till den [ **Automation prissättningssidan** ](https://azure.microsoft.com/pricing/details/automation/) för mer information.


I följande avsnitt beskrivs hur du kan registrera varje typ av dator till Azure Automation DSC.

## <a name="azure-virtual-machines-classic"></a>Azure-datorer (klassisk)

Med Azure Automation DSC kan du enkelt integrera Azure-datorer (klassisk) för konfigurationshantering med antingen Azure-portalen eller PowerShell. Under huven och utan att en administratör som har fjärråtkomst till den virtuella datorn, registrerar den virtuella datorn i Azure VM Desired State Configuration-tillägget med Azure Automation DSC. Eftersom Azure VM Desired State Configuration-tillägget körs asynkront, steg för att spåra förloppet eller felsöka den finns i följande [ **felsöka Azure VM onboarding** ](#troubleshooting-azure-virtual-machine-onboarding) avsnittet.

### <a name="azure-portal"></a>Azure Portal

I den [Azure-portalen](http://portal.azure.com/), klickar du på **Bläddra** -> **virtuella datorer (klassiska)**. Välj Windows VM som du vill publicera. Den virtuella datorns instrumentpanelen bladet och klicka på **alla inställningar** -> **tillägg** -> **Lägg till** -> **Azure Automation DSC** -> **skapa**. Ange den [PowerShell DSC lokal konfigurationshanterare värden](https://msdn.microsoft.com/powershell/dsc/metaconfig4) krävs för ditt användningsområde, Registreringsnyckeln för ditt Automation-konto och URL: en registrering och eventuellt en nodkonfiguration ska tilldelas den virtuella datorn.

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

Hitta URL: en för registrering och nyckel för Automation-konto för att registrera datorn för att se följande [ **säker registrering** ](#secure-registration) avsnittet:

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
> DSC-nodkonfiguration är skiftlägeskänsliga i portalen. Om det är felaktigt visas inte noden under DSC-noder.

## <a name="azure-virtual-machines"></a>Virtuella Azure-datorer

Azure Automation DSC kan du enkelt integrera Azure virtual machines för konfigurationshantering med Azure-portalen, Azure Resource Manager-mallar eller PowerShell. Under huven och utan att en administratör som har fjärråtkomst till den virtuella datorn, registrerar den virtuella datorn i Azure VM Desired State Configuration-tillägget med Azure Automation DSC. Eftersom Azure VM Desired State Configuration-tillägget körs asynkront, steg för att spåra förloppet eller felsöka den finns i följande [ **felsöka Azure VM onboarding** ](#troubleshooting-azure-virtual-machine-onboarding) avsnittet.

### <a name="azure-portal"></a>Azure Portal

I den [Azure-portalen](https://portal.azure.com/), gå till Azure Automation-konto där du vill publicera virtuella datorer. På instrumentpanelen i Automation-konto, klickar du på **DSC-noder** -> **+ Lägg till virtuell Azure-dator**.

Välj en Azure virtuell dator att publicera.

Om datorn inte har PowerShell desired tillstånd-tillägget installerat och energinivån körs, klickar du på **Connect**.

Under **registrering**, ange den [PowerShell DSC lokal konfigurationshanterare värden](https://msdn.microsoft.com/powershell/dsc/metaconfig4) krävs för ditt användningsområde och eventuellt en nodkonfiguration ska tilldelas den virtuella datorn.

![](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Azure-datorer kan distribueras och har integrerats i Azure Automation DSC via Azure Resource Manager-mallar. Se [konfigurera en virtuell dator via DSC-tillägget och Azure Automation DSC](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) för en exempelmall som registrerar virtuella en befintlig virtuell dator till Azure Automation DSC. Att hitta nyckeln för tjänstregistrering och Registreringswebbadress vidtas som indata i den här mallen, ser du följande [ **säker registrering** ](#secure-registration) avsnittet.

### <a name="powershell"></a>PowerShell

Den [registrera AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) cmdlet kan användas för att publicera virtuella datorer i Azure-portalen via PowerShell.

## <a name="amazon-web-services-aws-virtual-machines"></a>Amazon Web Services (AWS) virtuella datorer

Du kan enkelt integrera Amazon Web Services virtuella datorer för konfigurationshantering med Azure Automation DSC med hjälp av AWS DSC-verktygen. Du kan läsa mer om verktyget [här](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Fysiska/virtuella Windows-datorer lokalt eller i ett annat moln än Azure/AWS

Lokala Windows-datorer och Windows-datorer i Azure-moln (till exempel Amazon Web Services) kan du också vara har integrerats i Azure Automation DSC så länge som de har utgående åtkomst till internet via några få enkla steg:

1. Kontrollera att den senaste versionen av [WMF 5](http://aka.ms/wmf5latest) är installerat på datorer som du vill att publicera till Azure Automation DSC.
2. Följ anvisningarna i följande avsnitt [ **generera DSC metaconfigurations** ](#generating-dsc-metaconfigurations) att generera en mapp som innehåller nödvändiga DSC-metaconfigurations.
3. Via en fjärranslutning gäller PowerShell DSC-metaconfiguration för de datorer du vill publicera. **Den datorn som det här kommandot körs från måste ha den senaste versionen av [WMF 5](http://aka.ms/wmf5latest) installerat**:

    ```powershell
    Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
    ```

4. Om du inte använder PowerShell DSC-metaconfigurations via en fjärranslutning, kopiera mappen metaconfigurations från steg 2 till varje dator för att publicera. Anropa sedan **Set-DscLocalConfigurationManager** lokalt på varje dator för att publicera.
5. Med Azure portal eller cmdlet: ar, kontrollera att datorerna som ska publiceras som nu visas som registrerad i Azure Automation-konto DSC-noder.

## <a name="physicalvirtual-linux-machines-on-premises-in-azure-or-in-a-cloud-other-than-azure"></a>Fysiska/virtuella Linux-datorer lokalt, i Azure eller i ett annat moln än Azure

Lokala Linux-datorer, Linux-datorer i Azure och Linux-datorer i Azure-moln kan du också vara har integrerats i Azure Automation DSC så länge som de har utgående åtkomst till internet via några få enkla steg:

1. Kontrollera att den senaste versionen av [PowerShell Desired State Configuration för Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) är installerat på datorer som du vill att publicera till Azure Automation DSC.
2. Om den [PowerShell DSC lokal konfigurationshanterare standardvärden](https://msdn.microsoft.com/powershell/dsc/metaconfig4) matchar ditt användningsområde och du vill integrera datorer, till exempel att de **både** hämta från och rapportera till Azure Automation DSC:

   + På varje Linux-dator för att publicera till Azure Automation DSC, använder du Register.py att publicera med hjälp av PowerShell DSC Local Configuration Manager-standardinställningar:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   + Nyckel för tjänstregistrering och Registreringswebbadress för ditt Automation-konto finns i följande [ **säker registrering** ](#secure-registration) avsnittet.

     Om PowerShell DSC Local Configuration Manager som standard **gör** **inte** matchar ditt användningsfall, eller om du vill att publicera datorer så att de endast rapporterar till Azure Automation DSC, men inte hämta konfiguration eller PowerShell-moduler från den, Följ steg 3 – 6. Annars kan fortsätta direkt till steg 6.

3. Följ anvisningarna i följande [ **generera DSC metaconfigurations** ](#generating-dsc-metaconfigurations) avsnitt för att skapa en mapp som innehåller nödvändiga DSC-metaconfigurations.
4. Via en fjärranslutning gäller PowerShell DSC-metaconfiguration för de datorer du vill publicera:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String "<root password>" -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard

    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Den datorn som det här kommandot körs från måste ha den senaste versionen av [WMF 5](http://aka.ms/wmf5latest) installerad.

1. Om du inte kan använda PowerShell DSC-metaconfigurations via fjärranslutning, för varje Linux-dator för att publicera, kopierar du metaconfiguration som motsvarar den datorn från mappen i steg 5 till Linux-dator. Anropa sedan `SetDscLocalConfigurationManager.py` lokalt på varje Linux-dator du vill att publicera till Azure Automation DSC:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Med Azure portal eller cmdlet: ar, kontrollera att datorerna som ska publiceras som nu visas som registrerad i Azure Automation-konto DSC-noder.

## <a name="generating-dsc-metaconfigurations"></a>Generera DSC metaconfigurations

Att Allmänt publicera någon dator till Azure Automation DSC, en [DSC metaconfiguration](https://msdn.microsoft.com/powershell/dsc/metaconfig) kan vara genereras som, när tillämpas, säger till DSC-agenten på datorn för att hämta från och/eller rapportera till Azure Automation DSC. DSC-metaconfigurations för Azure Automation DSC kan genereras med hjälp av en PowerShell DSC-konfiguration eller Azure Automation PowerShell-cmdlets.

> [!NOTE]
> DSC-metaconfigurations innehålla hemligheterna som behövs för att registrera en dator till ett Automation-konto för hantering. Se till att skydda alla DSC-metaconfigurations som du skapar eller tar bort dem när du har använt.

### <a name="using-a-dsc-configuration"></a>Använda en DSC-konfiguration

1. Öppna PowerShell ISE som administratör på en dator i din lokala miljö. Datorn måste ha den senaste versionen av [WMF 5](http://aka.ms/wmf5latest) installerad.
2. Kopiera följande skript lokalt. Det här skriptet innehåller en PowerShell DSC-konfiguration för att skapa metaconfigurations och ett kommando för att sätta igång metaconfiguration skapandet.

> [!NOTE]
> DSC-nodkonfiguration är skiftlägeskänsliga i portalen. Om det är felaktigt visas inte noden under DSC-noder.

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

3. Fyll i registreringsnyckel och URL: en för ditt Automation-konto, samt namnen på datorerna som ska publiceras. Alla andra parametrar är valfria. Nyckel för tjänstregistrering och Registreringswebbadress för ditt Automation-konto finns i följande [ **säker registrering** ](#secure-registration) avsnittet.
4. Om du vill att datorerna rapportera DSC statusinformation till Azure Automation DSC, men inte pull-konfiguration eller PowerShell-moduler, anger du den **ReportOnly** parametern till true.
5. Kör skriptet. Du bör nu ha en mapp med namnet **DscMetaConfigs** som innehåller PowerShell DSC-metaconfigurations för datorer i din arbetskatalog att publicera (som administratör):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Med hjälp av Azure Automation-cmdletar

Om standardinställningarna för PowerShell DSC Local Configuration Manager som matchar ditt användningsområde och du vill publicera datorer så att de både hämta från och rapportera till Azure Automation DSC, ger Azure Automation-cmdlets en förenklad metod för att generera DSC metaconfigurations behövs:

1. Öppna PowerShell-konsolen eller PowerShell ISE som administratör på en dator i din lokala miljö.
2. Anslut till Azure Resource Manager med hjälp av **Connect-AzureRmAccount**
3. Ladda ned PowerShell DSC-metaconfigurations för datorer som du vill att publicera Automation-kontot som du vill publicera noder:

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
    
4. Du bör nu ha en mapp med namnet ***DscMetaConfigs***, som innehåller PowerShell DSC-metaconfigurations för virtuella datorer att publicera (som administratör):
    
    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Säker registrering

Datorer kan på ett säkert sätt integrera till ett Azure Automation-konto via WMF 5 DSC registrering protokollet, vilket gör att en DSC-nod att autentisera mot en Pull-PowerShell DSC eller Reporting server (inklusive Azure Automation DSC). Noden registrerar till servern på en **Registreringswebbadress**och autentiseringen med hjälp av en **Registreringsnyckeln**. Under registreringen, DSC-nod och DSC Pull/rapportservern att förhandla ett unikt certifikat för den här noden ska användas för autentisering till servern efter registreringen. Den här processen förhindrar integrerats noder från personifiera en en annan, till exempel om en nod har komprometterats och fungerar vissa ändringar. Efter registreringen nyckel för tjänstregistrering används inte för autentiseringen igen och tas bort från noden.

Du kan få information som krävs för protokollet för DSC-registreringen från **nycklar** under **kontoinställningar** i Azure-portalen. Öppna det här bladet genom att klicka på nyckelikonen på den **Essentials** panelen för Automation-kontot.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

* Registreringswebbadress är URL-fält i Hantera nycklar-bladet.
* Registreringsnyckeln är den primära åtkomstnyckeln eller sekundära åtkomstnyckeln på Hantera nycklar-bladet. Antingen nyckeln kan användas.

För extra säkerhet kan primära och sekundära åtkomstnycklarna för ett Automation-konto kan återskapas när som helst (på den **hantera nycklar** bladet) att förhindra framtida noden registreringar med tidigare nycklar.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Felsöka Azure-dator onboarding

Azure Automation DSC kan du enkelt integrera virtuella Azure Windows-datorer för konfigurationshantering. Under huven används Azure VM Desired State Configuration-tillägget för att registrera den virtuella datorn med Azure Automation DSC. Eftersom Azure VM Desired State Configuration-tillägget körs asynkront kan kan spåra förloppet och felsökning av körningen vara viktiga.

> [!NOTE]
> Valfri metod för registrering av en Azure Windows-dator till Azure Automation DSC som använder Azure VM Desired State Configuration-tillägget kan ta upp till en timme för noden att visa upp som har registrerats i Azure Automation. Det här är på grund av installationen av Windows Management Framework 5.0 på den virtuella datorn med Azure VM DSC-tillägget som krävs för att publicera den virtuella datorn till Azure Automation DSC.

För att felsöka eller visa status för Azure VM Desired State Configuration-tillägget i Azure-portalen navigerar du till den virtuella datorn som det gäller, och klicka sedan på **tillägg** under **inställningar**. Klicka sedan på **DSC** eller **DSCForLinux** beroende på operativsystemet. Mer information kan du klicka på **visa detaljerad statusinformation om**.

## <a name="certificate-expiration-and-reregistration"></a>Förfallodatum för certifikat och omregistrering

När du har registrerat en dator som en DSC-nod i Azure Automation DSC, finns det flera skäl till varför du kan behöva registrera om noden i framtiden:

* När du har registrerat förhandlar automatiskt ett unikt certifikat för autentisering som upphör att gälla efter ett år i varje nod. Protokoll för PowerShell DSC-registreringen kan inte för närvarande automatiskt förnya certifikat när de snart upphör att gälla, så du måste registrera om noderna efter ett års tid. Innan du registrerar om, kontrollera att alla noder kör Windows Management Framework 5.0 RTM. Om en nod Autentiseringscertifikatet upphör att gälla och noden inte har registrerats, noden kan inte kommunicera med Azure Automation och har markerats Unresponsive. Omregistrering utförs 90 dagar eller mindre från certifikatets förfallotid eller när som helst efter certifikatets förfallotid, leder till ett nytt certifikat som genereras och används.
* Ändra [PowerShell DSC lokal konfigurationshanterare värden](https://msdn.microsoft.com/powershell/dsc/metaconfig4) som angavs under registreringen av noden, till exempel ConfigurationMode. Dessa värden för DSC-agenten kan för närvarande kan bara ändras via omregistrering. Det enda undantaget är tilldelad till noden Nodkonfigurationen – detta kan ändras i Azure Automation DSC direkt.

Omregistrering kan utföras på samma sätt som du registrerade noden vilket först med någon av onboarding-metoderna som beskrivs i det här dokumentet. Du behöver inte avregistrera en nod från Azure Automation DSC innan du registrerar om den.

## <a name="related-articles"></a>Relaterade artiklar

* [Översikt över Azure Automation DSC](automation-dsc-overview.md)
* [Azure Automation DSC-cmdletar](/powershell/module/azurerm.automation/#automation)
* [Priser för Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)
