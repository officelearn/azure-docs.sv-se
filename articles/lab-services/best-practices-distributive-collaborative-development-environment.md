---
title: Distribuerad samarbetsutveckling av Azure DevTest Labs-resurser
description: Innehåller metodtips för hur du konfigurerar en distribuerad och samarbetsinriktad utvecklingsmiljö för att utveckla DevTest Labs-resurser.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9469591b1945adaffca973828d619d5d06655262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170110"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Metodtips för distribuerad och samarbetsinriktad utveckling av Azure DevTest Labs-resurser
Distribuerad samarbetsutveckling gör det möjligt för olika team eller personer att utveckla och underhålla en kodbas. För att lyckas beror utvecklingsprocessen på förmågan att skapa, dela och integrera information. Den här nyckelutvecklingsprincipen kan användas i Azure DevTest Labs. Det finns flera typer av resurser i ett labb som ofta fördelas mellan olika laboratorier inom ett företag. De olika typerna av resurser är inriktade på två områden:

- Resurser som lagras internt i labbet (lab-baserade)
- Resurser som lagras i [externa databaser som är anslutna till labbet](devtest-lab-add-artifact-repo.md) (koddatabasbaserad). 

I det här dokumentet beskrivs några metodtips som möjliggör samarbete och distribution mellan flera team samtidigt som anpassning och kvalitet säkerställs på alla nivåer.

## <a name="lab-based-resources"></a>Labbbaserade resurser

### <a name="custom-virtual-machine-images"></a>Anpassade avbildningar för virtuella datorer
Du kan ha en gemensam källa till anpassade avbildningar som distribueras till övningar på en nattlig basis. Detaljerad information finns i [Bildfabriken](image-factory-create.md).    

### <a name="formulas"></a>Formler
[Formler](devtest-lab-manage-formulas.md) är lab-specifika och inte har en distributionsmekanism. Labbmedlemmarna gör all utveckling av formler. 

## <a name="code-repository-based-resources"></a>Koddatabasbaserade resurser
Det finns två olika funktioner som baseras på koddatabaser, artefakter och miljöer. Den här artikeln går igenom funktionerna och hur du mest effektivt ställer in databaser och arbetsflöden så att möjligheten att anpassa tillgängliga artefakter och miljöer på organisationsnivå eller teamnivå.  Det här arbetsflödet baseras på [standardstrategi för förgrening av källkodskontroll](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops). 

### <a name="key-concepts"></a>Viktiga begrepp
Källinformationen för artefakter innehåller metadata, skript. Källinformationen för miljöer innehåller metadata och Resource Manager-mallar med alla stödfiler som PowerShell-skript, DSC-skript, Zip-filer och så vidare.  

### <a name="repository-structure"></a>Databasstruktur  
Den vanligaste konfigurationen för källkodskontroll (SCC) är att ställa in en struktur på flera nivåer för lagring av kodfiler (Resource Manager-mallar, metadata och skript) som används i labben. Skapa olika databaser för att lagra resurser som hanteras av de olika nivåerna i verksamheten:   

- Företagsomfattande resurser.
- Affärsenhet/Divisionsomfattande resurser
- Teamspecifika resurser.

Var och en av dessa nivåer länkar till en annan databas där huvudgrenen måste vara av produktionskvalitet. [Grenarna](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) i varje databas skulle vara för utveckling av dessa specifika resurser (artefakter eller mallar). Den här strukturen stämmer väl överens med DevTest Labs eftersom du enkelt kan ansluta flera databaser och flera grenar samtidigt till organisationens labb. Databasnamnet ingår i användargränssnittet för att undvika förvirring när det finns identiska namn, beskrivningar och utgivare.
     
I följande diagram visas två databaser: en företagsdatabas som underhålls av IT-avdelningen och en divisionsdatabas som underhålls av R&D-divisionen.

![Ett urval omfördelnings- och samarbetsutvecklingsmiljö](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Denna skiktade struktur möjliggör utveckling samtidigt som en högre kvalitetsnivå på huvudgrenen samtidigt som flera databaser är anslutna till ett labb möjliggör större flexibilitet.

## <a name="next-steps"></a>Nästa steg    
Se följande artiklar:

- Lägga till en databas i ett labb med [azure-portalen](devtest-lab-add-artifact-repo.md) eller via [Azure Resource Management-mallen](add-artifact-repository.md)
- [DevTest Labs artefakter](devtest-lab-artifact-author.md)
- [DevTest Labs miljöer](devtest-lab-create-environment-from-arm.md).
