---
title: Komma igång med Azure Automation State Configuration
description: Förklaring och exempel på de vanligaste uppgifterna i Azure Automation tillstånd Configuration (DSC)
services: automation
ms.service: automation
ms.component: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fc1c870d06d6bf4a0db941b261e9aebd317fdcb1
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634375"
---
# <a name="getting-started-with-azure-automation-state-configuration"></a>Komma igång med Azure Automation State Configuration

Den här artikeln förklarar hur du gör de vanligaste uppgifterna med Azure Automation tillstånd-konfiguration, till exempel skapa, importera, och kompilera konfigurationer, konfigurera datorer för att hantera, och visa rapporter. En översikt över vilka Azure Automation-Tillståndskonfiguration är finns i [översikt över Azure Automation tillstånd Configuration](automation-dsc-overview.md). Desired State Configuration (DSC) dokumentation finns i [Windows PowerShell Desired State Configuration-översikt](/powershell/dsc/overview).

Den här artikeln innehåller en stegvis guide till med hjälp av Azure Automation State Configuration. Om du vill att en exempel-miljö som redan har konfigurerats utan att följa stegen som beskrivs i den här artikeln kan du använda följande Resource Manager-mallen: [Azure Automation hanteras nodmallen för](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Den här mallen ställer in en slutförd tillståndskonfigurationen för Azure Automation-miljön, inklusive en Azure-dator som hanteras av Azure Automation State Configuration.

## <a name="prerequisites"></a>Förutsättningar

Om du vill utföra exemplen i den här artikeln, krävs följande:

- Ett Azure Automation-konto. Instruktioner om hur du skapar ett Kör som-konto för Azure Automation finns i [Azure Kör som-konto](automation-sec-configure-azure-runas-account.md).
- En Azure Resource Manager-VM (inte klassisk) som kör Windows Server 2008 R2 eller senare. Instruktioner om hur du skapar en virtuell dator finns i [Skapa din första virtuella Windows-dator i Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Skapa en DSC-konfiguration

Du skapar en enkel [DSC-konfiguration](/powershell/dsc/configurations) som säkerställer att antingen närvaron eller frånvaron av den **-webbserver** Windows funktion (IIS), beroende på hur du tilldelar noder.

1. Starta [VSCode](https://code.visualstudio.com/docs) (eller valfri textredigerare).
1. Skriv följande text:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Spara filen som `TestConfig.ps1`.

Den här konfigurationen anropar en resurs i varje nod i block på [WindowsFeature-resurs](/powershell/dsc/windowsfeatureresource), som ser till antingen närvaron eller frånvaron av den **-webbserver** funktionen.

## <a name="importing-a-configuration-into-azure-automation"></a>Importera en konfiguration till Azure Automation

Därefter måste importera du konfigurationen till Automation-kontot.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** väljer **tillståndskonfiguration (DSC)** under **konfigurationshantering**.
1. På den **tillståndskonfiguration (DSC)** klickar du på den **konfigurationer** och klicka sedan på **+ Lägg till**.
1. På den **importkonfigurationen** sidan, bläddra till den `TestConfig.ps1` fil på din dator.

   ![Skärmbild av den ** Import Configuration bladet](./media/automation-dsc-getting-started/AddConfig.png)

1. Klicka på **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Visa en konfiguration i Azure Automation

När du har importerat en konfiguration, kan du visa den i Azure-portalen.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** väljer **tillståndskonfiguration (DSC)** under **konfigurationshantering**.
1. På den **tillståndskonfiguration (DSC)** klickar du på den **konfigurationer** och klicka sedan på **TestConfig** (detta är namnet på konfigurationen du importerade i föregående proceduren).
1. På den **TestConfig Configuration** klickar du på **visa konfigurationskälla**.

   ![Skärmbild av bladet TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

   En **TestConfig konfigurationskälla** öppnas, visa PowerShell-kod för konfigurationen.

## <a name="compiling-a-configuration-in-azure-automation"></a>Kompilera en konfiguration i Azure Automation

Innan du kan tillämpa ett önskat tillstånd till en nod, måste en DSC-konfiguration som definierar det aktuella tillståndet kompileras till en eller flera nodkonfigurationer (MOF-dokument) och placeras på Automation DSC-Hämtningsserver. En mer detaljerad beskrivning av kompilera konfigurationer i Azure Automation-Tillståndskonfiguration finns i [kompilera konfigurationer i Azure Automation-Tillståndskonfiguration](automation-dsc-compile.md).
Mer information om att kompilera konfigurationer finns i [DSC-konfigurationer](/powershell/dsc/configurations).

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** klickar du på **tillståndskonfiguration (DSC)** under **konfigurationshantering**.
1. På den **tillståndskonfiguration (DSC)** klickar du på den **konfigurationer** och klicka sedan på **TestConfig** (namnet på den tidigare importerad konfigurationen).
1. På den **TestConfig Configuration** klickar du på **Kompilera**, och klicka sedan på **Ja**. Detta startar ett Kompileringsjobb.

   ![Skärmbild av sidan TestConfig markering kompilera knappen](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> När du kompilerar en konfiguration i Azure Automation distribuerar automatiskt alla skapade nodkonfiguration MOF: ar till den pull-servern.

## <a name="viewing-a-compilation-job"></a>Visa en Kompileringsjobb

När du har startat en sammanställning kan du visa den i den **Kompileringsjobb** panelen i den **Configuration** sidan. Den **Kompileringsjobb** panel visar för närvarande körs har slutförts och misslyckade jobb. När du öppnar en kompilering jobbsidan den visar information om jobbet, inklusive några fel eller varningar påträffades, indataparametrar som används i konfigurationen och kompilering loggar.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** klickar du på **tillståndskonfiguration (DSC)** under **konfigurationshantering**.
1. På den **tillståndskonfiguration (DSC)** klickar du på den **konfigurationer** och klicka sedan på **TestConfig** (namnet på den tidigare importerad konfigurationen).
1. Under **Kompileringsjobb**, Välj kompileringsjobbet och du vill visa. En **Kompileringsjobbet** öppnas som är märkt med det datum då kompileringsjobbet har startats.

   ![Skärmbild av sidan Kompileringsjobb](./media/automation-dsc-getting-started/CompilationJob.png)

1. Klicka på valfri panel i den **Kompileringsjobbet** och se ytterligare information om jobbet.

## <a name="viewing-node-configurations"></a>Visa nodkonfigurationer

Slutförande av en kompileringsjobbet skapar en eller flera nya nodkonfigurationer. En nodkonfiguration är en MOF-dokument som har distribuerats till pull-servern och redo att hämtas och tillämpas av en eller flera noder. Du kan visa nodkonfigurationer i ditt Automation-konto i den **tillståndskonfiguration (DSC)** sidan. En nodkonfiguration har ett namn med formatet *ConfigurationName*. *Nodnamn*.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** bladet klickar du på **tillståndskonfiguration (DSC)** under **konfigurationshantering**.
1. På den **tillståndskonfiguration (DSC)** klickar du på den **kompileras konfigurationer** fliken.

   ![Skärmbild av fliken kompileras konfigurationer](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Onboarding en Azure-dator för hantering med Azure Automation State Configuration

Du kan använda Azure Automation State Configuration för att hantera virtuella Azure-datorer (både klassiska och Resource Manager), lokala virtuella datorer, Linux-datorer, virtuella datorer i AWS och lokala fysiska datorer. I den här artikeln får du lära dig hur du publicera endast Azure Resource Manager virtuella datorer. Information om onboarding andra typer av datorer, finns i [konfigurera datorer för hantering av Azure Automation-Tillståndskonfiguration](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Att publicera en Azure Resource Manager-VM för hantering av Azure Automation State Configuration

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** bladet klickar du på **tillståndskonfiguration (DSC)** under **konfigurationshantering**.
1. På den **tillståndskonfiguration (DSC)** sidan medan på den **noder** fliken **+ Lägg till**.

   ![Skärmbild av sidan DSC-noder om du markerar knappen Lägg till virtuell Azure-dator](./media/automation-dsc-getting-started/OnboardVM.png)

1. På den **virtuella datorer** väljer du den virtuella datorn.
1. På den **VM** redogör för sidan, klickar du på **+ Connect**.

   > [!IMPORTANT]
   > Det här måste vara en Azure Resource Manager-VM som kör Windows Server 2008 R2 eller senare.

1. I den **registrering** markerar du namnet på nodkonfigurationen som du vill använda för den virtuella datorn i den **nodkonfigurationsnamn** box. Det är valfritt att ange ett namn i det här läget. Du kan ändra den tilldelade nodkonfigurationen när noden.
   Kontrollera **starta om nod vid behov**, klicka sedan på **OK**.

   ![Skärmbild av bladet registrering](./media/automation-dsc-getting-started/RegisterVM.png)

   Nodkonfiguration som du angett tillämpas på den virtuella datorn med intervall som anges av den **Konfigurationslägesfrekvens**, och den virtuella datorn söker efter uppdateringar till nodkonfiguration med intervall som anges av den **uppdatera Frekvens**. Läs mer om hur dessa värden används [konfigurerar den lokala Konfigurationshanteraren](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
1. I den **lägga till virtuella Azure-datorer** bladet klickar du på **skapa**.

Azure startar processen med onboarding den virtuella datorn. Den virtuella datorn när den är klar visas i den **noder** fliken den **tillståndskonfiguration (DSC)** sidan i Automation-kontot.

## <a name="viewing-the-list-of-managed-nodes"></a>Visa listan över hanterade noder

Du kan visa listan över alla datorer som har registrerats för hantering i ditt Automation-konto i den **noder** fliken den **tillståndskonfiguration (DSC)** sidan.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** bladet klickar du på **tillståndskonfiguration (DSC)** under **konfigurationshantering**.
1. På den **tillståndskonfiguration (DSC)** klickar du på den **noder** fliken.

## <a name="viewing-reports-for-managed-nodes"></a>Visa rapporter för hanterade noder

Varje gång Azure Automation-Tillståndskonfiguration utför en konsekvenskontroll på en hanterad nod skickar noden en statusrapport tillbaka till den pull-servern. Du kan visa rapporterna på sidan för noden.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** bladet klickar du på **tillståndskonfiguration (DSC)** under **konfigurationshantering**.
1. På den **tillståndskonfiguration (DSC)** klickar du på den **noder** fliken. Här kan du se en översikt över konfigurationstillstånd och information för varje nod.

   ![Skärmbild av nod-sidan](./media/automation-dsc-getting-started/NodesTab.png)

1. När du på den **noder** klickar du på posten för noden för att öppna reporting. Klicka på den rapport som du vill visa ytterligare information för rapportering.

   ![Skärmbild av bladet rapport](./media/automation-dsc-getting-started/NodeReport.png)

På bladet för en enskild rapport visas följande statusinformation för motsvarande konsekvenskontroll:

- Rapportstatus – om noden är ”kompatibel”, konfigurationen ”misslyckades” eller noden är ”inte kompatibel” (när den är i **ApplyandMonitor** läge och datorn är inte i önskat läge).
- Starttid för konsekvenskontrollen.
- Den totala körningstiden för konsekvenskontrollen.
- Typ av konsekvenskontroll.
- Eventuella fel, inklusive felkod och ett felmeddelande.
- Alla DSC-resurser som används i konfigurationen och status för varje resurs (om noden är i önskat läge för den resursen) – du kan klicka på varje resurs för att få mer detaljerad information för den resursen.
- Namn, IP-adress och konfigurationsläge för noden.

Du kan också klicka på **visa rå rapport** att se de faktiska data som noden skickar till servern.
Mer information om hur du använder dessa data finns i [med hjälp av en DSC-rapportserver](/powershell/dsc/reportserver).

Det kan ta lite tid när en nod har integrerats innan den första rapporten är tillgänglig. Du kan behöva vänta upp till 30 minuter innan den första rapporten när du har publicerat en nod.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Omtilldela en nod till en annan nod-konfiguration

Du kan tilldela en nod och använder en annan nodkonfiguration än den som du ursprungligen har tilldelats.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** bladet klickar du på **tillståndskonfiguration (DSC)** under **konfigurationshantering**.
1. På den **tillståndskonfiguration (DSC)** klickar du på den **noder** fliken.
1. På den **noder** fliken, klickar du på namnet på den nod som du vill tilldela.
1. På sidan för noden **tilldela nodkonfiguration**.

    ![Skärmbild av sidan noden om du markerar knappen Tilldela noden konfiguration](./media/automation-dsc-getting-started/AssignNode.png)

1. På den **tilldela nodkonfiguration** väljer nodkonfigurationen som du vill tilldela noden och klicka sedan på **OK**.

    ![Skärmbild av sidan tilldela nodkonfiguration](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Avregistrera en nod

Om du inte längre vill att en nod hanteras av Azure Automation DSC kan du avregistrera den.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** bladet klickar du på **tillståndskonfiguration (DSC)** under **konfigurationshantering**.
1. På den **tillståndskonfiguration (DSC)** klickar du på den **noder** fliken.
1. På den **noder** fliken, klickar du på namnet på den nod du vill avregistrera.
1. På sidan för noden **avregistrera**.

    ![Skärmbild av sidan noden om du markerar knappen Unregister](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Relaterade artiklar

- [Azure Automation State Configuration-översikt](automation-dsc-overview.md)
- [Konfigurera datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md)
- [Windows PowerShell Desired State Configuration-översikt](/powershell/dsc/overview)
- [Azure Automation State Configuration-cmdletar](/powershell/module/azurerm.automation/#automation)
- [Priser för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)