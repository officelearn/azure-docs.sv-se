---
title: Konfiguration baserad på STIG att använda i tillståndskonfiguration - Azure Automation
description: Lär dig mer om konfigurationer baserat på STIG för tillståndskonfiguration i Azure Automation.
keywords: dsc,powershell,konfiguration,setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d6d257198fcae54b1214d77f6b905d876d2687f5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585536"
---
# <a name="configuration-based-on-stig"></a>Konfiguration baserad på STIG

> Gäller: Windows PowerShell 5.1

Det kan vara svårt att skapa konfigurationsinnehåll för första gången.
I många fall är målet att automatisera konfigurationen av servrar efter en "baslinje" som förhoppningsvis anpassar sig till en branschrekommendation.

> [!NOTE]
> Den här artikeln refererar till en lösning som underhålls av open source-communityn.
> Support är endast tillgängligt i form av GitHub-samarbete, inte från Microsoft.

## <a name="community-project-powerstig"></a>Samhällsprojekt: PowerSTIG

Ett community-projekt med namnet [Powerstig](https://github.com/microsoft/powerstig) syftar till att lösa problemet genom att generera DSC-innehåll baserat på [offentlig information](https://public.cyber.mil/stigs/) om STIG (Security Technical Implementation Guide).

Att hantera baslinjer är mer komplicerat än det låter.
Många organisationer måste [dokumentera undantag](https://github.com/microsoft/powerstig#powerstigdata) från regler och hantera dessa data i stor skala.
Powerstig åtgärdar problemet genom att tillhandahålla [sammansatta resurser](https://github.com/microsoft/powerstig#powerstigdsc) för att ta itu med varje område i konfigurationen i stället för att försöka åtgärda hela inställningsområdet i en stor fil.

När konfigurationerna har genererats kan du använda [DSC-konfigurationsskripten](/powershell/scripting/dsc/configurations/configurations) för att generera MOF-filer och [ladda upp MOF-filerna till Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Registrera sedan dina servrar från antingen [lokalt](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) eller [i Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) för att hämta konfigurationer.

Om du vill prova PowerSTIG besöker du [PowerShell-galleriet](https://www.powershellgallery.com) och hämtar lösningen eller klickar på "Projektplats" för att visa [dokumentationen](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Nästa steg

- [Översikt över önskad tillståndskonfiguration i Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [DSC-resurser](/powershell/scripting/dsc/resources/resources)
- [Konfigurera den lokala konfigurationshanteraren](/powershell/scripting/dsc/managing-nodes/metaconfig)
