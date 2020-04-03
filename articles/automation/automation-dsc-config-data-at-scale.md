---
title: Konfigurationsdata i stor skala - Azure Automation
description: Lär dig hur du konfigurerar data i stor skala för tillståndskonfiguration i Azure Automation.
keywords: dsc,powershell,konfiguration,setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 644ea1c00af7e71ff56852298fff18e5293c137b
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585561"
---
# <a name="configuration-data-at-scale"></a>Konfigurationsdata i stor skala

> Gäller: Windows PowerShell 5.1

Hantera hundratals eller tusentals servrar kan vara en utmaning.
Kunderna har gett feedback om att den svåraste aspekten faktiskt hanterar [konfigurationsdata](/powershell/scripting/dsc/configurations/configdata).
Ordna information över logiska konstruktioner som plats, typ och miljö.

> [!NOTE]
> Den här artikeln refererar till en lösning som underhålls av open source-communityn.
> Support är endast tillgängligt i form av GitHub-samarbete, inte från Microsoft.

## <a name="community-project-datum"></a>Samhällsprojekt: Datum

En community-lösning med namnet [Datum](https://github.com/gaelcolas/Datum) har skapats för att lösa den här utmaningen.
Datum bygger på bra idéer från andra plattformar för konfigurationshantering och implementerar samma typ av lösning för PowerShell DSC.
Information [är ordnad i textfiler](https://github.com/gaelcolas/Datum#3-intended-usage) baserat på logiska idéer.
Exempel skulle vara:

- Inställningar som ska gälla globalt
- Inställningar som ska gälla för alla servrar på en plats
- Inställningar som ska gälla för alla databasservrar
- Individuella serverinställningar

Den här informationen är ordnad i det filformat du föredrar (JSON, Yaml eller PSD1).
Sedan cmdlets tillhandahålls för att generera konfigurationsdatafiler genom [att konsolidera informationen](https://github.com/gaelcolas/Datum#datum-tree) från varje fil i till enstaka vy av en server eller serverroll.

När datafilerna har genererats kan du använda dem med [DSC-konfigurationsskript](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) för att generera MOF-filer och [ladda upp MOF-filerna till Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Registrera sedan dina servrar från antingen [lokalt](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) eller [i Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) för att hämta konfigurationer.

Om du vill prova Datum besöker du [PowerShell-galleriet](https://www.powershellgallery.com/packages/datum/) och hämtar lösningen eller klickar på "Projektplats" för att visa [dokumentationen](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Nästa steg

- [Översikt över önskad tillståndskonfiguration i Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [DSC-resurser](/powershell/scripting/dsc/resources/resources)
- [Konfigurera den lokala konfigurationshanteraren](/powershell/scripting/dsc/managing-nodes/metaconfig)
