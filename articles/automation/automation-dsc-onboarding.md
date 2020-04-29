---
title: Onboarding-datorer för hantering genom Azure Automation tillstånds konfiguration
description: Konfigurera datorer för hantering med Azure Automation tillstånds konfiguration
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: d4e008a0bd43f10b01d78a1c388f1ca6fee983ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457747"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Onboarding-datorer för hantering genom Azure Automation tillstånds konfiguration

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Varför ska jag hantera datorer med Azure Automation tillstånds konfiguration?

Azure Automation tillstånds konfiguration är en konfigurations hanterings tjänst för DSC-noder (Desired State Configuration) i alla moln eller lokala data Center. Den används i Azure Portal genom att välja **tillstånds konfiguration (DSC)** under **konfigurations hantering**. 

Den här tjänsten möjliggör skalbarhet på tusentals datorer snabbt och enkelt från en central, säker plats. Du kan enkelt publicera datorer, tilldela dem deklarativ konfigurationer och Visa rapporter som visar varje dators kompatibilitet med önskat tillstånd som du anger.

Azure Automation tillstånds konfigurations tjänsten är till DSC vad Azure Automation runbooks är till PowerShell-skript. Med andra ord, på samma sätt som Azure Automation hjälper dig att hantera PowerShell-skript, kan du även hantera DSC-konfigurationer. Mer information om fördelarna med att använda Azure Automation tillstånds konfiguration finns i [Översikt över Azure Automation tillstånds konfiguration](automation-dsc-overview.md).

Azure Automation tillstånds konfiguration kan användas för att hantera flera olika datorer:

- Virtuella Azure-datorer
- Virtuella Azure-datorer (klassisk)
- Fysiska/virtuella Windows-datorer lokalt eller i ett annat moln än Azure (inklusive AWS EC2-instanser)
- Fysiska/virtuella Linux-datorer lokalt, i Azure eller i ett annat moln än Azure

Om du inte är redo att hantera dator konfigurationen från molnet kan du använda Azure Automation tillstånds konfiguration som en slut punkt för endast rapporter. Med den här funktionen kan du ange (push) konfigurationer via DSC och Visa rapporterings information i Azure Automation.

> [!NOTE]
> Hantering av virtuella Azure-datorer med Azure Automation tillstånds konfiguration ingår utan extra kostnad om det installerade tillägget för Azure VM Desired State Configuration är större än 2,70. Mer information finns på [**sidan med automatiserings priser**](https://azure.microsoft.com/pricing/details/automation/).

Följande avsnitt i den här artikeln beskriver hur du kan integrera de datorer som anges ovan för att Azure Automation tillstånds konfiguration.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="onboarding-azure-vms"></a>Onboarding av virtuella Azure-datorer

Med Azure Automation tillstånds konfiguration kan du enkelt publicera virtuella Azure-datorer för konfigurations hantering med hjälp av Azure Portal, Azure Resource Manager mallar eller PowerShell. Under huven, och utan att en administratör måste fjärrans luta till en virtuell dator, registreras den virtuella datorn med Azure Automation tillstånds konfiguration under det önskade tillstånds konfigurations tillägget i Azure Eftersom Azure-tillägget körs asynkront, följer steg för att följa förloppet eller fel söknings funktionen finns i avsnittet [Felsöka Azure Virtual Machine onboarding](#troubleshooting-azure-virtual-machine-onboarding) i den här artikeln.

> [!NOTE]
>Om du distribuerar DSC till en Linux-nod används mappen **katalogen/tmp** . Moduler som `nxautomation` tillfälligt hämtas för verifiering innan de installeras på lämpliga platser. För att se till att modulerna installeras korrekt behöver Log Analytics agent för Linux Läs-/Skriv behörighet för **katalogen/tmp** -mappen.<br><br>
>Log Analytics agenten för Linux körs som `omsagent` användaren. Om du vill ge `omsagent` användaren >Skriv behörighet kör du kommandot. `setfacl -m u:omsagent:rwx /tmp`

### <a name="onboard-a-vm-using-azure-portal"></a>Publicera en virtuell dator med hjälp av Azure Portal

Publicera en virtuell Azure-dator för att Azure Automation tillstånds konfiguration via [Azure Portal](https://portal.azure.com/):

1. Navigera till det Azure Automation konto där du vill publicera virtuella datorer. 

2. På sidan tillstånds konfiguration väljer du fliken **noder** och klickar sedan på **Lägg till**.

3. Välj en virtuell dator som ska publiceras.

4. Klicka på **Anslut**om datorn inte har önskat PowerShell-tillägg för önskat tillstånd installerat och energi läget körs.

5. Under **registrering**anger du de [lokala PowerShell DSC-Configuration Manager värden](/powershell/scripting/dsc/managing-nodes/metaConfig) som krävs för ditt användnings fall. Alternativt kan du ange en konfiguration för noden som ska tilldelas den virtuella datorn.

![Onboarding](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>Publicera en virtuell dator med Azure Resource Manager mallar

Du kan distribuera och publicera en virtuell dator för att Azure Automation tillstånds konfiguration med Azure Resource Manager-mallar. Se [Server som hanteras av önskad tillstånds konfigurations tjänst](https://azure.microsoft.com/resources/templates/101-automation-configuration/) för en exempel-mall som registrerar en befintlig virtuell dator för att Azure Automation tillstånds konfiguration. Om du hanterar en skalnings uppsättning för virtuella datorer, se exempel mal len i [konfigurationen för skalnings uppsättningar för virtuella datorer som hanteras av Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-machines-using-powershell"></a>Publicera datorer med PowerShell

Du kan använda cmdleten [register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) i PowerShell för att publicera virtuella datorer för att Azure Automation tillstånds konfiguration. 

> [!NOTE]
>`Register-AzAutomationDscNode` Cmdleten implementeras för närvarande endast för datorer som kör Windows, eftersom den utlöser bara Windows-tillägget.

### <a name="register-vms-across-azure-subscriptions"></a>Registrera virtuella datorer i Azure-prenumerationer

Det bästa sättet att registrera virtuella datorer från andra Azure-prenumerationer är att använda DSC-tillägget i en mall för Azure Resource Manager distribution. Exempel finns i [önskat tillstånds konfigurations tillägg med Azure Resource Manager mallar](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Information om registrerings nyckeln och registrerings-URL: en som ska användas som parametrar i mallen finns i avsnittet om att [registrera på ett säkert sätt med registrering](#onboarding-securely-using-registration) i den här artikeln.

## <a name="onboarding-physicalvirtual-windows-machines"></a>Onboarding av fysiska/virtuella Windows-datorer

Du kan publicera Windows-servrar som körs lokalt eller i andra moln miljöer (inklusive AWS EC2-instanser) för att Azure Automation tillstånds konfiguration. Servrarna måste ha [utgående åtkomst till Azure](automation-dsc-overview.md#network-planning).

1. Kontrol lera att den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) är installerad på datorerna för att Azure Automation tillstånds konfiguration. Dessutom måste WMF 5 vara installerat på den dator som du använder för onboarding-åtgärden.
1. Följ anvisningarna i avsnittet [generera DSC-metaconfigurations](#generating-dsc-metaconfigurations) för att skapa en mapp som innehåller den nödvändiga DSC-metaconfigurations. 
1. Använd följande cmdlet för att använda PowerShell DSC-metaconfigurations via fjärr anslutning till de datorer som du vill publicera. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Om du inte kan använda PowerShell DSC-metaconfigurations via fjärr anslutning kopierar du mappen **metaconfigurations** till de datorer som du registrerar. Lägg sedan till kod för att anropa [set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) lokalt på datorerna.
1. Använd Azure Portal-eller cmdletarna för att kontrol lera att de datorer som ska publiceras visas som noder som är registrerade i ditt Azure Automation-konto.

## <a name="onboarding-physicalvirtual-linux-machines"></a>Onboarding av fysiska/virtuella Linux-datorer

Du kan publicera Linux-servrar som körs lokalt eller i andra moln miljöer för att Azure Automation tillstånds konfiguration. Servrarna måste ha [utgående åtkomst till Azure](automation-dsc-overview.md#network-planning).

1. Kontrol lera att den senaste versionen av [PowerShell Desired State Configuration för Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) är installerad på datorerna för att Azure Automation tillstånds konfiguration.
2. Om de [lokala POWERSHELL DSC-Configuration Manager standardvärden](/powershell/scripting/dsc/managing-nodes/metaConfig4) matchar ditt användnings fall och du vill publicera datorer så att de båda hämtar och rapporterar till Azure Automation tillstånds konfiguration:

   - Använd `Register.py` för att publicera med hjälp av de lokala PowerShell-Configuration Manager standardvärdena för alla Linux-datorer som ska integreras i Azure Automation tillstånds konfiguration.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Information om registrerings nyckeln och registrerings-URL: en för ditt Automation-konto finns i avsnittet om [registrering på ett säkert sätt med registrering](#onboarding-securely-using-registration) i den här artikeln.

3. Om LCM-standardvärdena (PowerShell DSC Local Configuration Manager) inte matchar ditt användnings fall, eller om du vill publicera datorer som endast rapporterar till Azure Automation tillstånds konfiguration, följer du steg 4-7. Annars fortsätter du direkt till steg 7.

4. Följ anvisningarna i avsnittet [generera DSC-metaconfigurations](#generating-dsc-metaconfigurations) för att skapa en mapp som innehåller de DSC-metaconfigurations som krävs.

5. Kontrol lera att den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) är installerad på den dator som används för onboarding.

6. Lägg till kod enligt följande om du vill använda PowerShell DSC-metaconfigurations via fjärr anslutning till de datorer som du vill publicera.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Om du inte kan använda PowerShell DSC-metaconfigurations via fjärr anslutning kopierar du metaconfigurations som motsvarar fjärrdatorerna från den mapp som beskrivs i steg 4 till Linux-datorerna.

8. Lägg till kod för `Set-DscLocalConfigurationManager.py` att anropa lokalt på varje Linux-dator för att integrera Azure Automation tillstånds konfiguration.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Använd Azure Portal-eller cmdletarna för att se till att de datorer som ska publiceras nu visas som DSC-noder registrerade i ditt Azure Automation-konto.

## <a name="generating-dsc-metaconfigurations"></a>Genererar DSC-metaconfigurations

Du kan skapa en [DSC-metaconfiguration](/powershell/scripting/dsc/managing-nodes/metaConfig)för att publicera vilken dator som helst som ska Azure Automation tillstånds konfiguration. Den här konfigurationen instruerar DSC-agenten att hämta från och/eller rapportera till Azure Automation tillstånds konfiguration. Du kan generera en DSC-metaconfiguration för konfiguration av Azure Automation tillstånd med antingen en PowerShell DSC-konfiguration eller Azure Automation PowerShell-cmdletar.

> [!NOTE]
> DSC-metaconfigurations innehåller de hemligheter som krävs för att publicera en dator till ett Automation-konto för hantering. Se till att skydda alla DSC-metaconfigurations som du skapar eller ta bort dem efter användning.

Proxy-stöd för metaconfigurations styrs av LCM, som är Windows PowerShell DSC-motorn. LCM körs på alla målnoden och ansvarar för att anropa konfigurations resurserna som ingår i ett DSC metaconfiguration-skript. Du kan inkludera stöd för proxy i en metaconfiguration genom att inkludera definitioner av proxy `ConfigurationRepositoryWeb`-URL: en och autentiseringsuppgifter för proxyn `ResourceRepositoryWeb`enligt vad `ReportServerWeb` som behövs i, och. Se [Konfigurera den lokala Configuration Manager](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Generera DSC-metaconfigurations med en DSC-konfiguration

1. Öppna VSCode (eller din favorit redigerare) som administratör på en dator i din lokala miljö. Datorn måste ha den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) installerad.
1. Kopiera följande skript lokalt. Det här skriptet innehåller en PowerShell DSC-konfiguration för att skapa metaconfigurations och ett kommando för att starta metaconfiguration skapas.

    > [!NOTE]
    > Konfigurations namn för State Configuration-noden är Skift läges känsliga i Azure Portal. Om skiftet inte stämmer visas inte noden på fliken **noder** .

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

1. Fyll i registrerings nyckeln och URL: en för ditt Automation-konto, samt namnen på de datorer som ska publiceras. Alla andra parametrar är valfria. Information om registrerings nyckeln och registrerings-URL: en för ditt Automation-konto finns i avsnittet om att [registrera på ett säkert sätt med registrering](#onboarding-securely-using-registration) .

1. Om du vill att datorerna ska rapportera DSC-statuskoden till Azure Automation tillstånds konfiguration, men inte hämta konfiguration eller PowerShell-moduler `ReportOnly` , anger du parametern till true.

1. Om `ReportOnly` inte har angetts rapporterar DATORerna DSC-statusinformation till Azure Automation tillstånds konfiguration och hämtnings konfiguration eller PowerShell-moduler. Ange parametrar enligt dessa `ConfigurationRepositoryWeb`i- `ResourceRepositoryWeb`,- `ReportServerWeb` och-block.

1. Kör skriptet. Nu bör du ha en arbetskatalog-mapp med namnet **DscMetaConfigs**, som innehåller PowerShell DSC-metaconfigurations för de datorer som ska publiceras (som administratör).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Generera DSC-metaconfigurations med hjälp av Azure Automation-cmdletar

Om PowerShell DSC LCM-standardvärden stämmer överens med ditt användnings fall och du vill att datorer ska kunna hämtas från och rapporteras till Azure Automation tillstånds konfiguration, kan du generera den nödvändiga DSC-metaconfigurations mer enkelt genom att använda Azure Automation-cmdletar.

1. Öppna PowerShell-konsolen eller VSCode som administratör på en dator i din lokala miljö.
2. Anslut till Azure Resource Manager med [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
3. Ladda ned PowerShell DSC-metaconfigurations för de datorer som du vill publicera från Automation-kontot där du konfigurerar noder.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Nu bör du ha en **DscMetaConfigs** -mapp som innehåller PowerShell DSC-metaconfigurations för de datorer som ska publiceras (som administratör).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>Onboarding på ett säkert sätt med registrering

Datorer kan registreras på ett säkert sätt till ett Azure Automation konto via WMF 5 DSC registrerings protokollet. Med det här protokollet kan en DSC-nod autentisera till en PowerShell DSC-pull eller rapport Server, inklusive Azure Automation tillstånds konfiguration. Noden registreras på servern vid registrerings-URL: en och autentiseras med hjälp av en registrerings nyckel. Under registreringen förhandlar DSC-noden och DSC pull/Report Server ett unikt certifikat för noden som ska användas för autentisering till Server efter registreringen. Den här processen förhindrar att inaktiva noder personifierar varandra, t. ex. om en nod komprometteras och inte fungerar skadligt. Efter registreringen används inte registrerings nyckeln för autentisering igen och tas bort från noden.

Du kan hämta den information som krävs för registrerings protokollet för tillstånds konfiguration från **nycklar** under **konto inställningar** i Azure Portal. 

![Azure Automation-nycklar och URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Registrerings-URL är URL-fältet på sidan nycklar.
- Registrerings nyckeln är värdet för fältet **primär åtkomst nyckel** eller fältet **sekundär åtkomst nyckel** på sidan nycklar. Du kan använda någon av nycklarna.

För ökad säkerhet kan du när som helst återskapa de primära och sekundära åtkomst nycklarna för ett Automation-konto när som helst på sidan nycklar. När nyckeln återskapas förhindras framtida registreringar av noder från att använda tidigare nycklar.

## <a name="re-registering-a-node"></a>Registrera en nod igen

När du har registrerat en dator som en DSC-nod i Azure Automation tillstånds konfiguration finns det flera orsaker till varför du kan behöva registrera noden i framtiden igen.

- **Certifikat förnyelse.** För versioner av Windows Server före Windows Server 2019 förhandlar varje nod automatiskt ett unikt certifikat för autentisering som upphör att gälla efter ett år. Om ett certifikat upphör att gälla utan att förnyas, kan noden inte kommunicera med Azure Automation och har `Unresponsive`marker ATS. För närvarande kan PowerShell DSC-protokollet för registrering inte automatiskt förnya certifikat när de snart upphör att gälla och du måste registrera om noderna efter ett års tid. Innan du registrerar igen måste du kontrol lera att varje nod kör WMF 5 RTM. 

    Omregistreringen utförde 90 dagar eller mindre från certifikatets förfallo tid, eller när som helst efter förfallo tiden för certifikatet, resulterar i att ett nytt certifikat skapas och används. En lösning på det här problemet ingår i Windows Server 2019 och senare.

- **Ändringar av DSC LCM-värden.** Du kan behöva ändra [POWERSHELL DSC-LCM värden](/powershell/scripting/dsc/managing-nodes/metaConfig4) som anges under den inledande registreringen av noden, till exempel `ConfigurationMode`. För närvarande kan du bara ändra dessa DSC-Gent värden genom omregistrering. Det enda undantaget är det konfigurations värde för noden som tilldelats noden. Du kan ändra detta i Azure Automation DSC direkt.

Du kan registrera om en nod precis som du registrerade noden från början med någon av de onboarding-metoder som beskrivs i det här dokumentet. Du behöver inte avregistrera en nod från Azure Automation tillstånds konfiguration innan du registrerar om den.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Felsöka onboarding av virtuella Azure-datorer

Med Azure Automation tillstånds konfiguration kan du enkelt publicera virtuella Azure Windows-datorer för konfigurations hantering. Under huven används det önskade tillstånds konfigurations tillägget för Azure VM för att registrera den virtuella datorn med Azure Automation tillstånds konfiguration. Eftersom det önskade tillstånds konfigurations tillägget för Azure VM körs asynkront, kan det vara viktigt att spåra dess förlopp och felsöka körningen.

> [!NOTE]
> Alla metoder för att registrera en virtuell Azure Windows-dator för att Azure Automation tillstånds konfiguration som använder det önskade tillägget för tillstånds konfiguration i Azure VM kan ta upp till en timme för Azure Automation för att visa den som registrerad. Den här fördröjningen beror på installationen av WMF 5 på den virtuella datorn med tillägget för önskad tillstånds konfiguration i Azure VM, vilket krävs för att publicera den virtuella datorn i Azure Automation tillstånds konfiguration.

Felsöka eller Visa status för tillägget Azure VM Desired State Configuration:

1. I Azure Portal navigerar du till den virtuella dator som publicerats.
2. Klicka på **tillägg** under **Inställningar**. 
3. Välj **DSC** eller **DSCForLinux**, beroende på vilket operativ system du har. 
4. Du kan klicka på **Visa detaljerad status**om du vill ha mer information.

Mer information om fel sökning finns i [fel söknings problem med Azure Automation önskad tillstånds konfiguration (DSC)](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång finns i [komma igång med konfiguration av Azure Automation tillstånd](automation-dsc-getting-started.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till mål noder finns i [kompilera konfigurationer i Azure Automation tillstånds konfiguration](automation-dsc-compile.md).
- En PowerShell-cmdlet-referens finns i [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Pris information finns i pris information för [Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Ett exempel på hur du använder Azure Automation tillstånds konfiguration i en pipeline för kontinuerlig distribution finns i [användnings exempel: kontinuerlig distribution till virtuella datorer med Azure Automation tillstånds konfiguration och choklad](automation-dsc-cd-chocolatey.md).
