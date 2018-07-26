---
title: Översikt över Azure Automation DSC
description: En översikt över Azure Automation Desired State Configuration (DSC), dess villkor och kända problem
keywords: PowerShell dsc, önskad tillståndskonfiguration, powershell dsc azure
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f6d49ffa59ed53c0a1966a4132fd5fe1689a13ce
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247357"
---
# <a name="azure-automation-dsc-overview"></a>Översikt över Azure Automation DSC

Azure Automation DSC är en Azure-tjänst som gör det möjligt att skriva, hantera och kompilera PowerShell Desired State Configuration (DSC) [konfigurationer](https://msdn.microsoft.com/powershell/dsc/configurations), importera [DSC-resurser](https://msdn.microsoft.com/powershell/dsc/resources), och tilldela konfigurationer att målnoder, allt i molnet.

## <a name="why-use-azure-automation-dsc"></a>Varför ska jag använda Azure Automation DSC

Azure Automation DSC ger flera fördelar jämfört med hjälp av DSC utanför Azure.

### <a name="built-in-pull-server"></a>Inbyggda hämtningsserver

Azure Automation tillhandahåller en DSC-hämtningsserver som liknar den [Windows funktionen DSC-tjänst](/powershell/dsc/pullserver) så att målnoder får automatiskt konfigurationer, följer av önskat tillstånd och rapportera tillbaka på sin kompatibilitet.
Inbyggda hämtningsservern i Azure Automation eliminerar behovet av att ställa in och underhålla din egen hämtningsserver.
Azure Automation kan riktas mot virtuella eller fysiska Windows- eller Linux datorer, i molnet eller lokalt.

### <a name="management-of-all-your-dsc-artifacts"></a>Hantering av alla DSC-artefakter

Azure Automation DSC tar samma hanteringslager till [PowerShell Desired State Configuration](https://msdn.microsoft.com/powershell/dsc/overview) som Azure Automation erbjuder för PowerShell-skript.

Från Azure portal eller PowerShell, kan du hantera alla dina DSC-konfigurationer, resurser och målnoder.

![Skärmbild av bladet Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importera rapportering av data till Log Analytics

Noder som hanteras med Azure Automation DSC Skicka detaljerad status för rapporteringsdata till inbyggda hämtningsservern.
Du kan konfigurera Azure Automation DSC för att skicka dessa data till Log Analytics-arbetsytan.
Läs hur du skickar data för DSC-status till Log Analytics-arbetsytan i [vidarebefordra Azure Automation DSC rapporterar data till Log Analytics](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Introduktionsfilm

Föredrar du att titta eller läsa? Ta en titt på följande videoklipp från maj 2015, när Azure Automation DSC först lanserades.

>[!NOTE]
>Koncept och livscykel som beskrivs i den här videon är korrekta, har Azure Automation DSC utvecklats mycket sedan videon spelades.
>Det är nu allmänt tillgänglig, har ett mycket mer omfattande användargränssnitt i Azure-portalen och har stöd för många ytterligare funktioner.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Nästa steg

* Läs hur för att publicera noder som ska hanteras med Azure Automation DSC i [konfigurera datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md)
* Kom igång med Azure Automation DSC, se [komma igång med Azure Automation DSC](automation-dsc-getting-started.md)
* Läs om hur du kompilera DSC-konfigurationer så att du kan tilldela dem till målnoder i [kompilera konfigurationer i Azure Automation DSC](automation-dsc-compile.md)
* PowerShell-cmdlet-referens för Azure Automation DSC, se [Azure Automation DSC-cmdletar](/powershell/module/azurerm.automation/#automation)
* Information om priser finns i [priser för Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)
* Om du vill se ett exempel på hur du använder Azure Automation DSC i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution till IaaS-datorer med hjälp av Azure Automation DSC och Chocolatey](automation-dsc-cd-chocolatey.md)
