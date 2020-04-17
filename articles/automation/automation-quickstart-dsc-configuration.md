---
title: Azure snabbstart – Konfigurera en virtuell dator med DSC | Microsoft Docs
description: Konfigurera en LAMP-stack på en virtuell Linux-dator med DSC (Desired State Configuration)
services: automation
ms.subservice: dsc
keywords: dsc, configuration, automation
ms.date: 11/06/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 1a146ab7c05d200b71a33a72fa6362c3cf62629a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457526"
---
# <a name="configure-a-virtual-machine-with-desired-state-configuration"></a>Konfigurera en virtuell dator med önskad tillståndskonfiguration

Genom att aktivera Azure Automation State Configuration kan du hantera och övervaka konfigurationerna för dina Windows- och Linux-servrar med DSC (Desired State Configuration). Konfigurationer som driver från en önskad konfiguration kan identifieras eller korrigeras automatiskt. Den här snabbstarten går igenom registrering på en virtuell Linux-dator och distribution av en LAMP-stack med DSC.

## <a name="prerequisites"></a>Krav

Följande krävs för att slutföra den här snabbstarten:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/).
* Ett Azure Automation-konto. Instruktioner om hur du skapar ett Kör som-konto för Azure Automation finns i [Azure Kör som-konto](automation-sec-configure-azure-runas-account.md).
* En virtuell Azure Resource Manager-dator (inte klassisk) med Red Hat Enterprise Linux, CentOS eller Oracle Linux. Instruktioner om hur du skapar en virtuell dator finns i [Skapa din första virtuella Linux-dator i Azure Portal](../virtual-machines/linux/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på https://portal.azure.comAzure på .

## <a name="onboard-a-virtual-machine"></a>Registrera en virtuell dator

Det finns många olika metoder för att ombord på en maskin och aktivera DSC. I den här snabbstarten beskrivs registrering via ett Automation-konto. Du kan läsa mer om olika metoder för att gå in på dina datorer till tillståndskonfiguration genom att läsa [onboarding](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding) artikeln.

1. I det vänstra fönstret i Azure Portal väljer du **Automation-konton**. Om den inte visas i den vänstra rutan klickar du på **Alla tjänster** och söker i den resulterande vyn.
1. Välj ett Automation-konto i listan.
1. I det vänstra fönstret i Automation-kontot väljer du **Tillståndskonfiguration (DSC)**.
2. Klicka på **Lägg till** att öppna sidan för att välja virtuell dator.
3. Leta reda på den virtuella datorn som dsc kan aktiveras för. Du kan använda sökfältet och filteralternativ för att hitta en viss virtuell dator.
4. Klicka på den virtuella datorn och klicka sedan på **Anslut**
5. Välj lämpliga DSC-inställningar för den virtuella datorn. Om du redan har förberett en konfiguration `Node Configuration Name`kan du ange den som . Du kan ställa in [konfigurationsläget](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaConfig) på att styra datorns konfigurationsbeteende.
6. Klicka på **OK**. Medan DSC-tillägget distribueras till den virtuella datorn `Connecting`visas statusen som .

![Registrera en virtuell Azure-dator med DSC](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

## <a name="import-modules"></a>Importera moduler

Moduler innehåller DSC-resurser och många finns i [PowerShell-galleriet](https://www.powershellgallery.com). Alla resurser som används i dina konfigurationer måste importeras till Automation-kontot innan kompilering. För de här självstudierna krävs modulen med namnet **nx**.

1. Välj **Moduler galleri** under **Delade resurser**i den vänstra rutan i Automation-kontot.
1. Sök efter modulen som ska importeras `nx`genom att skriva en del av namnet: .
1. Klicka på modulen för att importera.
1. Klicka på **Importera**.

![Importera en DSC-modul](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>Importera konfigurationen

I den här snabbstarten används en DSC-konfiguration som konfigurerar Apache HTTP Server, MySQL och PHP på datorn. Se [DSC-konfigurationer](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations).

Skriv följande i en textredigerare och spara det lokalt som **AMPServer.ps1**.

```powershell-interactive
configuration LAMPServer {
   Import-DSCResource -module "nx"

   Node localhost {

        $requiredPackages = @("httpd","mod_ssl","php","php-mysql","mariadb","mariadb-server")
        $enabledServices = @("httpd","mariadb")

        #Ensure packages are installed
        ForEach ($package in $requiredPackages){
            nxPackage $Package{
                Ensure = "Present"
                Name = $Package
                PackageManager = "yum"
            }
        }

        #Ensure daemons are enabled
        ForEach ($service in $enabledServices){
            nxService $service{
                Enabled = $true
                Name = $service
                Controller = "SystemD"
                State = "running"
            }
        }
   }
}
```

Importera konfigurationen:

1. I det vänstra fönstret i Automation-kontot väljer du **Tillståndskonfiguration (DSC)** och klickar sedan på fliken **Konfigurationer**.
2. Klicka på **+ Lägg till**.
3. Välj den konfigurationsfil som du sparade i föregående steg.
4. Klicka på **OK**.

## <a name="compile-a-configuration"></a>Kompilera en konfiguration

Du måste kompilera en DSC-konfiguration till en nodkonfiguration (MOF-dokument) innan den kan tilldelas en nod. Kompilering verifierar konfigurationen och tillåter indata för parametervärden. Mer information om hur du kompilerar en konfiguration finns [i Kompilera konfigurationer i tillståndskonfiguration](automation-dsc-compile.md).

1. I den vänstra rutan i Automation-kontot väljer du **Tillståndskonfiguration (DSC)** och klickar sedan på fliken **Konfigurationer.**
1. Välj konfigurationen `LAMPServer`.
1. Välj **Kompilera** på menyalternativen och klicka sedan på **Ja**.
1. I konfigurationsvyn visas ett nytt kompileringsjobb i kö. När jobbet har slutförts kan du gå vidare till nästa steg. Om det finns några fel kan du klicka på kompileringsjobbet för mer information.

## <a name="assign-a-node-configuration"></a>Tilldela en nodkonfiguration

Du kan tilldela en kompilerad nodkonfiguration till en DSC-nod. Tilldelningen tillämpar konfigurationen på datorn och övervakar eller korrigerar automatiskt för all avdrift från den konfigurationen.

1. I den vänstra rutan i Automation-kontot väljer du **Tillståndskonfiguration (DSC)** och klickar sedan på fliken **Noder.**
1. Välj den nod som en konfiguration ska tilldelas.
1. Klicka på **Tilldela nodkonfiguration**
1. Markera nodkonfigurationen `LAMPServer.localhost` och klicka på **OK**. Tillståndskonfigurationen tilldelar nu den kompilerade konfigurationen till `Pending`noden och nodstatusen ändras till . Vid nästa periodiska kontroll hämtar noden konfigurationen, tillämpar den och rapporterar status. Det kan ta upp till 30 minuter för noden att hämta konfigurationen, beroende på nodinställningarna. 
1. Om du vill tvinga fram en omedelbar kontroll kan du köra följande kommando lokalt på den virtuella Linux-datorn: `sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Tilldela en nodkonfiguration](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="view-node-status"></a>Visa nodstatus

Du kan visa status för alla tillståndskonfigurationshanterade noder i ditt Automation-konto. Informationen visas genom att välja **Tillståndskonfiguration (DSC)** och klicka på fliken **Noder.** Du kan filtrera visningen efter status, nodkonfiguration eller namnsökning.

![DSC-nodstatus](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du onboarded en Linux VM till tillståndskonfiguration, skapat en konfiguration för en LAMP stack och distribuerade konfigurationen till den virtuella datorn. Om du vill veta hur du kan använda Azure Automation State Configuration för att aktivera kontinuerlig distribution fortsätter du till artikeln:

> [!div class="nextstepaction"]
> [Continuous deployment to a VM using DSC and Chocolatey](./automation-dsc-cd-chocolatey.md) (Kontinuerlig distribution på en virtuell datorn med hjälp av DSC och Chocolatey)

* Mer information om PowerShell DSC finns i [Översikt över PowerShell-önskad tillståndskonfiguration](https://docs.microsoft.com/powershell/scripting/dsc/overview/overview).
* Mer information om hur du hanterar tillståndskonfiguration från PowerShell finns i [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.automation/).
* Mer information om hur du vidarebefordrar DSC-rapporter till Azure Monitor-loggar för rapportering och avisering finns i [Vidarebefordra dsc-rapportering till Azure Monitor-loggar](automation-dsc-diagnostics.md).