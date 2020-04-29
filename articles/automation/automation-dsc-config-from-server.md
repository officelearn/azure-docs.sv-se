---
title: Skapa konfigurationer från befintliga servrar – Azure Automation
description: Lär dig hur du skapar konfigurationer från befintliga servrar för Azure Automation.
keywords: DSC, PowerShell, konfiguration, installation
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dff9b8f52207a38cf7eaddefa178aff262ddc546
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585552"
---
# <a name="create-configurations-from-existing-servers"></a>Skapa konfigurationer från befintliga servrar

> Gäller för: Windows PowerShell 5,1

Det kan vara en utmaning att skapa konfigurationer från befintliga servrar.
Du kanske inte vill ha *alla* inställningar, bara de som du bryr dig om.
Du måste även veta i vilken ordning inställningarna ska tillämpas för att konfigurationen ska kunna tillämpas.

> [!NOTE]
> Den här artikeln hänvisar till en lösning som underhålls av communityn för öppen källkod.
> Support är endast tillgängligt i form av GitHub-samarbete, inte från Microsoft.

## <a name="community-project-reversedsc"></a>Community-projekt: ReverseDSC

En community-baserad lösning med namnet [ReverseDSC](https://github.com/microsoft/reversedsc) har skapats för att fungera i det här avsnittet som startar SharePoint.

Lösningen bygger på SharePointDSC- [resursen](https://github.com/powershell/sharepointdsc) och utökar den för att dirigera [insamlings information](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) från befintliga SharePoint-servrar.
Den senaste versionen har flera [extraherings lägen](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) för att avgöra vilken nivå av information som ska inkluderas.

Resultatet av att använda lösningen är att skapa [konfigurations data](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) som ska användas med konfigurations skript för SharePointDSC.

När datafilerna har genererats kan du använda dem med [DSC-konfigurations skript](/powershell/scripting/dsc/overview/overview) för att generera MOF-filer och [Ladda upp MOF-filerna till Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Registrera sedan dina servrar från antingen [lokalt](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) eller [i Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) för att hämta konfigurationer.

Om du vill testa ReverseDSC går du till [PowerShell-galleriet](https://www.powershellgallery.com/packages/ReverseDSC/) och laddar ned lösningen eller klickar på "Project site" för att visa [dokumentationen](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Nästa steg

- [Översikt över önskad tillstånds konfiguration i Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [DSC-resurser](/powershell/scripting/dsc/resources/resources)
- [Konfigurera den lokala Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)
