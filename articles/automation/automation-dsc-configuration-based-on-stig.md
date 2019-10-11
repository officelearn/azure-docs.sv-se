---
title: Konfiguration baserad på STIG som ska användas i tillstånds konfiguration – Azure Automation
description: Lär dig mer om konfigurationer baserat på STIG för tillstånds konfiguration i Azure Automation.
keywords: DSC, PowerShell, konfiguration, installation
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 685b6bda09026e64154590afd66bdfbec43b8b1e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243532"
---
# <a name="configuration-based-on-stig"></a>Konfiguration baserad på STIG

> Gäller för: Windows PowerShell 5,1

Det kan vara svårt att skapa konfigurations innehåll för första gången.
I många fall är målet att automatisera konfigurationen av servrar som följer en "bas linje" som förhoppnings vis anpassas till en bransch rekommendation.

> [!NOTE]
> Den här artikeln hänvisar till en lösning som underhålls av communityn för öppen källkod.
> Support är endast tillgängligt i form av GitHub-samarbete, inte från Microsoft.

## <a name="community-project-powerstig"></a>Community-projekt: PowerSTIG

Ett community-projekt med namnet [PowerSTIG](https://github.com/microsoft/powerstig) syftar till att lösa det här problemet genom att generera DSC-innehåll baserat på [offentlig information](https://public.cyber.mil/stigs/) som tillhandahålls om Stig (Security Technical Implementation Guide).

Att hantera bas linjer är mer komplicerat än det låter.
Många organisationer behöver [dokumentera undantag](https://github.com/microsoft/powerstig#powerstigdata) för regler och hantera dessa data i stor skala.
PowerSTIG löser problemet genom att tillhandahålla [sammansatta resurser](https://github.com/microsoft/powerstig#powerstigdsc) för att adressera varje område i konfigurationen i stället för att försöka hantera hela intervallet med inställningar i en stor fil.

När konfigurationerna har genererats kan du använda DSC- [konfigurations skript](/powershell/scripting/dsc/configurations/configurations) för att generera MOF-filer och [Ladda upp MOF-filerna till Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Registrera sedan dina servrar från antingen [lokalt](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) eller [i Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) för att hämta konfigurationer.

Om du vill testa PowerSTIG går du till [PowerShell-galleriet](http://www.powershellgallery.com) och laddar ned lösningen eller klickar på "Project site" för att visa [dokumentationen](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Nästa steg

- [Översikt över önskad tillstånds konfiguration i Windows PowerShell](/powershell/dsc/overview/overview)
- [DSC-resurser](/powershell/dsc/resources/resources)
- [Konfigurera den lokala Configuration Manager](/powershell/dsc/managing-nodes/metaconfig)
