---
title: Introduktionsdatorer för hantering av Azure Automation State Configuration
description: Konfigurera datorer för hantering med Azure Automation State Configuration
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: 0bf70b73098427847c73b4dd962d56d44fe6ee2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283216"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Introduktionsdatorer för hantering av Azure Automation State Configuration

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Varför hantera datorer med Azure Automation State Configuration?

Azure Automation State Configuration är en konfigurationshanteringstjänst för DSC-noder (Desired State Configuration) i alla moln eller lokala datacenter. Den nås i Azure-portalen genom att välja **Tillståndskonfiguration (DSC)** under **Configuration Management**. 

Den här tjänsten möjliggör skalbarhet över tusentals datorer snabbt och enkelt från en central, säker plats. Du kan enkelt gå ombord på datorer, tilldela dem deklarativa konfigurationer och visa rapporter som visar varje dators överensstämmelse med önskat tillstånd som du anger.

Azure Automation State Configuration-tjänsten är till DSC vilka Azure Automation-runbooks är för PowerShell-skript. Med andra ord, på samma sätt som Azure Automation hjälper dig att hantera PowerShell-skript, hjälper det dig också att hantera DSC-konfigurationer. Mer information om fördelarna med att använda Azure Automation State Configuration finns i [översikt över Azure Automation State Configuration](automation-dsc-overview.md).

Azure Automation State Configuration kan användas för att hantera en mängd olika datorer:

- Virtuella Azure-datorer
- Virtuella Azure-datorer (klassisk)
- Fysiska/virtuella Windows-datorer lokalt eller i ett annat moln än Azure (inklusive AWS EC2-instanser)
- Fysiska/virtuella Linux-datorer lokalt, i Azure eller i ett annat moln än Azure

Om du inte är redo att hantera datorkonfiguration från molnet kan du använda Azure Automation State Configuration som en slutpunkt med endast rapporter. Med den här funktionen kan du ställa in (push)-konfigurationer via DSC och visa rapporteringsinformation i Azure Automation.

> [!NOTE]
> Hantering av virtuella Azure-datorer med Azure Automation State Configuration ingår utan extra kostnad om den installerade Azure VM Desired State Configuration-tilläggsversionen är större än 2,70. Mer information finns på [**prissidan för Automation**](https://azure.microsoft.com/pricing/details/automation/).

I följande avsnitt i den här artikeln beskrivs hur du kan gå in på datorerna ovan till Azure Automation State Configuration.

## <a name="onboarding-azure-vms"></a>Introduktion till virtuella Azure-datorer

Med Azure Automation State Configuration kan du enkelt gå in på virtuella Azure-datorer för konfigurationshantering med hjälp av Azure-portalen, Azure Resource Manager-mallarna eller PowerShell. Under huven och utan att en administratör behöver fjärrstyra till en virtuell dator registrerar azure vm-tillägget För önskad tillståndskonfiguration den virtuella datorn med Azure Automation State Configuration. Eftersom Azure-tillägget körs asynkront finns steg för att spåra dess förlopp eller felsöka det i avsnittet Felsökning av [Azure-datorns introduktion i](#troubleshooting-azure-virtual-machine-onboarding) den här artikeln.

> [!NOTE]
>Om du distribuerar DSC till en Linux-nod används mappen **/tmp.** Moduler som `nxautomation` hämtas tillfälligt för verifiering innan de installeras på lämpliga platser. För att säkerställa att modulerna installeras korrekt behöver Log Analytics-agenten för Linux läs-/skrivbehörighet i mappen **/tmp.**<br><br>
>Log Analytics-agenten `omsagent` för Linux körs som användare. Om du vill bevilja >`omsagent` skrivbehörighet till användaren kör du kommandot `setfacl -m u:omsagent:rwx /tmp`.

### <a name="onboard-a-vm-using-azure-portal"></a>Ombord på en virtuell dator med Azure-portal

Så här går du ombord på en Azure VM-konfiguration till Azure Automation State Configuration via [Azure-portalen:](https://portal.azure.com/)

1. Navigera till Azure Automation-kontot där du kan gå in på virtuella datorer. 

2. På sidan Tillståndskonfiguration väljer du fliken **Noder** och klickar sedan på **Lägg till**.

3. Välj en virtuell dator som ska finnas ombord.

4. Om datorn inte har det önskade tillståndstillägget PowerShell installerat och energiläget körs klickar du på **Anslut**.

5. Under **Registrering**anger du de [PowerShell DSC Local Configuration Manager-värden](/powershell/scripting/dsc/managing-nodes/metaConfig) som krävs för ditt användningsfall. Du kan också ange en nodkonfiguration som ska tilldelas den virtuella datorn.

![onboarding](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>Ombord på en virtuell dator med Azure Resource Manager-mallar

Du kan distribuera och gå in på en virtuell dator till Azure Automation State Configuration med Azure Resource Manager-mallar. Se [Server som hanteras av tjänsten Önskad tillståndskonfiguration](https://azure.microsoft.com/resources/templates/101-automation-configuration/) för en exempelmall som innehåller en befintlig virtuell dator till Azure Automation State Configuration. Om du hanterar en vm-skalningsuppsättning läser du exempelmallen i [VM-skalningsuppsättningskonfiguration som hanteras av Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-machines-using-powershell"></a>Inbyggda maskiner med PowerShell

Du kan använda [cmdleten Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) i PowerShell för att registrera virtuella datorer till Azure Automation State Configuration. 

> [!NOTE]
>Cmdleten `Register-AzAutomationDscNode` implementeras för närvarande endast för maskiner som kör Windows, eftersom det utlöser bara Windows-tillägget.

### <a name="register-vms-across-azure-subscriptions"></a>Registrera virtuella datorer i Azure-prenumerationer

Det bästa sättet att registrera virtuella datorer från andra Azure-prenumerationer är att använda DSC-tillägget i en Azure Resource Manager-distributionsmall. Exempel finns i [tillägget Önskat tillståndskonfiguration med Azure Resource Manager-mallar](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Information om hur du hittar registreringsnyckeln och registrerings-URL:en som ska användas som parametrar i mallen finns i avsnittet [Onboarding på ett säkert sätt med hjälp av](#onboarding-securely-using-registration) registreringsavsnittet i den här artikeln.

## <a name="onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances"></a>Introduktion till fysiska/virtuella Windows-datorer lokalt eller i ett annat moln än Azure (inklusive AWS EC2-instanser)

Du kan vara med på Windows-servrar som körs lokalt eller i andra molnmiljöer till Azure Automation State Configuration. Servrarna måste ha [utgående åtkomst till Azure](automation-dsc-overview.md#network-planning).

1. Kontrollera att den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) är installerad på datorerna för att gå ombord till Azure Automation State Configuration. Dessutom måste WMF 5 installeras på den dator som du använder för introduktionen.
1. Följ anvisningarna i avsnittet [Generera DSC-metakonfigurationer](#generating-dsc-metaconfigurations) för att skapa en mapp som innehåller de DSC-metakonfigurationer som krävs. 
1. Använd följande cmdlet för att fjärrsenderaringarna för PowerShell DSC på de datorer som du vill använda. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Om du inte kan använda PowerShell DSC-metakonfigurationerna på distans kopierar du **mappen metakonfigurationer** till de datorer som du är ombord på. Lägg sedan till `Set-DscLocalConfigurationManager` kod för att ringa lokalt på datorerna.
1. Med hjälp av Azure-portalen eller cmdlets kontrollerar du att datorerna som ska finnas ombord visas som noder för tillståndskonfiguration som är registrerade i ditt Azure Automation-konto.

## <a name="onboarding-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Introduktion till fysiska/virtuella Linux-datorer lokalt eller i ett annat moln än Azure

Du kan vara ombord på Linux-servrar som körs lokalt eller i andra molnmiljöer till Azure Automation State Configuration. Servrarna måste ha [utgående åtkomst till Azure](automation-dsc-overview.md#network-planning).

1. Kontrollera att den senaste versionen av [PowerShell Desired State Configuration för Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) är installerad på datorerna för att gå ombord till Azure Automation State Configuration.
2. Om [Standardinställningarna för PowerShell DSC Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig4) matchar ditt användningsfall och du vill använda datorer ombord så att de både hämtar från och rapporterar till Azure Automation State Configuration:

   - På varje Linux-dator som ska finnas `Register.py` ombord på Azure Automation State Configuration använder du för att använda standardinställningarna PowerShell DSC Local Configuration Manager.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Information om hur du hittar registreringsnyckeln och registreringsadressen för ditt Automation-konto läser du avsnittet [Onboarding på ett säkert sätt med hjälp av](#onboarding-securely-using-registration) registreringsavsnittet om den här artikeln.

3. Om standardvärdena För LCM (PowerShell DSC Local Configuration Manager) inte matchar ditt användningsfall, eller om du vill använda datorer som bara rapporterar till Azure Automation State Configuration, följer du steg 4-7. Annars går du direkt till steg 7.

4. Följ anvisningarna i avsnittet [Generera DSC-metakonfigurationer](#generating-dsc-metaconfigurations) för att skapa en mapp som innehåller de DSC-metakonfigurationer som krävs.

5. Se till att den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) är installerad på den maskin som används för introduktion.

6. Lägg till kod på följande sätt för att fjärrsenderaringarna för PowerShell DSC på de datorer som du vill använda.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Om du inte kan använda PowerShell DSC-metakonfigurationerna på distans kopierar du de metakonfigurationer som motsvarar fjärrdatorerna från mappen som beskrivs i steg 4 på Linux-datorerna.

8. Lägg till `Set-DscLocalConfigurationManager.py` kod för att anropa lokalt på varje Linux-dator för att gå ombord på Azure Automation State Configuration.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Med hjälp av Azure-portalen eller cmdlets, se till att datorerna som ska finnas ombord nu visas som DSC-noder som är registrerade i ditt Azure Automation-konto.

## <a name="generating-dsc-metaconfigurations"></a>Generera DSC-metakonfigurationer

Om du vill gå in på en dator till Azure Automation State Configuration kan du generera en [DSC-metakonfiguration](/powershell/scripting/dsc/managing-nodes/metaConfig). Den här konfigurationen talar om för DSC-agenten att hämta från och/eller rapportera till Azure Automation State Configuration. Du kan generera en DSC-metakonfiguration för Azure Automation State Configuration med antingen en PowerShell DSC-konfiguration eller Azure Automation PowerShell-cmdlets.

> [!NOTE]
> DSC-metakonfigurationer innehåller de hemligheter som behövs för att gå in på en dator till ett Automation-konto för hantering. Se till att skydda alla DSC-metakonfigurationer som du skapar eller ta bort dem efter användning.

Proxystöd för metakonfigurationer styrs av LCM, som är Windows PowerShell DSC-motorn. LCM körs på alla målnoder och ansvarar för att anropa konfigurationsresurserna som ingår i ett DSC-metakonfigurationsskript. Du kan inkludera proxystöd i en metakonfiguration genom att inkludera definitioner av proxy-URL:en och proxyautentiseringsuppgifterna efter behov i , `ConfigurationRepositoryWeb` `ResourceRepositoryWeb`och `ReportServerWeb` blocken. Se [Konfigurera den lokala konfigurationshanteraren](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Generera DSC-metakonfigurationer med hjälp av en DSC-konfiguration

1. Öppna VSCode (eller din favoritredigerare) som administratör på en dator i din lokala miljö. Maskinen måste ha den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) installerad.
1. Kopiera följande skript lokalt. Det här skriptet innehåller en PowerShell DSC-konfiguration för att skapa metakonfigurationer och ett kommando för att starta metakonfigurationen.

    > [!NOTE]
    > Konfigurationsnodkonfigurationsnamn för tillstånd är skiftlägeskänsliga i Azure-portalen. Om ärendet är felaktigt matchat visas inte noden under fliken **Noder.**

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

1. Fyll i registreringsnyckeln och webbadressen för ditt Automation-konto samt namnen på de datorer som ska finnas ombord. Alla andra parametrar är valfria. Information om hur du hittar registreringsnyckeln och registreringsadressen för ditt Automation-konto finns i avsnittet [Onboarding på ett säkert sätt med hjälp av](#onboarding-securely-using-registration) registreringsavsnittet.

1. Om du vill att datorerna ska rapportera DSC-statusinformation till Azure Automation State `ReportOnly` Configuration, men inte pull-konfiguration eller PowerShell-moduler, anger du parametern till true.

1. Om `ReportOnly` inte anges rapporterar datorerna DSC-statusinformation till Azure Automation State Configuration och pull-konfiguration eller PowerShell-moduler. Ange parametrar därefter `ConfigurationRepositoryWeb`i `ResourceRepositoryWeb`, `ReportServerWeb` och blocken.

1. Kör skriptet. Du bör nu ha en arbetskatalogmapp som heter **DscMetaConfigs**, som innehåller PowerShell DSC-metakonfigurationer för datorerna att vara ombord (som administratör).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Generera DSC-metakonfigurationer med Azure Automation-cmdlets

Om PowerShell DSC LCM-standardinställningar matchar ditt användningsfall och du vill registrera datorer för att både hämta från och rapportera till Azure Automation State Configuration, kan du generera de nödvändiga DSC-metakonfigurationerna enklare med hjälp av Azure Automation-cmdlets.

1. Öppna PowerShell-konsolen eller VSCode som administratör på en dator i din lokala miljö.
2. Ansluta till Azure Resource Manager med`Connect-AzAccount`
3. Hämta PowerShell DSC-metakonfigurationerna för de datorer som du vill registrera från automationskontot där du konfigurerar noder.

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

1. Du bör nu ha en mapp som heter **DscMetaConfigs**, som innehåller PowerShell DSC-metakonfigurationer för datorerna att vara ombord (som administratör).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>Introduktion säkert med registrering

Datorer kan vara säkert inbyggda i ett Azure Automation-konto via WMF 5 DSC-registreringsprotokollet. Med det här protokollet kan en DSC-nod autentiseras till en PowerShell DSC-pull- eller rapportserver, inklusive Azure Automation State Configuration. Noden registrerar sig med servern på registrerings-URL:en och autentiserar med hjälp av en registreringsnyckel. Under registreringen förhandlar DSC-noden och DSC-pull/rapport-servern om ett unikt certifikat som noden kan använda för autentisering till servern efter registreringen. Den här processen förhindrar att inbyggda noder personifierar varandra, till exempel om en nod har komprometterats och beter sig uppsåtligt. Efter registreringen används inte registreringsnyckeln för autentisering igen och tas bort från noden.

Du kan få den information som krävs för registreringsprotokollet för tillståndskonfiguration från **nycklar** under **Kontoinställningar** i Azure-portalen. 

![Azure-automatiseringsnycklar och URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Registrerings-URL är url-fältet på sidan Nycklar.
- Registreringsnyckel är värdet för fältet **Primär åtkomstnyckel** eller fältet **Sekundär åtkomstnyckel** på sidan Nycklar. Båda tangenterna kan användas.

För ökad säkerhet kan du när som helst återskapa de primära och sekundära åtkomstnycklarna för ett Automation-konto på sidan Nycklar. Nyckelförnyelse förhindrar att framtida nodregistreringar använder tidigare nycklar.

## <a name="re-registering-a-node"></a>Registrera om en nod

När du har registrerat en dator som en DSC-nod i Azure Automation State Configuration finns det flera orsaker till varför du kan behöva registrera om noden i framtiden.

- **Förnyelse av certifikat.** För versioner av Windows Server före Windows Server 2019 förhandlar varje nod automatiskt om ett unikt certifikat för autentisering som upphör att gälla efter ett år. Om ett certifikat upphör att gälla utan förnyelse kan noden inte `Unresponsive`kommunicera med Azure Automation och är markerad . För närvarande kan PowerShell DSC-registreringsprotokollet inte automatiskt förnya certifikat när de närmar sig förfallodatumet och du måste registrera noderna igen efter ett år. Innan du registrerar om, se till att varje nod kör WMF 5 RTM. 

    Omregistrering som utförs 90 dagar eller mindre från certifikatets utgångstid, eller när som helst efter certifikatets utgångstid, resulterar i att ett nytt certifikat genereras och används. En lösning på problemet ingår i Windows Server 2019 och senare.

- **Ändringar i DSC LCM-värden.** Du kan behöva ändra [PowerShell DSC LCM-värden](/powershell/scripting/dsc/managing-nodes/metaConfig4) som angetts `ConfigurationMode`under den första registreringen av noden, till exempel . För närvarande kan du bara ändra dessa DSC-agentvärden genom omregistrering. Det enda undantaget är nodkonfigurationsvärdet som tilldelats noden. Du kan ändra detta i Azure Automation DSC direkt.

Du kan registrera om en nod på samma sätt som du registrerade noden från början med någon av de introduktionsmetoder som beskrivs i det här dokumentet. Du behöver inte avregistrera en nod från Azure Automation State Configuration innan du registrerar den igen.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Felsöka onboarding av virtuella Azure-datorer

Med Azure Automation State Configuration kan du enkelt gå ombord på virtuella Azure Windows-datorer för konfigurationshantering. Under huven används Azure VM Desired State Configuration-tillägget för att registrera den virtuella datorn med Azure Automation State Configuration. Eftersom Azure VM Desired State Configuration-tillägget körs asynkront kan det vara viktigt att spåra dess förlopp och felsöka körningen.

> [!NOTE]
> Alla metoder för introduktion av en Azure Windows VM till Azure Automation State Configuration som använder azure vm Desired State Configuration-tillägget kan ta upp till en timme för Azure Automation att visa det som registrerat. Den här fördröjningen beror på installationen av WMF 5 på den virtuella datorn av azure VM Desired State Configuration-tillägget, som krävs för att registrera den virtuella datorn till Azure Automation State Configuration.

Så här felsöker eller visar du status för tillägget azure VM Desired State Configuration:

1. I Azure-portalen navigerar du till den virtuella datorn som är inbyggt.
2. Klicka på **Tillägg** under **Inställningar**. 
3. Välj nu **DSC** eller **DSCForLinux**, beroende på ditt operativsystem. 
4. Om du vill veta mer kan du klicka på **Visa detaljerad status**.

Mer information om felsökning finns i [Felsöka problem med DSC (Azure Automation Desired State Configuration).](./troubleshoot/desired-state-configuration.md)

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång finns [i Komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till målnoder finns [i Kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md).
- Cmdlet-referens för PowerShell finns i [cmdlets för Azure Automation State Configuration](/powershell/module/az.automation#automation).
- Prisinformation finns i [prissättningen för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Ett exempel på hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i Exempel på [användning: Kontinuerlig distribution till virtuella datorer med Azure Automation State Configuration och Chocolatey](automation-dsc-cd-chocolatey.md).
