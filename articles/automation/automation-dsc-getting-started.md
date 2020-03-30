---
title: Komma igång med Azure Automation State Configuration
description: Förklaring och exempel på de vanligaste uppgifterna i DSC (Azure Automation State Configuration)
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9fce9a769dd6d88c9926913d22716666284938c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74850966"
---
# <a name="getting-started-with-azure-automation-state-configuration"></a>Komma igång med Azure Automation State Configuration

I den här artikeln beskrivs hur du utför de vanligaste uppgifterna med Azure Automation State Configuration, till exempel skapa, importera och kompilera konfigurationer, introduktionsdatorer för att hantera och visa rapporter. En översikt över vad Azure Automation State Configuration är finns i [Översikt över Azure Automation State Configuration](automation-dsc-overview.md). Dokumentation för DSC (Desired State Configuration) finns i [översikt över konfiguration av windows PowerShell-önskad tillståndskonfiguration](/powershell/scripting/dsc/overview/overview).

Den här artikeln innehåller en steg-för-steg-guide till att använda Azure Automation State Configuration. Om du vill ha en exempelmiljö som redan har konfigurerats utan att följa stegen som beskrivs i den här artikeln kan du använda följande Resource Manager-mall: [Azure Automation Managed Node-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Den här mallen konfigurerar en slutförd Azure Automation State Configuration-miljö, inklusive en Azure VM som hanteras av Azure Automation State Configuration.

## <a name="prerequisites"></a>Krav

För att slutföra exemplen i den här artikeln krävs följande:

- Ett Azure Automation-konto. Instruktioner om hur du skapar ett Kör som-konto för Azure Automation finns i [Azure Kör som-konto](automation-sec-configure-azure-runas-account.md).
- En virtuell virtuell dator för Azure Resource Manager (inte Klassisk) som kör ett [operativsystem som stöds](automation-dsc-overview.md#operating-system-requirements). Instruktioner om hur du skapar en virtuell dator finns i [Skapa din första virtuella Windows-dator i Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Skapa en DSC-konfiguration

Du skapar en enkel [DSC-konfiguration](/powershell/scripting/dsc/configurations/configurations) som säkerställer antingen närvaron eller frånvaron av **Webbserver Windows-funktionen** (IIS), beroende på hur du tilldelar noder.

1. Starta [VSCode](https://code.visualstudio.com/docs) (eller textredigerare).
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

Den här konfigurationen anropar en resurs i varje nodblock, [WindowsFeature-resursen,](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource)som säkerställer antingen närvaron eller frånvaron av **webbserverfunktionen.**

## <a name="importing-a-configuration-into-azure-automation"></a>Importera en konfiguration till Azure Automation

Därefter importerar du konfigurationen till Automation-kontot.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Till vänster klickar du på **Alla resurser** och sedan namnet på ditt Automation-konto.
1. På sidan **Automation-konto** väljer du **Tillståndskonfiguration (DSC)** under **Konfigurationshantering**.
1. På sidan **Tillståndskonfiguration (DSC)** klickar du på fliken **Konfigurationer** och sedan på **+ Lägg till**.
1. Bläddra till filen på datorn `TestConfig.ps1` på sidan **Importera konfiguration.**

   ![Skärmbild av bladet **Import configuration**](./media/automation-dsc-getting-started/AddConfig.png)

1. Klicka på **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Visa en konfiguration i Azure Automation

När du har importerat en konfiguration kan du visa den i Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Till vänster klickar du på **Alla resurser** och sedan namnet på ditt Automation-konto.
1. På sidan **Automation-konto** väljer du **Tillståndskonfiguration (DSC)** under **Konfigurationshantering**.
1. På sidan **Tillståndskonfiguration (DSC)** klickar du på fliken **Konfigurationer** och sedan på **TestConfig** (det här är namnet på den konfiguration som du importerade i föregående procedur).
1. Klicka på **Visa konfigurationskälla**på sidan **TestConfig-konfiguration** .

   ![Skärmbild av testkonfigkonfigurationsbladet](./media/automation-dsc-getting-started/ViewConfigSource.png)

   En **TestConfig Configuration-källsida** öppnas och visar PowerShell-koden för konfigurationen.

## <a name="compiling-a-configuration-in-azure-automation"></a>Kompilera en konfiguration i Azure Automation

Innan du kan använda ett önskat tillstånd på en nod måste en DSC-konfiguration som definierar tillståndet kompileras till en eller flera nodkonfigurationer (MOF-dokument) och placeras på Automation DSC Pull Server. En mer detaljerad beskrivning av kompilering av konfigurationer i Azure Automation State Configuration finns [i Kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md).
Mer information om kompilering av konfigurationer finns i [DSC-konfigurationer](/powershell/scripting/dsc/configurations/configurations).

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Till vänster klickar du på **Alla resurser** och sedan namnet på ditt Automation-konto.
1. Klicka på **Tillståndskonfiguration (DSC)** på sidan **Automation-konto** under **Konfigurationshantering**.
1. På sidan **Tillståndskonfiguration (DSC)** klickar du på fliken **Konfigurationer** och sedan på **TestConfig** (namnet på den tidigare importerade konfigurationen).
1. Klicka på **Kompilera**på sidan **TestConfig-konfiguration** och klicka sedan på **Ja**. Detta startar ett kompileringsjobb.

   ![Skärmbild av knappen TestConfig-konfigurationssida som markerar kompileringsknappen](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> När du kompilerar en konfiguration i Azure Automation distribueras automatiskt alla skapade nodkonfigurations-MOFs till pull-servern.

## <a name="viewing-a-compilation-job"></a>Visa ett kompileringsjobb

När du har påbörjat en kompilering kan du visa den på panelen **Kompileringsjobb** på sidan **Konfiguration.** Panelen **Kompileringsjobb** visar jobb som körs, slutförs och misslyckas för närvarande. När du öppnar en samlingsjobbsida visas information om jobbet, inklusive eventuella fel eller varningar som påträffats, indataparametrar som används i konfigurationen och kompileringsloggar.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Till vänster klickar du på **Alla resurser** och sedan namnet på ditt Automation-konto.
1. Klicka på **Tillståndskonfiguration (DSC)** på sidan **Automation-konto** under **Konfigurationshantering**.
1. På sidan **Tillståndskonfiguration (DSC)** klickar du på fliken **Konfigurationer** och sedan på **TestConfig** (namnet på den tidigare importerade konfigurationen).
1. Under **Kompileringsjobb**väljer du det kompileringsjobb som du vill visa. En **sida för kompileringsjobb** öppnas med namnet på det datum då kompileringsjobbet startades.

   ![Skärmbild av sidan Samlingsjobb](./media/automation-dsc-getting-started/CompilationJob.png)

1. Klicka på en panel på sidan **Kompileringsjobb** för att se mer information om jobbet.

## <a name="viewing-node-configurations"></a>Visa nodkonfigurationer

Ett kompileringsjobb har slutförts skapar en eller flera nya nodkonfigurationer. En nodkonfiguration är ett MOF-dokument som distribueras till pull-servern och som är redo att hämtas och tillämpas av en eller flera noder. Du kan visa nodkonfigurationerna i ditt Automation-konto på sidan **Tillståndskonfiguration (DSC).** En nodkonfiguration har ett namn med formuläret *ConfigurationName*. *NodeName*.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Till vänster klickar du på **Alla resurser** och sedan namnet på ditt Automation-konto.
1. Klicka på **Tillståndskonfiguration (DSC)** under **Konfigurationshantering**på **bladet Automation-konto** .
1. Klicka på fliken **Kompilerade konfigurationer** på sidan **Tillståndskonfiguration (DSC).**

   ![Skärmbild av fliken Kompilerade konfigurationer](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Introduktion till en Azure VM för hantering med Azure Automation State Configuration

Du kan använda Azure Automation State Configuration för att hantera virtuella Azure-datorer (både Classic och Resource Manager), lokala virtuella datorer, Linux-datorer, VIRTUELLA AWS-datorer och lokala fysiska datorer. I den här artikeln får du lära dig hur du bara rapporterar virtuella Azure Resource Manager-datorer ombord. Information om introduktion av andra typer av datorer finns i [Onboarding-datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Så här går du ombord på en virtuell virtuell azure Resource Manager-dator för hantering av Azure Automation State Configuration

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Till vänster klickar du på **Alla resurser** och sedan namnet på ditt Automation-konto.
1. Klicka på **Tillståndskonfiguration (DSC)** under **Konfigurationshantering**på **bladet Automation-konto** .
1. Klicka på + Lägg till på sidan **Tillståndskonfiguration (DSC)** medan du klickar på + Lägg till på **fliken** **Munstyckeskonfiguration (DSC).**

   ![Skärmbild av sidan DSC-noder som markerar knappen Lägg till Azure VM](./media/automation-dsc-getting-started/OnboardVM.png)

1. På sidan **Virtuella datorer** väljer du den virtuella datorn.
1. På detaljsidan **för den virtuella datorn** klickar du på + **Anslut**.

   > [!IMPORTANT]
   > Detta måste vara en virtuell dator med Azure Resource Manager som kör ett [operativsystem som stöds](automation-dsc-overview.md#operating-system-requirements).

2. På sidan **Registrering** väljer du namnet på den nodkonfiguration som du vill använda på den virtuella datorn i rutan **Nodkonfigurationsnamn.** Det är valfritt att ange ett namn i det här läget. Du kan ändra den tilldelade nodkonfigurationen när noden har onboardats.
   Markera **Starta om noden om det behövs**och klicka sedan på **OK**.

   ![Skärmbild av registreringsbladet](./media/automation-dsc-getting-started/RegisterVM.png)

   Nodkonfigurationen som du angav tillämpas på den virtuella datorn med intervall som anges av **konfigurationslägesfrekvensen**och den virtuella datorn söker efter uppdateringar av nodkonfigurationen med intervall som anges av **uppdateringsfrekvensen**. Mer information om hur dessa värden används finns i [Konfigurera Den lokala konfigurationshanteraren](/powershell/scripting/dsc/managing-nodes/metaConfig).

Azure startar processen för introduktion av den virtuella datorn. När den är klar visas den virtuella datorn på fliken **Noder** på sidan **Tillståndskonfiguration (DSC)** i Automation-kontot.

## <a name="viewing-the-list-of-managed-nodes"></a>Visa listan över hanterade noder

Du kan visa listan över alla datorer som har tagits ombord för hantering i ditt Automation-konto på fliken **Noder** på sidan **Tillståndskonfiguration (DSC).**

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Till vänster klickar du på **Alla resurser** och sedan namnet på ditt Automation-konto.
1. Klicka på **Tillståndskonfiguration (DSC)** under **Konfigurationshantering**på **bladet Automation-konto** .
1. Klicka på fliken **Noder** på sidan **Tillståndskonfiguration (DSC).**

## <a name="viewing-reports-for-managed-nodes"></a>Visa rapporter för hanterade noder

Varje gång Azure Automation State Configuration utför en konsekvenskontroll på en hanterad nod skickar noden tillbaka en statusrapport till pull-servern. Du kan visa dessa rapporter på sidan för den noden.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Till vänster klickar du på **Alla resurser** och sedan namnet på ditt Automation-konto.
1. Klicka på **Tillståndskonfiguration (DSC)** under **Konfigurationshantering**på **bladet Automation-konto** .
1. Klicka på fliken **Noder** på sidan **Tillståndskonfiguration (DSC).** Här kan du se översikten över konfigurationstillståndet och information om varje nod.

   ![Skärmbild av nodsidan](./media/automation-dsc-getting-started/NodesTab.png)

1. Klicka på nodposten på fliken Noder för att öppna rapporteringen på fliken **Noder.** Klicka på den rapport som du vill visa ytterligare rapporteringsinformation.

   ![Skärmbild av rapportbladet](./media/automation-dsc-getting-started/NodeReport.png)

På bladet för en enskild rapport kan du se följande statusinformation för motsvarande konsekvenskontroll:

- Rapportstatusen – oavsett om noden är "Kompatibel", konfigurationen "Misslyckades" eller noden är "Inte kompatibel" (när noden är i **ApplyandMonitor-läge** och datorn inte är i önskat tillstånd).
- Starttiden för konsekvenskontrollen.
- Den totala körningen för konsekvenskontrollen.
- Typ av konsekvenskontroll.
- Eventuella fel, inklusive felkod och felmeddelande.
- Alla DSC-resurser som används i konfigurationen och tillståndet för varje resurs (om noden är i önskat tillstånd för den resursen) – du kan klicka på varje resurs för att få mer detaljerad information för den resursen.
- Namn, IP-adress och konfigurationsläge för noden.

Du kan också klicka på **Visa rårapport** om du vill visa de faktiska data som noden skickar till servern.
Mer information om hur du använder dessa data finns i [Använda en DSC-rapportserver](/powershell/scripting/dsc/pull-server/reportserver).

Det kan ta lite tid när en nod är inbyggd innan den första rapporten är tillgänglig. Du kan behöva vänta upp till 30 minuter på den första rapporten när du har hämtat en nod.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Tilldela om en nod till en annan nodkonfiguration

Du kan tilldela en nod att använda en annan nodkonfiguration än den du ursprungligen tilldelade.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Till vänster klickar du på **Alla resurser** och sedan namnet på ditt Automation-konto.
1. Klicka på **Tillståndskonfiguration (DSC)** under **Konfigurationshantering**på **bladet Automation-konto** .
1. Klicka på fliken **Noder** på sidan **Tillståndskonfiguration (DSC).**
1. Klicka på namnet på den nod som du vill tilldela om på fliken **Noder.**
1. Klicka på **Tilldela nodkonfiguration**på sidan för den noden .

    ![Skärmbild av sidan Nodinformation som markerar knappen Tilldela nodkonfiguration](./media/automation-dsc-getting-started/AssignNode.png)

1. På sidan **Tilldela nodkonfiguration** väljer du den nodkonfiguration som du vill tilldela noden till och klickar sedan på **OK**.

    ![Skärmbild av sidan Tilldela nodkonfiguration](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Avregistrera en nod

Om du inte längre vill att en nod ska hanteras av Azure Automation DSC kan du avregistrera den.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Till vänster klickar du på **Alla resurser** och sedan namnet på ditt Automation-konto.
1. Klicka på **Tillståndskonfiguration (DSC)** under **Konfigurationshantering**på **bladet Automation-konto** .
1. Klicka på fliken **Noder** på sidan **Tillståndskonfiguration (DSC).**
1. Klicka på namnet på den nod som du vill avregistrera på fliken **Noder.**
1. Klicka på **Avregistrera**på sidan för den noden .

    ![Skärmbild av sidan Nodinformation som markerar knappen Avregistrera](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Relaterade artiklar

- [Översikt över konfigurationen av Azure Automation State](automation-dsc-overview.md)
- [Introduktionsdatorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md)
- [Översikt över önskad tillståndskonfiguration i Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Cmdlets för konfiguration av Azure Automation State](/powershell/module/azurerm.automation/#automation)
- [Prissättning för konfiguration av Azure Automation-tillstånd](https://azure.microsoft.com/pricing/details/automation/)
