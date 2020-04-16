---
title: Inbyggda datorer för hantering av Azure Automation State Configuration
description: I den här artikeln beskrivs hur du konfigurerar datorer för hantering med Azure Automation State Configuration.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 04/15/2020
manager: carmonm
ms.openlocfilehash: 4fc9f701f4f832deb1fed34d7ac9f888521d3830
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406200"
---
# <a name="onboard-machines-for-management-by-azure-automation-state-configuration"></a>Inbyggda datorer för hantering av Azure Automation State Configuration

Azure Automation State Configuration är en konfigurationshanteringstjänst för DSC-noder (Desired State Configuration) i moln- eller lokala datacenter. Du kan komma åt tjänsten i Azure-portalen genom att välja **Tillståndskonfiguration (DSC)** under **Configuration Management**. 

Med Azure Automation State Configuration kan du snabbt och enkelt uppnå skalbarhet på tusentals datorer från en central, säker plats. Du kan enkelt gå ombord på datorer, tilldela dem deklarativa konfigurationer och visa rapporter som visar varje dators överensstämmelse med det tillstånd som du har angett.

Azure Automation State Configuration-tjänsten är till DSC vilka Azure Automation-runbooks är för PowerShell-skript. Med andra ord, på samma sätt som Azure Automation hjälper dig att hantera PowerShell-skript, hjälper det dig också att hantera DSC-konfigurationer. Mer information om fördelarna med att använda Azure Automation State Configuration finns i [översikt över Azure Automation State Configuration](automation-dsc-overview.md).

Du kan använda Azure Automation State Configuration för att hantera en mängd olika datorer:

- Virtuella Azure-datorer
- Virtuella Azure-datorer (klassisk)
- Fysiska/virtuella Windows-datorer lokalt eller i ett annat moln än Azure (inklusive Amazon Web Services [AWS] Elastic Compute Cloud [EC2]-instanser)
- Fysiska/virtuella Linux-datorer lokalt, i Azure eller i ett annat moln än Azure

Om du inte är redo att hantera datorkonfiguration från molnet kan du använda Azure Automation State Configuration som en slutpunkt med endast rapporter. Med den här funktionen kan du ställa in eller skicka konfigurationer via DSC och visa rapporteringsinformation i Azure Automation.

> [!NOTE]
> Hantering av virtuella Azure-datorer med Azure Automation State Configuration ingår utan extra kostnad om ditt installerade Azure VM Desired State Configuration-tillägg är version 2.70 eller senare. Mer information finns i [Priser för Automatisering](https://azure.microsoft.com/pricing/details/automation/).

I följande avsnitt i den här artikeln beskrivs hur du kan gå in på de tidigare listade datorerna till Azure Automation State Configuration.

## <a name="onboard-azure-vms"></a>Inbyggd Virtuella Azure-datorer

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul för hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [Uppdatera Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

Eftersom Azure-tillägget körs asynkront tillhandahåller vi steg för att spåra dess förlopp eller felsöka det i avsnittet [Felsöka Azure-datorns introduktion i](#troubleshoot-azure-virtual-machine-onboarding) den här artikeln.

> [!NOTE]
> Om du distribuerar DSC till en Linux-nod används mappen */tmp.* Moduler som `nxautomation` hämtas tillfälligt för verifiering innan de installeras. För att säkerställa att modulerna är korrekt installerade behöver Log Analytics-agenten för Linux läs-/skrivbehörighet i mappen */tmp.*<br><br>
> Log Analytics-agenten för Linux körs som *omsagent-användare.* Om du vill bevilja skrivbehörighet `setfacl -m u:omsagent:rwx /tmp`till *omsagent-användaren* kör du kommandot .

### <a name="onboard-vms-by-using-the-azure-portal"></a>Introduktion till virtuella datorer med hjälp av Azure-portalen

Så här går du ombord på virtuella Azure-datorer till Azure Automation State Configuration via [Azure-portalen:](https://portal.azure.com/)

1. Gå till Azure Automation-kontot där du vill vara ombord på virtuella datorer. 

1. På sidan **Tillståndskonfiguration** väljer du fliken **Noder** och väljer sedan **Lägg till**.

1. Välj en virtuell dator som ska finnas ombord.

1. Om datorn inte har powershell-tillägget För önskat tillstånd installerat och energiläget körs väljer du **Anslut**.

1. Under **Registrering**anger du de [LCM-värden (PowerShell DSC Local Configuration Manager)](/powershell/scripting/dsc/managing-nodes/metaConfig) som krävs för användningsfallet. Du kan också ange en nodkonfiguration som ska tilldelas den virtuella datorn.

![Registreringsfönstret för virtuell dator](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-vms-by-using-azure-resource-manager-templates"></a>Introduktion till virtuella datorer med hjälp av Azure Resource Manager-mallar

Du kan distribuera och gå in på en virtuell dator till Azure Automation State Configuration med hjälp av Azure Resource Manager-mallar. En exempelmall som innehåller en befintlig vm-konfiguration till Azure Automation State Configuration finns i [Server som hanteras av tjänsten Konfiguration av önskat tillstånd](https://azure.microsoft.com/resources/templates/101-automation-configuration/). Om du hanterar en skalningsuppsättning för virtuella datorer läser du exempelmallen i konfigurationen för [skalningsuppsättning för virtuella datorer som hanteras av Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-vms-by-using-powershell"></a>Inbyggda virtuella datorer med hjälp av PowerShell

Du kan använda [cmdleten Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) i PowerShell för att registrera virtuella datorer till Azure Automation State Configuration. 

> [!NOTE]
>Cmdleten `Register-AzAutomationDscNode` implementeras för närvarande endast för datorer som kör Windows, eftersom cmdleten endast utlöser Windows-tillägget.

### <a name="register-vms-across-azure-subscriptions"></a>Registrera virtuella datorer i Azure-prenumerationer

Det bästa sättet att registrera virtuella datorer från andra Azure-prenumerationer är att använda DSC-tillägget i en Azure Resource Manager-distributionsmall. Exempel finns i [tillägget Önskat tillståndskonfiguration med Azure Resource Manager-mallar](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Information om hur du hittar registreringsnyckeln och registrerings-URL:en som ska användas som parametrar i mallen finns i avsnittet [Onboard på ett säkert sätt med hjälp av](#onboard-securely-by-using-registration) registreringsavsnittet i den här artikeln.

## <a name="onboard-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Inbyggd fysisk/virtuell Windows-datorer lokalt eller i ett annat moln än Azure

Du kan vara med på Windows-servrar som körs lokalt eller i andra molnmiljöer, inklusive AWS EC2-instanser, till Azure Automation State Configuration. Servrarna måste ha [utgående åtkomst till Azure](automation-dsc-overview.md#network-planning).

1. Kontrollera att den senaste versionen av [Windows Management Framework 5](https://aka.ms/wmf5latest) är installerad på de datorer som ska finnas ombord på Azure Automation State Configuration. Dessutom måste Windows Management Framework 5 vara installerat på den dator som du använder för introduktionsåtgärden.
1. Om du vill skapa en mapp som innehåller de DSC-metakonfigurationer som krävs följer du anvisningarna i avsnittet [Generera DSC-metakonfigurationer.](#generate-dsc-metaconfigurations) 
1. Om du vill fjärrstyra PowerShell DSC-metakonfigurationerna på de datorer som du vill använda ombord använder du följande cmdlet: 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Om du inte kan använda PowerShell DSC-metakonfigurationerna på distans kopierar du **mappen metakonfigurationer** till de datorer som du är ombord på. Lägg sedan till kod för att anropa [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) lokalt på datorerna.

1. I Azure-portalen eller med hjälp av cmdlets kontrollerar du att datorerna som ska finnas ombord visas som tillståndskonfigurationsnoder som är registrerade i ditt Azure Automation-konto.

## <a name="onboard-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Inbyggd fysisk/virtuell Linux-datorer lokalt eller i ett annat moln än Azure

Du kan vara ombord på Linux-servrar som körs lokalt eller i andra molnmiljöer till Azure Automation State Configuration. Servrarna måste ha [utgående åtkomst till Azure](automation-dsc-overview.md#network-planning).

1. Kontrollera att den senaste versionen av [PowerShell Desired State Configuration för Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) är installerad på de datorer som ska finnas ombord på Azure Automation State Configuration.
1. Om [Standardinställningarna för PowerShell DSC LCM](/powershell/scripting/dsc/managing-nodes/metaConfig4) matchar ditt användningsfall och du vill använda datorer ombord så att de både hämtar från och rapporterar till Azure Automation State Configuration gör du följande:

   a. På varje Linux-dator som ska finnas `Register.py` ombord på Azure Automation State Configuration använder du för att gå ombord på dem med standardinställningarna PowerShell DSC Local Configuration Manager.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   b. Information om hur du hittar registreringsnyckeln och registreringsadressen för ditt Automation-konto läser du [inbordet på ett säkert sätt med hjälp](#onboard-securely-by-using-registration) av registreringsavsnittet i den här artikeln.  
   c. Hoppa till steg 4.
   
1. Om standardvärdena för PowerShell DSC LCM inte matchar ditt användningsfall, eller om du bara vill använda datorer som bara rapporterar till Azure Automation State Configuration, följer du steg a-d. Annars går du direkt till steg d.

    a. Om du vill skapa en mapp som innehåller de DSC-metakonfigurationer som krävs följer du anvisningarna i avsnittet [Generera DSC-metakonfigurationer.](#generate-dsc-metaconfigurations)

    b. Kontrollera att den senaste versionen av [Windows Management Framework 5](https://aka.ms/wmf5latest) är installerad på den dator som används för introduktion.

    c. Om du vill använda PowerShell DSC-metakonfigurationer på de datorer som du vill använda ombord lägger du till följande kod:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

    d. Om du inte kan använda PowerShell DSC-metakonfigurationerna på distans kopierar du de metakonfigurationer som motsvarar fjärrdatorerna från mappen som beskrivs i steg 3a till Linux-datorerna.

1. Lägg till kod för att anropa *Set-DscLocalConfigurationManager.py* lokalt på varje Linux-dator som ska finnas ombord på Azure Automation State Configuration.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Genom att använda Azure-portalen eller cmdlets, se till att datorerna som ska finnas ombord nu visas som DSC-noder som är registrerade i ditt Azure Automation-konto.

## <a name="generate-dsc-metaconfigurations"></a>Generera DSC-metakonfigurationer

Om du vill gå in på en dator till Azure Automation State Configuration kan du generera en [DSC-metakonfiguration](/powershell/scripting/dsc/managing-nodes/metaConfig). Den här konfigurationen talar om för DSC-agenten att hämta från eller rapportera till Azure Automation State Configuration. Du kan generera en DSC-metakonfiguration för Azure Automation State Configuration med hjälp av antingen en PowerShell DSC-konfiguration eller Azure Automation PowerShell-cmdlets.

> [!NOTE]
> DSC-metakonfigurationer innehåller de hemligheter som du behöver för att onboarding en dator till ett Automation-konto för hantering. Var noga med att skydda alla DSC-metakonfigurationer som du skapar eller ta bort dem efter användning.

Proxystöd för metakonfigurationer styrs av LCM, som är Windows PowerShell DSC-motorn. LCM körs på alla målnoder och ansvarar för att anropa konfigurationsresurserna som ingår i ett DSC-metakonfigurationsskript. 

Du kan inkludera proxystöd i en metakonfiguration genom att inkludera definitioner av proxy-URL:en och proxyautentiseringsuppgifterna efter behov i , `ConfigurationRepositoryWeb` `ResourceRepositoryWeb`och `ReportServerWeb` blocken. Se [Konfigurera lokal konfigurationshanterare](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-by-using-a-dsc-configuration"></a>Generera DSC-metakonfigurationer med hjälp av en DSC-konfiguration

1. Öppna Visual Studio Code (eller din favoritredigerare) som administratör på en dator i din lokala miljö. Maskinen måste ha den senaste versionen av [Windows Management Framework 5](https://aka.ms/wmf5latest) installerad.
1. Kopiera följande skript lokalt. Det här skriptet innehåller en PowerShell DSC-konfiguration för att skapa metakonfigurationer och ett kommando för att starta metakonfigurationen.

    > [!NOTE]
    > Konfigurationsnodkonfigurationsnamn för tillstånd är skiftlägeskänsliga i Azure-portalen. Om ärendet är felaktigt matchat visas inte noden under fliken **Noder.**

   ```powershell
   # The DSC configuration that will generate metaconfigurations.
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

    # Create the metaconfigurations.
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: Edit the following as needed for your use case.
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

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   DscMetaConfigs @Params
   ```

1. Fyll i registreringsnyckeln och webbadressen för ditt Automation-konto samt namnen på de datorer som ska finnas ombord. Alla andra parametrar är valfria. Information om hur du hittar registreringsnyckeln och registreringsadressen för ditt Automation-konto finns i avsnittet [Onboard på ett säkert sätt med hjälp av](#onboard-securely-by-using-registration) registreringsavsnittet.

1. Om du vill att datorerna ska rapportera DSC-statusinformation till Azure Automation State `ReportOnly` Configuration, men inte hämta konfiguration eller PowerShell-moduler, ställer du in parametern till *true*.

1. Om `ReportOnly` inte anges rapporterar datorerna DSC-statusinformation till Azure Automation State Configuration och pull-konfiguration eller PowerShell-moduler. Ange parametrar därefter `ConfigurationRepositoryWeb`i `ResourceRepositoryWeb`, `ReportServerWeb` och blocken.

1. Kör skriptet. Du bör nu ha en arbetskatalogmapp som heter *DscMetaConfigs*, som innehåller PowerShell DSC-metakonfigurationer för datorerna att vara ombord (som administratör).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-by-using-azure-automation-cmdlets"></a>Generera DSC-metakonfigurationer med hjälp av Azure Automation-cmdlets

Om Standardinställningarna för PowerShell DSC LCM matchar ditt användningsfall och du vill registrera datorer för att både hämta från och rapportera till Azure Automation State Configuration, kan du generera de nödvändiga DSC-metakonfigurationerna enklare genom att använda Azure Automation-cmdlets.

1. Öppna PowerShell-konsolen eller Visual Studio-koden som administratör på en dator i din lokala miljö.
1. Anslut till Azure Resource Manager med [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
1. Hämta PowerShell DSC-metakonfigurationerna för de datorer som du vill registrera från det Automation-konto där du konfigurerar noder.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting.
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Du bör nu ha en *DscMetaConfigs-mapp* som innehåller PowerShell DSC-metakonfigurationer för de datorer som du vill vara ombord (som administratör).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboard-securely-by-using-registration"></a>Ombord säkert med hjälp av registrering

Datorer kan vara inbyggda på ett Azure Automation-konto via Registreringsprotokollet för Windows Management Framework 5 DSC. Med det här protokollet kan en DSC-nod autentiseras till en PowerShell DSC-pull- eller rapportserver, inklusive Azure Automation State Configuration. Noden registrerar sig med servern på registrerings-URL:en och autentiserar med hjälp av en registreringsnyckel. 

Under registreringen förhandlar DSC-noden och DSC-pull/rapport-servern om ett unikt certifikat som noden kan använda för autentisering till servern efter registreringen. Den här processen förhindrar att inbyggda noder personifierar varandra (till exempel om en nod har komprometterats och beter sig skadligt). 

Efter registreringen återanvänds inte registreringsnyckeln för autentisering och tas bort från noden.

Om du vill hämta den information som krävs för registreringsprotokollet för tillståndskonfiguration går du till Azure-portalen och väljer **Nycklar**under **Kontoinställningar**. 

![Azure-automatiseringsnycklar och URL i fönstret Nycklar](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- **Registreringsadress**: Värdet i **rutan URL.**
- **Registreringsnyckel**: Värdet i rutan **Primär åtkomstnyckel** eller rutan **Sekundär åtkomstnyckel.** Du kan använda båda tangenterna.

För ökad säkerhet kan du när som helst återskapa de primära och sekundära åtkomstnycklarna för ett Automation-konto i fönstret **Nycklar.** Nyckelförnyelse förhindrar att framtida nodregistreringar använder tidigare nycklar.

## <a name="re-register-a-node"></a>Registrera om en nod

När du har registrerat en dator som en DSC-nod i Azure Automation State Configuration kan du behöva registrera noden i framtiden igen av något av följande skäl:

- **Förnyelse av certifikat**: För versioner av Windows Server tidigare än Windows Server 2019 förhandlar varje nod automatiskt om ett unikt certifikat för autentisering som upphör att gälla efter ett år. Om ett certifikat upphör att gälla utan förnyelse kan noden inte kommunicera med Azure Automation och har *markerats som inte svarar*. 

  För närvarande kan PowerShell DSC-registreringsprotokollet inte automatiskt förnya certifikat när de närmar sig förfallodatumet och du måste registrera noderna igen efter ett år. Innan du registrerar dig igen bör du se till att varje nod kör Windows Management Framework 5 RTM. 

    Omregistrering som utförs 90 eller färre dagar från certifikatets utgångstid, eller när som helst efter certifikatets utgångstid, resulterar i att ett nytt certifikat genereras och används. En lösning på problemet ingår i Windows Server 2019 och senare.

- **Ändringar av DSC LCM-värden:** Du kan behöva ändra [PowerShell DSC LCM-värden](/powershell/scripting/dsc/managing-nodes/metaConfig4) som `ConfigurationMode`angavs under den första registreringen av noden (till exempel ). För närvarande kan du ändra dessa DSC-agentvärden endast genom omregistrering. Det enda undantaget är nodkonfigurationsvärdet som tilldelats noden. Du kan ändra det här värdet i Azure Automation DSC direkt.

Du kan registrera om en nod på samma sätt som du registrerade den från början genom att använda någon av de introduktionsmetoder som beskrivs i det här dokumentet. Du behöver inte avregistrera en nod från Azure Automation State Configuration innan du registrerar den igen.

## <a name="troubleshoot-azure-virtual-machine-onboarding"></a>Felsöka onboarding av virtuella Azure-datorer

Med Azure Automation State Configuration kan du enkelt gå ombord på virtuella Azure Windows-datorer för konfigurationshantering. Under huven används Azure VM Desired State Configuration-tillägget för att registrera den virtuella datorn med Azure Automation State Configuration. Eftersom Azure VM Desired State Configuration-tillägget körs asynkront kan det vara viktigt att spåra dess förlopp och felsöka körningen.

> [!NOTE]
> Alla metoder för introduktion av en Azure Windows VM till Azure Automation State Configuration som använder azure vm Desired State Configuration-tillägget kan ta upp till en timme för Azure Automation att visa det som registrerat. Den här fördröjningen beror på installationen av Windows Management Framework 5 på den virtuella datorn av azure VM Desired State Configuration-tillägget, som krävs för att registrera den virtuella datorn till Azure Automation State Configuration.

Så här felsöker eller visar du status för tillägget azure VM Desired State Configuration:

1. Gå till den virtuella datorn som finns ombord i Azure-portalen.
2. Under **Inställningar** väljer du **Tillägg**. 
3. Välj **DSC** eller **DSCForLinux**, beroende på ditt operativsystem. 
4. Om du vill ha mer information om tillägget väljer du **Visa detaljerad status**.

Mer information om felsökning finns i [Felsöka problem med DSC (Azure Automation Desired State Configuration).](./troubleshoot/desired-state-configuration.md)

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång finns [i Komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till målnoder finns [i Kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md).
- En PowerShell-cmdlet-referens finns i [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Prisinformation finns i [prissättningen för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Ett exempel på hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i [Kontinuerlig distribution till virtuella datorer med Azure Automation State Configuration och Chocolatey](automation-dsc-cd-chocolatey.md).
