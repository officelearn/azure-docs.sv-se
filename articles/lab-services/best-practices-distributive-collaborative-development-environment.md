---
title: Distribuerade samarbetsfunktioner utvecklingen av resurser i Azure DevTest Labs | Microsoft Docs
description: Ta del av metodtips för att konfigurera en distribuerad och samarbetsfunktioner utvecklingsmiljö för att utveckla DevTest Labs-resurser.
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
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: d8892b2d00008c9d67f8bc28d1abb7d562dfd95c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079891"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Metodtips för distribuerade och samarbetsfunktioner utvecklingen av resurser i Azure DevTest Labs
Distribuerade samarbetsfunktioner utveckling kan olika team eller personer att utveckla och underhålla en kod som bas. För att lyckas, beror utvecklingsprocessen på möjligheten att skapa, dela och integrera information. Denna utveckling-princip kan användas i Azure DevTest Labs. Det finns flera typer av resurser i ett laboratorium som distribueras ofta mellan olika labs inom företaget. De olika typerna av resurser är fokus i två områden:

- Resurser som lagras internt i labbet (labb-baserad)
- Resurser som lagras i [externa databaser som är anslutna till labbet](devtest-lab-add-artifact-repo.md) (kod baserat på lagringsplatsen). 

Det här dokumentet beskriver metodtips som tillåter samarbete och distribution över flera team samtidigt anpassning och kvalitet på alla nivåer.

## <a name="lab-based-resources"></a>Labb-baserade resurser

### <a name="custom-virtual-machine-images"></a>Anpassade avbildningar
Du kan ha en gemensam källa med anpassade avbildningar som distribueras till labs på basis av varje natt. Detaljerad information finns i [bild factory](image-factory-create.md).    

### <a name="formulas"></a>Formler
[Formler](devtest-lab-manage-formulas.md) är labb-specifika och inte har en distributionsmekanism för. Lab medlemmar göra alla utvecklingen av formler. 

## <a name="code-repository-based-resources"></a>Kod lagringsplats-baserade resurser
Det finns två olika funktioner som är baserade på lagringsplatser, artefakter och miljöer. Den här artikeln går över funktionerna och hur du effektivt ställer in arbetsflöde och databaser så att möjligheten att anpassa tillgängliga artefakter och miljöer på organisationsnivå eller team.  Det här arbetsflödet baseras på Standardtaxor [källkoden styra branchning strategi](/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops). 

### <a name="key-concepts"></a>Viktiga begrepp
Källinformation för artefakter innehåller metadata, skript. Källinformation för miljöer innehåller metadata och Resource Manager-mallar med eventuella stödfiler som PowerShell-skript, DSC-skript, Zip-filer och så vidare.  

### <a name="repository-structure"></a>Struktur för databasen  
Den vanligaste konfigurationen för källkodskontroll (SCC) är att ställa in en struktur med flera nivåer för att lagra kodfiler (Resource Manager-mallar, metadata och skript) som används för att i labs. Mer specifikt kan skapa olika databaser för att lagra resurser som hanteras av de olika nivåerna av företaget:   

- Resurser för hela företaget.
- Business unit/Division-hela resurser
- Team-specifika resurser.

Var och en av dessa nivåer länkar till en annan databas där huvudgrenen måste vara av produktionskvalitet. Den [grenar](/devops/repos/git/git-branching-guidance?view=azure-devops) i varje databas är för utveckling av dessa specifika resurser (artefakter och mallar). Den här strukturen matchar labb som du enkelt kan ansluta flera databaser och flera grenar på samma gång till organisationens övningar. Namnet på lagringsplatsen ingår i användargränssnittet (UI) för att undvika förvirring när det är identiska namn, beskrivning och utgivare.
     
Följande diagram visar två databaser: en lagringsplats för företag som underhålls av IT-avdelningen och en division databas som hanteras av divisionen R & D.

![En exempel parti-och detaljhandeln och samarbetsfunktioner utvecklingsmiljö](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Den här överlappande strukturen möjliggör utveckling samtidigt som högre kvalitet på huvudgrenen när du har flera databaser som är anslutna till ett labb kan större flexibilitet.

## <a name="next-steps"></a>Nästa steg    
Se följande artiklar:

- Lägg till en databas till ett labb med hjälp av antingen den [Azure-portalen](devtest-lab-add-artifact-repo.md) eller via [Azure Resource Manager-mall](add-artifact-repository.md)
- [DevTest Labs artefakter](devtest-lab-artifact-author.md)
- [DevTest Labs miljöer](devtest-lab-create-environment-from-arm.md).
