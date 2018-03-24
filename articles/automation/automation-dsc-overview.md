---
title: Azure Automation DSC-översikt
description: Översikt av Azure Automation önskad tillstånd Configuration (DSC), dess villkoren och kända problem
keywords: PowerShell dsc, önskad tillståndskonfiguration, powershell dsc azure
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 3949b79c3729ecdc2dfdd6297a5f10852e061540
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-automation-dsc-overview"></a>Azure Automation DSC-översikt

Azure Automation DSC är en Azure-tjänst som gör att du kan skriva, hantera och kompilera PowerShell önskad tillstånd Configuration (DSC) [konfigurationer](https://msdn.microsoft.com/powershell/dsc/configurations), importera [DSC resurser](https://msdn.microsoft.com/powershell/dsc/resources), och tilldela konfigurationer på målnoder, i molnet.

## <a name="why-use-azure-automation-dsc"></a>Varför använda Azure Automation DSC

Azure Automation DSC innehåller flera fördelar jämfört med DSC utanför Azure.

### <a name="built-in-pull-server"></a>Inbyggda pull-server

Azure Automation ger en [hämtningsservern DSC](https://msdn.microsoft.com/en-us/powershell/dsc/pullserver) så att målnoder får automatiskt konfigurationer, motsvarar tillståndet och rapportera om kompatibiliteten.
Den inbyggda hämtningsservern i Azure Automation eliminerar behovet av att ställa in och underhålla din egen pull-server.
Azure Automation kan rikta virtuella eller fysiska Windows- eller Linux-datorer, i molnet eller lokalt.

### <a name="management-of-all-your-dsc-artifacts"></a>Hantering av alla DSC-artefakter

Azure Automation DSC ger samma hanteringslager [PowerShell Desired State Configuration](https://msdn.microsoft.com/powershell/dsc/overview) som ger Azure Automation PowerShell-skript.

Du kan hantera alla dina DSC-konfigurationer, resurser och målnoder från Azure-portalen eller från PowerShell.

![Skärmbild av Azure Automation-bladet](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importera rapporteringsdata till logganalys

Noder som hanteras med Azure Automation DSC Skicka detaljerad status rapporteringsdata till den inbyggda pull-servern.
Du kan konfigurera Azure Automation DSC för att skicka data till logganalys-arbetsytan.
Information om hur du skickar DSC statusdata till logganalys-arbetsytan finns [framåt Azure Automation DSC rapporterar data till logganalys](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Introduktionsfilm

Föredrar du att titta eller läsa? Ta en titt på följande videoklipp från maj 2015 när Azure Automation DSC första meddelande.

>[!NOTE]
>Koncept och livscykel som beskrivs i den här videon är korrekt, har Azure Automation DSC nått mycket eftersom den här videon registrerades.
>Nu är allmänt tillgänglig, har en mycket mer omfattande användargränssnitt i Azure-portalen och har stöd för många ytterligare funktioner.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Nästa steg

* Att lära dig hur du vill publicera noder som ska hanteras med Azure Automation DSC finns [Onboarding datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md)
* Om du vill komma igång med Azure Automation DSC, se [komma igång med Azure Automation DSC](automation-dsc-getting-started.md)
* Läs om kompilering av DSC-konfigurationer så att du kan tilldela dem till målnoder i [kompilering konfigurationer i Azure Automation DSC](automation-dsc-compile.md)
* PowerShell-cmdlet-referens för Azure Automation DSC, se [Azure Automation DSC-cmdlets](/powershell/module/azurerm.automation/#automation)
* Information om priser finns [priser för Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)
* Ett exempel på hur Azure Automation DSC i en pipeline för kontinuerlig distribution finns [kontinuerlig distribution IaaS virtuella datorer med hjälp av Azure Automation DSC och Chocolatey](automation-dsc-cd-chocolatey.md)