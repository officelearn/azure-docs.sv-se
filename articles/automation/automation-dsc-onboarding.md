---
title: Onboarding-datorer för hantering genom Azure Automation tillstånds konfiguration
description: Konfigurera datorer för hantering med Azure Automation tillstånds konfiguration
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: b003c0cc6480c5d03c3755e7c57785ab2026194b
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498394"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Onboarding-datorer för hantering genom Azure Automation tillstånds konfiguration

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Varför ska jag hantera datorer med Azure Automation tillstånds konfiguration?

Azure Automation tillstånds konfiguration är en konfigurations hanterings tjänst för DSC-noder i ett moln eller lokalt Data Center.
Det möjliggör skalbarhet på tusentals datorer snabbt och enkelt från en central, säker plats.
Du kan enkelt publicera datorer, tilldela dem deklarativ konfigurationer och Visa rapporter som visar varje dators kompatibilitet med önskat tillstånd som du har angett.
Azure Automation tillstånds konfigurations tjänsten är till DSC vad Azure Automation runbooks är till PowerShell-skript.
Med andra ord, på samma sätt som Azure Automation hjälper dig att hantera PowerShell-skript, kan du även hantera DSC-konfigurationer.
Mer information om fördelarna med att använda Azure Automation tillstånds konfiguration finns i [Översikt över Azure Automation tillstånds konfiguration](automation-dsc-overview.md).

Azure Automation tillstånds konfiguration kan användas för att hantera flera olika datorer:

- Virtuella Azure-datorer
- Virtuella Azure-datorer (klassisk)
- Amazon Web Services (AWS) EC2-instanser
- Fysiska/virtuella Windows-datorer lokalt eller i ett annat moln än Azure/AWS
- Fysiska/virtuella Linux-datorer lokalt, i Azure eller i ett annat moln än Azure

Om du inte är redo att hantera dator konfiguration från molnet kan Azure Automation tillstånds konfiguration också användas som en slut punkt för endast rapporter.
På så sätt kan du ange (push)-konfigurationer via DSC och Visa rapporterings information i Azure Automation.

> [!NOTE]
> Hantering av virtuella Azure-datorer med tillstånds konfiguration ingår utan extra kostnad om det installerade DSC-tillägget för virtuell dator är större än 2,70. Mer information finns på [**sidan med automatiserings priser**](https://azure.microsoft.com/pricing/details/automation/) .

I följande avsnitt beskrivs hur du kan publicera varje typ av dator för att Azure Automation tillstånds konfiguration.

## <a name="azure-virtual-machines"></a>Virtuella Azure-datorer

Med Azure Automation tillstånds konfiguration kan du enkelt publicera virtuella Azure-datorer för konfigurations hantering med hjälp av antingen Azure Portal, Azure Resource Manager mallar eller PowerShell. Under huven, och utan att en administratör måste fjärrans luta till den virtuella datorn, registrerar det önskade tillstånds konfigurations tillägget för Azure VM den virtuella datorn med Azure Automation tillstånds konfiguration.
Eftersom tillägget för Desired State Configuration för Azure VM körs asynkront, följer steg för att följa förloppet eller fel söknings funktionen finns i följande avsnitt om hur du [**felsöker Azure virtuell dator onboarding**](#troubleshooting-azure-virtual-machine-onboarding) .

### <a name="azure-portal"></a>Azure Portal

I [Azure Portal](https://portal.azure.com/)navigerar du till det Azure Automation konto där du vill publicera virtuella datorer. På sidan tillstånds konfiguration och fliken **noder** klickar du på **+ Lägg till**.

Välj en virtuell Azure-dator att publicera.

Klicka på **Anslut**om datorn inte har önskat PowerShell-tillägg för önskat tillstånd installerat och energi läget körs.

Under **registrering**anger du de [lokala PowerShell-Configuration Manager värdena](/powershell/dsc/managing-nodes/metaconfig) som krävs för ditt användnings fall och eventuellt en noduppsättning som ska tilldelas den virtuella datorn.

![Onboarding](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Virtuella Azure-datorer kan distribueras och integreras i Azure Automation tillstånds konfiguration via Azure Resource Manager mallar. Se [Server som hanteras av önskad tillstånds konfigurations tjänst](https://azure.microsoft.com/resources/templates/101-automation-configuration/) för en exempel-mall som registrerar en befintlig virtuell dator för att Azure Automation tillstånds konfiguration.
Om du hanterar en skalnings uppsättning för virtuella datorer, se exempel mal len [VM Scale set-konfiguration som hanteras av Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>PowerShell

[Register-AzAutomationDscNode-](/powershell/module/az.automation/register-azautomationdscnode) cmdlet: en kan användas för att publicera virtuella datorer i Azure Portal via PowerShell.

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Registrera virtuella datorer i Azure-prenumerationer

Det bästa sättet att registrera virtuella datorer från andra Azure-prenumerationer är att använda DSC-tillägget i en mall för Azure Resource Manager distribution.
Exempel finns i [önskat tillstånds konfigurations tillägg med Azure Resource Manager mallar](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).
För att hitta registrerings nyckeln och registrerings-URL: en som ska användas som parametrar i mallen, se följande avsnitt för [**säker registrering**](#secure-registration) .

## <a name="amazon-web-services-aws-virtual-machines"></a>Amazon Web Services (AWS) virtuella datorer

Du kan enkelt publicera Amazon Web Services virtuella datorer för konfigurations hantering genom att Azure Automation tillstånds konfiguration med hjälp av AWS DSC Toolkit. Du kan lära dig mer om verktygen [här](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Fysiska/virtuella Windows-datorer lokalt eller i ett annat moln än Azure/AWS

Windows-servrar som körs lokalt eller i andra moln miljöer kan också registreras för att Azure Automation tillstånds konfiguration, så länge de har [utgående åtkomst till Azure](automation-dsc-overview.md#network-planning):

1. Se till att den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) är installerad på de datorer som du vill använda för att Azure Automation tillstånds konfiguration.
1. Följ anvisningarna i följande avsnitt när du [**genererar DSC-metaconfigurations**](#generating-dsc-metaconfigurations) för att skapa en mapp som innehåller den nödvändiga DSC-metaconfigurations.
1. Använd fjärranslutna PowerShell DSC-metaconfiguration till de datorer som du vill publicera. **Datorn som det här kommandot körs från måste ha den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) installerat**:

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Om du inte kan använda PowerShell DSC-metaconfigurations via fjärr anslutning kopierar du mappen metaconfigurations från steg 2 till varje dator som ska publiceras. Anropa sedan **set-DscLocalConfigurationManager** lokalt på varje dator som ska publiceras.
1. Använd Azure Portal-eller cmdletarna för att kontrol lera att de datorer som ska publiceras nu visas som de noder som är registrerade i ditt Azure Automation-konto.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Fysiska/virtuella Linux-datorer lokalt eller i ett annat moln än Azure

Linux-servrar som körs lokalt eller i andra moln miljöer kan också registreras för att Azure Automation tillstånds konfiguration, så länge de har [utgående åtkomst till Azure](automation-dsc-overview.md#network-planning):

1. Se till att den senaste versionen av [PowerShell Desired State Configuration för Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) är installerad på de datorer som du vill publicera för att Azure Automation tillstånds konfiguration.
1. Om de [lokala POWERSHELL DSC-Configuration Manager](/powershell/dsc/metaconfig4) standardvärden matchar ditt användnings fall och du vill publicera datorer så att de **båda** hämtar från och rapporterar till Azure Automation tillstånds konfiguration:

   - På varje Linux-dator som ska integreras med Azure Automation tillstånds konfiguration använder `Register.py` du för att publicera med de lokala Configuration Manager-standardvärdena för PowerShell:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Information om registrerings nyckeln och registrerings-URL: en för ditt Automation-konto finns i följande avsnitt om [**säker registrering**](#secure-registration) .

     Om den lokala PowerShell DSC-Configuration Manager standardvärden **inte** matchar ditt användnings fall, eller om du vill publicera datorer så att de endast rapporterar till Azure Automation tillstånds konfiguration, följer du steg 3-6. Annars fortsätter du direkt till steg 6.

1. Följ anvisningarna i följande avsnitt om att skapa [**DSC-metaconfigurations**](#generating-dsc-metaconfigurations) för att skapa en mapp som innehåller nödvändiga DSC-metaconfigurations.
1. Använd fjärranslutna PowerShell DSC-metaconfiguration till de datorer som du vill publicera:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Datorn som det här kommandot körs från måste ha den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) installerat.

1. Om du inte kan använda PowerShell DSC-metaconfigurations kan du kopiera metaconfiguration som motsvarar den datorn från mappen i steg 5 till Linux-datorn. Anropa `SetDscLocalConfigurationManager.py` sedan lokalt på varje Linux-dator som du vill publicera för att Azure Automation tillstånds konfiguration:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Använd Azure Portal-eller cmdletarna för att kontrol lera att de datorer som ska publiceras nu visas som DSC-noder registrerade i ditt Azure Automation-konto.

## <a name="generating-dsc-metaconfigurations"></a>Genererar DSC-metaconfigurations

En [DSC-metaconfiguration](/powershell/dsc/metaconfig) kan genereras som talar om för DSC-agenten att hämta från och/eller rapportera till Azure Automation tillstånds konfiguration för att allmänt publicera alla datorer som Azure Automation tillstånds konfiguration DSC-metaconfigurations för konfiguration av Azure Automation tillstånd kan genereras med hjälp av en PowerShell DSC-konfiguration eller Azure Automation PowerShell-cmdletar.

> [!NOTE]
> DSC-metaconfigurations innehåller de hemligheter som krävs för att publicera en dator till ett Automation-konto för hantering. Se till att skydda alla DSC-metaconfigurations som du skapar eller ta bort dem efter användning.

### <a name="using-a-dsc-configuration"></a>Använda en DSC-konfiguration

1. Öppna VSCode (eller din favorit redigerare) som administratör på en dator i din lokala miljö. Datorn måste ha den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) installerad.
1. Kopiera följande skript lokalt. Det här skriptet innehåller en PowerShell DSC-konfiguration för att skapa metaconfigurations och ett kommando för att starta metaconfiguration skapas.

> [!NOTE]
> Konfigurations namn för State Configuration-noden är Skift läges känsliga i portalen. Om SKIFT läget inte stämmer visas inte noden på fliken **noder** .

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

1. Fyll i registrerings nyckeln och URL: en för ditt Automation-konto, samt namnen på de datorer som ska publiceras. Alla andra parametrar är valfria. Information om registrerings nyckeln och registrerings-URL: en för ditt Automation-konto finns i följande avsnitt om [**säker registrering**](#secure-registration) .
1. Om du vill att datorerna ska rapportera DSC-statuskoden till Azure Automation tillstånds konfiguration, men inte hämta konfigurations-eller PowerShell-moduler, anger du parametern **ReportOnly** till true.
1. Kör skriptet. Nu bör du ha en mapp med namnet **DscMetaConfigs** i din arbets katalog som innehåller PowerShell DSC-metaconfigurations för de datorer som ska publiceras (som administratör):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Använda Azure Automation-cmdletar

Om de lokala PowerShell DSC-Configuration Manager standardvärden matchar ditt användnings fall och du vill publicera datorer så att de båda hämtar från och rapporterar till Azure Automation tillstånds konfiguration, ger Azure Automation-cmdletar en förenklad metod för att generera DSC-metaconfigurations krävs:

1. Öppna PowerShell-konsolen eller VSCode som administratör på en dator i din lokala miljö.
2. Anslut till Azure Resource Manager med`Connect-AzAccount`
3. Ladda ned PowerShell DSC-metaconfigurations för de datorer som du vill publicera från det Automation-konto som du vill använda för att publicera noder:

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Nu bör du ha en mapp med namnet ***DscMetaConfigs***, som innehåller PowerShell DSC-metaconfigurations för de datorer som ska publiceras (som administratör):

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Säker registrering

Datorer kan integreras på ett Azure Automation-konto via WMF 5 DSC registrerings protokoll, vilket gör att en DSC-nod kan autentisera till en PowerShell DSC-pull eller rapporterings Server (inklusive Azure Automation tillstånds konfiguration). Noden registreras på servern vid en registrerings- **URL**och autentiseras med hjälp av en **registrerings nyckel**. Under registreringen förhandlar DSC-noden och DSC-pull/Reporting-servern ett unikt certifikat för den här noden som ska användas för autentisering till Server efter registreringen. Den här processen förhindrar att underställda noder personifierar varandra, till exempel om en nod komprometteras och att den fungerar skadligt. Efter registreringen används inte registrerings nyckeln för autentisering igen och tas bort från noden.

Du kan hämta den information som krävs för registrerings protokollet för tillstånds konfiguration från **nycklar** under **konto inställningar** i Azure Portal. Öppna det här bladet genom att klicka på nyckel  ikonen på panelen Essentials för Automation-kontot.

![Azure Automation-nycklar och URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Registrerings-URL är URL-fältet på bladet hantera nycklar.
- Registrerings nyckeln är den primära åtkomst nyckeln eller sekundär åtkomst nyckel på bladet hantera nycklar. Du kan använda någon av nycklarna.

För ökad säkerhet kan de primära och sekundära åtkomst nycklarna för ett Automation-konto återskapas när som helst (på sidan **Hantera nycklar** ) för att förhindra framtida Node-registreringar med hjälp av tidigare nycklar.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Felsöka onboarding av virtuella Azure-datorer

Med Azure Automation tillstånds konfiguration kan du enkelt publicera virtuella Azure Windows-datorer för konfigurations hantering. Under huven används det önskade tillstånds konfigurations tillägget för Azure VM för att registrera den virtuella datorn med Azure Automation tillstånds konfiguration. Eftersom det önskade tillstånds konfigurations tillägget för Azure VM körs asynkront, kan det vara viktigt att spåra förloppet och felsöka körningen.

> [!NOTE]
> Alla metoder för att registrera en virtuell Azure Windows-dator för att Azure Automation tillstånds konfiguration som använder det önskade tillägget för tillstånds konfiguration i Azure VM kan ta upp till en timme innan noden visas som registrerad i Azure Automation. Detta beror på installationen av Windows Management Framework 5,0 på den virtuella datorn med tillägget för Azure VM DSC, vilket krävs för att publicera den virtuella datorn i Azure Automation tillstånds konfiguration.

Om du vill felsöka eller Visa status för tillägget Azure VM Desired State Configuration går du till Azure Portal navigera till den virtuella dator som har registrerats och klickar sedan på **tillägg** under **Inställningar**. Klicka sedan på **DSC** eller **DSCForLinux** beroende på vilket operativ system du har. Du kan klicka på **Visa detaljerad status**om du vill ha mer information.

## <a name="certificate-expiration-and-reregistration"></a>Certifikatets giltighets tid och omregistrering

När du har registrerat en dator som en DSC-nod i Azure Automation tillstånds konfiguration, finns det ett antal orsaker till varför du kan behöva registrera om noden i framtiden:

- Efter registreringen förhandlar varje nod automatiskt ett unikt certifikat för autentisering som upphör att gälla efter ett år. För närvarande kan PowerShell DSC-protokollet för registrering inte automatiskt förnya certifikat när de snart upphör att gälla, så du måste registrera om noderna efter ett års tid. Innan du registrerar igen kontrollerar du att varje nod kör Windows Management Framework 5,0 RTM. Om autentiseringen av en nods certifikat upphör att gälla, och noden inte registreras igen, kan inte noden kommunicera med Azure Automation och har marker ATS som "svarar inte". Omregistreringen utförde 90 dagar eller mindre från certifikatets förfallo tid, eller när som helst efter det att certifikatet upphör att gälla, leder det till att ett nytt certifikat skapas och används.
- Om du vill ändra de [lokala PowerShell-Configuration Manager värdena](/powershell/dsc/metaconfig4) som angavs under den inledande registreringen av noden, till exempel ConfigurationMode. För närvarande kan dessa DSC-agenttjänsten bara ändras genom omregistrering. Det enda undantaget är den Node-konfiguration som är tilldelad noden. Detta kan ändras i Azure Automation DSC direkt.

Omregistrering kan utföras på samma sätt som du registrerade noden från början med någon av de onboarding-metoder som beskrivs i det här dokumentet. Du behöver inte avregistrera en nod från Azure Automation tillstånds konfiguration innan du registrerar om den.

## <a name="next-steps"></a>Nästa steg

- För att komma igång, se [komma igång med konfiguration av Azure Automation tillstånd](automation-dsc-getting-started.md)
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till mål noder finns i [kompilera konfigurationer i Azure Automation tillstånds konfiguration](automation-dsc-compile.md)
- Referens för PowerShell-cmdlet finns i [Azure Automation cmdlets för tillstånds konfiguration](/powershell/module/az.automation#automation)
- För pris information, se [priser för Azure Automation tillstånds konfiguration](https://azure.microsoft.com/pricing/details/automation/)
- Om du vill se ett exempel på hur du använder Azure Automation tillstånds konfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med Azure Automation tillstånds konfiguration och choklad](automation-dsc-cd-chocolatey.md)
