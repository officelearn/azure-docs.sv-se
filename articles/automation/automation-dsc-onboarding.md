---
title: Konfigurera datorer för hantering av Azure Automation State Configuration
description: Hur du ställer in datorer för hantering med Azure Automation State Configuration
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: 8a505e88ff92c5227d3b42da2adaf1dce58e6fbb
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441495"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Konfigurera datorer för hantering av Azure Automation State Configuration

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Varför hantera datorer med Azure Automation-Tillståndskonfiguration?

Azure Automation State Configuration är en management-konfigurationstjänsten för DSC-noder i ett moln eller lokala datacenter.
Den möjliggör skalbarhet till tusentals datorer snabbt och enkelt från en central och säker plats.
Du kan enkelt integrera datorer, tilldela dem deklarativa konfigurationer och visa rapporter som visar var och en dator är efterlevnad av önskat tillstånd som du har angett.
Azure Automation State Configuration-tjänsten är att DSC vad Azure Automation-runbooks är att PowerShell-skript.
Med andra ord på samma sätt som Azure Automation hjälper dig att hantera PowerShell-skript får också hantera DSC-konfigurationer.
Läs mer om fördelarna med att använda Azure Automation-Tillståndskonfiguration i [översikt över Azure Automation-Tillståndskonfiguration](automation-dsc-overview.md).

Azure Automation-Tillståndskonfiguration kan användas för att hantera olika datorer:

- Virtuella Azure-datorer
- Azure-datorer (klassisk)
- Amazon Web Services (AWS) EC2-instanser
- Fysiska/virtuella Windows-datorer lokalt eller i ett annat moln än Azure/AWS
- Fysiska/virtuella Linux-datorer lokalt, i Azure eller i ett annat moln än Azure

Dessutom om du inte är redo att hantera datorkonfigurationen från molnet kan tillståndskonfigurationen för Azure Automation också användas som en endast-slutpunkt.
På så sätt kan du ange (push) konfigurationer via DSC och visa rapporteringsuppgifter i Azure Automation.

> [!NOTE]
> Hantera virtuella Azure-datorer med Tillståndskonfiguration ingår utan extra kostnad om VM DSC-tillägget installerat är större än 2.70. Referera till den [ **Automation prissättningssidan** ](https://azure.microsoft.com/pricing/details/automation/) för mer information.

I följande avsnitt beskrivs hur du kan registrera varje typ av dator till Azure Automation State Configuration.

## <a name="azure-virtual-machines"></a>Virtuella Azure-datorer

Tillståndskonfiguration för Azure Automation kan du enkelt integrera Azure virtual machines för konfigurationshantering med Azure-portalen, Azure Resource Manager-mallar eller PowerShell. Under huven och utan att en administratör som har fjärråtkomst till den virtuella datorn registreras tillägget Azure VM Desired State Configuration den virtuella datorn med Azure Automation State Configuration.
Eftersom Azure VM Desired State Configuration-tillägget körs asynkront, steg för att spåra förloppet eller felsöka den finns i följande [ **felsöka Azure VM onboarding** ](#troubleshooting-azure-virtual-machine-onboarding) avsnittet.

### <a name="azure-portal"></a>Azure Portal

I den [Azure-portalen](https://portal.azure.com/), gå till Azure Automation-konto där du vill publicera virtuella datorer. På konfigurationssidan för tillstånd och **noder** fliken **+ Lägg till**.

Välj en Azure virtuell dator att publicera.

Om datorn inte har PowerShell desired tillstånd-tillägget installerat och energinivån körs, klickar du på **Connect**.

Under **registrering**, ange den [PowerShell DSC lokal konfigurationshanterare värden](/powershell/dsc/metaconfig4) krävs för ditt användningsområde och eventuellt en nodkonfiguration ska tilldelas den virtuella datorn.

![Onboarding](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Azure-datorer kan distribueras och har integrerats i Azure Automation-Tillståndskonfiguration via Azure Resource Manager-mallar. Se [Server hanteras av Desired State Configuration-tjänsten](https://azure.microsoft.com/resources/templates/101-automation-configuration/) för en exempelmall som registrerar virtuella en befintlig virtuell dator till Azure Automation State Configuration.
Om du hanterar en Virtual Machine Scale Sets, se exempel mallen [ange konfigurationen för virtuell dator skala hanteras av Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>PowerShell

Den [registrera AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) cmdlet kan användas för att publicera virtuella datorer i Azure-portalen via PowerShell.

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Registrera virtuella datorer mellan Azure-prenumerationer

Det bästa sättet att registrera virtuella datorer från andra Azure-prenumerationer är att använda DSC-tillägg i en Distributionsmall av Azure Resource Manager.
Exempel finns i [Desired State Configuration-tillägget med Azure Resource Manager-mallar](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).
Nyckel för tjänstregistrering och URL: en för registrering ska användas som parametrar i mallen finns i följande [ **säker registrering** ](#secure-registration) avsnittet.

## <a name="amazon-web-services-aws-virtual-machines"></a>Amazon Web Services (AWS) virtuella datorer

Du kan enkelt integrera Amazon Web Services virtuella datorer för konfigurationshantering av tillståndskonfigurationen för Azure Automation med hjälp av AWS DSC-verktygen. Du kan läsa mer om verktyget [här](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Fysiska/virtuella Windows-datorer lokalt eller i ett annat moln än Azure/AWS

Windows-servrar som körs lokalt eller i andra miljöer i molnet kan också vara har integrerats i Azure Automation State Configuration, så länge som de har [utgående åtkomst till Azure](automation-dsc-overview.md#network-planning):

1. Kontrollera att den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) är installerat på datorer som du vill att publicera till Azure Automation State Configuration.
1. Följ anvisningarna i följande avsnitt [ **generera DSC metaconfigurations** ](#generating-dsc-metaconfigurations) att generera en mapp som innehåller nödvändiga DSC-metaconfigurations.
1. Via en fjärranslutning gäller PowerShell DSC-metaconfiguration för de datorer du vill publicera. **Den datorn som det här kommandot körs från måste ha den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) installerat**:

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Om du inte använder PowerShell DSC-metaconfigurations via en fjärranslutning, kopiera mappen metaconfigurations från steg 2 till varje dator för att publicera. Anropa sedan **Set-DscLocalConfigurationManager** lokalt på varje dator för att publicera.
1. Med Azure portal eller cmdlet: ar, kontrollera att datorerna som ska publiceras som nu visas som Tillståndskonfiguration noder som registrerats i Azure Automation-kontot.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Fysiska/virtuella Linux-datorer lokalt eller i ett annat moln än Azure

Linux-servrar som körs lokalt eller i andra miljöer i molnet kan också vara har integrerats i Azure Automation State Configuration, så länge som de har [utgående åtkomst till Azure](automation-dsc-overview.md#network-planning):

1. Kontrollera att den senaste versionen av [PowerShell Desired State Configuration för Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) är installerat på datorer som du vill att publicera till Azure Automation State Configuration.
1. Om den [PowerShell DSC lokal konfigurationshanterare standardvärden](/powershell/dsc/metaconfig4) matchar ditt användningsområde och du vill integrera datorer, till exempel att de **både** hämta från och rapportera till Azure Automation-Tillståndskonfiguration:

   - På varje Linux-dator för att publicera till Azure Automation-Tillståndskonfiguration, använder du `Register.py` att publicera med hjälp av PowerShell DSC Local Configuration Manager-standardinställningar:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Nyckel för tjänstregistrering och Registreringswebbadress för ditt Automation-konto finns i följande [ **säker registrering** ](#secure-registration) avsnittet.

     Om PowerShell DSC Local Configuration Manager som standard **inte** matchar ditt användningsområde eller du vill publicera datorer så att de endast rapporterar till Azure Automation-Tillståndskonfiguration, Följ steg 3 – 6. Annars kan fortsätta direkt till steg 6.

1. Följ anvisningarna i följande [ **generera DSC metaconfigurations** ](#generating-dsc-metaconfigurations) avsnitt för att skapa en mapp som innehåller nödvändiga DSC-metaconfigurations.
1. Via en fjärranslutning gäller PowerShell DSC-metaconfiguration för de datorer du vill publicera:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Den datorn som det här kommandot körs från måste ha den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) installerad.

1. Om du inte kan använda PowerShell DSC-metaconfigurations via en fjärranslutning, kopiera metaconfiguration som motsvarar den datorn från mappen i steg 5 till Linux-dator. Anropa sedan `SetDscLocalConfigurationManager.py` lokalt på varje Linux-dator du vill att publicera till Azure Automation-Tillståndskonfiguration:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Med Azure portal eller cmdlet: ar, kontrollera att datorerna som ska publiceras som nu visas som registrerad i Azure Automation-konto DSC-noder.

## <a name="generating-dsc-metaconfigurations"></a>Generera DSC metaconfigurations

Att Allmänt publicera någon dator tillståndskonfigurationen för Azure Automation, en [DSC metaconfiguration](/powershell/dsc/metaconfig) kan genereras som säger till DSC-agenten för att hämta från och/eller rapportera till Azure Automation State Configuration. DSC-metaconfigurations för Azure Automation-Tillståndskonfiguration kan genereras med hjälp av en PowerShell DSC-konfiguration eller Azure Automation PowerShell-cmdlets.

> [!NOTE]
> DSC-metaconfigurations innehålla hemligheterna som behövs för att registrera en dator till ett Automation-konto för hantering. Se till att skydda alla DSC-metaconfigurations som du skapar eller tar bort dem när du har använt.

### <a name="using-a-dsc-configuration"></a>Använda en DSC-konfiguration

1. Öppna VSCode (eller redigeringsprogram du föredrar) som en administratör i en virtuell dator i din lokala miljö. Datorn måste ha den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) installerad.
1. Kopiera följande skript lokalt. Det här skriptet innehåller en PowerShell DSC-konfiguration för att skapa metaconfigurations och ett kommando för att sätta igång metaconfiguration skapandet.

> [!NOTE]
> Statliga är Configuration nodkonfiguration skiftlägeskänsliga i portalen. Om det är felaktigt noden visas inte den **noder** fliken.

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

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
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

1. Fyll i registreringsnyckel och URL: en för ditt Automation-konto, samt namnen på datorerna som ska publiceras. Alla andra parametrar är valfria. Nyckel för tjänstregistrering och Registreringswebbadress för ditt Automation-konto finns i följande [ **säker registrering** ](#secure-registration) avsnittet.
1. Om du vill att datorerna rapportera DSC statusinformation till Azure Automation State Configuration, men inte pull-konfiguration eller PowerShell-moduler, anger du den **ReportOnly** parametern till true.
1. Kör skriptet. Du bör nu ha en mapp med namnet **DscMetaConfigs** som innehåller PowerShell DSC-metaconfigurations för datorer i din arbetskatalog att publicera (som administratör):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Med hjälp av Azure Automation-cmdletar

Om standardinställningarna för PowerShell DSC Local Configuration Manager som matchar ditt användningsområde och du vill publicera datorer så att de både hämta från och rapportera till Azure Automation State Configuration, ger Azure Automation-cmdlets en förenklad metod för att generera DSC-metaconfigurations behövs:

1. Öppna PowerShell-konsolen eller VSCode som administratör på en dator i din lokala miljö.
2. Ansluta till Azure Resource Manager med `Connect-AzureRmAccount`
3. Ladda ned PowerShell DSC-metaconfigurations för datorer som du vill att publicera Automation-kontot som du vill publicera noder:

   ```powershell
   # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzureRmAutomationDscOnboardingMetaconfig @Params
   ```

1. Du bör nu ha en mapp med namnet ***DscMetaConfigs***, som innehåller PowerShell DSC-metaconfigurations för virtuella datorer att publicera (som administratör):

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Säker registrering

Datorer kan på ett säkert sätt integrera till ett Azure Automation-konto via WMF 5 DSC registrering protokollet, vilket gör att en DSC-nod att autentisera mot en Pull-PowerShell DSC eller Reporting server (inklusive Azure Automation State Configuration). Noden registrerar till servern på en **Registreringswebbadress**och autentiseringen med hjälp av en **Registreringsnyckeln**. Under registreringen, DSC-nod och DSC Pull/rapportservern att förhandla ett unikt certifikat för den här noden ska användas för autentisering till servern efter registreringen. Den här processen förhindrar integrerats noder från personifiera en en annan, till exempel om en nod har komprometterats och fungerar vissa ändringar. Efter registreringen nyckel för tjänstregistrering används inte för autentiseringen igen och tas bort från noden.

Du kan hämta den information som krävs för protokollet Tillståndskonfiguration registrering från **nycklar** under **kontoinställningar** i Azure-portalen. Öppna det här bladet genom att klicka på nyckelikonen på den **Essentials** panelen för Automation-kontot.

![Azure automation-nycklar och URL: en](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Registreringswebbadress är URL-fält i Hantera nycklar-bladet.
- Registreringsnyckeln är den primära åtkomstnyckeln eller sekundära åtkomstnyckeln på Hantera nycklar-bladet. Antingen nyckeln kan användas.

För extra säkerhet kan primära och sekundära åtkomstnycklarna för ett Automation-konto kan återskapas när som helst (på den **hantera nycklar** sidan) att förhindra framtida noden registreringar med tidigare nycklar.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Felsöka Azure-dator onboarding

Tillståndskonfiguration för Azure Automation kan du enkelt integrera virtuella Azure Windows-datorer för konfigurationshantering. Under huven används Azure VM Desired State Configuration-tillägget för att registrera den virtuella datorn med Azure Automation State Configuration. Eftersom Azure VM Desired State Configuration-tillägget körs asynkront kan kan spåra förloppet och felsökning av körningen vara viktiga.

> [!NOTE]
> Valfri metod för registrering av en Azure Windows-dator till Azure Automation-Tillståndskonfiguration som använder Azure VM Desired State Configuration-tillägget kan ta upp till en timme för noden att visa upp som har registrerats i Azure Automation. Det här är på grund av installationen av Windows Management Framework 5.0 på den virtuella datorn med Azure VM DSC-tillägget som krävs för att publicera den virtuella datorn till Azure Automation State Configuration.

För att felsöka eller visa status för Azure VM Desired State Configuration-tillägget i Azure-portalen navigerar du till den virtuella datorn som det gäller, och klicka sedan på **tillägg** under **inställningar**. Klicka sedan på **DSC** eller **DSCForLinux** beroende på operativsystemet. Mer information kan du klicka på **visa detaljerad statusinformation om**.

## <a name="certificate-expiration-and-reregistration"></a>Förfallodatum för certifikat och omregistrering

När du har registrerat en dator som en DSC-nod i Azure Automation-Tillståndskonfiguration, finns det flera skäl till varför du kan behöva registrera om noden i framtiden:

- När du har registrerat förhandlar automatiskt ett unikt certifikat för autentisering som upphör att gälla efter ett år i varje nod. Protokoll för PowerShell DSC-registreringen kan inte för närvarande automatiskt förnya certifikat när de snart upphör att gälla, så du måste registrera om noderna efter ett års tid. Innan du registrerar om, kontrollera att alla noder kör Windows Management Framework 5.0 RTM. Om en nod Autentiseringscertifikatet upphör att gälla och noden inte har registrerats, noden kan inte kommunicera med Azure Automation och har markerats Unresponsive. Omregistrering utförs 90 dagar eller mindre från certifikatets förfallotid eller när som helst efter certifikatets förfallotid, leder till ett nytt certifikat som genereras och används.
- Ändra [PowerShell DSC lokal konfigurationshanterare värden](/powershell/dsc/metaconfig4) som angavs under registreringen av noden, till exempel ConfigurationMode. Dessa värden för DSC-agenten kan för närvarande kan bara ändras via omregistrering. Det enda undantaget är tilldelad till noden Nodkonfigurationen – detta kan ändras i Azure Automation DSC direkt.

Omregistrering kan utföras på samma sätt som du registrerade noden vilket först med någon av onboarding-metoderna som beskrivs i det här dokumentet. Du behöver inte avregistrera en nod från Azure Automation-Tillståndskonfiguration innan du registrerar om den.

## <a name="next-steps"></a>Nästa steg

- Kom igång genom att se [komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md)
- Läs om hur du kompilera DSC-konfigurationer så att du kan tilldela dem till målnoder i [kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md)
- PowerShell-cmdlet-referens, se [tillståndskonfigurationen för Azure Automation-cmdletar](/powershell/module/azurerm.automation/#automation)
- Information om priser finns i [priser för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Om du vill se ett exempel på hur du använder Azure Automation-Tillståndskonfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med hjälp av Azure Automation Tillståndskonfiguration och Chocolatey](automation-dsc-cd-chocolatey.md)
