---
title: Om Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur DevTest Labs kan göra det enkelt att skapa, hantera och övervaka virtuella Azure-datorer
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: b7cd6bb1fd0377ca1440d9c667453df922aacbd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561463"
---
# <a name="about-azure-devtest-labs"></a>Om Azure DevTest Labs
Azure DevTest Labs gör det möjligt för utvecklare i team att effektivt självhantera virtuella datorer och PaaS-resurser utan att vänta på godkännanden.

DevTest Labs skapar labb som består av förkonfigurerade baser eller Azure Resource Manager-mallar. Dessa har alla nödvändiga verktyg och programvara som du kan använda för att skapa miljöer. Du kan skapa miljöer på några minuter, i motsats till timmar eller dagar.

Genom att använda DevTest Labs kan du testa de senaste versionerna av dina program genom att utföra följande uppgifter:

- Etablera snabbt Windows- och Linux-miljöer med hjälp av återanvändbara mallar och artefakter.
- Integrera enkelt din distributionspipeline med DevTest Labs och etablera miljöer på begäran.
- Skala upp dina belastningstester genom att etablera flera testagenter och skapa företablerade miljöer för utbildning och demonstrationer.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Funktioner
DevTest Labs tillhandahåller följande funktioner för utvecklare som arbetar med virtuella datorer:

- Skapa virtuella datorer snabbt genom att följa färre än fem enkla steg.
- Välj från en kurerad lista över VM-baser som är konfigurerade, godkända och auktoriserade av gruppledningen eller den centrala IT-datorn.
- Skapa virtuella datorer från förskapade anpassade avbildningar som har all programvara och alla verktyg som redan är installerade. 
- Skapa virtuella datorer från formler som i huvudsak är anpassade avbildningar i kombination med de senaste versionerna av programvaran som installeras när de virtuella datorerna skapas. 
- Installera artefakter som är tillägg som distribueras på virtuella datorer när de har etablerats.
- Ange automatisk avstängning och automatisk start scheman på virtuella datorer.
- Gör anspråk på en förskapad virtuell dator utan att gå igenom skapandeprocessen.

DevTest Labs tillhandahåller följande funktioner för utvecklare som arbetar med PaaS-miljöer:

- Använd Resource Manager för att snabbt skapa PaaS-miljöer genom att följa färre än tre enkla steg.
- Välj från en kurerad lista över Resource Manager-mallar, som är konfigurerade och auktoriserade av gruppledningen eller den centrala IT-tjänsten.
- Skapa en tom resursgrupp (sandlåda) med hjälp av en Resource Manager-mall för att utforska Azure inom ramen för ett labb.

DevTest Labs gör det också möjligt för central IT att styra avfall, optimera kostnaderna för resurser och hålla sig inom budgetarna genom att utföra följande uppgifter:  

- Ställa in automatisk avstängning och automatisk start scheman på virtuella datorer.
- Ange principer för antalet virtuella datorer som användare kan skapa.
- Ange principer för virtuella datorers storlekar och galleribilder som användarna väljer mellan.
- Spåra kostnader och sätta upp mål på labb.
- Få meddelanden om höga beräknade kostnader för labb så att du kan vidta nödvändiga åtgärder.

DevTest Labs ger följande fördelar när du skapar, konfigurerar och hanterar miljöer i molnet.

## <a name="cost-control-and-governance"></a>Kostnadskontroll och styrning
DevTest Labs gör det enklare att kontrollera kostnaderna genom att låta dig utföra följande uppgifter:

- [Ange principer för dina labb,](devtest-lab-get-started-with-lab-policies.md)till exempel antal virtuella datorer per användare eller per labb. 
- Skapa [principer för att automatiskt stänga av](devtest-lab-set-lab-policy.md) och starta virtuella datorer.
- Spåra kostnader på virtuella datorer och PaaS-resurser som snurrats upp i labb för att hålla dig inom [din budget.](devtest-lab-configure-cost-management.md)
- Håll dig inom ramen för dina labb så att du inte snurra upp resurser utanför dem.

## <a name="quickly-get-to-ready-to-test"></a>Snabbt komma till redo att testa
Med DevTest Labs kan du skapa företablerade miljöer som är utrustade med allt ditt team behöver för att utveckla och testa program. Bara [göra anspråk på de miljöer](devtest-lab-add-claimable-vm.md) där den sista bra bygga av ditt program är installerat och börja arbeta. Eller använd behållare för ännu snabbare, smalare miljöskapande.

## <a name="create-once-use-everywhere"></a>Skapa en gång, använd överallt
Samla in och dela PaaS-miljömallar och [artefakter](add-artifact-repository.md) i ditt team eller din organisation – allt i källkontroll – för att enkelt skapa utvecklar- och testmiljöer. [environment templates](devtest-lab-create-environment-from-arm.md)

## <a name="worry-free-self-service"></a>Bekymmersfri självbetjäning
DevTest Labs gör det möjligt för utvecklare och testare att snabbt och enkelt [skapa virtuella IaaS-datorer](devtest-lab-add-vm.md) och [PaaS-resurser](devtest-lab-create-environment-from-arm.md) med hjälp av en uppsättning förkonfigurerade resurser.

## <a name="use-iaas-and-paas-resources"></a>Använd IaaS- och PaaS-resurser 
Utvecklare kan också skapa PaaS-resurser, till exempel Azure Service Fabric-kluster, webbappfunktionen i Azure App Service och SharePoint-grupper, med hjälp av Resource Manager-mallar. Om du vill komma igång med PaaS i labb använder du mallarna från den [offentliga miljöns databas](devtest-lab-configure-use-public-environments.md) eller [ansluter labbet till din egen Git-databas](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). Du kan också spåra kostnader för dessa resurser för att hålla dig inom din budget.

## <a name="integrate-with-your-existing-toolchain"></a>Integrera med din befintliga verktygskedja
Använd färdiga plugin-program eller API:et för att etablera utvecklings-/testmiljöer direkt från ditt önskade [verktyget för kontinuerlig integrering (CI),](devtest-lab-integrate-ci-cd-vsts.md)integrerad utvecklingsmiljö (IDE) eller automatisk versionspipeline. Du kan också använda det omfattande kommandoradsverktyget.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- Mer information om DevTest Labs finns i [DevTest Labs-begrepp](devtest-lab-concepts.md).
- En genomgång med steg-för-steg-instruktioner finns i [Självstudiekurs: Konfigurera ett labb med hjälp av Azure DevTest Labs](tutorial-create-custom-lab.md).