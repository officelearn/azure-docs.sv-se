---
title: Konfigurations data i Scale-Azure Automation
description: Lär dig hur du konfigurerar data i skala för tillstånds konfiguration i Azure Automation.
keywords: DSC, PowerShell, konfiguration, installation
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f6bb89370c85389d6c41306ed224d27d710cd7c8
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559613"
---
# <a name="configuration-data-at-scale"></a>Konfigurations data i skala

> Gäller för: Windows PowerShell 5.1

Det kan vara en utmaning att hantera hundratals eller tusentals servrar.
Kunderna har fått feedback om att den svåraste aspekten faktiskt hanterar [konfigurations data](/powershell/dsc/configurations/configdata).
Organisera information över logiska konstruktioner som plats, typ och miljö.

> [!NOTE]
> Den här artikeln hänvisar till en lösning som underhålls av communityn för öppen källkod.
> Support är endast tillgängligt i form av GitHub-samarbete, inte från Microsoft.

## <a name="community-project-datum"></a>Community-projekt: Symbol

En community-baserad lösning med namnet [datum](https://github.com/gaelcolas/Datum) har skapats för att lösa den här utmaningen.
Datum bygger på fantastiska idéer från andra konfigurations hanterings plattformar och implementerar samma typ av lösning för PowerShell DSC.
Informationen är [ordnad i textfiler](https://github.com/gaelcolas/Datum#3-intended-usage) baserat på logiska idéer.
Exempel:

- Inställningar som ska gälla globalt
- Inställningar som ska gälla för alla servrar på en plats
- Inställningar som ska gälla för alla databas servrar
- Enskilda Server inställningar

Den här informationen är ordnad i det fil format som du föredrar (JSON, yaml eller PSD1).
Sedan tillhandahålls cmdlets för att generera konfigurationsfiler för data genom att [konsolidera informationen](https://github.com/gaelcolas/Datum#datum-tree) från varje fil i till en enskild vy av en server eller server roll.

När datafilerna har genererats kan du använda dem med [DSC-konfigurations skript](/powershell/dsc/configurations/write-compile-apply-configuration) för att generera MOF-filer och [Ladda upp MOF-filerna till Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Registrera sedan dina servrar från antingen [lokalt](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) eller [i Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) för att hämta konfigurationer.

Om du vill prova datum går du till [PowerShell-galleriet](https://www.powershellgallery.com/packages/datum/) och laddar ned lösningen eller klickar på "Project site" för att visa [dokumentationen](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Nästa steg

- [Översikt över önskad tillstånds konfiguration i Windows PowerShell](/powershell/dsc/overview/overview)
- [DSC-resurser](/powershell/dsc/resources/resources)
- [Konfigurera den lokala Configuration Manager](/powershell/dsc/managing-nodes/metaconfig)
