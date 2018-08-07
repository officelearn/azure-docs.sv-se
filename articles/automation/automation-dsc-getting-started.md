---
title: Komma igång med Azure Automation DSC
description: Förklaring och exempel på de vanligaste uppgifterna i Azure Automation Desired State Configuration (DSC)
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 08/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9f2312064e9fb7676d5609ee077d5ed7e02e8f30
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524472"
---
# <a name="getting-started-with-azure-automation-dsc"></a>Komma igång med Azure Automation DSC

Den här artikeln förklarar hur du gör de vanligaste uppgifterna med Azure Automation Desired State Configuration (DSC), som att skapa, importera, och kompilera konfigurationer, konfigurera datorer för att hantera, och visa rapporter. En översikt över vilka Azure Automation DSC är finns i [översikt över Azure Automation DSC](automation-dsc-overview.md). DSC-dokumentation finns i [Windows PowerShell Desired State Configuration-översikt](/powershell/dsc/overview).

Den här artikeln innehåller en stegvis guide till med hjälp av Azure Automation DSC. Om du vill att en exempel-miljö som redan har konfigurerats utan att följa stegen som beskrivs i den här artikeln kan du använda följande Resource Manager-mallen: den här mallen ställer in en slutförd Azure Automation DSC-miljön, inklusive en Azure-dator som är hanteras av Azure Automation DSC.

## <a name="prerequisites"></a>Förutsättningar

Om du vill utföra exemplen i den här artikeln, krävs följande:

* Ett Azure Automation-konto. Instruktioner om hur du skapar ett Kör som-konto för Azure Automation finns i [Azure Kör som-konto](automation-sec-configure-azure-runas-account.md).
* En Azure Resource Manager-VM (inte klassisk) som kör Windows Server 2008 R2 eller senare. Instruktioner om hur du skapar en virtuell dator finns i [Skapa din första virtuella Windows-dator i Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Skapa en DSC-konfiguration

Du skapar en enkel [DSC-konfiguration](/powershell/dsc/configurations) som säkerställer att antingen närvaron eller frånvaron av den **-webbserver** Windows funktion (IIS), beroende på hur du tilldelar noder.

1. Starta Windows PowerShell ISE (eller valfri textredigerare).
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

Den här konfigurationen anropar en resurs i varje nod i block på [WindowsFeature-resurs](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource), som ser till antingen närvaron eller frånvaron av den **-webbserver** funktionen.

## <a name="importing-a-configuration-into-azure-automation"></a>Importera en konfiguration till Azure Automation

Därefter måste importera du konfigurationen till Automation-kontot.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** väljer **DSC-konfigurationer** under **konfigurationshantering**.
1. På den **DSC-konfigurationer** klickar du på **+ Lägg till en konfiguration**.
1. På den **importkonfigurationen** sidan, bläddra till den `TestConfig.ps1` fil på din dator.

    ![Skärmbild av den ** Import Configuration ** sidan](./media/automation-dsc-getting-started/AddConfig.png)
1. Klicka på **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Visa en konfiguration i Azure Automation

När du har importerat en konfiguration, kan du visa den i Azure-portalen.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** väljer **DSC-konfigurationer** under **konfigurationshantering**.
1. På den **DSC-konfigurationer** klickar du på **TestConfig** (detta är namnet på konfigurationen du importerade i föregående procedur).
1. På den **TestConfig Configuration** klickar du på **visa konfigurationskälla**.

    ![Skärmbild av sidan TestConfig konfiguration](./media/automation-dsc-getting-started/ViewConfigSource.png)

    En **TestConfig konfigurationskälla** öppnas, visa PowerShell-kod för konfigurationen.

## <a name="compiling-a-configuration-in-azure-automation"></a>Kompilera en konfiguration i Azure Automation

Innan du kan tillämpa ett önskat tillstånd till en nod, måste en DSC-konfiguration som definierar det aktuella tillståndet kompileras till en eller flera nodkonfigurationer (MOF-dokument) och placeras på Automation DSC-Hämtningsserver. En mer detaljerad beskrivning av kompilera konfigurationer i Azure Automation DSC finns i [kompilera konfigurationer i Azure Automation DSC](automation-dsc-compile.md). Mer information om att kompilera konfigurationer finns i [DSC-konfigurationer](/powershell/dsc/configurations).

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** klickar du på **DSC-konfigurationer** under **konfigurationshantering**.
1. På den **DSC-konfigurationer** klickar du på **TestConfig** (namnet på den tidigare importerad konfigurationen).
1. På den **TestConfig Configuration** klickar du på **Kompilera**, och klicka sedan på **Ja**. Detta startar ett Kompileringsjobb.

    ![Skärmbild av sidan TestConfig markering kompilera knappen](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> När du kompilerar en konfiguration i Azure Automation distribuerar automatiskt alla skapade nodkonfiguration MOF: ar till den pull-servern.

## <a name="viewing-a-compilation-job"></a>Visa en Kompileringsjobb

När du har startat en sammanställning kan du visa den i den **Kompileringsjobb** panelen i den **Configuration** sidan. Den **Kompileringsjobb** panel visar för närvarande körs har slutförts och misslyckade jobb. När du öppnar en kompilering jobbsidan den visar information om jobbet, inklusive några fel eller varningar påträffades, indataparametrar som används i konfigurationen och kompilering loggar.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** klickar du på **DSC-konfigurationer** under **konfigurationshantering**.
1. På den **DSC-konfigurationer** klickar du på **TestConfig** (namnet på den tidigare importerad konfigurationen).
1. Under **Kompileringsjobb**, Välj kompileringsjobbet och du vill visa. En **Kompileringsjobbet** öppnas som är märkt med det datum då kompileringsjobbet har startats.

    ![Skärmbild av sidan Kompileringsjobb](./media/automation-dsc-getting-started/CompilationJob.png)
1. Klicka på valfri panel i den **Kompileringsjobbet** och se ytterligare information om jobbet.

## <a name="viewing-node-configurations"></a>Visa nodkonfigurationer

Slutförande av en kompileringsjobbet skapar en eller flera nya nodkonfigurationer. En nodkonfiguration är en MOF-dokument som har distribuerats till pull-servern och redo att hämtas och tillämpas av en eller flera noder. Du kan visa nodkonfigurationer i ditt Automation-konto i den **DSC-Nodkonfigurationer** sidan. En nodkonfiguration har ett namn med formatet *ConfigurationName*. *Nodnamn*.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. På navmenyn klickar du på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** klickar du på **tillståndskonfiguration (DSC)** och välj sedan **konfigurationer**.

    ![Skärmbild av sidan DSC-Nodkonfigurationer](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>Onboarding en Azure-dator för hantering med Azure Automation DSC

Du kan använda Azure Automation DSC för att hantera virtuella Azure-datorer (både klassiska och Resource Manager), lokala virtuella datorer, Linux-datorer, virtuella datorer i AWS och lokala fysiska datorer. I den här artikeln får du lära dig hur du publicera endast Azure Resource Manager virtuella datorer. Information om onboarding andra typer av datorer, finns i [konfigurera datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>Att publicera en Azure Resource Manager-VM för hantering av Azure Automation DSC

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** klickar du på **tillståndskonfiguration (DSC)** under **konfigurationshantering** och välj **noder**.
1. I den **noder** klickar du på **+ Lägg till**.

    ![Skärmbild av sidan DSC-noder om du markerar knappen Lägg till virtuell Azure-dator](./media/automation-dsc-getting-started/OnboardVM.png)

1. På sidan virtuella datorer väljer du den virtuella datorn. **Lägg till virtuella Azure-datorer** klickar du på **Välj virtuella datorer för att publicera**.
1. Klicka på **Anslut**.

   > [!IMPORTANT]
   > Det här måste vara en Azure Resource Manager-VM som kör Windows Server 2008 R2 eller senare.

1. I den **registrering** anger du namnet på nodkonfigurationen som du vill använda för den virtuella datorn i den **Nodkonfigurationsnamn** box. Detta måste exakt matcha namnet på en nodkonfiguration i Automation-kontot. Det är valfritt att ange ett namn i det här läget. Du kan ändra den tilldelade nodkonfigurationen när noden.
   Kontrollera **starta om nod vid behov**, och klicka sedan på **OK**.

    ![Skärmbild av sidan registrering](./media/automation-dsc-getting-started/RegisterVM.png)

    Nodkonfiguration som du angett tillämpas på den virtuella datorn med intervall som anges av den **Konfigurationslägesfrekvens**, och den virtuella datorn söker efter uppdateringar till nodkonfiguration med intervall som anges av den **uppdatera Frekvens**. Läs mer om hur dessa värden används [konfigurerar den lokala Konfigurationshanteraren](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
1. I den **lägga till virtuella Azure-datorer** klickar du på **skapa**.

Azure startar processen med onboarding den virtuella datorn. Den virtuella datorn när den är klar visas i den **noder** fliken på den **tillståndskonfiguration (DSC)** sidan i Automation-kontot.

## <a name="viewing-the-list-of-dsc-nodes"></a>Visa listan över DSC-noder

Du kan visa listan över alla datorer som har registrerats för hantering i ditt Automation-konto i den **noder** fliken på den **tillståndskonfiguration (DSC)** sidan.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
3. På den **Automation-konto** klickar du på **tillståndskonfiguration (DSC)**, klicka sedan på den **noder** fliken.

## <a name="viewing-reports-for-dsc-nodes"></a>Visa rapporter för DSC-noder

Varje gång Azure Automation DSC utför en konsekvenskontroll på en hanterad nod skickar noden en statusrapport tillbaka till den pull-servern. Du kan visa rapporterna på sidan för noden.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
3. På den **automatiseringskontot** klickar du på **tillståndskonfiguration (DSC)** och klicka sedan på den **noder** fliken.
4. I den **noder** väljer du den nod som du vill visa.
5. På den **nod** klickar du på den rapport du vill visa **rapporter**.

    ![Skärmbild av sidan](./media/automation-dsc-getting-started/NodeReport.png)

På sidan för en enskild rapport, kan du se följande statusinformation för motsvarande konsekvenskontroll:

* Rapportstatus – om noden är ”kompatibel”, konfigurationen ”misslyckades” eller noden är ”inte kompatibel” (när den är i **applyandmonitor** läge och datorn är inte i önskat läge).
* Starttid för konsekvenskontrollen.
* Den totala körningstiden för konsekvenskontrollen.
* Typ av konsekvenskontroll.
* Eventuella fel, inklusive felkod och ett felmeddelande.
* Alla DSC-resurser som används i konfigurationen och status för varje resurs (om noden är i önskat läge för den resursen) – du kan klicka på varje resurs för att få mer detaljerad information för den resursen.
* Namn, IP-adress och konfigurationsläge för noden.

Du kan också klicka på **visa rå rapport** att se de faktiska data som noden skickar till servern. Mer information om hur du använder dessa data finns i [med hjälp av en DSC-rapportserver](https://msdn.microsoft.com/powershell/dsc/reportserver).

Det kan ta lite tid när en nod har integrerats innan den första rapporten är tillgänglig. Du kan behöva vänta upp till 30 minuter innan den första rapporten när du har publicerat en nod.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Omtilldela en nod till en annan nod-konfiguration

Du kan tilldela en nod och använder en annan nodkonfiguration än den som du ursprungligen har tilldelats.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
3. På den **automatiseringskontot** klickar du på **tillståndskonfiguration (DSC)** och klicka sedan på den **noder** fliken.
4. På den **noder** fliken, klickar du på namnet på den nod som du vill tilldela.
5. På sidan för noden **tilldela nodkonfiguration**.

    ![Skärmbild av sidan noden om du markerar knappen Tilldela noden](./media/automation-dsc-getting-started/AssignNode.png)
6. På den **tilldela nodkonfiguration** väljer nodkonfigurationen som du vill tilldela noden och klicka sedan på **OK**.

    ![Skärmbild av sidan tilldela nodkonfiguration](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Avregistrera en nod

Om du inte längre vill att en nod hanteras av Azure Automation DSC kan du avregistrera den.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
3. På den **automatiseringskontot** klickar du på **tillståndskonfiguration (DSC)** och klicka sedan på den **noder** tab.git 
4. På den **noder** fliken, klickar du på namnet på den nod du vill avregistrera.
5. På sidan för noden **avregistrera**.

    ![Skärmbild av sidan noden om du markerar knappen Unregister](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Relaterade artiklar

* [Översikt över Azure Automation DSC](automation-dsc-overview.md)
* [Konfigurera datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md)
* [Windows PowerShell Desired State Configuration-översikt](https://msdn.microsoft.com/powershell/dsc/overview)
* [Azure Automation DSC-cmdletar](/powershell/module/azurerm.automation/#automation)
* [Priser för Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)
