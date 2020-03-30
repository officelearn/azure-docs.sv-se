---
title: Skapa konfigurationer från befintliga servrar - Azure Automation
description: Lär dig hur du skapar konfigurationer från befintliga servrar för Azure Automation.
keywords: dsc,powershell,konfiguration,setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d888c8fd3bf7cd44c37e7225618bd197f419d275
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76030123"
---
# <a name="create-configurations-from-existing-servers"></a>Skapa konfigurationer från befintliga servrar

> Gäller: Windows PowerShell 5.1

Att skapa konfigurationer från befintliga servrar kan vara en utmanande uppgift.
Du kanske inte vill *ha alla* inställningar, bara de som du bryr dig om.
Även då måste du veta i vilken ordning inställningarna måste tillämpas för att konfigurationen ska kunna tillämpas.

> [!NOTE]
> Den här artikeln refererar till en lösning som underhålls av open source-communityn.
> Support är endast tillgängligt i form av GitHub-samarbete, inte från Microsoft.

## <a name="community-project-reversedsc"></a>Gemenskapsprojekt: ReverseDSC

En community-lösning med namnet [ReverseDSC](https://github.com/microsoft/reversedsc) har skapats för att fungera i det här området som startar SharePoint.

Lösningen bygger på [SharePointDSC-resursen](https://github.com/powershell/sharepointdsc) och utökar den till att samordna [insamling av information](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) från befintliga SharePoint-servrar.
Den senaste versionen har flera [extraheringslägen](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) för att avgöra vilken informationsnivå som ska inkluderas.

Resultatet av att använda lösningen genererar [konfigurationsdata](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) som ska användas med SharePointDSC-konfigurationsskript.

När datafilerna har genererats kan du använda dem med [DSC-konfigurationsskript](/powershell/scripting/dsc/overview/overview) för att generera MOF-filer och [ladda upp MOF-filerna till Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Registrera sedan dina servrar från antingen [lokalt](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) eller [i Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) för att hämta konfigurationer.

Om du vill prova ReverseDSC besöker du [PowerShell-galleriet](https://www.powershellgallery.com/packages/ReverseDSC/) och hämtar lösningen eller klickar på "Projektplats" för att visa [dokumentationen](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Nästa steg

- [Översikt över önskad tillståndskonfiguration i Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [DSC-resurser](/powershell/scripting/dsc/resources/resources)
- [Konfigurera den lokala konfigurationshanteraren](/powershell/scripting/dsc/managing-nodes/metaconfig)
