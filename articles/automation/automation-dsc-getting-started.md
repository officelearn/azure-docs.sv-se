---
title: "Komma igång med Azure Automation DSC | Microsoft Docs"
description: "Förklaring och exempel på de vanligaste uppgifterna i Azure Automation önskad tillstånd Configuration (DSC)"
services: automation
documentationcenter: na
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: a3816593-70a3-403b-9a43-d5555fd2cee2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/21/2016
ms.author: magoedte;eslesar
ms.openlocfilehash: 8a10d961ad7c107c68b57c64ee6c88544ff8832b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="getting-started-with-azure-automation-dsc"></a>Komma igång med Azure Automation DSC
Det här avsnittet beskriver hur du utför de vanligaste uppgifterna med Azure Automation önskad tillstånd Configuration (DSC), till exempel att skapa, importera, och kompilering konfigurationer, onboarding datorer för att hantera, och visa rapporter. En översikt över vilka Azure Automation DSC är finns [översikt över Azure Automation DSC](automation-dsc-overview.md). DSC-dokumentation finns [Windows PowerShell Desired Configuration översikt över](https://msdn.microsoft.com/PowerShell/dsc/overview).

Det här avsnittet innehåller stegvisa instruktioner till med hjälp av Azure Automation DSC. Om du vill att en exempel-miljö som redan har konfigurerat utan att följa stegen som beskrivs i det här avsnittet, kan du använda [följande ARM-mallen](https://github.com/azureautomation/automation-packs/tree/master/102-sample-automation-setup). Den här mallen ställer in en slutförd Azure Automation DSC-miljö, inklusive en Azure-dator som hanteras av Azure Automation DSC.

## <a name="prerequisites"></a>Krav
Om du vill utföra exemplen i det här avsnittet, krävs följande:

* Ett Azure Automation-konto. Instruktioner om hur du skapar ett Azure Automation kör som-konto finns i [Azure kör som-konto](automation-sec-configure-azure-runas-account.md).
* En Azure Resource Manager VM (inte klassiskt) kör Windows Server 2008 R2 eller senare. Anvisningar om hur du skapar en virtuell dator finns [skapa din första Windows-dator i Azure-portalen](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Skapa en DSC-konfiguration
Vi skapar en enkel [DSC-konfigurationen](https://msdn.microsoft.com/powershell/dsc/configurations) som säkerställer förekomsten eller frånvaron av den **Web Server** Windows funktionen (IIS), beroende på hur du tilldelar noder.

1. Starta Windows PowerShell ISE (eller valfri textredigerare).
2. Skriv följande text:
   
    ```powershell
    configuration TestConfig
    {
        Node WebServer
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
3. Spara filen som `TestConfig.ps1`.

Den här konfigurationen anropar en resurs i varje nod i block i [WindowsFeature resurs](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource), som säkerställer förekomsten eller frånvaron av den **Web Server** funktion.

## <a name="importing-a-configuration-into-azure-automation"></a>Importera en konfiguration i Azure Automation
Vi kan sedan importera konfigurationen till Automation-kontot.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn klickar du på **alla resurser** och sedan namnet på ditt Automation-konto.
3. På den **automatiseringskontot** bladet, klickar du på **DSC-konfigurationer**.
4. På den **DSC-konfigurationer** bladet, klickar du på **lägga till en konfiguration**.
5. På den **importkonfigurationen** bladet, bläddra till den `TestConfig.ps1` filen på datorn.
   
    ![Skärmbild av den ** importera konfigurationen ** bladet](./media/automation-dsc-getting-started/AddConfig.png)
6. Klicka på **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Visa en konfiguration i Azure Automation
När du har importerat en konfiguration, kan du visa den i Azure-portalen.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn klickar du på **alla resurser** och sedan namnet på ditt Automation-konto.
3. På den **automatiseringskontot** bladet, klickar du på **DSC-konfigurationer**
4. På den **DSC-konfigurationer** bladet, klickar du på **TestConfig** (detta är namnet på den konfiguration som du har importerat i föregående procedur).
5. På den **TestConfig Configuration** bladet, klickar du på **visa konfigurationskälla**.
   
    ![Skärmbild av bladet TestConfig konfiguration](./media/automation-dsc-getting-started/ViewConfigSource.png)
   
    En **TestConfig konfigurationskälla** öppnas bladet med PowerShell-koden för konfigurationen.

## <a name="compiling-a-configuration-in-azure-automation"></a>Kompilera en konfiguration i Azure Automation
Innan du kan tillämpa en tillstånd till en nod, måste en DSC-konfiguration som definierar det aktuella tillståndet vara kompileras till ett eller flera nodkonfigurationer (MOF dokument) och placeras i Automation DSC Pull-Server. En mer detaljerad beskrivning av kompilering konfigurationer i Azure Automation DSC, se [kompilering konfigurationer i Azure Automation DSC](automation-dsc-compile.md). Mer information om kompilering konfigurationer finns [DSC-konfigurationer](https://msdn.microsoft.com/PowerShell/DSC/configurations).

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn klickar du på **alla resurser** och sedan namnet på ditt Automation-konto.
3. På den **automatiseringskontot** bladet, klickar du på **DSC-konfigurationer**
4. På den **DSC-konfigurationer** bladet, klickar du på **TestConfig** (namnet på den tidigare importerat konfigurationen).
5. På den **TestConfig Configuration** bladet, klickar du på **Kompilera**, och klicka sedan på **Ja**. Detta startar ett jobb för kompilering.
   
    ![Skärmbild av bladet TestConfig configuration syntaxmarkering kompilera knappen](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> När du kompilerar en konfiguration i Azure Automation distribuerar automatiskt alla skapade nodkonfiguration MOF-filer till den pull-servern.
> 
> 

## <a name="viewing-a-compilation-job"></a>Visa en kompileringsjobbet
När du startar en sammanställning, du kan visa den i den **Kompileringsjobb** panelen i den **Configuration** bladet. Den **Kompileringsjobb** panelen visar för närvarande körs slutfört och misslyckade jobb. När du öppnar en sammanställning jobb-bladet visas information om det jobbet inklusive fel eller varningar uppstod, indataparametrar som används i konfigurationen och kompilering loggar.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn klickar du på **alla resurser** och sedan namnet på ditt Automation-konto.
3. På den **automatiseringskontot** bladet, klickar du på **DSC-konfigurationer**.
4. På den **DSC-konfigurationer** bladet, klickar du på **TestConfig** (namnet på den tidigare importerat konfigurationen).
5. På den **Kompileringsjobb** panelen av den **TestConfig Configuration** bladet, klickar du på någon av de jobb som visas. En **Kompileringsjobbet** blad öppnas med det datum då kompileringsjobbet startades.
   
    ![Skärmbild av bladet Kompileringsjobbet](./media/automation-dsc-getting-started/CompilationJob.png)
6. Klicka på panelen i den **Kompileringsjobbet** bladet för att se ytterligare information om jobbet.

## <a name="viewing-node-configurations"></a>Visa nodkonfigurationer
Slutförande av en kompileringsjobbet skapar en eller flera nya nodkonfigurationer. Konfiguration av noden är en MOF-dokument som har distribuerats till den pull-servern och redo att dras in och används av en eller flera noder. Du kan visa nodkonfigurationer i ditt Automation-konto i den **DSC-Nodkonfigurationer** bladet. Konfiguration av noden har ett namn med formatet *ConfigurationName*. *Nodnamn*.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn klickar du på **alla resurser** och sedan namnet på ditt Automation-konto.
3. På den **automatiseringskontot** bladet, klickar du på **DSC-Nodkonfigurationer**.
   
    ![Skärmbild av bladet DSC-Nodkonfigurationer](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>Ett Azure virtuella datorn för hantering med Azure Automation DSC
Du kan använda Azure Automation DSC för att hantera virtuella Azure-datorer (både klassiska och hanteraren för filserverresurser), lokala virtuella datorer, Linux-datorer, AWS virtuella datorer och lokala fysiska datorer. I det här avsnittet beskriver vi hur du publicera endast Azure Resource Manager virtuella datorer. Information om onboarding finns andra typer av datorer, [Onboarding datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>Publicera en Azure Resource Manager-VM för hantering av Azure Automation DSC
1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn klickar du på **alla resurser** och sedan namnet på ditt Automation-konto.
3. På den **automatiseringskontot** bladet, klickar du på **DSC-noder**.
4. I den **DSC-noder** bladet, klickar du på **lägga till Azure VM**.
   
    ![Skärmbild av bladet DSC-noder Markera knappen Lägg till Azure VM](./media/automation-dsc-getting-started/OnboardVM.png)
5. I den **lägga till virtuella Azure-datorer** bladet, klickar du på **Välj virtuella datorer som ska publiceras**.
6. I den **Välj virtuella datorer** bladet, Välj den virtuella datorn som du vill publicera och klickar på **OK**.
   
   > [!IMPORTANT]
   > Det här måste vara en Azure Resource Manager VM kör Windows Server 2008 R2 eller senare.
   > 
   > 
7. I den **lägga till virtuella Azure-datorer** bladet, klickar du på **konfigurera registreringsdata**.
8. I den **registrering** bladet anger du namnet på nodkonfigurationen som du vill koppla till den virtuella datorn i den **Nodkonfigurationsnamnet** rutan. Detta måste exakt matcha namnet på en nodkonfiguration i Automation-kontot. Att tillhandahålla ett namn i det här läget är valfritt. Du kan ändra den tilldelade nodkonfigurationen efter onboarding noden.
   Kontrollera **starta om noden om det behövs**, och klicka sedan på **OK**.
   
    ![Skärmbild av bladet registrering](./media/automation-dsc-getting-started/RegisterVM.png)
   
    Nodkonfiguration som du angett tillämpas på den virtuella datorn med intervall som anges av den **Konfigurationslägesfrekvens**, och den virtuella datorn kommer att söka efter uppdateringar till nodkonfiguration med intervall som anges av den **uppdateringsfrekvens**. Läs mer om hur dessa värden används [konfigurera den lokala Configuration Manager](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
9. I den **lägga till virtuella Azure-datorer** bladet, klickar du på **skapa**.

Azure kommer att starta processen för den virtuella datorn. När den är klar, den virtuella datorn kommer att visas i den **DSC-noder** bladet i Automation-kontot.

## <a name="viewing-the-list-of-dsc-nodes"></a>Visa listan över DSC-noder
Du kan visa listan över alla datorer som har publicerats så för hantering i ditt Automation-konto i den **DSC-noder** bladet.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn klickar du på **alla resurser** och sedan namnet på ditt Automation-konto.
3. På den **automatiseringskontot** bladet, klickar du på **DSC-noder**.

## <a name="viewing-reports-for-dsc-nodes"></a>Visa rapporter för DSC-noder
Varje gång som Azure Automation DSC utför en konsekvenskontroll på en hanterad nod skickar noden en statusrapport tillbaka till den pull-servern. Du kan visa rapporterna på bladet för noden.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn klickar du på **alla resurser** och sedan namnet på ditt Automation-konto.
3. På den **automatiseringskontot** bladet, klickar du på **DSC-noder**.
4. På den **rapporter** panelen, klickar du på någon av rapporterna i listan.
   
    ![Skärmbild av bladet rapport](./media/automation-dsc-getting-started/NodeReport.png)

I bladet för en enskild rapport kan se du följande statusinformation för motsvarande konsekvenskontrollen:

* Rapportstatus – om noden är ”kompatibla”, ”misslyckades” konfigurationen eller noden ”inkompatibla” (när noden är i **applyandmonitor** läge och datorn inte är i önskad tillstånd).
* Starttid för konsekvenskontrollen.
* Den totala körtiden för konsekvenskontrollen.
* Typ av konsekvenskontroll.
* Fel, inklusive felkoden och felmeddelande. 
* Alla DSC-resurser som används i konfigurationen och statusen för varje resurs (om noden är i tillståndet önskade för den här resursen) – du kan klicka på varje resurs för att få mer detaljerad information för den här resursen.
* Namn, IP-adress och konfigurationsläge för noden.

Du kan också klicka på **Visa rapport om oformaterat** att se de faktiska data som noden skickar till servern. Mer information om hur du använder dessa data finns [med hjälp av en rapportserver för DSC](https://msdn.microsoft.com/powershell/dsc/reportserver).

Det kan ta lite tid när en nod har publicerats så innan den första rapporten är tillgänglig. Du kan behöva vänta upp till 30 minuter för den första rapporten när du har registrerat en nod.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Tilldela om en nod till en annan nod-konfiguration
Du kan tilldela en nod ska använda en annan nodkonfiguration än den som ursprungligen tilldelades.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn klickar du på **alla resurser** och sedan namnet på ditt Automation-konto.
3. På den **automatiseringskontot** bladet, klickar du på **DSC-noder**.
4. På den **DSC-noder** bladet, klickar du på namnet på den nod som du vill tilldela.
5. Klicka på bladet för noden **tilldela noden**.
   
    ![Skärmbild av bladet nod Markera knappen Tilldela noden](./media/automation-dsc-getting-started/AssignNode.png)
6. På den **tilldela nodkonfiguration** bladet välj nodkonfiguration som du vill tilldela noden och klicka sedan på **OK**.
   
    ![Skärmbild av bladet tilldela nodkonfiguration](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Avregistrerar en nod
Om du inte längre vill att en nod som ska hanteras av Azure Automation DSC kan du avregistrera det.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn klickar du på **alla resurser** och sedan namnet på ditt Automation-konto.
3. På den **automatiseringskontot** bladet, klickar du på **DSC-noder**.
4. På den **DSC-noder** bladet, klickar du på namnet på den nod som du vill avregistrera.
5. Klicka på bladet för noden **avregistrera**.
   
    ![Skärmbild av bladet nod syntaxmarkering Unregister-knappen](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Relaterade artiklar
* [Översikt över Azure Automation DSC](automation-dsc-overview.md)
* [Onboarding-datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md)
* [Windows PowerShell Desired State Configuration-översikt](https://msdn.microsoft.com/powershell/dsc/overview)
* [Azure Automation DSC-cmdlets](/powershell/module/azurerm.automation/#automation)
* [Priser för Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)

