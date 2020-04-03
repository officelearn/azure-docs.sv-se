---
title: Konvertera konfigurationer till sammansatta resurser för tillståndskonfiguration - Azure Automation
description: Lär dig hur du konverterar konfigurationer till sammansatta resurser för tillståndskonfiguration i Azure Automation.
keywords: dsc,powershell,konfiguration,setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a39b038d31d1b4a614ff0acf7df2586706bb0404
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585516"
---
# <a name="convert-configurations-to-composite-resources"></a>Konvertera konfigurationer till sammansatta resurser

> Gäller: Windows PowerShell 5.1

När du har börjat skapa konfigurationer kan du snabbt skapa "scenarier" som hanterar grupper av inställningar.
Exempel skulle vara:

- skapa en webbserver
- skapa en DNS-server
- skapa en SharePoint-server
- konfigurera ett SQL-kluster
- hantera brandväggsinställningar
- hantera lösenordsinställningar

Om du är intresserad av att dela detta arbete med andra, är det bästa alternativet att paketera konfigurationen som en [sammansatt resurs](/powershell/scripting/dsc/resources/authoringresourcecomposite).
Att skapa sammansatta resurser för första gången kan vara överväldigande.

> [!NOTE]
> Den här artikeln refererar till en lösning som underhålls av open source-communityn.
> Support är endast tillgängligt i form av GitHub-samarbete, inte från Microsoft.

## <a name="community-project-compositeresource"></a>Community-projekt: CompositeResource

En community underhållen lösning med namnet [CompositeResource](https://github.com/microsoft/compositeresource) har skapats för att lösa den här utmaningen.

CompositeResource automatiserar processen med att skapa en ny modul från konfigurationen.
Du börjar med [att punktkopa](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) konfigurationsskriptet på din arbetsstation (eller byggserver) så att det läses in i minnet.
Använd sedan funktionen som tillhandahålls av modulen CompositeResource för att automatisera en konvertering i stället för att köra konfigurationen för att generera en MOF-fil.
Cmdleten kommer att läsa in innehållet i din konfiguration, få en lista över parametrar och generera en ny modul med allt du behöver.

När du har genererat en modul kan du öka versionen och lägga till viktig information varje gång du gör ändringar och publicera den i din egen [PowerShellGet-databas](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

När du har skapat en sammansatt resursmodul som innehåller din konfiguration (eller flera konfigurationer) kan du använda dem i [den komposterbara redigeringsupplevelsen](/azure/automation/compose-configurationwithcompositeresources) i Azure eller lägga till dem i [DSC-konfigurationsskript](/powershell/scripting/dsc/configurations/configurations) för att generera MOF-filer och [ladda upp MOF-filerna till Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Registrera sedan dina servrar från antingen [lokalt](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) eller [i Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) för att hämta konfigurationer.
Den senaste uppdateringen av projektet har också publicerat [runbooks](https://www.powershellgallery.com/packages?q=DscGallerySamples) för Azure Automation för att automatisera processen med att importera konfigurationer från PowerShell-galleriet.

Om du vill prova att automatisera skapandet av sammansatta resurser för DSC besöker du [PowerShell-galleriet](https://www.powershellgallery.com/packages/compositeresource/) och hämtar lösningen eller klickar på "Projektplats" för att visa [dokumentationen](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Nästa steg

- [Översikt över önskad tillståndskonfiguration i Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [DSC-resurser](/powershell/scripting/dsc/resources/resources)
- [Konfigurera den lokala konfigurationshanteraren](/powershell/scripting/dsc/managing-nodes/metaconfig)
