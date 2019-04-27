---
title: Om Azure DevTest Labs | Microsoft Docs
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561463"
---
# <a name="about-azure-devtest-labs"></a>Om Azure DevTest Labs
Azure DevTest Labs kan utvecklare på team effektivt själv hantera virtuella datorer (VM) och PaaS-resurser utan att behöva vänta godkännanden.

DevTest Labs skapar labs som består av förkonfigurerade baser eller Azure Resource Manager-mallar. Dessa har alla nödvändiga verktyg och program som du kan använda för att skapa miljöer. Du kan skapa miljöer på några minuter, istället för timmar eller dagar.

Genom att använda DevTest Labs kan testa du de senaste versionerna av dina program genom att göra följande uppgifter:

- Snabbt etablera Windows och Linux-miljöer med återanvändningsbara mallar och artefakter.
- Integrera enkelt din distributionspipeline med DevTest Labs och etablera miljöer på begäran.
- Skala upp dina belastningstester genom att etablera flera testagenter och skapa företablerade miljöer för utbildning och demonstrationer.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Funktioner
DevTest Labs erbjuder följande funktioner för utvecklare som arbetar med virtuella datorer:

- Skapa virtuella datorer snabbt genom att följa mindre än fem enkla steg.
- Välj bland en granskad lista över VM-databaser som är konfigurerad, godkända och auktoriserats av gruppledare eller central IT.
- Skapa virtuella datorer från färdiga anpassade avbildningar som har den programvara och verktyg som installerats. 
- Skapa virtuella datorer från formler som är i stort sett anpassade avbildningar som kombineras med de senaste versioner av programvaran som installeras när de virtuella datorerna skapas. 
- Installera artefakter som är tillägg distribuerade på virtuella datorer när de är etablerade.
- Ställa in automatisk avstängning och scheman för virtuella datorer med automatisk start.
- Gör anspråk på en förskapad virtuell dator utan att gå via skapandeprocessen.

DevTest Labs erbjuder följande funktioner för utvecklare som arbetar med PaaS-miljöer:

- Använd Resource Manager för att snabbt skapa PaaS-miljöer med färre än tre enkla steg.
- Välj bland en granskad lista över Resource Manager-mallar som är konfigurerad och auktoriserats av gruppledare eller central IT.
- Skapa en tom resursgrupp (sandbox) med hjälp av Resource Manager-mall för att utforska Azure inom ramen för ett labb.

DevTest Labs kan också centrala IT-avdelningen att kontrollera avfall, optimera kostnader för resurser och stannar inom budgetar genom att göra följande uppgifter:  

- Ställa in automatisk avstängning och automatisk start på virtuella datorer.
- Ange principer för hur många virtuella datorer som användare kan skapa.
- Ange principer på VM-storlekar och avbildningar i galleriet som användare välja från.
- Med labb för att spåra kostnader och inställningen mål.
- Få ett meddelande på hög beräknade kostnader för labb så att du kan vidta nödvändiga åtgärder.

DevTest Labs ger följande fördelar skapa, konfigurera och hantera miljöer i molnet.

## <a name="cost-control-and-governance"></a>Kostnad kontroll och styrning
DevTest Labs gör det lättare att kontrollera kostnaderna genom att du kan utföra följande uppgifter:

- [Ange principer för dina labb](devtest-lab-get-started-with-lab-policies.md), till exempel antal virtuella datorer per användare eller per labb. 
- Skapa [principer för att automatiskt stänga ned](devtest-lab-set-lab-policy.md) och starta virtuella datorer.
- Spåra kostnader på virtuella datorer och PaaS-resurser kunde i labb för hålla dig inom [din budget](devtest-lab-configure-cost-management.md).
- Håll dig inom ramen för dina labb så att du inte få fram resurser utanför dem.

## <a name="quickly-get-to-ready-to-test"></a>Få snabb tillgång till redo att testa
DevTest Labs kan du skapa företablerade miljöer som är utrustade med allt ditt team behöver för att utveckla och testa program. Bara [hämta miljöerna](devtest-lab-add-claimable-vm.md) där det senaste framgångsrika bygget i programmet är installerat och börja arbeta. Eller Använd behållare för att skapa en ännu snabbare, mer slimmade miljö.

## <a name="create-once-use-everywhere"></a>Skapa en gång, använd överallt
Samla in och dela PaaS [miljömallar](devtest-lab-create-environment-from-arm.md) och [artefakter](add-artifact-repository.md) inom gruppen eller organisationen – allt inom källkontroll – lätt kan skapa developer-och testmiljöer.

## <a name="worry-free-self-service"></a>Problemfri självbetjäning
DevTest Labs kan dina utvecklare och testare att snabbt och enkelt [skapa virtuella IaaS-datorer](devtest-lab-add-vm.md) och [PaaS-resurser](devtest-lab-create-environment-from-arm.md) med hjälp av en uppsättning förkonfigurerade resurser.

## <a name="use-iaas-and-paas-resources"></a>Använda IaaS och PaaS-resurser 
Utvecklare kan också skapa PaaS-resurser, till exempel Azure Service Fabric-kluster, funktionen Web Apps i Azure App Service och SharePoint-grupper, med hjälp av Resource Manager-mallar. Kom igång på PaaS i labs genom att använda mallar från den [offentliga miljön databasen](devtest-lab-configure-use-public-environments.md) eller [ansluta labbet till din egen Git-lagringsplats](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). Du kan också spåra kostnader på dessa resurser för att hålla sig inom din budget.

## <a name="integrate-with-your-existing-toolchain"></a>Integreras med din befintliga verktygskedja
Använd färdiga plugin-program eller API: et att etablera miljöer för utveckling/testning direkt från ditt önskade [verktyg för kontinuerlig integrering (CI)](devtest-lab-integrate-ci-cd-vsts.md), integrated development environment (IDE) eller automatiserade releasepipeline. Du kan också använda omfattande kommandoradsverktyget.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- Läs mer om DevTest Labs i [DevTest Labs-koncept](devtest-lab-concepts.md).
- En genomgång med stegvisa instruktioner finns i [självstudien: Konfigurera ett labb med hjälp av Azure DevTest Labs](tutorial-create-custom-lab.md).