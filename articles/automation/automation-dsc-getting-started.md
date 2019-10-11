---
title: Komma igång med konfiguration av Azure Automation tillstånd
description: Förklaring och exempel på de vanligaste uppgifterna i Azure Automation State Configuration (DSC)
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f943aac4a91217983963fac6f8d0b2b3ba6895a1
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243611"
---
# <a name="getting-started-with-azure-automation-state-configuration"></a>Komma igång med konfiguration av Azure Automation tillstånd

I den här artikeln förklaras hur du utför de vanligaste uppgifterna med Azure Automation tillstånds konfiguration, till exempel skapa, importera och kompilera konfigurationer, onboarding-datorer som ska hanteras och Visa rapporter. En översikt över hur Azure Automation tillstånds konfiguration finns i [Översikt över Azure Automation tillstånds konfiguration](automation-dsc-overview.md). För dokumentation om Desired State Configuration (DSC), se [Översikt över Desired State Configuration i Windows PowerShell](/powershell/scripting/dsc/overview/overview).

Den här artikeln innehåller en steg-för-steg-guide om hur du använder Azure Automation tillstånds konfiguration. Om du vill ha en exempel miljö som redan har kon figurer ATS utan att följa stegen som beskrivs i den här artikeln kan du använda följande Resource Manager-mall: [Azure Automation hanterad Node-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Den här mallen konfigurerar en slutförd konfigurations miljö för Azure Automation tillstånd, inklusive en virtuell Azure-dator som hanteras av Azure Automation tillstånds konfiguration.

## <a name="prerequisites"></a>Krav

Följande krävs för att slutföra exemplen i den här artikeln:

- Ett Azure Automation-konto. Instruktioner om hur du skapar ett Kör som-konto för Azure Automation finns i [Azure Kör som-konto](automation-sec-configure-azure-runas-account.md).
- En Azure Resource Manager virtuell dator (inte klassisk) som kör ett [operativ system som stöds](automation-dsc-overview.md#operating-system-requirements). Instruktioner om hur du skapar en virtuell dator finns i [Skapa din första virtuella Windows-dator i Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Skapa en DSC-konfiguration

Du kan skapa en enkel [DSC-konfiguration](/powershell/scripting/dsc/configurations/configurations) som garanterar antingen närvaron eller frånvaron av Windows-funktionen i **Web Server** (IIS), beroende på hur du tilldelar noder.

1. Starta [VSCode](https://code.visualstudio.com/docs) (eller valfri text redigerare).
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

Den här konfigurationen anropar en resurs i varje Node-block, den [WindowsFeature-resurs](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource)som garanterar antingen närvaron eller frånvaron av **webb server** funktionen.

## <a name="importing-a-configuration-into-azure-automation"></a>Importera en konfiguration till Azure Automation

Därefter importerar du konfigurationen till Automation-kontot.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På sidan **Automation-konto** väljer du **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan **tillstånds konfiguration (DSC)** klickar du på fliken **konfigurationer** och sedan på **+ Lägg till**.
1. På sidan **Importera konfiguration** bläddrar du till filen `TestConfig.ps1` på datorn.

   ![Skärm bild av bladet * * import Configuration * *](./media/automation-dsc-getting-started/AddConfig.png)

1. Klicka på **OK**

## <a name="viewing-a-configuration-in-azure-automation"></a>Visa en konfiguration i Azure Automation

När du har importerat en konfiguration kan du Visa den i Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På sidan **Automation-konto** väljer du **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan **tillstånds konfiguration (DSC)** klickar du på fliken **konfigurationer** och sedan på **TestConfig** (det här är namnet på den konfiguration som du importerade i föregående procedur).
1. På sidan **konfiguration av TestConfig** klickar du på **Visa konfigurations källa**.

   ![Skärm bild av bladet TestConfig konfiguration](./media/automation-dsc-getting-started/ViewConfigSource.png)

   En **TestConfig konfigurations käll** sida öppnas och visar PowerShell-koden för konfigurationen.

## <a name="compiling-a-configuration-in-azure-automation"></a>Kompilera en konfiguration i Azure Automation

Innan du kan använda ett önskat tillstånd för en nod, måste en DSC-konfiguration som definierar det läget kompileras till ett eller flera nodkonfigurationer (MOF-dokument) och placeras på Automation DSC hämtnings Server. En mer detaljerad beskrivning av hur du kompilerar konfigurationer i Azure Automation tillstånds konfiguration finns i [kompilera konfigurationer i Azure Automation tillstånds konfiguration](automation-dsc-compile.md).
Mer information om hur du kompilerar konfigurationer finns i [DSC-konfigurationer](/powershell/scripting/dsc/configurations/configurations).

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På sidan **Automation-konto** klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan **tillstånds konfiguration (DSC)** klickar du på fliken **konfigurationer** och sedan på **TestConfig** (namnet på den tidigare importerade konfigurationen).
1. På sidan **konfiguration av TestConfig** klickar du på **kompilera**och sedan på **Ja**. Detta startar ett kompilerings jobb.

   ![Skärm bild av knappen kompilera i TestConfig konfigurations sida](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> När du kompilerar en konfiguration i Azure Automation distribueras automatiskt alla skapade konfigurations MOF: ar till hämtnings servern.

## <a name="viewing-a-compilation-job"></a>Visa ett Compilation-jobb

När du har startat en kompilering kan du Visa den på panelen för att **kompilera jobb** på **konfigurations** sidan. På panelen **Compilation Jobs** visas pågående, slutförda och misslyckade jobb. När du öppnar en Compilation-sida visas information om det jobbet, inklusive eventuella fel eller varningar som påträffats, indataparametrar som används i konfigurations-och kompilerings loggar.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På sidan **Automation-konto** klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan **tillstånds konfiguration (DSC)** klickar du på fliken **konfigurationer** och sedan på **TestConfig** (namnet på den tidigare importerade konfigurationen).
1. Under **Compilation Jobs**väljer du det Compilation-jobb som du vill visa. Sidan för **Compilation-jobb** öppnas med det datum då Compilation-jobbet startades.

   ![Skärm bild av sidan för att kompilera jobb](./media/automation-dsc-getting-started/CompilationJob.png)

1. Klicka på valfri panel på sidan för att **kompilera jobb** om du vill visa mer information om jobbet.

## <a name="viewing-node-configurations"></a>Visa nodkonfigurationer

Slutfört slut för ande av ett Compilation-jobb skapar en eller flera nya nodkonfigurationer. En Node-konfiguration är ett MOF-dokument som distribueras till pull-servern och är redo att hämtas och tillämpas av en eller flera noder. Du kan visa nodkonfigurationer i ditt Automation-konto på sidan för **tillstånds konfiguration (DSC)** . En Node-konfiguration har ett namn med formatet *ConfigurationName*. *Nodnamn*.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På bladet **Automation-konto** klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan **tillstånds konfiguration (DSC)** klickar du på fliken **kompilerade konfigurationer** .

   ![Skärm bild av fliken kompilerade konfigurationer](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Publicera en virtuell Azure-dator för hantering med Azure Automation tillstånds konfiguration

Du kan använda Azure Automation tillstånds konfiguration för att hantera virtuella Azure-datorer (både klassiska och Resource Manager), lokala virtuella datorer, Linux-datorer, virtuella AWS-datorer och lokala fysiska datorer. I den här artikeln får du lära dig hur du endast registrerar Azure Resource Manager virtuella datorer. Information om hur du registrerar andra typer av datorer finns i [onboarding Machines for Management by Azure Automation State Configuration](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>För att publicera en Azure Resource Manager virtuell dator för hantering genom Azure Automation tillstånds konfiguration

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På bladet **Automation-konto** klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan för **tillstånds konfiguration (DSC)** går du till fliken **noder** och klickar på **+ Lägg till**.

   ![Skärm bild av sidan DSC-noder som markerar knappen Lägg till virtuell Azure-dator](./media/automation-dsc-getting-started/OnboardVM.png)

1. På sidan **Virtual Machines** väljer du den virtuella datorn.
1. Klicka på **+ Anslut**på sidan information om **virtuell dator** .

   > [!IMPORTANT]
   > Detta måste vara en Azure Resource Manager virtuell dator som kör ett [operativ system som stöds](automation-dsc-overview.md#operating-system-requirements).

2. På sidan **registrering** väljer du namnet på den Node-konfiguration som du vill tillämpa på den virtuella datorn i rutan **konfigurations namn för noden** . Det är valfritt att ange ett namn i det här läget. Du kan ändra den tilldelade nodens konfiguration efter att noden har registrerats.
   Markera **noden omstart om det behövs**och klicka sedan på **OK**.

   ![Skärm bild av registrerings bladet](./media/automation-dsc-getting-started/RegisterVM.png)

   Den Node-konfiguration som du har angett tillämpas på den virtuella datorn med intervall som anges av **frekvensen för konfigurations läge**och den virtuella datorn söker efter uppdateringar av nodens konfiguration enligt intervall som anges i **uppdaterings frekvensen**. Mer information om hur dessa värden används finns i [Konfigurera den lokala Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

Azure startar processen med att onboarda den virtuella datorn. När den är klar visas den virtuella datorn på fliken **noder** på sidan för **tillstånds konfiguration (DSC)** i Automation-kontot.

## <a name="viewing-the-list-of-managed-nodes"></a>Visa listan över hanterade noder

Du kan visa listan över alla datorer som har registrerats för hantering i ditt Automation-konto på fliken **noder** på sidan för **tillstånds konfiguration (DSC)** .

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På bladet **Automation-konto** klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan **State Configuration (DSC)** klickar du på fliken **noder** .

## <a name="viewing-reports-for-managed-nodes"></a>Visa rapporter för hanterade noder

Varje gången Azure Automation tillstånds konfiguration utför en konsekvens kontroll på en hanterad nod, skickar noden en status rapport tillbaka till hämtnings servern. Du kan visa rapporterna på sidan för noden.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På bladet **Automation-konto** klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan **State Configuration (DSC)** klickar du på fliken **noder** . Här kan du se en översikt över konfigurations status och information för varje nod.

   ![Skärm bild av Node-sida](./media/automation-dsc-getting-started/NodesTab.png)

1. På fliken **noder** klickar du på nodens post för att öppna rapporten. Klicka på rapporten som du vill visa mer information om rapporter.

   ![Skärm bild av rapport bladet](./media/automation-dsc-getting-started/NodeReport.png)

På bladet för en enskild rapport kan du se följande status information för motsvarande konsekvens kontroll:

- Rapport statusen – oavsett om noden är "kompatibel", konfigurationen "misslyckades" eller noden är "inte kompatibel" (när noden är i **ApplyandMonitor** -läge och datorn inte har önskat tillstånd).
- Start tiden för konsekvens kontrollen.
- Den totala körningen för konsekvens kontroll.
- Typ av konsekvens kontroll.
- Eventuella fel, inklusive felkoden och fel meddelandet.
- Alla DSC-resurser som används i konfigurationen och statusen för varje resurs (om noden har önskat tillstånd för den resursen) – du kan klicka på varje resurs för att få mer detaljerad information om resursen.
- Nodens namn, IP-adress och konfigurations läge.

Du kan också klicka på **Visa rå rapport** om du vill se de faktiska data som noden skickar till servern.
Mer information om hur du använder dessa data finns i [använda en DSC-rapport Server](/powershell/scripting/dsc/pull-server/reportserver).

Det kan ta lite tid efter att en nod har publicerats innan den första rapporten är tillgänglig. Du kan behöva vänta upp till 30 minuter för den första rapporten när du har publicerat en nod.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Tilldela om en nod till en annan nod-konfiguration

Du kan tilldela en nod till att använda en annan nods konfiguration än den som du ursprungligen tilldelade.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På bladet **Automation-konto** klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan **State Configuration (DSC)** klickar du på fliken **noder** .
1. På fliken **noder** klickar du på namnet på den nod som du vill omtilldela.
1. Klicka på **tilldela konfiguration av nod**på sidan för noden.

    ![Skärm bild av sidan information om nod som markerar knappen tilldela nod-konfiguration](./media/automation-dsc-getting-started/AssignNode.png)

1. På sidan **tilldela konfiguration av nod** väljer du den Node-konfiguration som du vill tilldela noden till och klickar sedan på **OK**.

    ![Skärm bild av sidan tilldela konfiguration av nod](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Avregistrera en nod

Om du inte längre vill att en nod ska hanteras av Azure Automation DSC kan du avregistrera den.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På bladet **Automation-konto** klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan **State Configuration (DSC)** klickar du på fliken **noder** .
1. På fliken **noder** klickar du på namnet på den nod som du vill avregistrera.
1. Klicka på **avregistrera**på sidan för noden.

    ![Skärm bild av sidan information om noden som markerar knappen avregistrera](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Relaterade artiklar

- [Översikt över Azure Automation tillstånds konfiguration](automation-dsc-overview.md)
- [Onboarding-datorer för hantering genom Azure Automation tillstånds konfiguration](automation-dsc-onboarding.md)
- [Översikt över önskad tillstånds konfiguration i Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Cmdletar för Azure Automation tillstånds konfiguration](/powershell/module/azurerm.automation/#automation)
- [Prissättning för Azure Automations tillstånds konfiguration](https://azure.microsoft.com/pricing/details/automation/)
