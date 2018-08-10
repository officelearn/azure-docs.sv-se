---
title: Azure Automation State Configuration-översikt
description: En översikt över Azure Automation tillstånd Configuration (DSC), dess villkor och kända problem
keywords: PowerShell dsc, önskad tillståndskonfiguration, powershell dsc azure
services: automation
ms.service: automation
ms.component: dsc
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2d46803f81b369f8f24a6f0e3c7f32955503e4a
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006069"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation State Configuration-översikt

Azure Automation State Configuration är en Azure-tjänst som gör det möjligt att skriva, hantera och kompilera PowerShell Desired State Configuration (DSC) [konfigurationer](/powershell/dsc/configurations), importera [DSC-resurser](/powershell/dsc/resources), och tilldela konfigurationer till målnoder, allt i molnet.

## <a name="why-use-azure-automation-state-configuration"></a>Varför ska jag använda Azure Automation State Configuration

Azure Automation-Tillståndskonfiguration ger flera fördelar jämfört med hjälp av DSC utanför Azure.

### <a name="built-in-pull-server"></a>Inbyggda hämtningsserver

Azure Automation-Tillståndskonfiguration tillhandahåller en DSC-hämtningsserver som liknar den [Windows funktionen DSC-tjänst](/powershell/dsc/pullserver) så att målnoder får automatiskt konfigurationer, följer av önskat tillstånd och rapportera tillbaka om deras efterlevnad. Inbyggda hämtningsservern i Azure Automation eliminerar behovet av att ställa in och underhålla din egen hämtningsserver. Azure Automation kan riktas mot virtuella eller fysiska Windows- eller Linux datorer, i molnet eller lokalt.

### <a name="management-of-all-your-dsc-artifacts"></a>Hantering av alla DSC-artefakter

Azure Automation State Configuration tar samma hanteringslager till [PowerShell Desired State Configuration](/powershell/dsc/overview) som Azure Automation erbjuder för PowerShell-skript.

Från Azure portal eller PowerShell, kan du hantera alla dina DSC-konfigurationer, resurser och målnoder.

![Skärmbild av bladet Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importera rapportering av data till Log Analytics

Noder som hanteras med Azure Automation-Tillståndskonfiguration Skicka detaljerad status för rapporteringsdata till inbyggda hämtningsservern. Du kan konfigurera Azure Automation State Configuration för att skicka dessa data till Log Analytics-arbetsytan. Läs hur du skickar Tillståndskonfiguration statusdata till Log Analytics-arbetsytan i [vidarebefordra Azure Automation Tillståndskonfiguration rapportdata till loggen nalytics](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Introduktionsfilm

Föredrar du att titta eller läsa? Ta en titt på följande videoklipp från maj 2015, när Azure Automation-Tillståndskonfiguration först lanserades.

> [!NOTE]
> Koncept och livscykel som beskrivs i den här videon är korrekta, har Azure Automation-Tillståndskonfiguration utvecklats mycket sedan videon spelades. Det är nu allmänt tillgänglig, har ett mycket mer omfattande användargränssnitt i Azure-portalen och har stöd för många ytterligare funktioner.

[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Nästa steg

- Kom igång genom att se [komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md)
- Läs hur för att publicera noder i [konfigurera datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md)
- Läs om hur du kompilera DSC-konfigurationer så att du kan tilldela dem till målnoder i [kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md)
- PowerShell-cmdlet-referens, se [tillståndskonfigurationen för Azure Automation-cmdletar](/powershell/module/azurerm.automation/#automation)
- Information om priser finns i [priser för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Om du vill se ett exempel på hur du använder Azure Automation-Tillståndskonfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med hjälp av Azure Automation Tillståndskonfiguration och Chocolatey](automation-dsc-cd-chocolatey.md)