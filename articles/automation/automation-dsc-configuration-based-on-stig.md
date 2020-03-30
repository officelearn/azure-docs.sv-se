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
ms.openlocfilehash: 275b3bd25f931b73e8a378433899ef9ade4d47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028273"
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
Registrera sedan dina servrar från antingen [lokalt](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) eller [i Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) för att hämta konfigurationer.

Om du vill prova PowerSTIG besöker du [PowerShell-galleriet](https://www.powershellgallery.com) och hämtar lösningen eller klickar på "Projektplats" för att visa [dokumentationen](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Nästa steg

- [Översikt över önskad tillståndskonfiguration i Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [DSC-resurser](/powershell/scripting/dsc/resources/resources)
- [Konfigurera den lokala konfigurationshanteraren](/powershell/scripting/dsc/managing-nodes/metaconfig)
